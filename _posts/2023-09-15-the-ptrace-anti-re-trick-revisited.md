---
layout: post
category: Malware
tags: [hacking, programming, malwaredev]
---

# Introduction
A [previous blog post]({% post_url 2023-08-26-the-ptrace-anti-re-trick %})
introduced a well-known anti reverse-engineering technique under Linux
using ptrace.
A program was introduced that showed different
behavior on whether it is being debugged or not.

This program relied on the function `ptrace()`. The program executed
`ptrace()` on itself, and if it failed, it concluded that it was
debugged by another process.

In this blog post, we will go two steps further in the cat-and-mouse
game of binary analysis.
We will show how to defeat the previous trick so that the program can
again be dynamically analyzed.
And then we will show how to overcome this trick and make the life of
the binary analyst hard again.

# Background
In the [last blog post]({% post_url 2023-08-26-the-ptrace-anti-re-trick %})
we introduced the following program containing an Anti
reverse-engineering mechanism. Its behavior depends on whether it is
dynamically analyzed or not.

{% highlight C %}
// compile with gcc -o selfptrace selfptrace.c
#include <sys/ptrace.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[]) {
    if (ptrace(PTRACE_TRACEME,0) < 0) {
        printf("I am traced");
        exit(1);
    }
    printf("I am not traced");
    return 0;
}
{% endhighlight %}

Its behaviour is as follows.

```
$ ./selfptrace
I am not traced
```

```
$ strace ./selfptrace
execve("./selfptrace", ["./selfptrace"], 0x7ffc1c38fb10 /* 67 vars */) = 0
....
ptrace(PTRACE_TRACEME)                  = -1 EPERM (The operation is not permitted)
...
write(1, "I am traced", 11I am traced)  = 11
exit_group(1)                           = ?
+++ exited with 1 +++
```

This program cannot be analyzed using dynamic analysis as dynamic
analysis executes `ptrace()` on its target. However, then the
`ptrace()` that is executed by the program on itself fails and we are
in a different branch of execution.

# Enabling Dynamic Analysis Again

In order to be able to dynamically analyze the program without
altering its behaviour, we can hook the `ptrace()` function and substitute
it by a fake `ptrace()` function that always returns zero.

First of all, let us look at the section `.dynsym` that contains the
dynamic symbol table section of the binary file.

```
$ readelf --dyn-syms --wide selfptrace

Symbol table '.dynsym' contains 6 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.34 (2)
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND printf@GLIBC_2.2.5 (3)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND ptrace@GLIBC_2.2.5 (3)
     5: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND exit@GLIBC_2.2.5 (3)
```

We can clearly see that the function `ptrace()` of glibc is called, as the
string `ptrace@GLIBC_2.2.5` is present.
Important note: There is also a syscall called `ptrace()`. The library
call `ptrace()` provides a wrapper around this syscall.

We can now write a library that provides an alternative implementation
of `ptrace()`.

{% highlight C %}
// compile with gcc bypass_ptrace_trick.c -o bypass_ptrace_trick.so -fPIC -shared
long ptrace(int request, int pid) {
    return 0;
}
{% endhighlight %}

This library is loaded using `LD_PRELOAD` as follows.

```
$ LD_PRELOAD=./bypass_ptrace_trick.so ./selfptrace
I am not traced
```

How can we strace this?
We could `export LD_PRELOAD=/bypass_ptrace_trick.so` to set out fake
library as an environment variable to preload, or use it directly in
front of the strace command.

```
$ LD_PRELOAD=./bypass_ptrace_trick.so strace ./selfptrace
```

However, this leads to the strace process hanging, as we are also
hooking the `ptrace()` functions that are executed within the strace
command.

During researching on the internet I found the following two examples
which again do not work.

```
$ export LD_PRELOAD=/bypass_ptrace_trick.so
$ ltrace -S -l ./bypass_ptrace_trick.so ./selfptrace
$ ltrace -l ./bypass_ptrace_trick.so -x @ ./selfptrace
```

