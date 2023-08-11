---
layout: post
tags : [hacking, ctf, review, programming]
---

## Introduction
In the middle of March i decided to take the [Certified Red Team
Operator (CRTO)
certification](https://training.zeropointsecurity.co.uk/courses/red-team-ops) by [ZeroPoint Security](https://www.zeropointsecurity.co.uk/).

As I would have loved to read some more reviews of it before taking
it, here's mine.

## General
The CRTO is a pretty novel certificate in the area of IT security and
especially red teaming (in contrast to penetration testing). It is
considered a beginner certificate as it is very general. It covers
phishing, Kerberos, Active Directory misconfigurations, Windows
privilege escalation and much more. However, if you think it is easy,
due to the fact that it is considered a beginner certificate then you
are wrong.
In fact, you need to know quite some stuff, like how to
use a command line and understand a lot of the things that may be
considered basic in IT security. Still, when i was back at university,
I would probably have failed it.

Well, my company granted me some time for the certificate. I used a
week for going through the course material, one more week for the lab
(in fact, I only spend 30 hours in the lab), and then i did the exam
and passed (again in 30 hours).

## The Course Material
The course material is great. Everything you need to know for the exam
is in there. The texts are very short and applied. For everything
there is an example that you can try in the lab. For the more
complicated things there were videos.

The course material was fun to read and I even looked forward to it.
It is clearly written and there are no ambiguities. The sections are
short and encourage you to participate.

Sometimes alternative ways of reaching a goal are explained. This is
the principle of "attack in depth", according to which a good red
teamer should know more ways to perform the same attack, as some may
be monitored or blocked.

The course material was where this course shined for me. I studied
one week full-time.

## The Lab
The lab was nice. You get a Cobalt Strike license and can perform all
the attacks. But mostly I was not using any special CS functionality,
but rather took CS as a platform to launch other tools such as Rubeus
or Whisker.
Still, CS is probably considered the industry standard, so it makes
sense to know it.

The lab is inside a guacamole session. So everything is browser-based
and you do not need any special VPN configuration. The tools are
already installed, which in my opinion is great. You can perform
attacks and apply your knowledge instead of fiddling with compiling some
abandoned code from github as in other labs.

The lab is not a challenge lab, where you have to think by yourself,
but rather a "copy paste lab". You can copy the code from the course
and apply it directly in the lab. In my opinion that was okay, but i
did some of the attacks by memory to increase the learning effect for
myself.
As an example, some tools need a FQDN, and if you are using the
Netbios name instead, then there will be an error and you will be
unable to figure out why. Also with Kerberos, sometimes you need a new
logon session to log in to other services with your stolen tickets.

The lab is sold in monthly subscriptions. I purchased 40 hours, but
only needed 30 hours to test everything i wanted to try.

You have the lab by yourself, so there is no other hacker that
reverts your machines or breaks the services you want to exploit. You
can pause the lab and are only billed by the time you are using it.

## The Exam
At the end of my two weeks of learning i tried my hands at the exam.
I was not looking forward to it but who likes exams anyway.
The exam is in a lab with 8 machines, and you need to compromise 6 of
them. You have 48 hours for this, but can split it over four days. So
at the worst, you have four days where you are hacking 12 hours per
day.

At least, there is no report necessary. You just have to copy the
content of the flag files from the desktops of the administrators as a
proof of compromise.

As a result of that hazing you may get a hip certificate if you pass.
It's not that you can do anything with the certificate. The CRTO is a
pretty novel certification so HR does not know it and you will not
find it in any job description. But if you are speaking to someone
with more background in IT security it may be possible that he knows
it and is impressed. It may be good for your ego, though.

One attempt at an exam was included in the price of the course, so i
thought that i should at least try it.
I don't know if i would have attempted the exam if it wasn't included
in the price.

Well, the exam was well designed as it covered the content of the
course pretty well. There was no knowledge necessary that was
unexpected. The course was a good preparation for the exam.

But still there were technical difficulties. While the connectivity
between the machines was very good in the lab, in the exam it was
apeshit. At one point i was unsure if it was part of the exam or if
there were really technical problems.

While the lab felt like building a sandcastle, the exam felt like
building a sandcastle when sometimes a wave came by.
I did a lot of the same steps multiple times as my beacons died.
Whereas in the lab nearly every method of lateral movement worked, in
the exam i needed to try all methods one after another until one
worked.
One attack was especially flaky and i needed to perform it multiple
times until it worked. In fact, when it did not work the first time i
thought that it did not work at all and tried different things
(including taking a walk) for six hours.

My DNS beacons did not work at all. I suspect that the domains were
not registered in the DNS server, but in the end i was fine without
using DNS beacons.

In the exam it was always clear what the next step was. The machines
can only be completed in a fixed succession. On the one hand, this is
fun, as you know what is expected. You don't have to be lucky
in finding the one vulnerability when enumerating your target. On the
other hand, you are stuck when you are stuck and there is no way to
hack a different machine until you have a new idea.

In the end, i solved the six machines in 31 hours over 3 days. So i
even had 17 hours left. I was attempting to hack further, but as i was
unable to execute a beacon on the sixth machine i called it a day.

![Leaderboard]({{ site.url }}/assets/CRTO-course-review/leaderboard.png)

I had a technical problem, as one of the desktops did not contain the
flag file. I needed to ask in the discord channel for support.
Rastamouse, the head behind ZeroPoint Security helped me within at
most five minutes. Could I have been stuck on that issue and failed my
course due to that? No. The flags are numbered. And as soon as you
find a flag with a number where you don't have the predecessor you
know that something is wrong.  This brings me to my next point.

## Community
There is a forum on the web page of the course, but the real community
is on Discord. Discord as a community may not be ideal. But as a place
for technical support it is great. In other certifications I attended
there was only mail contact (which is slow) or you needed to use IRC
(which is okay).
Also, the community is very helpful if you ask the right questions and
show that you are trying. I have not read even one "try harder", as it
is unfortunately common in infosec communities.

## Conclusion
In summary, regarding the presentation of content, the CRTO was
probably one of the best courses I ever attended. I learned a lot.
Even though some of the attacks were already known to me, it's still
good to be able to perform them with different tools.
The exam was fair, but i am not sure if i would have been sad if i did
not make it.

Banana Bunny says thanks to Rastamouse! As my photoshop skills are
non-existent i hope you like some AI generated art.

![AI Art]({{ site.url }}/assets/CRTO-course-review/ai-art.png)
