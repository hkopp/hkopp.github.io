---
layout: post
category: Misc
tagline: please don't mind the clickbait
tags: [hacking, rant]
---

## My background
When i left academia after pursuing my PhD and got into penetration
testing, i was irked by how people used the word "security research",
but I could not quite put my finger onto it.

For me, research was an activity that increased the knowledge of
humanity. As an example the theorems of Pythagoras or Thales of Milet
still hold and are still relevant for today.
The same holds true for physicists such as Newton, Laplace, or Kepler.
The purpose of research for me was always inquiry into the laws of
reality.

## Security "Research"
The vulnerabilities found by security research of course also still
hold, but they are not relevant today. Usually these vulnerabilities
are reported to the respective vendor who releases an update which
fixes the vulnerability. Of course the process is not as easy as
explained, as issuing an update may take half a year or more.
But after that, the vulnerability and hence the research is rendered
moot.

Is it fair to call such an endeavor research?

Most vulnerabilities do not lead to lessons learned for the field, or
even pose a large degree of novelty.
Shouldn't such an activity be called engineering rather than security
research?

Unfortunately, the notion security engineering is already taken by a
different field of study. But still, something about the term
"security research" for the act of identifying vulnerabilities feels
wrong to me.

As an example for laughable security research, take a look at [the
Jasmin ransomware](https://github.com/codesiddhant/Jasmin-Ransomware).
This is just a ransomware role-playing as security research. Of course
it is possible to create ransomware for educational purposes. But none
of that is research. Rather it is engineering. There is no scientific
value in this.

Another example of contemporary security research is the article [Certified
Pre-Owned by specterops](https://posts.specterops.io/certified-pre-owned-d95910965cd2).
It concerns a common misconfiguration in Windows Active Directory
that allows an attacker to escalate privileges. While i respect the
immense effort that went into the writing, to me this is still
borderline research. The novel attack primitive is a way forward. But
still, this primitive is only relevant for Windows Active Directory,
and only as long as Microsoft has not admitted the issue. In ten to
twenty years, no one will care about this issue, except as a
historical anecdote. Mind you, this issue is economically significant.
But not scientifically.

And then there is also "show research" or "stunt security research",
where people hack a satellite or a car. Something that is highly
marketable but has limited impact.

Contrast this with fundamental theorems of computer science such as
the [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem), the
[Church-Turing
thesis](https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis),
or the [Curry-Howard-Lambek
correspondence](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence).
Or look at some of the old cryptographic research that introduced new
attack primitives such as timing attacks or chosen plaintext attacks,
that are reusable and can be applied in different contexts.
These results are more akin to the results of Fermat, as they will not
lose their relevance in times to come.

Do you think Pythagoras would still be so widely regarded if his
research practices resembled those of contemporary security
researchers? Would Pythagoras still be relevant today if he described
security issues in a horse-drawn carriage that only apply to a
single manufacturer from his town?

## A Way Forward
Instead of just being grumpy and negative about security research, i
want to propose a way forward.

I would like to see more security research that has an impact on the
field of knowledge and is still relevant in ten or even more years. I
would like to see new attacker models, new attacking primitives. I
would like to see research on how different attacking primitives can
be combined. I would like to see comparative studies between different
protocol designs or different system architectures. I want to see
novel security models, instead of rehashing the same old
vulnerabilities in different implementations. I want advances in
fuzzing and symbolic execution. I want more research into the
applicability of formal methods into security research.
This is the only way security research can continue to stay relevant.
