---
layout: post
category: Malware
tags: [hacking, programming, malwaredev]
---

# Introduction

This blog post describes an old Anti-RE technique under Linux using
the ptrace syscall. A program is introduced that shows different
behavior on whether it is being debugged or not.

# Background
When malware is analyzed, it tries to hide itself. There are various
different techniques for this. As a first step, malware is often analyzed
automatically in a sandbox, due to the high number of malware
submissions at antivirus vendors.

This could previously be defeated by
letting the program wait using the sleep syscall, before any malicious
functionality is executed. Thus, the automated analysis likely runs
into a timeout. As a next step in the cat-and-mouse-game antivirus
vendors decided to remove sleep syscalls before analyzing the code.
However, malware authors used different techniques to waste time, such
as computing numerically intense tasks, such as prime factorizations
or enough digits of pi.
Other methods include checking for more than 8GB RAM, as usually
desktop computers have this, but sandboxes not.

# Introducing Ptrace
When the malware is reverse-engineered manually, then usually a
debugger is involved. There is a neat trick to detect the presence of
a debugger using the ptrace syscall.

The ptrace syscall is usually used to debug a process. It asks a
program "Hey, may i debug you?". And the program answers with "Yes, of
course.", or "No, go away!". When it is already ptraced by another
process, then it answers "No, go away!".

So the idea is that a program can try to ptrace itself (from the same
thread). If that fails, then it is already being ptraced by another
process, that may be operated by some reverse-engineer.
Hence, the program can alter its behavior if it is reverse-engineered.
And that is why it is called an Anti-Reverse-Engineering (Anti-RE)
technique.


# A C Program
First, we show a high-level version in C.

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


If this program is executed without a debugger, then it prints that it
is not traced:

```
$ ./selfptrace
I am not traced
```

If, however, it is executed from a debugger, such as executing it with
strace or radare2, it prints that it is traced.

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

```
$ r2 -Ad selfptrace
[0x7fc50d772d80]> dc
I am traced(2888) Process exited with status=0x100
```

# The Same Trick in Assembly
As we are 1337 coderz, we can do the same trick directly in assembly.

{% highlight nasm %}
; compile with
; nasm -f elf64 -o selfptrace.o selfptrace.asm
; ld -o selfptrace.out selfptrace.o

BITS 64

global _start

section .text       ; section declaration

_start:
  mov rax, 101      ; ptrace syscall is 101 (see /usr/include/asm/unistd_64.h)
  mov rdi, 0        ; PTRACE_TRACEME is 0
  mov rsi, 0
  syscall           ; execute

  cmp rax, 0
  jl traced

not_traced:
  call print_not_traced ; put string on stack
  msg_n_tr db "I am not traced!", 16
print_not_traced:
  mov rax, 1        ; write syscall
  mov rdi, 1        ; stdout file descriptor in destination index (di)
  pop rsi           ; put message in source index (si)
  mov rdx, 17       ; length of the message
  syscall           ; execute

  jmp end

traced:
  call print_traced ; put string on stack
  msg_tr db "I am traced!", 12
print_traced:
  mov rax, 1        ; write syscall
  mov rdi, 1        ; stdout file descriptor in destination index (di)
  pop rsi           ; put message in source index (si)
  mov rdx, 12       ; length of the message
  syscall           ; execute

end:
  mov rax, 60       ; exit syscall
  mov rdi, 0        ; return value success
  syscall           ; execute
{% endhighlight %}

I will not show the output of running it with and without a debugger
again, as it is similar to the output above.

# Epilogue
As any lazy hacker with few knowledge of morals writes at the end of
its articles: "Don't use this for malicious purposes. This is for
educational use only."

Now go and write some interesting code.
