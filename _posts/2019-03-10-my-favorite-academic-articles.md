---
layout: post
category : Lesson
tags : [math]
---

When i was a PhD student I got a lot in contact with bad scientific papers.
Often these fall into two main categories: badly written or
unscientific.
On the other hand there are a few papers I come back to over the years
with a broadened understanding and read them again, because they are
simply good. In this post I present some of my favorite papers
and encourage you to go and read them by yourself.

## Functional Programming with Bananas, Lenses, Envelopes and Barbed Wire, by Meijer et al.
This paper formalizes recursively defined data types and constructs a
calculus for recursion operators. Although the topic is probably mainly of
interest to functional programmers, the notation is very well chosen
and some academics should learn a lesson from these guys.
If you know category theory or some Haskell this paper should be
understandable to you. Additionally it is self-contained and you can
get away with not looking up any references. 

The only thing that sucks about this paper is its nondescriptive
title. I hate it when authors try to be funny in the title,
instead of telling me what the article is about.

## Topics in Computational Algebraic Number Theory, by Karim Belabas
Karim is the maintainer of the famous computer algebra system
[PARI/GP](http://pari.math.u-bordeaux1.fr/). If you have only heard of
Matlab and R, but not of PARI/GP, it is probably because PARI/GP is
mainly used for number theory. There are a bunch of operations
number theorists use routinely, like multiplication of ideals,
norms of algebraic integers, or computations with prime ideals.
Often mathematicians do not care how these are implemented and the
efficient algorithms were shared directly between the developers of
computer algebra systems. Most of the algorithms are not complex, so
no one has bothered to publish them. What Karim describes are
implementations and implementation tricks for these operations and I
sometimes find myself looking up an algorithm, simply because there
are not many other references, except maybe [A Course in Computational
Algebraic Number
Theory](http://www.springer.com/us/book/9783540556404) of Henri Cohen.

## Notation as a Tool of Thought, by Kenneth Iverson
Kenneth Iverson is the designer of the
[APL](https://en.wikipedia.org/wiki/APL_(programming_language))
programming language. Even though this language has not seen
widespread usage, it is build upon some very interesting ideas. One of
them is that a computer language should be succinct and more like
mathematical notation. Seeing some Java code where most of the
symbols are boilerplate and distract from the semantics i have to agree.
A more mathematical notation for code further eases formal proofs of
properties of the code.

I admire the exposition of the ideas of Kenneth. However, as already mentioned
APL is not really used. In my opinion that is due to the
use of non-ASCII symbols and very dense semantics. APL reads like
complex mathematical proofs without comments.

## Composing Contracts: An Adventure in Financial Engineering, by Simon Peyton Jones et al.
This article is about formalizing financial contracts like
derivatives, bonds, ... Further, the way in which they are formalized, is
very clever. The authors describe and implement a combinator library in
Haskell. This basically means that contracts can be combined: One can
compose contracts with "or" or "and" or have options on arbitrary
contracts. From a few primitives and some combinators 
the whole financial universe of contracts can be composed.

But the authors go beyond this. Since the price of the
primitives is known, and it is known how the price changes when
combined, one can price financial contracts built from these
primitives.

As a side note, only recently I learned to admire the use of colons in
titles. I like hyphens more from a visual point of view, but colons
are clearly superior in a bibliography.
