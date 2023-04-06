---
layout: post
category : Lesson
tags : [theory, astro, ctf, programming]
---


## Introduction

Last weekend i agreed to support as a mathematician in a CTF team. We
were participating in the [Hack-a-sat CTF](https://hackasat.com/),
which is an international competition sponsored by the air force about
hacking satellites.
We scored 7th place.
As i had nearly no knowledge about satellites, i used a few evenings
to read a little bit.
These are my (somewhat) cleaned up notes.

This is not a writeup, as my team was much more clever than me and so
i was not able to contribute as much as i hoped i could.

## TLE

The orbit of satellites is usually given as Two-line element set
(TLE).
This is a data format consisting of two lines (obviously).
Each line has 69 symbols due to some historical limitations dating
back to punch cards.

The TLE for the ISS looks as follows:

```
ISS (ZARYA)
1 25544U 98067A   08264.51782528 -.00002182  00000-0 -11606-4 0  2927
2 25544  51.6416 247.4627 0006703 130.5360 325.0288 15.72125391563537
```

The first line is information about the satellite and its speed.

```
Columns  Meaning
01       Line Number of Element Data
03-07    Satellite Number
08       Classification
10-11    International Designator (Last two digits of launch year)
12-14    International Designator (Launch number of the year)
15-17    International Designator (Piece of the launch)
19-20    Epoch Year (Last two digits of year)
21-32    Epoch (Day of the year and fractional portion of the day)
34-43    First Time Derivative of the Mean Motion
45-52    Second Time Derivative of Mean Motion (decimal point assumed)
54-61    BSTAR drag term (decimal point assumed)
63       Ephemeris type
65-68    Element number
69       Checksum (Modulo 10)
```

BSTAR is the drag term of the satellite. There are [five
models](https://en.wikipedia.org/wiki/Simplified_perturbations_models)
to compute the orbital state vectors, i.e., the position of the
satellite. As the Earth's shape, magnetic field, atmosphere are all
highly irregular, you need such a model to predict its effects.

The second line is about the position of the satellite:

```
Columns  Meaning
01      Line number
03–07   Satellite Catalog number
09–16   Inclination (degrees)
18–25   Right ascension of the ascending node (degrees)
27–33   Eccentricity (decimal point assumed)
35–42   Argument of perigee (degrees)
44–51   Mean anomaly (degrees)
53–63   Mean motion (revolutions per day)
64–68   Revolution number at epoch (revolutions)
69      Checksum (modulo 10)
```


## Curiosities

Note that both of these lines have a checksum as a form of error
checking. However, it's only the sum modulo 10. This is in general not
a good form of error checking, as it is unable to detect
any errors from transpositions of characters.

Any particular satellite TLE set is only valid for a couple of weeks
to either side of that TLE's epoch. This is due to maneuvers of the
satellites or the magnetic field and the drag of earth's atmosphere.
The five simplified models mentioned above cannot account for all of
that. That's why they are called simplified.

What i found very interesting is, that it is possible to get a list of
all larger objects surrounding space in TLE format.
You don't even have to get some expensive government license.
You can just click [this link](http://celestrak.org/NORAD/elements/active.txt).
There are also [other sources for TLEs](http://www.satobs.org/tletools.html)

### Short but Fun Exercise for You, Dear Reader

Here are the TLEs from two of the Starlink satellites. Compare them.
What is different? What is similar? What is the same? Why?
Would you have expected that?

```
STARLINK-1007
1 44713U 19074A   23096.54092968  .00004826  00000+0  34276-3 0  9997
2 44713  53.0527 301.7232 0001403  75.8888 284.2257 15.06390401187929
STARLINK-1008
1 44714U 19074B   23096.05531000  .00019972  00000+0  13542-2 0  9999
2 44714  53.0528 303.9072 0001111  67.2224 292.8882 15.06446350187858
```

## Software Support for TLEs

If you want to work with them in Python, there is the [Skyfield
library](https://rhodesmill.org/skyfield/earth-satellites.html) with
great documentation, as well as the [astropy
library](https://www.astropy.org/).


## Other stuff

There is an XML format for spacecraft data telemetry that is widely
used and called the XML Telemetric and Command Exchange format (XTCE).
Its specification can be found [here](https://www.omg.org/xtce/index.htm).

Firmware on satellites is often in some obscure architecture such as
SPARC or some other RISC-architecture.

If we have an image of the sky and a catalog of stars and want to
match them, this is called the star identification problem and is a
special case of the [subgraph isomorphism
problem](https://en.wikipedia.org/wiki/Subgraph_isomorphism_problem)
which is in general NP-complete.


## Acknowledgments
Thanks
- [AllesCTF!](https://twitter.com/allesctf) It was a pleasure hacking
  with you.
- [TheVamp](https://twitter.com/TheHaloVamp) Thanks for inviting me to
  your ctf team.
- [Hack-A-Sat](https://twitter.com/hack_a_sat) Thanks for organizing the CTF
  and building such nice challenges.
