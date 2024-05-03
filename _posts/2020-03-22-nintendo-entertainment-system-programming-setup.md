---
layout: post
category: Computer Stuff
tags: [programming, easy, nes]
---

## Introduction
Well, I thought about doing some retro coding on the Nintendo
Entertainment system (the NES) and researched a lot.
Then i was able to set up a development environment, follow a
tutorial, and write a "Hello World" program.
My original goal was to write an endless runner or a space shooter or
some other crappy game, though i am still very far from that.
In this post, I am going to share the setup of my environment and
which parts have to be covered for programming on the NES.

## Why Retro programming?

Sometimes I have a deep yearning for programming like it used to be
before the internet. Back when code was not simply copied from
stackoverflow. Back when code was not buried under tons of frameworks
and abstraction layers. Before all that object-oriented stuff. When I
did not know what a library was and instead had a bunch of helper
functions that I copied into every project i wrote.
I have to say that writing code for the NES fulfills that desire.

## Why NES?
The nintendo system is the console of my generation. Even though I did
not own a nintendo at the time i often played with my friends together
when we met. That was the time when there was no internet and
multiplayer meant that you sat together on a couch and stared at a
screen.
So, part of choosing the NES is out of nostalgia, part is, because it
really is a cool console.

I also looked into Gameboy programming and SNES programming.
Well, gameboy programming is surely interesting, but I do not have
such a personal connection to the gameboy as i have to the NES.
Regarding the SNES, the Super Nintendo Entertainment System, i.e., the
successor of the NES, there are not many tools available in order to
program that. Further, i looked into the SNES audio chip in a little more
depth, and it is completely sample-based, instead of having to program
the oscillators by yourself. Thus, it is less fun.

## Setting up a development workflow

Programming the NES is divided in approximately three areas:

- Code
- Music
- Graphic

Additionally, you want to have an emulator (such as fceux or nestopia)
in order to test your code.
I will talk about each of the three areas in the following paragraphs.

