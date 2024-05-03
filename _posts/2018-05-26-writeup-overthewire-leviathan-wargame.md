---
layout: post
category: CTF Writeups
tags: [hacking, ctf, spoiler, leviathan, writeup]
---


### NOTE
I am seeking a full-time position in (southern) Germany or
Switzerland starting from October related to my skills as demonstrated
on this blog. If you know of any opportunities please message me on
[LinkedIn](https://www.linkedin.com/in/henning-kopp-482393156/) or via
mail under hkopp22 (at) yahoo (dot) de.

---

## Intro
Leviathan is a hacking wargame by the geniuses of [overthewire](http://overthewire.org).
Its main focus is on reversing.
This is a writeup of their challenges.
I edited out the passwords, so whenever you encounter stars (*), this was
a password. If you have not done these exercises yet, I urge you to do
them by yourself to maximize the learning factor and only read the
writeups where you are completely stuck.
Have fun reading.

## Level 0
First, we need to ssh to leviathan0@leviathan.labs.overthewire.org.
The first password is simply leviathan0, as mentioned on the website.
After connecting to the server, we find... well, basically nothing,
but a file `.backup/bookmarks.html`.    
How about we grep there?

    $ grep -i levia .backup/bookmarks.html 
    <DT><A HREF="http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is ******" ADD_DATE="1155384634" LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>

Bam! Done.

## Level 1
In Level 1, there is an executable called check with the suid bit set. Let us
disassemble it:
    
    leviathan1@melinda:~$ r2 -A check
    [0x08048430]> pdf @sym.main
    / (fcn) sym.main 189
    |           ; DATA XREF from 0x08048447 (sym.main)
    |           ;-- main:
    |           0x0804852d    55             push ebp
    |           0x0804852e    89e5           mov ebp, esp
    |           0x08048530    83e4f0         and esp, 0xfffffff0
    |           0x08048533    83ec30         sub esp, 0x30
    |           0x08048536    65a114000000   mov eax, dword gs:[0x14]      ; [0x14:4]=1
    |           0x0804853c    8944242c       mov dword [esp + 0x2c], eax
    |           0x08048540    31c0           xor eax, eax
    |           0x08048542    c74424187365.  mov dword [esp + 0x18], 0x786573 ; [0x786573:4]=-1
    |           0x0804854a    c74424257365.  mov dword [esp + 0x25], 0x72636573 ; [0x72636573:4]=-1
    |           0x08048552    66c744242965.  mov word [esp + 0x29], 0x7465 ; [0x7465:2]=0xffff 
    |           0x08048559    c644242b00     mov byte [esp + 0x2b], 0
    |           0x0804855e    c744241c676f.  mov dword [esp + 0x1c], 0x646f67 ; [0x646f67:4]=-1
    |           0x08048566    c74424206c6f.  mov dword [esp + 0x20], 0x65766f6c ; [0x65766f6c:4]=-1
    |           0x0804856e    c644242400     mov byte [esp + 0x24], 0
    |           0x08048573    c70424808604.  mov dword [esp], str.password: ; [0x8048680:4]=0x73736170  LEA str.password: ; "password: " @ 0x8048680
    |           0x0804857a    e841feffff     call sym.imp.printf
    |           0x0804857f    e84cfeffff     call sym.imp.getchar
    |           0x08048584    88442414       mov byte [esp + 0x14], al
    |           0x08048588    e843feffff     call sym.imp.getchar
    |           0x0804858d    88442415       mov byte [esp + 0x15], al
    |           0x08048591    e83afeffff     call sym.imp.getchar
    |           0x08048596    88442416       mov byte [esp + 0x16], al
    |           0x0804859a    c644241700     mov byte [esp + 0x17], 0
    |           0x0804859f    8d442418       lea eax, [esp + 0x18]         ; 0x18 
    |           0x080485a3    89442404       mov dword [esp + 4], eax
    |           0x080485a7    8d442414       lea eax, [esp + 0x14]         ; 0x14 
    |           0x080485ab    890424         mov dword [esp], eax
    |           0x080485ae    e8fdfdffff     call sym.imp.strcmp
    |           0x080485b3    85c0           test eax, eax
    |       ,=< 0x080485b5    750e           jne 0x80485c5                
    |       |   0x080485b7    c704248b8604.  mov dword [esp], str._bin_sh  ; [0x804868b:4]=0x6e69622f  LEA str._bin_sh ; "/bin/sh" @ 0x804868b
    |       |   0x080485be    e83dfeffff     call sym.imp.system
    |      ,==< 0x080485c3    eb0c           jmp 0x80485d1                
    |      ||   ; JMP XREF from 0x080485b5 (sym.main)
    |      |`-> 0x080485c5    c70424938604.  mov dword [esp], str.Wrong_password__Good_Bye_... ; [0x8048693:4]=0x6e6f7257  LEA str.Wrong_password__Good_Bye_... ; "Wrong password, Good Bye ..." @ 0x8048693
    |      |    0x080485cc    e81ffeffff     call sym.imp.puts
    |      |    ; JMP XREF from 0x080485c3 (sym.main)
    |      `--> 0x080485d1    b800000000     mov eax, 0
    |           0x080485d6    8b54242c       mov edx, dword [esp + 0x2c]   ; [0x2c:4]=0x280009  ; ','
    |           0x080485da    653315140000.  xor edx, dword gs:[0x14]
    |           0x080485e1    7405           je 0x80485e8                 
    |           0x080485e3    e8f8fdffff     call sym.imp.__stack_chk_fail
    |           ; JMP XREF from 0x080485e1 (sym.main)
    |           0x080485e8    c9             leave
    \           0x080485e9    c3             ret

We see that the program asks for a password. Then it reads three
characters into al (sym.imp.getchar) and writes them to the stack. The three characters are compared to
something on the stack (sym.imp.strcmp) and depending on equality we
jump to a wrong password section or we get a shell (bin/sh).
We want to have the shell. So we need to see, what is compared.
A little bit above, there are some things written to the stack at the mov dword stuff.
In the following dump I have added comments by changing the hex into ascii-characters.

    0x08048542    c74424187365.  mov dword [esp + 0x18], 0x786573 ; sex
    0x0804854a    c74424257365.  mov dword [esp + 0x25], 0x72636573 ; secr
    0x08048552    66c744242965.  mov word [esp + 0x29], 0x7465 ; et
    0x08048559    c644242b00     mov byte [esp + 0x2b], 0
    0x0804855e    c744241c676f.  mov dword [esp + 0x1c], 0x646f67 ; god
    0x08048566    c74424206c6f.  mov dword [esp + 0x20], 0x65766f6c ; love
    0x0804856e    c644242400     mov byte [esp + 0x24], 0

We have sex, secret, god, and love. Sex is written on the top of the stack, so it should be the correct password.
And indeed. We have to type it in, get the suid-bash and read out the password for the next level.

    leviathan1@melinda:~$ ./check 
    password: sex
    $ cat /etc/leviathan_pass/leviathan2
    ******

## Level 2
Level 2 is interesting. We have a file, called printfile, which takes
a file as argument and prints its input if the file has the correct
permissions.

To check the permissions, it calls access on the whole filename.
Printing is done with `/bin/cat %s`.
I make an example:

    leviathan2@melinda:/tmp/hkoppwashere$ echo "aa" > a
    leviathan2@melinda:/tmp/hkoppwashere$ ~/printfile a                    
    aa

With ltrace, we can see the library calls. ltrace is to libraries,
what strace is to syscalls:

    leviathan2@melinda:/tmp/hkoppwashere$ ltrace ~/printfile /tmp/hkoppwashere/a
    __libc_start_main(0x804852d, 2, 0xffffd734, 0x8048600 <unfinished ...>
    access("/tmp/hkoppwashere/a", 4)                                                                           = 0
    snprintf("/bin/cat /tmp/hkoppwashere/a", 511, "/bin/cat %s", "/tmp/hkoppwashere/a")                        = 28
    system("/bin/cat /tmp/hkoppwashere/a"aa
     <no return ...>
    --- SIGCHLD (Child exited) ---
    <... system resumed> )                                                                                     = 0
    +++ exited (status 0) +++

This one took me some time to get. The main idea is looking at what happens if
we give it a filename with spaces in it. If we have a file called `a b`, then
it checks if we have the correct access rights to the file `a b`, and then
executes `cat a b`, which of course is something completely different.
To exploit this behavior, we create a symbolic link to
`/etc/leviathan_pass/leviathan3`, called `a` and a file called `a b`, where we
have the correct permissions. Finally we execute printfile on `a b`.
The permission of `a b` will be checked, resulting in the execution of `cat a b`. The first one gives us the content of `/etc/leviathan_pass/leviathan3`, since it is a symbolic link. And `b` gives us an error, since it does not exist.

    leviathan2@melinda:/tmp/hkoppwashere$ ln -s /etc/leviathan_pass/leviathan3 a
    leviathan2@melinda:/tmp/hkoppwashere$ touch a\ b
    leviathan2@melinda:/tmp/hkoppwashere$ ~/printfile a\ b 
    ******
    /bin/cat: b: No such file or directory

On to the next level.

## Level 3
We have a sweet little file called level3. We load it into radare2 and take a look.

    leviathan3@melinda:~$ r2 -A level3
    [0x08048450]> pdf @sym.main
    / (fcn) sym.main 201
    |           ; DATA XREF from 0x08048467 (sym.main)
    |           ;-- main:
    |           0x080485fe    55             push ebp                      ; level3.c:24  
    |           0x080485ff    89e5           mov ebp, esp
    |           0x08048601    83e4f0         and esp, 0xfffffff0
    |           0x08048604    83ec50         sub esp, 0x50
    |           0x08048607    65a114000000   mov eax, dword gs:[0x14]      ; [0x14:4]=1 ; level3.c:24  
    |           0x0804860d    8944244c       mov dword [esp + 0x4c], eax
    |           0x08048611    31c0           xor eax, eax
    |           0x08048613    c7442423626f.  mov dword [esp + 0x23], 0x626d6f62 ; [0x626d6f62:4]=-1 ; level3.c:25  
    |           0x0804861b    66c744242761.  mov word [esp + 0x27], 0x6461 ; [0x6461:2]=0xffff 
    |           0x08048622    c644242900     mov byte [esp + 0x29], 0
    |           0x08048627    c74424382e2e.  mov dword [esp + 0x38], 0x732e2e2e ; [0x732e2e2e:4]=-1 ; level3.c:26  
    |           0x0804862f    c744243c3363.  mov dword [esp + 0x3c], 0x33726333 ; [0x33726333:4]=-1
    |           0x08048637    66c744244074.  mov word [esp + 0x40], 0x74   ; [0x74:2]=1 ; 't'
    |           0x0804863e    c744242a6830.  mov dword [esp + 0x2a], 0x6f6e3068 ; [0x6f6e3068:4]=-1 ; level3.c:28  
    |           0x08048646    66c744242e33.  mov word [esp + 0x2e], 0x3333 ; [0x3333:2]=0xffff 
    |           0x0804864d    c644243000     mov byte [esp + 0x30], 0
    |           0x08048652    c74424316b61.  mov dword [esp + 0x31], 0x616b616b ; [0x616b616b:4]=-1 ; level3.c:29  
    |           0x0804865a    66c74424356b.  mov word [esp + 0x35], 0x616b ; [0x616b:2]=0xffff 
    |           0x08048661    c644243700     mov byte [esp + 0x37], 0
    |           0x08048666    c74424422a33.  mov dword [esp + 0x42], 0x2e32332a ; [0x2e32332a:4]=-1 ; level3.c:30  
    |           0x0804866e    c7442446322a.  mov dword [esp + 0x46], 0x785b2a32 ; [0x785b2a32:4]=-1
    |           0x08048676    66c744244a5d.  mov word [esp + 0x4a], 0x5d   ; [0x5d:2]=0x481  ; ']'
    |           0x0804867d    8d442431       lea eax, [esp + 0x31]         ; 0x31  ; '1' ; level3.c:33  
    |           0x08048681    89442404       mov dword [esp + 4], eax
    |           0x08048685    8d44242a       lea eax, [esp + 0x2a]         ; 0x2a  ; '*'
    |           0x08048689    890424         mov dword [esp], eax
    |           0x0804868c    e83ffdffff     call sym.imp.strcmp
    |           0x08048691    85c0           test eax, eax
    |       ,=< 0x08048693    7508           jne 0x804869d                
    |       |   0x08048695    c744241c0100.  mov dword [esp + 0x1c], 1     ; level3.c:34  
    |       |   ; JMP XREF from 0x08048693 (sym.main)
    |       `-> 0x0804869d    c704248f8704.  mov dword [esp], str.Enter_the_password_ ; [0x804878f:4]=0x65746e45  LEA str.Enter_the_password_ ; "Enter the password> " @ 0x804878f ; level3.c:36  
    |           0x080486a4    e837fdffff     call sym.imp.printf
    |           0x080486a9    e89ffeffff     call sym.do_stuff             ; level3.c:38  
    |           0x080486ae    b800000000     mov eax, 0                    ; level3.c:40  
    |           0x080486b3    8b54244c       mov edx, dword [esp + 0x4c]   ; [0x4c:4]=5 ; 'L' ; level3.c:41  
    |           0x080486b7    653315140000.  xor edx, dword gs:[0x14]
    |           0x080486be    7405           je 0x80486c5                 
    |           0x080486c0    e83bfdffff     call sym.imp.__stack_chk_fail
    |           ; JMP XREF from 0x080486be (sym.main)
    |           0x080486c5    c9             leave
    \           0x080486c6    c3             ret

There is a bunch of stuff which gets moved around and compared at 0x08048691. This is just to confuse us, since it is not processing any input from us.
Then it asks for a password and calls `sym.do_stuff`. This seems important, so let us check that.

    [0x08048450]> pdf @sym.do_stuff
    / (fcn) sym.do_stuff 177
    |           ; arg int arg_665_2    @ ebp+0xa66
    |           ; arg int arg_471538588_3 @ ebp+0x706c6e73
    |           ; arg int arg_488348252_2 @ ebp+0x746e6972
    |           ; var int local_0_1    @ ebp-0x1
    |           ; var int local_3      @ ebp-0xc
    |           ; var int local_67     @ ebp-0x10c
    |           ; var int local_69_3   @ ebp-0x117
    |           ; CALL XREF from 0x080486a9 (sym.do_stuff)
    |           0x0804854d    55             push ebp                      ; level3.c:7  
    |           0x0804854e    89e5           mov ebp, esp
    |           0x08048550    81ec28010000   sub esp, 0x128
    |           0x08048556    65a114000000   mov eax, dword gs:[0x14]      ; [0x14:4]=1 ; level3.c:7  
    |           0x0804855c    8945f4         mov dword [ebp-local_3], eax
    |           0x0804855f    31c0           xor eax, eax
    |           0x08048561    c785e9feffff.  mov dword [ebp-local_69_3], 0x706c6e73 ; [0x706c6e73:4]=-1 ; level3.c:9  
    |           0x0804856b    c785edfeffff.  mov dword [ebp - 0x113], 0x746e6972 ; [0x746e6972:4]=-1
    |           0x08048575    66c785f1feff.  mov word [ebp - 0x10f], 0xa66 ; [0xa66:2]=0
    |           0x0804857e    c685f3feffff.  mov byte [ebp - 0x10d], 0
    |           0x08048585    a13ca00408     mov eax, dword [obj.stdin__GLIBC_2.0] ; [0x804a03c:4]=0x203a4343  LEA section..bss ; "CC: (Ubuntu 4.8.2-19ubuntu1) 4.8.2" @ 0x804a03c ; level3.c:10  
    |           0x0804858a    89442408       mov dword [esp + 8], eax
    |           0x0804858e    c74424040001.  mov dword [esp + 4], 0x100    ; [0x100:4]=0x80487a4 section..eh_frame_hdr
    |           0x08048596    8d85f4feffff   lea eax, [ebp-local_67]
    |           0x0804859c    890424         mov dword [esp], eax
    |           0x0804859f    e84cfeffff     call sym.imp.fgets
    |           0x080485a4    8d85e9feffff   lea eax, [ebp-local_69_3]     ; level3.c:12  
    |           0x080485aa    89442404       mov dword [esp + 4], eax
    |           0x080485ae    8d85f4feffff   lea eax, [ebp-local_67]
    |           0x080485b4    890424         mov dword [esp], eax
    |           0x080485b7    e814feffff     call sym.imp.strcmp
    |           0x080485bc    85c0           test eax, eax
    |       ,=< 0x080485be    751a           jne 0x80485da                
    |       |   0x080485c0    c70424608704.  mov dword [esp], str._You_ve_got_shell__ ; [0x8048760:4]=0x756f595b  LEA str._You_ve_got_shell__ ; "[You've got shell]!" @ 0x8048760 ; level3.c:13  
    |       |   0x080485c7    e844feffff     call sym.imp.puts
    |       |   0x080485cc    c70424748704.  mov dword [esp], str._bin_sh  ; [0x8048774:4]=0x6e69622f  LEA str._bin_sh ; "/bin/sh" @ 0x8048774 ; level3.c:14  
    |       |   0x080485d3    e848feffff     call sym.imp.system
    |      ,==< 0x080485d8    eb0c           jmp 0x80485e6                
    |      ||   ; JMP XREF from 0x080485be (sym.do_stuff)
    |      |`-> 0x080485da    c704247c8704.  mov dword [esp], str.bzzzzzzzzap._WRONG ; [0x804877c:4]=0x7a7a7a62  LEA str.bzzzzzzzzap._WRONG ; "bzzzzzzzzap. WRONG" @ 0x804877c ; level3.c:16  
    |      |    0x080485e1    e82afeffff     call sym.imp.puts
    |      |    ; JMP XREF from 0x080485d8 (sym.do_stuff)
    |      `--> 0x080485e6    b800000000     mov eax, 0                    ; level3.c:19  
    |           0x080485eb    8b55f4         mov edx, dword [ebp-local_3]  ; level3.c:20  
    |           0x080485ee    653315140000.  xor edx, dword gs:[0x14]
    |           0x080485f5    7405           je 0x80485fc                 
    |           0x080485f7    e804feffff     call sym.imp.__stack_chk_fail
    |           ; JMP XREF from 0x080485f5 (sym.do_stuff)
    |           0x080485fc    c9             leave
    \           0x080485fd    c3             ret
    
Some stuff is moved around and then compared against our input at 0x080485b7.
Depending on the correctness we either get the shell at 0x080485cc or jump over
it to the badboy at 0x080485da.
We could compute the ASCII from the values on the stack, which is not hard, but
we already did something similar in Level 1. So I am going to show an
alternative approach. Just wrap it in ltrace.

    leviathan3@melinda:~$ ltrace ./level3
    __libc_start_main(0x80485fe, 1, 0xffffd794, 0x80486d0 <unfinished ...>
    strcmp("h0no33", "kakaka")                          = -1
    printf("Enter the password> ")                      = 20
    fgets(Enter the password> hkopp is a genius
    "hkopp is a genius\n", 256, 0xf7fcac20)       = 0xffffd58c
    strcmp("hkopp is a genius\n", "snlprintf\n")        = -1
    puts("bzzzzzzzzap. WRONG"bzzzzzzzzap. WRONG
    )                          = 19
    +++ exited (status 0) +++

We can see the first comparison which was there to confuse us. It compares
"h0no33" against "kakaka". This always fails. Next, we should enter the
password. I entered "hkopp is a genius" which got compared against the string
"snlprintf". And since it is not equal we go to the loser.
Now it is clear what we need to do. The password for the file is "snlprintf"
which grants us a shell allowing us to read out the password for the next
level.

    leviathan3@melinda:~$ ./level3 
    Enter the password> snlprintf
    [You've got shell]!
    $ cat /etc/leviathan_pass/leviathan4
    ******

Done.

## Level 4
This level is pretty straightforward to solve by educated guessing.

    leviathan4@leviathan:~$ ls -la
    total 28
    drwxr-xr-x  4 leviathan4 leviathan4 4096 Oct  3 09:54 .
    drwxr-xr-x 11 root       root       4096 Oct  3 09:54 ..
    -rw-r--r--  1 leviathan4 leviathan4  220 Apr  9  2014 .bash_logout
    -rw-r--r--  1 leviathan4 leviathan4 3637 Apr  9  2014 .bashrc
    drwx------  2 leviathan4 leviathan4 4096 Oct  3 09:54 .cache
    -rw-r--r--  1 leviathan4 leviathan4  675 Apr  9  2014 .profile
    dr-xr-x---  2 root       leviathan4 4096 Oct  2 04:15 .trash
    leviathan4@leviathan:~$ cd .trash/
    leviathan4@leviathan:~/.trash$ ls
    bin
    leviathan4@leviathan:~/.trash$ ./bin 
    01010100 01101001 01110100 01101000 00110100 01100011 01101111 01101011 01100101 01101001 00001010 

This is binary. If you throw this into one of the online binary to ASCII converters, you get the password for the next level.

## Level 5 
In this level we have a binary which cats the content of a file called /tmp/file.log and then removes that file, by calling unlink.
So we simply link /tmp/file.log to our passwordfile.

    leviathan5@leviathan:~$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
    leviathan5@leviathan:~$ ./leviathan5
    ******

## Level 6

    leviathan6@leviathan:~$ ./leviathan6 
    usage: ./leviathan6 <4 digit code>
    leviathan6@leviathan:~$ ./leviathan6 1111
    Wrong
    leviathan6@leviathan:~$ ./leviathan6 2222
    Wrong

Okay, so we have to guess a password. Since there are not that many
possibilities we can resolve to brute-forcing. The script then drops us to a
shell and we can retrieve the password for the next level.

    leviathan6@leviathan:~$ for i in $(seq 9999); do ./leviathan6 $i; done|grep -v Wrong
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    -bash: fork: retry: No child processes
    whoami
    leviathan7
    cat /etc/leviathan_pass/leviathan7
    ******
    exit

## Level 7
Well, actually this is not really a level. Just some congratulations, that we
made it.
