---
layout: post
category: CTF Writeups
tags: [hacking, ctf, spoiler, writeup, crackme]
---

## Introduction
In Spring 2015 there was a course called [Modern Binary Exploitation](http://security.cs.rpi.edu/courses/binexp-spring2015/) at Rensselaer
Polytechnic Institute. This course was organized by the [rpisec team](https://rpis.ec/).
They published the course material, so anyone can look at them and
educate themselves.
In this post I am going to look at the crackme challenges which you
can find
[here](http://security.cs.rpi.edu/courses/binexp-spring2015/lectures/2/challenges.zip)
or on a [local mirror]({{ site.url }}/assets/rpisec-mbe-crackmes/challenges.zip).

## crackme0x00a
The first crackme is fairly easy. We use the command ``strings`` to
look for ASCII-strings in the binary

    $ strings crackme0x00a 
    /lib/ld-linux.so.2
    __gmon_start__
    libc.so.6
    ...
    UWVS
    [^_]
    Enter password: 
    Congrats!
    Wrong!
    ;*2$"
    g00dJ0B!
    GCC: (Ubuntu/Linaro 4.6.1-9ubuntu3) 4.6.1
    .symtab
    .strtab
    ...

The string g00dJ0B! looks suspicious. So let us try it.

    $ ./crackme0x00a 
    Enter password: g00dJ0B!
    Congrats!

## crackme0x00b
This does not work so trivial anymore, but in the slides of the
lecture there is the tip to use a different encoding. After a little
bit of testing we come to the following result.

    $ strings -e L crackme0x00b 
    w0wgreat

The switch -e specifies the encoding and L means 32-bit littleendian.

    $ ./crackme0x00b 
    Enter password: w0wgreat
    Congrats!

Nice Challenge.

## crackme0x01
On to some oldschool command-line disassembling. The command ``objdump``
displays various information about object files, like their
disassembly.

		$ objdump -d crackme0x01
		...
		080483e4 <main>:
		80483e4:	55                   	push   %ebp
		80483e5:	89 e5                	mov    %esp,%ebp
		80483e7:	83 ec 18             	sub    $0x18,%esp
		80483ea:	83 e4 f0             	and    $0xfffffff0,%esp
		80483ed:	b8 00 00 00 00       	mov    $0x0,%eax
		80483f2:	83 c0 0f             	add    $0xf,%eax
		80483f5:	83 c0 0f             	add    $0xf,%eax
		80483f8:	c1 e8 04             	shr    $0x4,%eax
		80483fb:	c1 e0 04             	shl    $0x4,%eax
		80483fe:	29 c4                	sub    %eax,%esp
		8048400:	c7 04 24 28 85 04 08 	movl   $0x8048528,(%esp)
		8048407:	e8 10 ff ff ff       	call   804831c <printf@plt>
		804840c:	c7 04 24 41 85 04 08 	movl   $0x8048541,(%esp)
		8048413:	e8 04 ff ff ff       	call   804831c <printf@plt>
		8048418:	8d 45 fc             	lea    -0x4(%ebp),%eax
		804841b:	89 44 24 04          	mov    %eax,0x4(%esp)
		804841f:	c7 04 24 4c 85 04 08 	movl   $0x804854c,(%esp)
		8048426:	e8 e1 fe ff ff       	call   804830c <scanf@plt>
		804842b:	81 7d fc 9a 14 00 00 	cmpl   $0x149a,-0x4(%ebp)
		8048432:	74 0e                	je     8048442 <main+0x5e>
		8048434:	c7 04 24 4f 85 04 08 	movl   $0x804854f,(%esp)
		804843b:	e8 dc fe ff ff       	call   804831c <printf@plt>
		8048440:	eb 0c                	jmp    804844e <main+0x6a>
		8048442:	c7 04 24 62 85 04 08 	movl   $0x8048562,(%esp)
		8048449:	e8 ce fe ff ff       	call   804831c <printf@plt>
		804844e:	b8 00 00 00 00       	mov    $0x0,%eax
		8048453:	c9                   	leave  
		8048454:	c3                   	ret
		...

At the adress 804842b something on the stack is compared with 0x149a. Then
there is a jump if the values are equal (je). Let us compute this back
to hex and try it as a password.

    $ echo $((0x149a))
    5274
    $ ./crackme0x01 
    IOLI Crackme Level 0x01
    Password: 5274
    Password OK :)

Oh, it is the IOLI-crackme. We know them from a previous post already.
Interestingly the following crackmes of RPISEC are the other
IOLI-crackmes. So, we are done, since i have already covered them [in a
previous post]({% post_url 2017-02-19-the-ioli-crackmes %}).