[Edit 2020-03-28: Actually, the emulator part is not trivial either.
While there are a large number of emulators
([Nestopia](http://0ldsk00l.ca/nestopia/), [fceux](http://www.fceux.com/web/), [Mesen](https://www.mesen.ca/)),
not all of them offer debugging support. The Windows version of fceux
(which works in wine) has debug support. Mesen also offers debugging
functionality.]

### Code
The NES was originally programmed in 6502 assembler. I'd like to think
that there was a bunch of tooling involved, but all of these original
tools are lost to history.

A modern approach to NES development is either writing it in Assembly
or using C. Originally I thought that using C is shitty, as the
hardware of the NES is very restricted and C does not optimally use
the resources. While C is certainly not as well-performing as assembly, you
can compile your C to assembly and then modify the assembly. This is
easier than writing all of your code in assembly. It may also help you
in learning the assembly, when you already know a bit of C.

If you plan to use assembly, we are talking here about 6502 assembly.
In contrast to "modern" x86 assembly, this was at a time when assembly
was targeted to be written by humans. Nowadays, assembly has thousands
of commands  and is primarily targeted to be written by compilers.
6502 assembly has just a bunch of commands, due to it being a RISC
architecture. But note that this also means that you have to do more
things by yourself. As an example, 6502 does not have an instruction
for multiplication. And there is no stack. And you have to reuse
variables which served a totally different purpose, since you want to
behave like a cheapskate and do not simply hand out your precious memory to
anyone.

So, the current way to go seem to be the following tools:

- cc65 - A C compiler
- ca65 - An assembler for 6502

Note that when you are using older tutorials with cc65, when compiling you may
get the error `Error: Call to undefined function 'waitvblank'`. This
function is now called waitvsync in more modern versions of nes.h.

### Music
The audio chip (APU = Audio processing unit) of the NES is a real
old-school chip, from before sound cards were invented. Having some
experience with the SID chip, the NES APU is very similar but more
shitty.
There are five oscillators builtin. You cannot use more, as these are
hardware oscillators. And they have a fixed purpose in contrast to the
SID chip, where the oscillators can play different wave forms.

- 2 pulse waves
- 1 triangle wave
- 1 noise
- 1 sample

Last time I played around with that, i think I used
[Famitracker](http://www.famitracker.com/), but had problems getting
it to work reliably with wine.
There is also the [Deflemask Tracker](http://www.deflemask.com/).
If you have never worked with a tracker, prepare to get frustrated.
If you have worked with a tracker before, prepare to get perfectionist and
never write any code but instead waste hours composing shitty music.
Maybe my [synth recipe post]({% post_url 2018-06-27-synthesizer-recipes %}) helps you with being
perfectionist.

Well, at least music is not a necessary part of anything you do at the
NES. If you manage to finish programming a game, just state in the
user manual that there is no music and listening to fucking Slayer
while playing is recommended. That's what we did back then anyway.

### Graphics
Regarding the graphic chip (PPU = Picture Processing Unit), as the
hardware of the NES is so limited, images are stored in a special
format, such that many parts can be reused.

Let me first explain [Hires Mode on the
Commodore C64](https://www.c64-wiki.com/wiki/High_Resolution), as that is
conceptually similar, but easier.
Hires stands for high-resolution (yeah!), as there are 320x200 pixel
you can use. This is not a typo. Back then, that was high resolution.
These 320x200 pixel are divided into 40x25 chars of 8x8 pixel each.
They are called chars, as they are usually the size of a character.
Per char you can use 2 colors. So you have one bit per pixel for the
color. In total you had a palette of 16 colors to choose from.
This subdivision into chars and palettes per char is very similar to
the NES PPU.
(Note that the C64 had some additional graphic modes to Hires, whereas
the NES PPU seems to have only one graphic mode according to my
research.)

In the NES PPU, there are 256x240 pixel.
These are divided into 16x15 blocks.
Each block in turn is divided into 2x2 CHR tiles of 8x8 pixel.
So, in total, there are 960 CHR tiles per screen.
The fun thing is that only 256 CHR tiles are allowed, so you have to
reuse CHR tiles.
Regarding the colors, there are in total 64 colors you can choose
from (instead of the 16 for the C64).
But you can only use 4 palettes consisting of 3 colors and a
background color which is shared between all palettes.
Per block (=2x2 CHR tiles), you need to choose one palette. So you
have 2 bits per pixel.
The image is then stored as the following data structures.

1. a bunch of CHR tiles (at most 256)
2. a nametable, which assigns CHR tiles to the tiles of the screen
3. four palettes
4. an assignment of palettes to blocks of the screen

This format of course makes very good use of the limited memory of the
NES, but is not very handy for drawing. To be honest, it looks like a
great pain.

When drawing art for the NES, you probably want to have a graphic
editor, which can take care of these limitations.
There is [YY-CHR](http://wiki.nesdev.com/w/index.php/YY-CHR) which
works under wine and can work with tiles. Previously, I have used
YY-CHR for dumping tiles out of finished roms.
There is also [makechr](https://github.com/dustmop/makechr) which can
take in a png file and convert it into the four data structures needed for
representing the image in the NES.
Additionally, there is [this
thing](https://github.com/pinobatch/nesbgeditor) which i did not test
yet and [tiled](https://www.mapeditor.org/) which I also did not test
yet.

I thought there should be a Lua extension for GIMP which at least can
count the CHR tiles (to ensure that you are not going beyond 256), or
otherwise ensure that you are working within the restrictions of the
NES. I did not find one. Maybe I can write it by myself.
In fact, I should probably admit at this point, that I was unable to
successfully draw an image for NES except the most basic stuff, such
as a black background.

[Edit 2020-03-28: I was able to draw an image. My current workflow
uses the [NES Screen
Tool](https://shiru.untergrund.net/software.shtml).]

Additionally to the background images, which I explained above, there
are also sprites. You can think of these as "floating stuff". I will
not explain them here. Read
[this](http://wiki.nesdev.com/w/index.php/PPU_OAM) instead.

If you want to have another explanation of the PPU, there is one
[here](http://www.dustmop.io/blog/2015/04/28/nes-graphics-part-1/).

And that is all you have to know in order to start with NES
development.

## Tutorials:
Here is a bunch of tutorials i am currently working with.

### Bottom up
These tutorials are bottom-up, i.e., you start with nothing and then
learn step by step.
- [Nesdougs tutorial](https://nesdoug.com/)
- [Nerdy Nights](https://nerdy-nights.nes.science/)

### Top Down
These tutorials are top-down, i.e., you start with a bunch of stuff
which you only partially understand and then hack around.
- [PwnAdventures](https://github.com/Vector35/PwnAdventureZ)
- [openNES-Snake](https://github.com/sebastiandine/openNES-Snake)
- [NES starter kit](https://github.com/cppchriscpp/nes-starter-kit/)
  Note: this describes a windows-only toolchain.
- You can also take any rom and reverse engineer it. Play around with
  the included sprites. Maybe try to dump the music.

## Other resources:
[Nesdev Wiki](http://wiki.nesdev.com/w/index.php/Nesdev_Wiki) is
probably the best resource when it comes to the NES. However, it is
often overwhelming for a beginner such as me.

## Thanks
Thanks to [@cppchriscpp](https://twitter.com/cppchriscpp) and [@nesdoug](https://twitter.com/nesdoug2).
