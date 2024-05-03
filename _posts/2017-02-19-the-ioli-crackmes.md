---
layout: post
category: CTF Writeups
tags: [hacking, ctf, spoiler, writeup, crackme]
---

## Introduction
In this post we will take a look at the IOLI crackmes.
A crackme is a binary program which accepts input and mostly answers
the input is wrong. The task is to reverse engineer the binary to
learn the correct input. Another option is to modify the binary to
accept all inputs.

The IOLI crackmes are a popular set of 10 crackmes which can be downloaded
[here](https://github.com/Maijin/Workshop2015/tree/master/IOLI-crackme),
[here](http://pof.eslack.org/tmp/IOLI-crackme.tar.gz), or
[here](https://dustri.org/b/files/IOLI-crackme.tar.gz).

There are multiple solutions and tutorials flying around the web, but
if I only read that stuff, I am not going to learn it. Further, If I
do not write a blogpost about it, I have no pressure to work on them.

I am going to look exclusively at the linux binaries. The others should
be similar.

## Level 0
First, let's test the binary.

    $ ./crackme0x00 
    IOLI Crackme Level 0x00
    Password: ABC
    Invalid Password!

Just, like we expected. Let's see if there are any interesting
printable strings in the binary.

	  $ strings crackme0x00 
	  /lib/ld-linux.so.2
	  __gmon_start__
	  libc.so.6
	  printf
	  strcmp
	  scanf
	  _IO_stdin_used
	  __libc_start_main
	  GLIBC_2.0
	  PTRh
	  IOLI Crackme Level 0x00
	  Password: 
	  250382
	  Invalid Password!
	  Password OK :)
	  GCC: (GNU) 3.4.6 (Gentoo 3.4.6-r2, ssp-3.4.6-1.0, pie-8.7.10)
	  ...

Looks like it first prints with printf the string "IOLI Crackme Level
0x00" and "Password:", then it
reads the input with scanf and compares something with strcmp.
Depending on the input it either writes "Invalid Password!" or
"Password OK :)". But what does the number "250382" do? Turns out it
is the password.

	  $ ./crackme0x00 
	  IOLI Crackme Level 0x00
	  Password: 250382
	  Password OK :)

## Level 1

Level 1 is more challenging. We start from the same position.

    $ ./crackme0x01 
    IOLI Crackme Level 0x01
    Password: ABC
    Invalid Password!

But this time strings only prints garbage.

### Extracting the Password

We load the binary with
the binary analysis framework [radare2](http://www.radare.org/) which
is one of the best binary analysis software out there. Forget gdb!

    $ radare2 crackme0x01
    -- Don't do this.
    [0x08048330]> aa
    [x] Analyze all flags starting with sym. and entry0 (aa)

aa is short for analyze all.... Not really. But this is how I remember
it. aa is an alias for 'af@@ sym.*;af@entry0;afva'.
If something in radare2 is unclear, you can write the command followed
by `?`. For example `aa?`. This shows a help text which is sometimes
understandable.
Next, we [p]rint the [d]isassembled [f]unction @sym.main

    [0x08048330]> pdf @sym.main
                ;-- main:
    / (fcn) sym.main 113
    |   sym.main ();
    |           ; var int local_4h @ ebp-0x4
    |           ; var int local_4h_2 @ esp+0x4
    |           ; DATA XREF from 0x08048347 (entry0)
    |           0x080483e4      55             push ebp
    |           0x080483e5      89e5           mov ebp, esp
    |           0x080483e7      83ec18         sub esp, 0x18
    |           0x080483ea      83e4f0         and esp, 0xfffffff0
    |           0x080483ed      b800000000     mov eax, 0
    |           0x080483f2      83c00f         add eax, 0xf
    |           0x080483f5      83c00f         add eax, 0xf
    |           0x080483f8      c1e804         shr eax, 4
    |           0x080483fb      c1e004         shl eax, 4
    |           0x080483fe      29c4           sub esp, eax
    |           0x08048400      c70424288504.  mov dword [esp], str.IOLI_Crackme_Level_0x01_n ; [0x8048528:4]=0x494c4f49 LEA str.IOLI_Crackme_Level_0x01_n ; "IOLI Crackme Level 0x01." @ 0x8048528
    |           0x08048407      e810ffffff     call sym.imp.printf
    |           0x0804840c      c70424418504.  mov dword [esp], str.Password: ; [0x8048541:4]=0x73736150 LEA str.Password: ; "Password: " @ 0x8048541
    |           0x08048413      e804ffffff     call sym.imp.printf
    |           0x08048418      8d45fc         lea eax, [ebp - local_4h]
    |           0x0804841b      89442404       mov dword [esp + local_4h_2], eax
    |           0x0804841f      c704244c8504.  mov dword [esp], 0x804854c  ; [0x804854c:4]=0x49006425 ; "%d"
    |           0x08048426      e8e1feffff     call sym.imp.scanf
    |           0x0804842b      817dfc9a1400.  cmp dword [ebp - local_4h], 0x149a ; [0x149a:4]=0x2ec0804
    |       ,=< 0x08048432      740e           je 0x8048442
    |       |   0x08048434      c704244f8504.  mov dword [esp], str.Invalid_Password__n ; [0x804854f:4]=0x61766e49 LEA str.Invalid_Password__n ; "Invalid Password!." @ 0x804854f
    |       |   0x0804843b      e8dcfeffff     call sym.imp.printf
    |      ,==< 0x08048440      eb0c           jmp 0x804844e
    |      |`-> 0x08048442      c70424628504.  mov dword [esp], str.Password_OK_:__n ; [0x8048562:4]=0x73736150 LEA str.Password_OK_:__n ; "Password OK :)." @ 0x8048562
    |      |    0x08048449      e8cefeffff     call sym.imp.printf
    |      |    ; JMP XREF from 0x08048440 (sym.main)
    |      `--> 0x0804844e      b800000000     mov eax, 0
    |           0x08048453      c9             leave
    \           0x08048454      c3             ret
    
We can see that something in compared (cmp) with 0x149a, and if the comparison was valid, we jump (je = jump if equal) over the block which prints "Invalid_Password" to the address 0x8048442. There is some code which prints "Password_OK".
Maybe 0x149a is already the password. Let us convert this from hexadecimal to binary.

    [0x0000149a]> ? 0x149a
    5274 0x149a 012232 5.2K 0000:049a 5274 0001010010011010 5274.0 5274.000000f 5274.000000

And now test it:

    $ ./crackme0x01 
    IOLI Crackme Level 0x01
    Password: 5274
    Password OK :)

Now we are officially done.

### Binary patching
Since we are am curious, we want to patch the
binary such that it accepts all passwords. For this we need to change the jump
if equal instruction `je 0x8048442` into something which always jumps, i.e. a `jmp 0x8048442`.
`je 0x8048442` is 740e. A jump seems to start with eb and then  the length. so we need to write eb0e instead of 740e.
We need to open the binary with writing enabled:

    $ radare2 -w crackme0x01 
     -- What has been executed cannot be unexecuted

V toggles into visual mode, where we can search with `/740e`
My syntax highlighting on the blog is not sophisticated enough to handle this,
so I just explain it. i goes into insert mode and then we can just write eb0e over 740e.
q exists the visual mode. Let us check if everything is correct.

    [0x08048314]> pdf @sym.main
                ;-- main:
    / (fcn) sym.main 113
    |   sym.main ();
    |           ; var int local_4h @ ebp-0x4
    |           ; var int local_4h_2 @ esp+0x4
    |           ; DATA XREF from 0x08048347 (entry0)
    |           0x080483e4      55             push ebp
    |           0x080483e5      89e5           mov ebp, esp
    |           0x080483e7      83ec18         sub esp, 0x18
    |           0x080483ea      83e4f0         and esp, 0xfffffff0
    |           0x080483ed      b800000000     mov eax, 0
    |           0x080483f2      83c00f         add eax, 0xf
    |           0x080483f5      83c00f         add eax, 0xf
    |           0x080483f8      c1e804         shr eax, 4
    |           0x080483fb      c1e004         shl eax, 4
    |           0x080483fe      29c4           sub esp, eax
    |           0x08048400      c70424288504.  mov dword [esp], str.IOLI_Crackme_Level_0x01_n ; [0x8048528:4]=0x494c4f49 LEA str.IOLI_Crackme_Level_0x01_n ; "IOLI Crackme Level 0x01." @ 0x8048528
    |           0x08048407      e810ffffff     call sym.imp.printf
    |           0x0804840c      c70424418504.  mov dword [esp], str.Password: ; [0x8048541:4]=0x73736150 LEA str.Password: ; "Password: " @ 0x8048541
    |           0x08048413      e804ffffff     call sym.imp.printf
    |           0x08048418      8d45fc         lea eax, [ebp - local_4h]
    |           0x0804841b      89442404       mov dword [esp + local_4h_2], eax
    |           0x0804841f      c704244c8504.  mov dword [esp], 0x804854c  ; [0x804854c:4]=0x49006425 ; "%d"
    |           0x08048426      e8e1feffff     call sym.imp.scanf
    |           0x0804842b      817dfc9a1400.  cmp dword [ebp - local_4h], 0x149a ; [0x149a:4]=0x2ec0804
    |       ,=< 0x08048432      eb0e           jmp 0x8048442
    |       |   0x08048434      c704244f8504.  mov dword [esp], str.Invalid_Password__n ; [0x804854f:4]=0x61766e49 LEA str.Invalid_Password__n ; "Invalid Password!." @ 0x804854f
    |       |   0x0804843b      e8dcfeffff     call sym.imp.printf
    |      ,==< 0x08048440      eb0c           jmp 0x804844e
    |      |`-> 0x08048442      c70424628504.  mov dword [esp], str.Password_OK_:__n ; [0x8048562:4]=0x73736150 LEA str.Password_OK_:__n ; "Password OK :)." @ 0x8048562
    |      |    0x08048449      e8cefeffff     call sym.imp.printf
    |      |    ; JMP XREF from 0x08048440 (sym.main)
    |      `--> 0x0804844e      b800000000     mov eax, 0
    |           0x08048453      c9             leave
    \           0x08048454      c3             ret
    
The jumps seems to look okay.

		$ ./crackme0x01 
		IOLI Crackme Level 0x01
		Password: ABCDEF
		Password OK :)

