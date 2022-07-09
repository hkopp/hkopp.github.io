---
layout: post
category: Music
tags : [music]
---

## Introduction
When I first started making electronic music I was overwhelmed with
all the possibilities of sound generation and it took me a long time to
figure out which instruments can be made which way. Even after I knew
what all the knobs in synthesizers do, I was still unable to apply
that knowledge to, e.g., make a drum sound.

So, I thought I could write the manual which I hope I could have had at
the time. If you know nothing about sound synthesis this may be a hard
read. However, if you know what a filter and modulation is, this may
hopefully help you.

## Recipes

### Bassline Wobbles
This effect can be heard often in dubstep or neurofunk. Essentially it
is a pulse wave which is lowpass filtered with resonance.
The cutoff frequency of the lowpass filter is modulated with
something slow. Sometimes there is also modulation of the pulse width to
make the sound more lively.
Even if the cutoff frequency is modulated with a sine wave it can
sound cool if parts of the sound are muted and the brain cannot hear
the sine. Normally the modulation is irregular and done by some
hand drawn automation.
Often the input is not a pulse wave but may be more processed, e.g,
some frequency modulated stuff or a distorted pulse. The only rule is
that it should have a large spectrum to be interesting.

### Neurofunk Bass
This is basically a very gritty bass.
Take some saw waves and detune them a little bit. Then put a second
saw wave one octave below. And then put a sine two octaves below. This
sine is the secret ingredient which makes it sound very deep and dark.
Finally, put an LFO on the lowpass cutoff as above and add some
phasers.

### Trance Leads
Have you ever listened to trance music and noted the typical leads
which are the same in nearly every trance song? This is called a
supersaw and is done with a lot of saw waves.
Take some saw saves and detune them very little. Then copy this to the
octave above. And to the octave above that.
Put some chorus and reverb over it. Done.

### Kick Drum
Put the sustain off. A kick drum is very dry and does not have any
sustain. If you think about it, when a drum is hit, its skin
stretches. Stretched stuff produces higher notes (think of guitar
strings). If the drum head relaxes, its frequency is lower.
And that is how you synthesize drums. Take a sine wave which starts
high and then decreases quickly in frequency. This can be automated by a
frequency envelope, even on most hardware, since the envelope is not
needed for the volume.
Depending on taste, put some distortion on it.

### Snare Drum
Snare drums can be made like a kick drum, but with a higher starting
and ending frequency and some highpass filtered noise. Try to make the
noise and the frequency enveloped sine sound as belonging to the same
instrument.

### Bonus Snare Drum
What also works is filtering white noise with a bandpass envelope with
quickly decaying cutoff frequency. This makes it sound more dirty.
Then more unfiltered noise can be added.

### Hi Hat
There are many ways to create a hi hat sound by putting together wave
forms. The easiest way is to make a very high pitched snare.
Another way is to play around with subtractive synthesis. Take some
noise and filter it with a high pass or band pass. If you want an open
hi hat, simply change the envelope by adding some sustain.

### Clap
A Clap is a snare with more noise. This sounds somehow wrong, but in
the world of sound synthesis it is true. The noise can be either
louder or the cutoff frequency of the high pass can be lower.

## Bonus: Chiptunes
Recently, I played around with some chiptune music. This is music from
old hardware chips like the SID, built into the Commodore 64 or the
old NES sounds. These two examples are the ones I have looked
into. Old platforms have very hard restrictions which makes it
challenging to create anything that sounds good.

The SID for example has three oscillators, which have four waveforms
each: triangle, pulse, noise, and saw. From each of those four wave
forms each of the three oscillators can play one or multiple at each time. So
there are basically three channels. There are no fancy effects like
chorus or echo.

The NES is even more shitty, since it has only four oscillators each
of whom can only do one waveform. The
first two can do pulses, the second is for triangles and the third for
noise. There is an additional fifth channel which can play lo-fi
samples, but I never managed to work with that successfully.
Yeah, there is no saw.

### Drums
On NES, there are two styles of drums. One is made by taking the
noise and filtering it with a band pass. The mid frequency of the
bandpass then decreases. If this is done at the "correct" frequencies
it sounds like a snare or a kick drum or a hi hat. This can be heard
in a lot of asian games.

The western way works the same, but with a triangle which quickly
decreases in frequency, instead of the noise. Sometimes the noise is
added, if you can afford to block two channels. This gives the drums a
clearer defined pitch.

For the SID, I did not understand how to make drums, since there are
no fast frequency envelopes. So I will reference other people who were
able to make drums. I give you the hex values for the registers in
brackets, so you can play along in your tracker software.

The snare drum from the [goattracker manual](https://cadaver.github.io/tools.html) works as follows:
The first frame is square and triangle and gate (in hex 40+10+01=51)
at the fixed pitch 90.
The second frame is just saw and gate (20+1=21) at the fixed pitch A0.
Then it ends in noise without gate (80) at the pitch DF.
Yeah, noise in SID can have a pitch.
Do not forget to stop the execution of the wave table by the FF command.
The ADSR is set to 32F9.

The e-book [Creating Chip Tunes with Sid Wizard)](http://csdb.dk/release/?id=112754) also contains a nice snare:

  - noise + gate (81), fixed pitch CE
  - pulse (41), pitch AD
  - pulse (41), pitch AC
  - noise + no gate (80), pitch C4
  - noise + no gate (80), pitch C5
  - in pulse table 88
  - in ADSR: 0DE9

### Bass
For the SID Bass, let me again reference "Creating Chip Tunes with Sid Wizard":

  - pulse + gate (41)
  - in pulse table 82 (+ octave transpose -3)
  - in pulse table 40 20 (increase pulse width by 20, 40 times)
  - ADSR: 089A
  - add some vibrato
  - low-pass in filter table 9B 45 (9 selects the low pass, 4 is the resonance, 45 is the cutoff frequency.
  - in the nect row of the filter table: 40 05 (add 05 to the cutoff frequency 40 times)

Actually SID basses are easy. Simply play around with one of the
basic waveforms. Do not touch noise.

For the NES Bass take the square. Or take two squares and detune them
a little bit, if you can afford to occupy two channels. You can also
take the triangle.
Since the NES does not have a saw, I think those are all the options.

### Flute
I have reverse engineered this one from SID sounds and then recreated on
NES. A flute is a high pitched triangle wave with vibrato.


## Conclusion
I hope this helped someone out there. For recreating sounds on SID or
NES it is possible to look at the wave forms, e.g., in audacity, and
then use educated guessing to get something similar. In the commodore
stuff I heard that it is also possible to run an emulator and then
prick the values of the sound chip from the register, but I have never
done this.

For recreating more complex sounds it sometimes helps to start with a
waveform which seems appropriate and then modify the sound until you
are happy with it. This is harder than in the chiptune world and when
it comes to frequency modulated sound I am still completely lost.
