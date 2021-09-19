---
layout: post
category : lesson
tags : [math, philosophy, elementary, logic]
---

## Introduction
Today, I talk a little bit about constructive or intuitionistic logic.
I am not a constructive logician but I enjoy their ideas and hope that I repeat their point of view undistorted.

In a nutshell, constructive logic is a new approach to the foundations of mathematics that only allows for constructive proofs.

## Arguments for Constructive Logic

### Computability

In classical math, there were theorems that proved the existence of objects without telling how to find them.
Many jokes about mathematicians are based on this fact.
The punch line of these types of jokes is usually that problems are "solvable" or even "uniquely solvable" without presenting the solution.

It cannot be dismissed that through the emergence of computers, it may be nice to have a way to construct objects with special properties, instead of only knowing about their existence.
More formally, we can consider a theorem along the following lines:
For all objects x from some domain there is an object y, such that some proposition A(x,y) is true.
However, there is no way to find that y. What we want instead of the existence theorem is a function y(x) that computes a y given an x, such that A(x,y(x)) is true.

### Truth

A second more philosophical argument for constructive logic revolves around the notion of truth itself.
In classical logic, truth is simply a value that is assigned to a statement.
The truth of a statement follows from internal logical conclusions, probably referring to an external reality.
In constructive logic, the notion of truth is substituted by constructibility.
Things are only true if they can be constructed.

## The Law of the Excluded Middle (LEM) and Constructibility

In a nutshell, constructive logic is constructive because it gets rid of the law of the excluded middle (LEM).

The history of LEM goes back to Aristotle.
Aristotle said that of two contradictory statements one must be true, and the other false.
In more modern language this means that a statement is either true or false, there is no third truth value. I.e., the "middle" is excluded.

So, How does LEM lead to unconstructiveness?
This is due to the fact that LEM allows for proofs by contradiction which are not constructive.
These proofs are generally like the following.

> Assume there is no x, such that a proposition P(x) is true. Then, after some logical inductions, we receive a contradiction.
> Consequently, the assumption was not true.
> According to LEM, this means that the assumption that there is no such x is wrong.
> So, there is such an x, but we do not know how to find it.

This is clearly not constructive.
And that is the reason why constructive logic does not use LEM.

Note that constructive logicians do not think that LEM is wrong.
Merely, that it may not always hold.

## A Note on the Axiom of Choice

If you are a fan of the axiom of choice and are interested in constructive mathematics I have bad news for you.
The axiom of choice implies the law of the excluded middle.
This fact is called [Diaconescu's Theorem](https://en.wikipedia.org/wiki/Diaconescu%27s_theorem).
As a consequence, a constructive set theory cannot use the axiom of choice.

## Summary

Constructive or intuitionistic logic is a weaker form of logic than classical logic.
This means that it is more conservative in what it allows to infer.
Everything that is true in constructive logic is true in classical logic but not the other way around.
However, the main benefit of constructive logic is that its objects can be constructed.