Yep. done.

## Level 2

Same game as before

		$ ./crackme0x02 
		IOLI Crackme Level 0x02
		Password: AAA
		Invalid Password!

### Binary Patching

We open it in Radare 2 and take a look.
		
    $ radare2 crackme0x02
     -- Unk, unk, unk, unk
    [0x08048330]> aa
    [x] Analyze all flags starting with sym. and entry0 (aa)
    [0x08048330]> pdf @sym.main
                ;-- main:
    / (fcn) sym.main 144
    |   sym.main ();
    |           ; var int local_ch @ ebp-0xc
    |           ; var int local_8h @ ebp-0x8
    |           ; var int local_4h @ ebp-0x4
    |           ; var int local_4h_2 @ esp+0x4
    |           ; DATA XREF from 0x08048347 (entry0)
    |           0x080483e4      55             push ebp
    |           0x080483e5      89e5           mov ebp, esp
    |           0x080483e7      83ec18         sub esp, 0x18
    |           0x080483ea      83e4f0         and esp, 0xfffffff0
    |           0x080483ed      b800000000     mov eax, 0
    |           0x080483f2      83c00f         add eax, 0xf
    |           0x080483f5      83c00f         add eax, 0xf
    |           0x080483f8      c1e804         shr eax, 4
    |           0x080483fb      c1e004         shl eax, 4
    |           0x080483fe      29c4           sub esp, eax
    |           0x08048400      c70424488504.  mov dword [esp], str.IOLI_Crackme_Level_0x02_n ; [0x8048548:4]=0x494c4f49 LEA str.IOLI_Crackme_Level_0x02_n ; "IOLI Crackme Level 0x02." @ 0x8048548
    |           0x08048407      e810ffffff     call sym.imp.printf
    |           0x0804840c      c70424618504.  mov dword [esp], str.Password: ; [0x8048561:4]=0x73736150 LEA str.Password: ; "Password: " @ 0x8048561
    |           0x08048413      e804ffffff     call sym.imp.printf
    |           0x08048418      8d45fc         lea eax, [ebp - local_4h]
    |           0x0804841b      89442404       mov dword [esp + local_4h_2], eax
    |           0x0804841f      c704246c8504.  mov dword [esp], 0x804856c  ; [0x804856c:4]=0x50006425 ; "%d"
    |           0x08048426      e8e1feffff     call sym.imp.scanf
    |           0x0804842b      c745f85a0000.  mov dword [ebp - local_8h], 0x5a ; 'Z'
    |           0x08048432      c745f4ec0100.  mov dword [ebp - local_ch], 0x1ec
    |           0x08048439      8b55f4         mov edx, dword [ebp - local_ch]
    |           0x0804843c      8d45f8         lea eax, [ebp - local_8h]
    |           0x0804843f      0110           add dword [eax], edx
    |           0x08048441      8b45f8         mov eax, dword [ebp - local_8h]
    |           0x08048444      0faf45f8       imul eax, dword [ebp - local_8h]
    |           0x08048448      8945f4         mov dword [ebp - local_ch], eax
    |           0x0804844b      8b45fc         mov eax, dword [ebp - local_4h]
    |           0x0804844e      3b45f4         cmp eax, dword [ebp - local_ch]
    |       ,=< 0x08048451      750e           jne 0x8048461
    |       |   0x08048453      c704246f8504.  mov dword [esp], str.Password_OK_:__n ; [0x804856f:4]=0x73736150 LEA str.Password_OK_:__n ; "Password OK :)." @ 0x804856f
    |       |   0x0804845a      e8bdfeffff     call sym.imp.printf
    |      ,==< 0x0804845f      eb0c           jmp 0x804846d
    |      |`-> 0x08048461      c704247f8504.  mov dword [esp], str.Invalid_Password__n ; [0x804857f:4]=0x61766e49 LEA str.Invalid_Password__n ; "Invalid Password!." @ 0x804857f
    |      |    0x08048468      e8affeffff     call sym.imp.printf
    |      |    ; JMP XREF from 0x0804845f (sym.main)
    |      `--> 0x0804846d      b800000000     mov eax, 0
    |           0x08048472      c9             leave
    \           0x08048473      c3             ret
    
