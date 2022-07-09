---
layout: post
category: Lesson
tags : [programming, easy, nes]
---

## Introduction
In my journey through NES development I encountered some quirks and surprises.
These are well known in the community, but may still be interesting for other people outside of the community.
This is an exposition of my adventures.

## Reading the Controllers
As you may know, a NES controller has exactly eight buttons that can be pressed.
Consequently an intuitive design would have been to simply use a byte.
This byte could then be read out.
The first bit could indicate if A was pressed. The second bit could indicate if B was pressed, and so on.

As you may have guessed due to the context of this post, this is not the case.
Instead, while there is one byte at $4016 which can be read out for getting the state of the controllers,
it only gives one button per read.
So, the least significant bit of the first read indicates if A is pressed.
The least significant bit of the second read indicates if B is pressed, and so on.
Consequently, the controllers have to be read out in a loop.

Below, you can see controller reading code from [my code](https://github.com/hkopp/nes-adventures/blob/master/04_controller/controller.s).

```
player1_buttons:    .res 1  ; Buttons of player1.
                            ; Bits are set as follows:
                            ; A|B|Select|Start|Up|Down|Left|Right
; ========

LatchController:    ; tell both controllers to latch buttons
  lda #$01
  sta $4016
  lda #$00
  sta $4016
  ldx #$08
ReadControllerLoop:
  ; This idea is from the nerdy nights tutorial series.
  lda $4016
  lsr a                ; least significant byte is stored in carry
  rol player1_buttons  ; and shifted into the variable
  dex
  bne ReadControllerLoop
  rti ; return
.endproc
```

I can only guess that the engineers made the decision to read the controllers this way in order to support other input devices.

There is a second issue with reading the controllers, which I have not addressed in my code above.
Namely, there is a hardware bug.
If you are using the sample playback capabilities of the audio device (DPCM) this can mess with your controller readings.
So, the safe way (and what [Super Marios Bros.](https://github.com/captainsouthbird/smb3/blob/master/PRG/prg031.asm#L3238) does) is to read out the controllers twice, and check if the same has been read.
This bug was later fixed in the PAL NES, so this hardware bug does not even affect all NESs.
Moreover, when using an emulator, this hardware bug is often not simulated.
Consequently, you could program a game which works fine in an emulator, but the controllers are not read out correctly on a real NES.
Have fun searching debugging that issue.

If you want to code your own controller reading code, there is also a [nice template](https://github.com/pinobatch/nrom-template/blob/master/src/pads.s) by pinobatch.
More information can be found [in the NESdev wiki](https://wiki.nesdev.com/w/index.php/Controller_reading).

## Detecting Sprite Overflows
The NES PPU can at most have eight sprites on a horizontal line.
If you have more sprites on a horizontal line this is called a sprite overflow.
In this case you have to cycle through the sprites.
In each drawing, a different sprite is hidden, leading to a flickering effect.
This flickering could be considered as bad, but it is much better than not flickering and thus not completely hiding a sprite.
Especially, if the hidden sprite is an enemy and then kills you this could lead to a very bad game experience.
Killed by an invisible sprite, lol.

You have to write this sprite cycling code by yourself.
Thus, it may be good to have a flag, that is only set if sprite overflow occurs.
You could check the flag and only cycle sprites if the flag is set.
Otherwise you do not cycle sprites and use your valuable computing power for something else.
Remember, back then computing power was scarce.

Well, it turns out, that there is such a sprite overflow flag.
But it does not work.
The [NESdev wiki](https://wiki.nesdev.com/w/index.php/PPU_sprite_evaluation#Sprite_overflow_bug) says that there are both false positives and false negatives.
This is simply a polite way of saying that it is totally broken.
Not only do you have to write your own cycling code, additionally you have to write your own sprite overflow detection code.

Nevertheless, the broken behavior of the sprite overflow detection is broken in such a deterministic way, that there are [some games](https://wiki.nesdev.com/w/index.php/Sprite_overflow_games) which make use of the bug.

## Getting Randomness
In modern CPUs, there is usually a dedicated random number generator.
Often, the noise of a diode is measured to come up with 'real randomness'.
These measurements are then enriched by the operating system with other seemingly random events and put into an entropy pool.
Of course the NES does not have such a mechanism.
Instead, you have to write your own pseudorandom number generator (PRNG).
With my cryptographic background I was okay with this, but it is of course also possible to "borrow" one from [here](https://github.com/bbbradsmith/prng_6502).

Nevertheless, you need a starting value called a seed to come up with the random looking sequence.
Where do you get the seed on a NES?
Initially I thought of reading from some memory positions.
When the memory positions are not initialized, they are at least undefined.
It turns out that this works if you do it correctly, but it comes with some problems.

Firstly, it is unclear if an emulator simulates this appropriately.
You may run into the situation that you cannot use an emulator for programming but have to test it on real hardware instead.
This is just unnecessarily hard.

Secondly, there is a whole community interested in tool assisted speedruns.
If you use indeterministic behavior, these people stop being interested in your program.

The 'recommended' way to get a good seed is by using timings.
As an example, games often have an intro screen and the start button needs to be pressed in order to advance to the game screen.
A nice seed would simply be the time (number of frames) elapsed between the intro screen and the game screen.
This way, the tool assisted speedrunners are happy and emulators can handle it.

## Acknowledgements
Thanks to the people at the nesdev discord. Without them, it would have been significantly harder to acquire the knowledge for writing this article.
