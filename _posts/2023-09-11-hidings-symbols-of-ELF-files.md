---
layout: post
category : walkthrough
tags : [hacking, programming, malwaredev]
---

# Introduction
This blog post describes how to hide symbols in ELF files from static
analysis.
While this may seem like a curious gadget, this approach is used in
the real world to prevent software from being analyzed, e.g., in software
protection mechanisms or malware.

As an example, we take a program that prints "Hallo World!" using the
function `puts` from libc.

{% highlight C %}
// compile with gcc -o main main.c

#include<stdio.h>

int main(){
    puts("Hallo World!");
    return 0;
}
{% endhighlight %}

When this is compiled, we can see that it works as expected:
```
$ ./main
Hallo World!
```

If we print all strings in the binary, e.g., using the UNIX tool
`strings` we can see that the binary contains the string `puts`.

```
$ strings main | grep puts 
puts
puts@GLIBC_2.2.5
```

The goal of this blog post is to hide the symbol `puts` from such a
static analysis method without altering the functionality.
That means that the program should still print "Hallo World!" using
the puts method, but in the compiled binary, the string `puts` should
not be present.

In a real-world example the binary may have more complex
functionality. The goal is, that a reverse engineer cannot simply dump
all the functions by looking at the strings of the binary, but has to
work harder.

# Explanation
First and foremost, why is the string `puts` in the library to begin
with?

This string is contained in the dynamic symbol table in the ELF
section `.dynsym`. As far as I understand this table is used by the
dynamic linker at runtime to find the function in libc.

```
$ readelf --dyn-syms --wide main

Symbol table '.dynsym' contains 4 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.34 (2)
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@GLIBC_2.2.5 (3)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
```

# First Step: Resolving Symbols at Runtime
As a first step, we will look for the location of the function `puts`
within libc at runtime.
For this, we open a handle to libc and obtain the address of the
symbol `puts` using the function `dlsym` from `dlfcn.h`.

{% highlight C %}
// compile with gcc -o hide1 hide1.c

#include<dlfcn.h>

int main(){
  // handle to libc
  void *handle = dlopen ("/lib64/libc.so.6", RTLD_LAZY);
  // function pointer to puts within libc
  long (*fp)(char *s);
  fp = dlsym(handle, "puts");
  fp("Hallo World!");
  return 0;
}
{% endhighlight %}

Note that in this program `stdio.h` is not imported. Thus, we have
already removed the import symbol.

This time the string `puts` cannot be found in the `.interp` section.
However, it is used as an argument to the function ``dlsym`.
As this function argument is a static string that is not changed
during runtime, the compiler has decided to put the string `puts` in
the section `.rodata`.
This section contains read-only data.

```
$ readelf -x .rodata hide1

Hex dump of section '.rodata':
  0x00402000 01000200 00000000 00000000 00000000 ................
  0x00402010 2f6c6962 36342f6c 6962632e 736f2e36 /lib64/libc.so.6
  0x00402020 00707574 73004861 6c6c6f20 576f726c .puts.Hallo Worl
  0x00402030 642100                              d!.
```

# Second Step: Putting Strings on the Heap
As a next step, we can remove the string `puts` from the argument to
the function `dlsym`. We can store it in a variable and then invoke
`dlsym` with the variable.

{% highlight C %}
// compile with gcc -o hide2 hide2.c

#include<dlfcn.h>

int main(){
  char puts[] = "puts";
  // handle to libc
  void *handle = dlopen ("/lib64/libc.so.6", RTLD_LAZY);
  // function pointer to puts within libc
  long (*fp)(char *s);
  fp = dlsym(handle, puts);
  fp("Hallo World!");
  return 0;
}
{% endhighlight %}

Note that even though I have named the variable `puts`, this is not
found in the compiled binary. Variables are usually just syntactic
sugar for the human programmer and are substituted by memory
addresses during compile time.

If we trace the library calls, we can see that `dlopen` and `dlsym`
are called. The library function `puts` is not shown by the library call
tracer ltrace.

```
$ ltrace hide2 
dlopen("/lib64/libc.so.6", 1)                                                                                        = 0x7f3577fed080
dlsym(0x7f3577fed080, "puts")                                                                                        = 0x7f3577c7b020
Hallo World!
+++ exited (status 0) +++
```

We can find in the binary string "puts" on the heap though. This is a
further step forward, as we can now manipulate the
string at runtime (in contrast to compiletime).


# Third Approach: Deobfuscating Strings During Runtime
So, as a next step, we store an obfuscated string in the binary that
is deobfuscated at runtime. In this concrete case, we use the
xor-obfuscation. We store the string `$! '` and `T` in the binary.
Then, during runtime we compute `$! '` xor `TTTT` which is equal to
the string `puts`. As a next step, `libc` is loaded and a pointer to
`puts` is obtained. The function at this pointer is then executed. And
voila! We have executed the function `puts` without having the string
`puts` in our binary at runtime.

{% highlight C %}
// compile with gcc -o hide3 hide3.c

#include<dlfcn.h>

int main(){

  char puts[] = "$! '";
  // handle to libc
  void *handle = dlopen ("/lib64/libc.so.6", RTLD_LAZY);
  // function pointer to puts within libc
  long (*fp)(char *s);

  char key = 'T';
  int i = 0;
  for (i; i<sizeof(puts) - 1; i++) {
    puts[i] = puts[i]^key;
  }
  fp = dlsym(handle, puts);
  fp("Hallo World!");
  return 0;
}
{% endhighlight %}

# Epilogue
This is not the most innovative approach to hide symbols in binaries.
Further, usage of `dlopen` and `dlsym` is easily detected.
Another approach is to directly use the syscall `write` instead of
using libc.
However, if library functions are not used at all, but only syscalls,
that may also be suspicious.

So, while this may be a feasible approach when focussing on software
protection mechanisms, in the case of malware development there is
again a tradeoff between "easy to detect" on the one hand and "hard to
detect, but suspicious" on the other hand.

Don't use this for malicious purposes or in military environments.
