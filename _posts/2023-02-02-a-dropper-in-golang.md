---
layout: post
category: Malware
tags: [hacking, programming, malwaredev]
---

## Introduction

This blog post is about a fancy program that i wrote.
Due to dramaturgical reasons we will start from the end.
I will first show the program and explain how it works.
Then i will jump back to the other side and explain how i wrote it.

If you are interested in calling C functions from Golang and position
independent assembly code, then read on. If you know how to write a
dropper then you may already know everything that i will show you.

# The Program

Without further ado, let me show you my program.

{% highlight golang %}
package main

/*
#include <stdio.h>
#include <sys/mman.h>
#include <string.h>
#include <unistd.h>
void call(char *shellcode, size_t length) {
	if(fork()) {
		return;
	}
	unsigned char *ptr;
	ptr = (unsigned char *) mmap(0, length, \
		PROT_READ|PROT_WRITE|PROT_EXEC, MAP_ANONYMOUS | MAP_PRIVATE, -1, 0);
	if(ptr == MAP_FAILED) {
		perror("mmap");
		return;
	}
	memcpy(ptr, shellcode, length);
	( *(void(*) ()) ptr)();
}
*/
import "C"

import (
	"encoding/hex"
	"fmt"
	"unsafe"
)

func main() {
	string := "eb1eb801000000bf010000005eba0d0000000f05b83c000000bf000000000f05e8ddffffff48656c6c6f2c20776f726c64210a"
	sc, err := hex.DecodeString(string)
	if err != nil {
		fmt.Println("Unable to convert hex to byte. ", err)
	}
	C.call((*C.char)(unsafe.Pointer(&sc[0])), (C.size_t)(len(sc)))
}
{% endhighlight %}

If you like puzzles, you may want to stop reading here and figure out
for yourself what it does.

This is Golang code that can be compiled as usual (`go build`). It
interfaces C code using the cgo package. In particular, there is a C
function called `call` that can be called from within Golang.
There is a string called `string` that is decoded as hexadecimal and
then given to the function `call`. The function `call` interprets the
decoded string as a function and executes it.

But what does the string
`"eb1eb801000000bf010000005eba0d0000000f05b83c000000bf000000000f05e8ddffffff48656c6c6f2c20776f726c64210a"`
interpreted as function do? Well, it prints `Hello, World!`.
You can see for yourself by writing it to a file and disassembling it, e.g, with radare or objdump (`objdump -b binary -D -m i386:x86-64 -M intel Opcodes.bin`).

But why would anyone program such a monster, you may ask?
Usually programming patterns such as this can be used to load more functionality at run-time (in contrast to having the functionality already baked in at compile-time).
Note how the string does not necessarily need to be compiled in the binary.
It would also be possible for the string to be given at runtime by the
user or even be downloaded from a remote server.

Malware often employs similar mechanism in order to obfuscate its functionality.
In the language of malware development this would be considered a dropper or a loader.
In this case the string would offer some malicious functionality.
The string could lie encrypted in the binary and then be decrypted at runtime in order to evade static analysis by antivirus engines.
Even more sophisticated, the decryption key could be the host name of
the targeted machine, in order to evade analysis by antivirus engines
in the cloud.
But I digress.

# How I Arrived Here

Let us now hop to the other side of this adventure and start at the beginning.
How did i create that string that prints `Hello, World!`?


## First Attempt at Assembly
First of all i needed to get the machine code of a program that emits `Hello, World!`.
So I simply wrote one in assembly.
Below you can see `HalloW64.asm`. This program is targeted towards an
x64 computer running Linux.

{% highlight nasm %}
;; compile with 
;; nasm -f elf64 -o HalloW64.o HalloW64.asm
;; ld -o HalloW64.out HalloW64.o

global _start

section .rodata                 ; section declaration for the string
  msg: db "Hello, world!", 10

section .text       ; section declaration

_start:
  mov rax, 1        ; write syscall
  mov rdi, 1        ; stdout file descriptor in destination index (di)
  mov rsi, msg      ; "Hello, world!" in source index (si)
  mov rdx, 13       ; length of "Hello, world!"
  syscall           ; execute

  mov rax, 60       ; exit syscall
  mov rdi, 0        ; return value success
  syscall           ; execute
{% endhighlight %}

The program has a `.rodata` section that contains the string to be printed.
In this case `Hello, World!`. Then there is a section `.text` that
holds the code for printing the string.
When the CPU processes this code, it starts at `_start:`, fills the
registers necessary for performing the write syscall, executes the
syscall, and then exits the program.

Even though we now have a program that prints `Hello, World!`, you may
already see a problem.
The string that is printed is in a different section than the code
itself.
In particular when executing the write syscall, the register rsi
contains a pointer to a different section. This will not work in our dropper.

We can verify this by looking at the assembly.
```
$ objdump -D HalloW64.out -M intel

Disassembly of section .text:

0000000000401000 <_start>:
  401000:	b8 01 00 00 00       	mov    eax,0x1
  401005:	bf 01 00 00 00       	mov    edi,0x1
  40100a:	48 be 00 20 40 00 00 	movabs rsi,0x402000
  401011:	00 00 00 
  401014:	ba 0d 00 00 00       	mov    edx,0xd
  401019:	0f 05                	syscall 
  40101b:	b8 3c 00 00 00       	mov    eax,0x3c
  401020:	bf 00 00 00 00       	mov    edi,0x0
  401025:	0f 05                	syscall 

Disassembly of section .rodata:

0000000000402000 <msg>:
  402000:	48                   	rex.W
  402001:	65 6c                	gs ins BYTE PTR es:[rdi],dx
  402003:	6c                   	ins    BYTE PTR es:[rdi],dx
  402004:	6f                   	outs   dx,DWORD PTR ds:[rsi]
  402005:	2c 20                	sub    al,0x20
  402007:	77 6f                	ja     402078 <msg+0x78>
  402009:	72 6c                	jb     402077 <msg+0x77>
  40200b:	64 21 0a             	and    DWORD PTR fs:[rdx],ecx
```