Again, ltrace is broken, as the `ptrace()` functions in ltrace return
zero.

The correct way of stracing a binary that is `LD_PRELOAD`ed with a
library is using the parameter `-E`.

```
$ strace -E LD_PRELOAD=./bypass_ptrace_trick.so ./selfptrace
execve("./selfptrace", ["./selfptrace"], 0x5572e3ae5730 /* 68 vars */) = 0
...
openat(AT_FDCWD, "./bypass_ptrace_trick.so", O_RDONLY|O_CLOEXEC) = 3
...
write(1, "I am not traced", 15I am not traced)         = 15
exit_group(0)                           = ?
+++ exited with 0 +++
```

# The Next Bypass
So now that we have seen how an analyst can substitute functions, how
can we as developers that are interested in protecting our software
defend against that?
In fact, there are multiple ways.

The first approach is to not use the function
`ptrace()`, instead the syscall of the same name, as was already shown
at the [end of the last blog post]({% post_url 2023-08-26-the-ptrace-anti-re-trick %}). In this case, the function
cannot be hooked, as it is not a call to a library and thus is not
resolved in the first place.
In this case an analyst has to patch the binary and remove the `jmp`
instruction for the respective branch, such that the branch is taken,
that is usually taken when the program is not `ptrace`d. This can
again be circumvented by the developer, by checksumming the functions
and checking if they have been altered. If that is the case, a
separate execution branch can be taken.

However, the approach we want to take in the remainder is to stay on
the level of C code (and not use inline assembly).
One idea is to execute `ptrace()` twice on itself. If the process is
not analyzed, then the first call to `ptrace()` succeeds and the
second one fails. If the program is analyzed naively, then the first
and the second `ptrace()` call on itself both fail. If the program is
analyzed by a substituted `ptrace()` function, then both calls
succeed.

{% highlight C %}
// compile with gcc -o selfptrace2 selfptrace2.c
#include <sys/ptrace.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[]) {
    int state = 0;
    if (ptrace(PTRACE_TRACEME,0) == 0) {
        // if first ptrace suceeds, then we are not traced.
        state = 5;
    }
    if (ptrace(PTRACE_TRACEME,0) < 0) {
        // if second ptrace fails, then we are not traced.
        state *= 2;
    }

    if (state == 5*2) { // both conditions need to hold
        printf("I am not traced");
        return 0;
    } else {
        printf("I am traced");
        exit(1);
    }
}
{% endhighlight %}

As explained above, the program can again reliable detect if it is
analyzed.

```
$ ./selfptrace2
I am not traced
```

```
$ strace ./selfptrace2
execve("./selfptrace2", ["./selfptrace2"], 0x7ffcfc6bd5e0 /* 67 vars */) = 0
...
ptrace(PTRACE_TRACEME)                  = -1 EPERM (The operation is not permitted)
ptrace(PTRACE_TRACEME)                  = -1 EPERM (The operation is not permitted)
...
write(1, "I am traced", 11I am traced)             = 11
exit_group(1)                           = ?
+++ exited with 1 +++
```

```
$ strace -E LD_PRELOAD=./bypass_ptrace_trick.so ./selfptrace2
execve("./selfptrace2", ["./selfptrace2"], 0x55ba89345730 /* 68 vars */) = 0
...
write(1, "I am traced", 11I am traced)             = 11
exit_group(1)                           = ?
+++ exited with 1 +++
```

This concludes my blog post.
In a further step of the cat-and-mouse game, the analyst would have to
dive on the level of assembly and patch the jumps manually.

# Acknowledgements
Thanks to my colleague [esj4y](https://twitter.com/esj4y) who told me
about the `-E` argument of strace and thus helped me progress on this
anti reversing journey.

# References
- https://dev.to/nuculabs_dev/bypassing-ptrace-calls-with-ldpreload-on-linux-12jl
- https://seblau.github.io/posts/linux-anti-debugging
