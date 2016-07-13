---
layout: post
category : Writeup
tags : [writeup, hacking, tu ctf, crypto]
---
{% include math %}

Introduction
------------
Last weekend i participated together with the [Ulm Security
Sparrows](https://uss.informatik.uni-ulm.de/) in the TU CTF of the
University of Tulsa Tandy School.
A CTF, for those of you who do not know is a hacking contest where
hackers break security stuff and have fun overall.
I was mainly busy with the "neverending crypto challenge" and in this
post I am going to document what I have done there.
Many thanks to Uni Tulsa and to the Ulm Security Sparrows.

This post, together with the writeups of my comrades can also be found
[here](https://uss.informatik.uni-ulm.de/?p=429).

Level1
------
The first hint that was given was to connect to port 24069 at the ip
146.148.102.236. Of course I did that and was greeted

> Welcome to The Neverending Crypto!    
> Quick, find Falkor and get through this!    
> This is level 1, the Bookstore    
> Round 1. Give me some text:    

So, I typed 'a' on my keyboard and the server responded

> a encrypted is .-    
> What is -.. --- -. -   ..-. --- .-. --. . -   .- ..- .-. -.-- -. decrypted?    

Well, I am not a specialist of morse code, but i recognize morse when I see
it. Nevertheless I was too slow to search on wikipedia for a table,
decode that stuff and type it back. The server closed the connection.
So I wrote a script to decode morse.
After many trial and errors I finally succeeded and answered correctly
to the server who only wanted that I decode more morse.

> Correct!    
> Round 2. Give me some text:    
> a encrypted is .-     
> What is .- - .-. . -.-- ..-   ...- ...   --. -- --- .-. -.- decrypted?    

So I had to modify my script. I put a loop around the decoder. At
first it was an endless loop, but at 50 rounds the script exploded.

> Round 50. Give me some text:    
> a encrypted is .-     
> What is - .-. -.--   ... .-- .. -- -- .. -. --.  decrypted?    
> :    
> Cleartext is:try swimming    
> Correct!    
> Round complete!    
> TUCTF{i_wi11_n0t_5teal}    
> ------------------------------------------    
> You take the book    
> This is level 2 the attic    
> Round 1. Give me some text:    

So. we finally made it to level 2.
Sweet victory. This TUCTF{i_wi11_n0t_5teal}-stuff is a token which you
could put in the website and then my team got 10 credits. That's how
CTFs work.

What follows is my code. The file was named scriptl1.py
{% highlight python %}
ip='146.148.102.236'
port=24069
CODEL1 = {  '.-'  : 'a',  '-...':'b',  '-.-.':'c', 
            '-..' : 'd',  '.'   :'e',  '..-.':'f',
            '--.' : 'g',  '....':'h',  '..'  :'i',
            '.---': 'j',  '-.-' :'k',  '.-..':'l',
            '--'  : 'm',  '-.'  :'n',  '---' :'o',
            '.--.': 'p',  '--.-':'q',  '.-.' :'r',
            '...' : 's',  '-'   :'t',  '..-' :'u',
            '...-': 'v',  '.--' :'w',  '-..-':'x',
            '-.--': 'y',  '--..':'z',
        }

import socket
import re

# Level 1
sock = socket.socket()
sock.connect((ip, port))
for i in xrange(50):
    print(sock.recv(256))
    # Round i. Give me some text:
    sock.send("a\n")
    print("#######")
    answer=sock.recv(256)
    print("server answer:" +answer)
    # a encrypted is .- 
    # What is ..-. .-. .- --. -- . -. - .- - .. --- -.  decrypted?
    # :
    match=re.search('What is (.*) decrypted?', answer)
    morse=match.group(1)
    
    cleartext=''
    for morsechar in morse.replace("   "," space ").split():
        if morsechar == 'space':
            cleartext += ' '
        else:
            cleartext += CODEL1[morsechar]
    
    print("Cleartext is:" + cleartext)
    sock.send(cleartext+"\n")
{% endhighlight %}

The stuff with the space is a dirty hack. If you split a string
containing multiple spaces in Python the sucker will just disregard
it. So i substituted multiple spaces by " space ", split and then
appended a space when encountering the word "space". Well, at CTFs you
are sometimes under timepressure and code elegance is subordinate.

Level 2
-------
For level 2 I had to go through level 1 every time. At least that is
what the guys at the irc-channel said:

> (13:35:28) uss_cryptobunny: whoisjohngalt: in the crypto challenge is there a shortcut to level2 or do i have to run through level1 every time?    
> (13:35:59) neptunia: ^ every time    
> (13:36:04) neptunia: that's why its neverending    

So i restructured my code. I made a main.py which executed the
different files for the different levels. At the end it looked like
this:
{% highlight python %}
execfile("scriptl1.py")
execfile("scriptl2.py")
execfile("scriptl3.py")
{% endhighlight %}

For level 2 i tried some letters. If the phrase was too long, the
server said it is too long and closed the connection. It was a really
slow process, since i had to go through the first level every time.
I quickly discovered that the cipher was a simple monoalphabetic
substitution. That is one of the easiest ciphers in existence. One
simply substitutes each letter with a different letter. For example
'a' becomes 'n', 'b' becomes 'o'.
It was simply a matter of trying out all the letters, making a
codebook and reusing the code from the first level.
Oh, and did I mention putting everything in a loop with 50 rounds?

And then finally, level 3:

> Round 50. Give me some text:    
> ABCDEFGH encrypted is NOPQRSTU    
> What is v-|%r-'|# decrypted?    
> :    
> Cleartext is:i owe you    
> Correct!    
> Round complete!    
> TUCTF{c4n_s0me0ne_turn_a_1ight_0n}    
> ------------------------------------------    
> You have found The Nothing    
> This is level 3, The Nothing.    
> Round 1. Give me some text:    


So, here is the code of scriptl2.py which solved the second level:
{% highlight python %}
ip='146.148.102.236'
port=24069

import socket
import re

# Round complete!
# TUCTF{i_wi11_n0t_5teal}
# ------------------------------------------
# You take the book
# This is level 2 the attic

# Level 2
CODEL2 = {
    '-'  : ' ',
    'n'  : 'a',
    'o'  : 'b',
    'p'  : 'c',
    'q'  : 'd',
    'r'  : 'e',
    's'  : 'f',
    't'  : 'g',
    'u'  : 'h',
    'v'  : 'i',
    'w'  : 'j',
    'x'  : 'k',
    'y'  : 'l',
    'z'  : 'm',
    '{'  : 'n',
    '|'  : 'o',
    '}'  : 'p',
    '~'  : 'q',
    ' '  : 'r',
    '!'  : 's',
    '"'  : 't',
    '#'  : 'u',
    '$'  : 'v',
    '%'  : 'w',
    '&'  : 'x',
    "'"  : 'y',
    '('  : 'z'
    }
# built using many trials and assuming monoalphabetic substitution
# hopefully they did not put any numbers in

for i in xrange(50):
    print(sock.recv(256))
    # Round i. Give me some text:
    sock.send("ABCDEFGH\n")
    print("#######")
    answer=sock.recv(256)
    print("server answer:" +answer)
    # server answer: STUVW encrypted is -`abcd
    match=re.search('What is (.*) decrypted?', answer)
    ciphertext=match.group(1)
    cleartext=''
    for cipherletter in ciphertext:
        cleartext += CODEL2[cipherletter]
    print("Cleartext is:" + cleartext)
    sock.send(cleartext+"\n")
{% endhighlight %}

Level 3
-------
The third level was hard.
If i remember correctly 'b' decrypted was 'b', 'bb' decrypted was
'yy', 'bbb' decrypted was again 'bbb'. I had no idea what was going
on. And for each trial I had to walk through the two levels before. I
opened multiple shells to run through the stuff in parallel. Spaces
were preserved, so there was no permutation, just substitution of
characters through other characters. I also assumed that it was a
deterministic encryption, since groups of characters were always
substituted by the same group of characters with the same length. As i
later discovered this was not the case. the nondeterminism was just
very seldom.

And then I had an idea. As with all good ideas, it is not entirely
clear how they form. Just that small spark of intuition. I looked at
all the cleartexts I had decrypted before and discovered that there
was about a hand full of them, all having to do with the neverending
story. Nice. So i thought that they will perhaps be the same again and
tried them all.
It was made a little more difficult by the fact, that for some
cleartexts there were multiple ciphertexts. For example 'dont forget
auryn' could either encrypt to 'erby urpi.y agpfb' or to 'sykg typdfg
alpjk'.

After I made that codebook with the words, the rest was simple. And
after 50 rounds, I saw the familiar text:

> Round 50. Give me some text:    
> the nothing encrypted is ghf kyghukd    
> What is ravf ghf ;pukcfrr decrypted?    
> :    
> Cleartext is:save the princess    
> Correct!    
> Round complete!    
> TUCTF{5omething_is_b3tt3r_th4n_n0thing}    
> ------------------------------------------    
> You have run into a bad place...    
> This is level 4, the swamps of sadness.    
> Round 1. Give me some text:    


Here is the code for scriptl3.py:
{% highlight python %}
ip='146.148.102.236'
port=24069

import socket
import re

# Round complete!
# TUCTF{i_wi11_n0t_5teal}
# ------------------------------------------
# You take the book
# This is level 2 the attic

# Level 2
CODEL3 = {
        "agpfjl vr dmype"       : "atreyu vs gmork",
        "ayp.fg ko imrpt"       : "atreyu vs gmork",
        "xaoycab"               : "bastian",
        "barguak"               : "bastian",
        "erby urpi.y agpfb"     : "dont forget auryn",
        "sykg typdfg alpjk"     : "dont forget auryn",
        "sgpfjl vr dmype"       : "dtreyu vs gmork",
        "tpadmfkgaguyk"         : 'fragmentation',
        "upaim.byaycrb"         : 'fragmentation',
        "duakg glpgif"          : "giant turtle",
        "icaby ygpyn."          : "giant turtle",
        "dmyper cyyi"           : "gmorks cool",
        "imrpto jrrn"           : "gmorks cool",
        "c r,. frg"             : "i owe you",
        "u ywf jyl"             : "i owe you",
        "myyk chuis"            : "moon child",
        "mrrb jdcne"            : "moon child",
        "mf ngjtepairb"         : "my luckdragon",
        "mj ilcespadyk"         : "my luckdragon",
        "pfasukd ur sakdfpylpr" : "reading is dangerours",
        "p.aecbi co eabi.prgpo" : "reading is dangerours",
        "ravf ghf ;pukcfrr"     : "save the princess",
        "oak. yd. lpcbj.oo"     : "save the princess",
        "ghf kyghukd"           : "the nothing",
        "yd. brydcbi"           : "the nothing",
        "ghf ypacif"            : "the oracle",
        "yd. rpajn."            : "the oracle",
        "ypf o,cmmcbi"          : "try swimming",
        "gpj rwummukd"          : "try swimming",
        "wficymf agpfjl"        : "welcome atreyu",
        ",.njrm. ayp.fg"        : "welcome atreyu"
     }

# we found out, that in the previous challenges, we always had the
# same words, so we built a table

for i in xrange(50):
    print(sock.recv(256))
    # Round i. Give me some text:
    sock.send("the nothing\n")
    print("#######")
    answer=sock.recv(256)
    print("server answer:" +answer)
    
    match=re.search('What is (.*) decrypted?', answer)
    ciphertext=match.group(1)
    
    cleartext = CODEL3[ciphertext]
    
    print("Cleartext is:" + cleartext)
    sock.send(cleartext+"\n")

print(sock.recv(256))

# Round complete!
# TUCTF{5omething_is_b3tt3r_th4n_n0thing}
# ------------------------------------------
# You have run into a bad place...
# This is level 4, the swamps of sadness.
# Round 1. Give me some text:
=crypto3=
{% endhighlight %}


Level 4
-------
Level 4 was really damn weird. The encryptions were always totally
different. And I had to wait half an eternity to run through all my
script to get more cleartext-ciphertext pairs. I have no idea how the
code there worked. And again if I succeeded I would only get 10
points.
In comparison some web sql-injection challenge yielded at least 50 points.
This was basically the reason I quit and tried some other challenges.
So no code beyond level 3.

I have one log which is the transcript of the communication between my
program and the server, which is perhaps interesting. It can be found
[here]({{ site.url }}/assets/writeup-the-neverending-crypto-challenge/transcript.txt).