The `.text` section is as we would expect it, but the instruction at address `40100a`
moves the absolute value `0x402000` to the rsi register. This is
the address of the msg label.

Objdump thinks that the `.rodata` contains assembly and
therefore tries to show us instructions. These are bogus. It's the
static string `Hello, World!`.

When we execute the assembly above with the loader in Golang, we cannot
control where the message is put. Thus, the program would fail.

## Second Attempt at Assembly
As discussed in the section above, we need to re-write our
assembly as position independent code (PIC).

Let's take a look at the following file `HalloW64nodata.asm`.
The name is due to the fact, that the data section is missing.

{% highlight nasm %}
;; compile with 
;; nasm -f elf64 -o HalloW64nodata.o HalloW64nodata.asm
;; ld -o HalloW64nodata.out HalloW64nodata.o

global _start

section .text       ; section declaration

_start:
  jmp end           ; jmp to the end
start:
  mov rax, 1        ; write syscall
  mov rdi, 1        ; stdout file descriptor in destination index (di)
  pop rsi           ; "Hello, world!" in source index (si)
  mov rdx, 13       ; length of "Hello, world!"
  syscall           ; execute

  mov rax, 60       ; exit syscall
  mov rdi, 0        ; return value success
  syscall           ; execute

end:
  call start
  msg db "Hello, world!", 10
{% endhighlight %}

Execution starts by jumping to `end`. From there `start` is called,
pushing the address of the instruction pointer to the stack. This
instruction pointer is popped to rsi. Hence, rsi contains the address
of the string `Hello World!`.

This code contain the string `Hello, World!` in the `.text`
section. The address of this string is computed at runtime by the
call/pop-trick.

Let's again take a look at the disassembly.
```
$ objdump -d -M intel HalloW64nodata.out 

Disassembly of section .text:

0000000000401000 <_start>:
  401000:	eb 1e                	jmp    401020 <end>

0000000000401002 <start>:
  401002:	b8 01 00 00 00       	mov    eax,0x1
  401007:	bf 01 00 00 00       	mov    edi,0x1
  40100c:	5e                   	pop    rsi
  40100d:	ba 0d 00 00 00       	mov    edx,0xd
  401012:	0f 05                	syscall 
  401014:	b8 3c 00 00 00       	mov    eax,0x3c
  401019:	bf 00 00 00 00       	mov    edi,0x0
  40101e:	0f 05                	syscall 

0000000000401020 <end>:
  401020:	e8 dd ff ff ff       	call   401002 <start>

0000000000401025 <msg>:
  401025:	48                   	rex.W
  401026:	65 6c                	gs ins BYTE PTR es:[rdi],dx
  401028:	6c                   	ins    BYTE PTR es:[rdi],dx
  401029:	6f                   	outs   dx,DWORD PTR ds:[rsi]
  40102a:	2c 20                	sub    al,0x20
  40102c:	77 6f                	ja     40109d <msg+0x78>
  40102e:	72 6c                	jb     40109c <msg+0x77>
  401030:	64 21 0a             	and    DWORD PTR fs:[rdx],ecx
```

This is exactly how we want it.

## Creating a Frankenstein Binary

Next, I cut the position independent code from the binary and
converted it to hex.

We cannot simply take the binary as it is, as it's in the ELF
format. There is some additional information about which sections
exist and where they need to be mapped in the virtual memory when
executing the file.

We don't need this. We need just the `.text` section.
We can either cut it manually, or use `objcopy`.
```
$ objcopy -O binary  --only-section=.text -I elf64-x86-64 HalloW64nodata.out Opcodes.bin
```

The file `Opcodes.bin` now contains exactly the content of the `.text` section.
```
$ xxd Opcodes.bin 
00000000: eb1e b801 0000 00bf 0100 0000 5eba 0d00  ............^...
00000010: 0000 0f05 b83c 0000 00bf 0000 0000 0f05  .....<..........
00000020: e8dd ffff ff48 656c 6c6f 2c20 776f 726c  .....Hello, worl
00000030: 6421 0a                                  d!.
```

As you can see, this is exactly what we got above from the disassembly
by `objdump`. However, when we want to have it as a hex string for
putting it into our dropper we need to cut the first and last column.

```
$ xxd Opcodes.bin | sed 's/........: //;s/  .*//;s/ //g'| tr -d '\n'
eb1eb801000000bf010000005eba0d0000000f05b83c000000bf000000000f05e8ddffffff48656c6c6f2c20776f726c64210a
```

And boom! We have the string.

Next step is putting it into the loader, compiling, executing and
staring at the screen in awe.

## Interfacing Golang with C
I should probably also write a little bit about how I interfaced Go with
C. Actually, my original goal when writing the dropper was to program
a nontrivial program in Golang. You could use any language for the
code, but for educational reasons, i was choosing Golang
There is really not much to say about the C interface. I just read the
documentation of cgo and followed the steps.

# Epilogue
I hope you learned something reading this.
Stay Cheeki Breeki!
