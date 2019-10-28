---
layout: post
category :
tags : [philosophy, math, easy, programming]
---

{% include math %}

## Introduction
Today, I want to write about idealism in mathematics for a general
audience. I will cover some arguments that I think are strong
indicators for the idealist position.

These include
- observations on prime numbers, where I will talk about the Ishango
  bone,
- the Church-Turing thesis, and
- the Curry-Howard correspondence.

Feel free to skip to the parts that interest you.

I think most mathematicians are idealists in some way or another.
Idealism in this case does not mean that they are somehow positive,
but rather refers to an old philosophical stance going back to Plato,
that there is a world of ideals, and this material world is just a
reflection of that ideal world. This includes the viewpoint that
non-material things like numbers really exist.

But do they? Does a number like two or three really exist outside of
that which it is counting? Have numbers been discovered (the idealist
position) or invented (the materialist position)?

For a while I was not very convinced that infinity exists. All things
in this universe and in my perception are finite. So why should an
infinity exist.
There is a famous quote by the great mathematician Leopold Kronecker,
that says "Die ganzen Zahlen hat der liebe Gott gemacht, alles andere
ist Menschenwerk" ("God made natural numbers; all else is the work of
man ").
Up to now I am still not very convinced that infinity really exists as
a thing in itself. But I made a bunch of other discoveries.

