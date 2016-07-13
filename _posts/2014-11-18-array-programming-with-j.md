---
layout: post
category : Messing around
tags : [programming, J, elementary]
---
{% include math %}

## Introduction
In what follows, I will explain a little bit about J.
J is the decessor of APL.
APL is the brainchild of Ken Iverson and feels more like mathematical notation instead of a programming language.
One of the main drawbacks I see in APL is its confusing non-ASCII characterset.
J uses ASCII.
Some APL defendants say that the original APL-characterset made APL to a tool of thought an J sucks at that.
For code blocks, I will use the notation of my jconsole:

      input
    output

## Arithmetic
Addition, subtraction, multiplication, and division work the obvious way:

      2+3
    6
      2-3
    _1
      2*3
    6
      2 % 3
    0.666667

Note that in the subtraction, the leading minus is _.
This is to distinguish it from the minus-operator.
If you know dc, you should be familiar with that.
The operator `|` means modulo and `^` is exponentiation.

Additionally since J is array oriented, arithmetic also works on arrays.
In fact, every operator I encountered works on arrays out of the box.

      1 2 3 + 2
    3 4 5

      1 2 3 + 1 2 3
    2 4 6

      1 2 3 + 2 3
    |length error
    |   1 2 3    +2 3

You can do the same with the other operations.
J evaluates from right to left if there are no brackets.
This could be confusing at first, but I think it is actually easier than to remember thousands of precence rules.

      3*2+1
    9
      (3*2)+1
    7

Variables are assigned with `=:` so for example `x =: 100` sets x to 100.

## Terminology
Unfortunately J uses some pretty strange terminology.
For example with monad, they mean a monadic function.
This really has nothing to do with monads like in Haskell or category theory.
At first thought I wanted to call them unary functions. But that is not correct, since they can take in an array.
A monadic function is thus a function which operates on single elements.
Monadic functions do have prefix notation:

      - 2
    _2

      - 2 3 4
    _2 _3 _4

A dyad or a dyadic function is a function which takes multiple arguments.
Dyads have infix notation:

      1 - 2
    _1

Whoa, wait.
I just used the same function but in another context, namely a dadic context instead of a monadic one.
And it behaves differently.
In fact, that is true for most of the operators in J.
Another example:

      % 2 3 4
    0.5 0.333333 0.25
      2 % 2 3 4
    1 0.666667 0.5


They also use the term 'verb' for what I have up to now called an operator.
An operator, that operates on verbs, i.e., a higher-order function, is called an 'adverb'.

## Working with Arrays
As seen above, all operators work for arrays out of the box.
There is a special operator for building arrays.
In modern languages this would be called `range()` or something similar.

      i. 3
    0 1 2

Since J is array oriented, of course this also works on arrays.

      i. 3 4
    0 1  2  3
    4 5  6  7
    8 9 10 11

If we now want to sum over the whole list, we want to define a new verb, that sums up a list.
There is an adverb for that in J, namely `/`.
This is comparable to 'fold' in Haskell.
If V is any dyadic function, then `V / a b c` computes `a V b V c`.

      + / 2 3 4
    9

To compute the length of an array we use `#`.

      # 1 2 3
    3

In a dyadic context, this is the choice function.

      1 0 1 # 1 2 3
    1 3


You can even choose some special stuff directly.
For example if you want to compare two arrays and choose the smaller entries:

      1 2 3 4 < 4 3 2 1
    1 1 0 0
      1 2 3 4 <. 4 3 2 1
    1 2 2 1

## Booleans
I introduced booleans implicitly somewhere above.
They are really straightforward if you have ever writte a program.
'0' is false, '1' is true.
So

      2 > 1
    1
      2 = 1
    0
      2 < 1
    0
      2 < 1 2 3 4
    0 0 1 1

The only stumbling block I have encountered is when I want to compare two arrays, because the verb `=` works componentwise:

      2 3 4 = 2 3 2
    1 1 0
      2 3 4 -: 2 3 2
    0
      2 3 4 -: 2 3 4
    1

## Solving the first project Euler exercise
If you read this, you are probably familiar with project Euler.
If not, google it.
The assignment is to compute the sum of all multiples of 3 or 5 below 1000.
So we first list all numbers below 1000 and save it to an array with `list =:1+ i.999`.
Next we want to choose those numbers which are divisible by 3 or 5.
Well, a number is zero modulo 3 or 5, if `(0 = (5 | list) <. (3 | list))`.
We now choose those from the original list with `(0 = (5 | list) <. (3 | list)) # list`
and add all together with `+ /`.
So the final function is

    list =: 1 + i. 999
    + / (0 = (5 | list) <. (3 | list)) # list

Exercise: Compare this to your shitty perl-golfed solution.
In fact, to make it even shorter, we can stuff the declaration in the same line and get rid of all the spaces.
We can also let the list begin at 0 and name it just 'a'.

    +/(0=(5|a)<.(3|a))#a=:i.1e3

28 characters, bitches!