There is a comparison `cmp eax, dword [ebp - local_ch]` and a conditional jump
(jne = jump if not equal). The jump takes us right over the block which prints
"Password_OK" to the "Invalid_Password"-section.
We do not want to jump, so we overwrite the JNE (750e) two NOP operations. A NOP is a
special operation which tells the processor to do nothing. So we do not have to
worry about the addresses changing. Interestingly the NOP operation has a real
purpose other than security exploits. Processors read data from memory in big
chunks and the NOP is used for alignment, such that one does not have to read
the previous block for the last few bytes.
The NOP operation in hexadecimal is 0x90 in x86 Assembly. It is useful to know
this by heart, instead of looking it up all the time. This instruction is used
quite often in binary exploitation.
Using the editor from radare 2 was covered in the last level, so I
just show you the 'fixed' binary. It may be interesting that one can
toggle into the disassembly from visual mode by pressing `A`.

    [0x08048451]> pdf @sym.main
                ;-- main:
    / (fcn) sym.main 144
    |   sym.main ();
    |           ; var int local_ch @ ebp-0xc
    |           ; var int local_8h @ ebp-0x8
    |           ; var int local_4h @ ebp-0x4
    |           ; var int local_4h_2 @ esp+0x4
    |           ; DATA XREF from 0x08048347 (entry0)
    |           0x080483e4      55             push ebp
    |           0x080483e5      89e5           mov ebp, esp
    |           0x080483e7      83ec18         sub esp, 0x18
    |           0x080483ea      83e4f0         and esp, 0xfffffff0
    |           0x080483ed      b800000000     mov eax, 0
    |           0x080483f2      83c00f         add eax, 0xf
    |           0x080483f5      83c00f         add eax, 0xf
    |           0x080483f8      c1e804         shr eax, 4
    |           0x080483fb      c1e004         shl eax, 4
    |           0x080483fe      29c4           sub esp, eax
    |           0x08048400      c70424488504.  mov dword [esp], str.IOLI_Crackme_Level_0x02_n ; [0x8048548:4]=0x494c4f49 LEA str.IOLI_Crackme_Level_0x02_n ; "IOLI Crackme Level 0x02." @ 0x8048548
    |           0x08048407      e810ffffff     call sym.imp.printf
    |           0x0804840c      c70424618504.  mov dword [esp], str.Password: ; [0x8048561:4]=0x73736150 LEA str.Password: ; "Password: " @ 0x8048561
    |           0x08048413      e804ffffff     call sym.imp.printf
    |           0x08048418      8d45fc         lea eax, [ebp - local_4h]
    |           0x0804841b      89442404       mov dword [esp + local_4h_2], eax
    |           0x0804841f      c704246c8504.  mov dword [esp], 0x804856c  ; [0x804856c:4]=0x50006425 ; "%d"
    |           0x08048426      e8e1feffff     call sym.imp.scanf
    |           0x0804842b      c745f85a0000.  mov dword [ebp - local_8h], 0x5a ; 'Z'
    |           0x08048432      c745f4ec0100.  mov dword [ebp - local_ch], 0x1ec
    |           0x08048439      8b55f4         mov edx, dword [ebp - local_ch]
    |           0x0804843c      8d45f8         lea eax, [ebp - local_8h]
    |           0x0804843f      0110           add dword [eax], edx
    |           0x08048441      8b45f8         mov eax, dword [ebp - local_8h]
    |           0x08048444      0faf45f8       imul eax, dword [ebp - local_8h]
    |           0x08048448      8945f4         mov dword [ebp - local_ch], eax
    |           0x0804844b      8b45fc         mov eax, dword [ebp - local_4h]
    |           0x0804844e      3b45f4         cmp eax, dword [ebp - local_ch]
    |           0x08048451      90             nop
    |           0x08048452      90             nop
    |           0x08048453      c704246f8504.  mov dword [esp], str.Password_OK_:__n ; [0x804856f:4]=0x73736150 LEA str.Password_OK_:__n ; "Password OK :)." @ 0x804856f
    |           0x0804845a      e8bdfeffff     call sym.imp.printf
    |       ,=< 0x0804845f      eb0c           jmp 0x804846d
    |       |   0x08048461      c704247f8504.  mov dword [esp], str.Invalid_Password__n ; [0x804857f:4]=0x61766e49 LEA str.Invalid_Password__n ; "Invalid Password!." @ 0x804857f
    |       |   0x08048468      e8affeffff     call sym.imp.printf
    |       |   ; JMP XREF from 0x0804845f (sym.main)
    |       `-> 0x0804846d      b800000000     mov eax, 0
    |           0x08048472      c9             leave
    \           0x08048473      c3             ret
    
Testing if it works.

    $ ./crackme0x02
    IOLI Crackme Level 0x02
    Password: ABCD
    Password OK :)

Level up? Yeah, under normal circumstances yes. But we are curious
and want to know the password.

### Extracting the password by concolic execution

We could look at the disassembly really
hard or run it with some test-input and look at the registers. But we
are also lazy. So, let us look at concolic execution.

#### How it works
Symbolic execution is a technique for analyzing binaries where the
binary is executed symbolically.
Take the following program as an example.

    if (x<4)
      print "a"
    else
      print "b"

A concrete execution means that we execute the program with concrete
input, e.g. x=10 or x=1 or something like that.

Symbolic execution on the other hand executes all program paths with
symbolic variables.  I.e., the execution comes to the comparison x<4
and separates the input space into x<4 and x>=4. Depending on these
equations we take the first or the second branch. After executing the
whole program, we have a bunch of equations which partition the input
space for each branch. We can now solve the equations with an SMT
solver to get the set of all concrete inputs such that we execute a
special branch.

For example in our case we want to find the input such that the branch
of the program is taken where it prints that we have found the correct
password.

Symbolic execution has a number of drawbacks, like path explosion,
weird behavior with loops, and problems when we encounter one-way
functions like hashes. So concolic execution was born. Concolic
execution is a little bit like symbolic execution, but for each branch
taken we remember a concrete instantiation of the variables. In fact,
there is more to it, but I did not quite get it.

#### Angr me!
We use angr, which is a python framework for concolic execution. The
following steps are performed in an ipython environment, but could
also be written as a standalone program.

    > a = angr.Project(''crackme0x02.org')
    	# Here we are loading the binary as a new project
    > state = a.factory.entry_state()
			# We store the entry state
    > path = a.factory.path(state)
			# We store the path beginning at the entry state
    > pathgroup = a.factory.path_group(path)
			# Later there will be multiple paths, so we use a pathgroup, which
			# is just a set of paths
    > pathgroup.step(until=lambda lpg: len(lpg.active) > 1)
			# We walk through the binary until we reach a branch
			# Then we print the two inputs
    > pathgroup.active[0].state.posix.dumps(0)
			'+000338724B'
    > pathgroup.active[1].state.posix.dumps(0)
			'-242422222\x04'

One of these inputs corresponds to the execution path where we get an
invalid password, the other corresponds to the valid password. Let us
just test them:

    $ ./crackme0x02
    IOLI Crackme Level 0x02
    Password: -242444422
    Invalid Password!
    $ ./crackme0x02
    IOLI Crackme Level 0x02
    Password: 338724
    Password OK :)

Bingo. Now we are done with that level.

## Level 3

I learned that I can start radare2 with the -A switch. This saves us the burden of having to type aa every time.
Up to now I have always included the whole output. This will change now, since most of it is irrelevant noise for us.
Let us look at the main method.

    $ r2 -A crackme0x03
    [x] Analyze all flags starting with sym. and entry0 (aa)
    [x] Analyze len bytes of instructions for references (aar)
    [x] Analyze function calls (aac)
    [ ] [*] Use -AA or aaaa to perform additional experimental analysis.
    [x] Constructing a function name for fcn.* and sym.func.* functions (aan))
     -- Wait a moment ...
    [0x08048360]> pdf @main
    |           0x080484c0      c70424298604.  mov dword [esp], str.Password: ; [0x8048629:4]=0x73736150 LEA str.Password: ; "Password: " @ 0x8048629
    |           0x080484c7      e884feffff     call sym.imp.printf
    |           0x080484cc      8d45fc         lea eax, [ebp - local_4h]
    |           0x080484cf      89442404       mov dword [esp + local_4h_2], eax
    |           0x080484d3      c70424348604.  mov dword [esp], 0x8048634  ; [0x8048634:4]=0x6425 ; "%d"
    |           0x080484da      e851feffff     call sym.imp.scanf
    |           0x080484df      c745f85a0000.  mov dword [ebp - local_8h], 0x5a ; 'Z'
    |           0x080484e6      c745f4ec0100.  mov dword [ebp - local_ch], 0x1ec
    |           0x080484ed      8b55f4         mov edx, dword [ebp - local_ch]
    |           0x080484f0      8d45f8         lea eax, [ebp - local_8h]
    |           0x080484f3      0110           add dword [eax], edx
    |           0x080484f5      8b45f8         mov eax, dword [ebp - local_8h]
    |           0x080484f8      0faf45f8       imul eax, dword [ebp - local_8h]
    |           0x080484fc      8945f4         mov dword [ebp - local_ch], eax
    |           0x080484ff      8b45f4         mov eax, dword [ebp - local_ch]
    |           0x08048502      89442404       mov dword [esp + local_4h_2], eax
    |           0x08048506      8b45fc         mov eax, dword [ebp - local_4h]
    |           0x08048509      890424         mov dword [esp], eax
    |           0x0804850c      e85dffffff     call sym.test
    |           0x08048511      b800000000     mov eax, 0
    |           0x08048516      c9             leave
    \           0x08048517      c3             ret

We can see that the password request is printed, then some voodoo happens and
finally a function sym.test is called. This is the first Crackme of the
IOLI-Crackmes with more than one function. I wonder what sym.test does.

    [0x08048360]> pdf @sym.test
    / (fcn) sym.test 42
    |   sym.test (int arg_8h, int arg_ch);
    |           ; arg int arg_8h @ ebp+0x8
    |           ; arg int arg_ch @ ebp+0xc
    |           ; CALL XREF from 0x0804850c (sym.main)
    |           0x0804846e      55             push ebp
    |           0x0804846f      89e5           mov ebp, esp
    |           0x08048471      83ec08         sub esp, 8
    |           0x08048474      8b4508         mov eax, dword [ebp + arg_8h] ; [0x8:4]=0
    |           0x08048477      3b450c         cmp eax, dword [ebp + arg_ch] ; [0xc:4]=0
    |       ,=< 0x0804847a      740e           je 0x804848a
    |       |   0x0804847c      c70424ec8504.  mov dword [esp], str.Lqydolg_Sdvvzrug_ ; [0x80485ec:4]=0x6479714c LEA str.Lqydolg_Sdvvzrug_ ; "Lqydolg#Sdvvzrug$" @ 0x80485ec
    |       |   0x08048483      e88cffffff     call sym.shift
    |      ,==< 0x08048488      eb0c           jmp 0x8048496
    |      ||   ; JMP XREF from 0x0804847a (sym.test)
    |      |`-> 0x0804848a      c70424fe8504.  mov dword [esp], str.Sdvvzrug_RN______ ; [0x80485fe:4]=0x76766453 LEA str.Sdvvzrug_RN______ ; "Sdvvzrug#RN$$$#=," @ 0x80485fe
    |      |    0x08048491      e87effffff     call sym.shift
    |      |    ; JMP XREF from 0x08048488 (sym.test)
    |      `--> 0x08048496      c9             leave
    \           0x08048497      c3             ret

Like we could have expected sym.test does a test, namely a comparison. If eax
is equal to `dword [ebp + arg_ch]` we jump to `0x804848a`. Is this a good or a
bad jump? We could argue that this is the first jump, and stuff being equal is
a very rare condition. So we assume this jump is not often taken. We assume it
is a good jump, which is only taken if the password is correct.  So we have to
exchange it with a jump instruction which is always taken. Writing eb0e instead
of 740e works.
Intestingly there are some strings which we cannot read, since they look
encrypted. I strongly assume that the stuff in sym.main that I called voodoo
was some kind of decryption routine.

#### Angr me!
With Angr we can again find out the correct password. We are doing the same as
in Level 2.

		> a = angr.Project('crackme0x03')
		> state = a.factory.entry_state()
		> path = a.factory.path(state)
		> pathgroup = a.factory.path_group(path)
		> pathgroup.step(until=lambda lpg: len(lpg.active) > 1)
		> pathgroup.active[0].state.posix.dumps(0)
		'+000338724B'
		> pathgroup.active[1].state.posix.dumps(0)
		'-224244844\x12'

		$ ./crackme0x03.org 
		IOLI Crackme Level 0x03
		Password: 338724
		Password OK!!! :)


## The other levels
The other levels are fairly similar, except there are more jumps to
nop. So I won't go into details but leave them as an exercise.
If instead of patching the binaries, we want to understand the
password from the disassembly, that is more challenging and I will not
cover it (yet?) since my disassembly skills do not suffice for that.
Interestingly, our angr-script fails at the later levels.

## References
If you want to look at solutions for the other levels which you should not do except if you are stuck, you can find them under the following links:

- [Cracking IOLI by understanding assembly](https://dustri.org/b/defeating-ioli-with-radare2.html)
- [Solving IOLI with angr](https://github.com/angr/angr-doc/tree/master/examples/CSCI-4968-MBE/challenges)