## Prime Numbers and a Prehistoric Bone
Some 18000 to 20000 years ago there was someone scratching curious
marks on a bone.
These marks are arranged in mathematical patterns.
We know of this fact, because that bone has later been found and
became known as the [Ishango bone](https://en.wikipedia.org/wiki/Ishango_bone).

The third of column of scratches on that bone are arranged in four sets of 19, 17,
13, and 11 scratches. Any fairly competent mathematician would
immediately think of prime numbers when mentioning these scratched numbers.
These special types of numbers are strongly tied to the concept of
dividing things, which I would argue is a natural operation. Dividing
cookies between children or dividing loot from a hunt between families
is, in my opinion, a very natural thing to do.

Prime numbers, like 19, 17, 13, or 11 are numbers that cannot be
"sensibly" divided.
If you have 13 cookies and want do divide them fairly between any number
of children, this only works if you have only one child or 13
children.
Otherwise you end up cutting up cookies.
Under the assumption that fair division is a natural concept, the
phenomenon of prime numbers is in some sense natural.

Moreover, prime numbers behave like "things of the material world".
With this, I mean that material things are not mathematical points or
without any physical friction, or other such nonsense that has been
conflated with the term idealism.
In fact, prime numbers behave "randomly" in the sense that they evade
characterisation in some formulaic way.
To be more precise, there is no polynomial function $$f$$, such that
$$f(n)$$ is the $$n$$-th prime number.
This is a property I would expect from discovered things, but not from
invented things.

What was that person producing these scratching marks on that bone
experiencing? Was he/she discovering prime numbers?

To be fair, I have to mention, that it is disputed if these numbers
are really meant to be prime numbers, or if this is just a
coincidence. Maybe it was a calendar or some other thing.
However, prime numbers better fit into the arc of my story.

## The Discovery of Computation, the Invention of Computers
When trying to find an example of something that is definitely
man-made, computers and programming languages seem to be a good
starting point.
However, they are not.
Computation itself behaves like what I would expect from "natural
things" that occur "in the wild", as I will discuss in the following.

In the 1930s -- long before computers have become common -- there have
been multiple attempts to define what computation itself is.
It was somehow clear that given enough time some things can be
computed given a paper and a pencil.
However, there was no formal notion of computability.

The most famous of these approaches is perhaps the Turing machine by
Alan Turing. This is not a real physical machine, but rather a
hypothetical machine with multiple states and an infinitely long tape
that can be read from and written to. Alan Turing defined functions to
be "computable" if they can be represented as a Turing machine.

In contrast to Turing machines, Kurt Gödel worked on
$$\mu$$-recursive functions.
These are a special class of functions, defined by a bunch of basic
functions, together with a bunch of rules to create more complex
functions from these basic functions.
Gödel defined functions to be "computable" if they can be realized as
a $$\mu$$-recursive function.

Additionally to Turing and Gödel, Alonzo Church worked on a formalism
to define computability.
This formalism is now known as the $$\lambda$$-calculus.
The lambda calculus is a set of rules on how to manipulate terms.
Church defined functions to be "computable" if they can be
represented as terms in the $$\lambda$$-calculus.

The big surprise is that all of these approaches to computability --
though they have been independently "invented" -- have the same power.
All of these three definitions of computable functions are equivalent.
Computable function for Turing machines, for $$\mu$$-recursive
functions, and in the framework of the $$\lambda$$-calculus are the
same.
This suggests that "computable functions" in the intuitive sense, are
exactly those functions that can be computed in one (then all) of
these frameworks.
This hypothesis or definition of computability, became known as the
[Church-Turing thesis](https://en.wikipedia.org/wiki/Church%E2%80%93Turing_thesis).

This observation suggests that computability itself is not an invented
concept, but rather a discovered natural notion and is woven deep into
the fabric of the universe.
There seems to be no other concept of computation that is more
powerful than the current concepts.
Moreover, the current concepts all have exactly the same power.
If computation would have been invented, I would have expected that
some formalisms are strictly more powerful than others, and the most
powerful formalism wins.

This implies that all programming languages (up to some very primitive
languages) are built to have the same power.
Functions that are computable in one programming language are also
computable in another programming language.
The observation of the equivalence between these different
formalisations of computability even suggests that there will be no
more powerful programming language in the sense that this new language
can compute more things
This holds up to now, and even quantum computers will
only get faster, but will obey these natural laws to computability.

## Logic and Computation
I have another point up my sleeve, namely the [Curry-Howard correspondence](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence).
The Curry-Howard correspondence is an isomorphism between mathematical
proofs and programs.
An isomorphism means that they have the same structure:
Proofs and programs are the same, up to terminology.

Let's do an example.
Informally speaking, if there is a program that converts $$A$$ to
$$B$$, and another program that converts $$B$$ to $$C$$, then you can
combine these two programs.
The result is a program that takes as input some $$A$$ and outputs
$$C$$.
The same is true of proofs. If you have a proof that $$B$$ is true
under the assumption $$A$$, and a second proof that $$C$$ is true
under the assumption $$B$$, then you can combine these proofs.
Thus, if $$A$$ is true, then $$B$$ is true (due to the first proof),
and if that is the case, then $$C$$ is true (due to the second proof).
Thus, the combined proofs state that $$C$$ is true, given that $$A$$
is true.

This Curry-Howard correspondence leads to a number of interesting
observations.
As an example, in computability, there are some functions that can be
proven to be not computable. This is known as the [Halting
problem](https://en.wikipedia.org/wiki/Halting_problem).
The halting problem is a function that cannot be computed on a Turing
machine. And as Turing machines, $$\mu$$-recursive functions, the
$$\lambda$$-calculus, and all other formalizations of computability
are equivalent, it can be said that this Halting function cannot be
computed at all.
What does this mean if we apply the Curry-Howard correspondence?
Well, as computation and proofs are equivalent, this yields [Gödel's
incompleteness theorem](https://en.wikipedia.org/wiki/G%C3%B6del%27s_incompleteness_theorems).
This theorem tells us that there are statements that can be expressed
in a given set of axioms, but can neither be proven nor disproven:
They are undecidable.

Hence, computation and logic are strongly intertwined. They are even
isomorphic, i.e., the same up to terminology.
Thus, I cannot doubt any longer that an alien culture with sufficient
intelligence will eventually come up with some formalization that is
isomorphic to, say, Turing machines.
Consequently these have been discovered, instead of invented.

# Conclusion
To conclude, I will repeat my main points.
Things like primes, computation, and logic -- though they may seem
man-made -- are not invented, but discovered.

Primes behave "randomly", in some sense. Exactly, as one would expect
from naturally occuring things.
Formalizations of computability have exactly the same expressiveness, thus
suggesting a natural law to computability.
Proofs are isomorphic to computation, and thus logic and computation
itself are two sides of the same coin.
