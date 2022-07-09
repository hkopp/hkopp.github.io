---
layout: post
category: Recreational Math
tags : [math, programming, easy]
---
{% include math %}

## Introduction
Today, I want to talk about FRACTRAN, a simple (though not easy)
programming language.
FRACTRAN was invented by the great John Conway which is mostly known
for his puzzles and his recreational math.
I first encountered FRACTRAN as a teenager in the book "Ancient puzzles:
Classic brainteasers and other timeless mathematical games of the last
10 centuries" by Dominic Olivastro.
Actually I read the german version "Das chinesische Dreieck", since I
was a teenager and at that time read most books in my native language.
Olivastro explained a very simple model of computation and dumbfounded
me with the PRIMEGAME example, a program in FRACTRAN which can compute
primes. I did not know how it works and it was a mystery to me.
Nevertheless I found it very captivating and it put me under its
spell. So I revisited it and wrote a blog post with the intention to
document how I lifted the mysteries from FRACTRAN.

If you are interested in the original sources, you may probably want
to check "FRACTRAN: A simple universal programming language for
arithmetic" by John Conway in the journal Open Problems in Communication and
Computation.

## How FRACTRAN works
A FRACTRAN program is a finite list of fractions

$$
\frac{a_1}{b_1}, ..., \frac{a_i}{b_i}, ..., \frac{a_n}{b_n}
$$

Bamm! That is all the syntax you need to know for this article.
Forget specs of thousands of pages like in Java. Just lists of
fractions.

So, how do we evaluate such a program?
You simply give it as input a natural number $$N_0$$ and it gives you back an
output computed as follows:
We define $$N_j$$ as $$N_{j-1}\frac{a_i}{b_i}$$, where $$1\leq i\leq n$$ is the
smallest index such that $$N_{j-1}\frac{a_i}{b_i}$$ is an integer.
If there is no $$i$$, such that $$N_{j-1}\frac{a_i}{b_i}$$ is an integer we return $$N_{j-1}$$.

## Examples

### Moving a value
Let us take a look at the following program

$$
\frac{3}{2}
$$

On input $$2^a$$ for some $$a$$, it tests if $$2^a\frac{3}{2}$$ is an
integer and if yes, replaces the current state $$2^a$$ with $$2^a\frac{3}{2}=2^{a-1}3$$. It then repeats, until we cannot get integers anymore.
The result is then $$3^a$$.

### PRIMEGAME
This is too early to understand the PRIMEGAME, but i think it is
nice to introduce it here, since it played such an important role for
my fascination with FRACTRAN.

Consider the program

$$
\frac{17}{91}
,\frac{78}{85}
,\frac{19}{51}
,\frac{23}{38}
,\frac{29}{33}
,\frac{77}{29}
,\frac{95}{23}
,\frac{77}{19}
,\frac{1}{17}
,\frac{11}{13}
,\frac{13}{11}
,\frac{15}{14}
,\frac{15}{2}
,\frac{55}{1}
$$

When given 2 as input it outputs an endless series which contains only
prime powers of 2. I.e. $$2, ..., 2^2, ..., 2^5, ..., 2^7, ..., 2^{11},
...$$.
If you are looking for a nice nerdy tattoo and do not want to have the
graph of a sine or the euler product you may want to consider this sequence of fractions.

## More verbose explanation
FRACTRAN is basically a register machine, where the values of multiple
registers are stored in the valuations (exponents) of the primes.
The value of the first register is the power of 2. The value of the
second register is the power of 3, and so on.
More concretely, the number $$2^a3^b5^c$$ corresponds to three registers
with values $$(a,b,c)$$.
Each fraction, i.e., each instruction $$\frac{a_i}{b_i}$$ tests if the
registers corresponding to the prime factors of $$b_i$$ are set. If
that is the case, these registers are decremented and the registers
corresponding to the prime factors of $$a_i$$ are incremented.

This model of computation is turing-complete, which means that any
computable function can be computed by a FRACTRAN program. If you do
not believe me, check out the
original article of Conway, where a FRACTRAN program which enumerates
all computable functions is given. Another way of recognizing
turing-completeness is if you are familiar with register machines,
especially Minksy machines.

## Doing interesting stuff

We have already seen how to move a value from one register to another.
Next, we want to do more interesting stuff.
To this end I have written a FRACTRAN interpreter in Python 3.

{% highlight python %}
verbose = 1

def eval_fractran(_prog, _input):
    """
    evaluates the fractran program _program with input _input.
    The fractran program is given as a list of
    (numerator,denominator)-pairs.
    """
    state = _input
    statechange = True
    while statechange:
        statechange = False
        for (num, denom) in _prog:
            if state % denom == 0:
                statechange = True
                if verbose:
                    print("Applying " + str((num, denom)) +" to " + str(state))
                state = state // denom * num
                break
    return state


assert(eval_fractran([(3,2)], 2) == 3)
assert(eval_fractran([(3,2)], 4) == 9)
{% endhighlight %}

### Copying a value
We want to input $$2^a$$ and have as output $$2^a3^a$$.
Unfortunately this does not work. Since the first input is only
divisible by powers of two, there needs to be a fraction with two in
the denominator (otherwise no instruction is executed). However, this
instruction would also be carried out on the output.

That means we need to set some control registers. As input we take
$$2^ap$$ for some prime p and as output we want to have $$2^a3^a$$.
The prime $$p$$ indicates our state.
Since checking for a state is basically division, the register is
decremented when we check. Thus we need two state bits, which we use
alternately.
Since $$2^a$$ is also decremented, we need first to copy the value to
another register.

Let us try it step by step.
The program $$\frac{3\cdot 5}{2}$$ yields $$3^a5^a$$ given input
$$2^a$$.
To copy the value of the $$5$$-register back, we need to change the
state.
Marking the first state with the two primes $$7$$ and $$11$$ we get
the program $$\frac{3\cdot 5\cdot 11}{2\cdot 7}\frac{7}{11}$$. If we
evaluate this on $$2^a7$$ we receive $$3^a5^a7$$.
But now it is trivial to move the value back from the $$5$$-register.

The final program which outputs $$2^a3^a$$ given input $$2^a7$$ is
thus

$$
\begin{array}{ll}
\frac{3\cdot 5\cdot 11}{2\cdot 7},\frac{7}{11}, & \text{to copy the stuff from $2^a$ to $3^a5^a$} \\
\frac{1}{7}, & \text{to change the state that it stops copying when
we fill the $2$-register}\\
\frac{2}{5} & \text{to move the value back from $5$ to $2$}
\end{array}
$$

Let us check:
{% highlight python %}
In [54]: copy = [(3*5*11,2*7),(7,11),(1,7),(2,5)]
    ...: a=3
    ...: 

In [55]: eval_fractran(copy,2**a*7) == 2**a * 3**a
Applying (165, 14) to 56
Applying (7, 11) to 660
Applying (165, 14) to 420
Applying (7, 11) to 4950
Applying (165, 14) to 3150
Applying (7, 11) to 37125
Applying (1, 7) to 23625
Applying (2, 5) to 3375
Applying (2, 5) to 1350
Applying (2, 5) to 540
Out[55]: True
{% endhighlight %}

### Addition
Actually, addition is the same as our copy command, when we do not
have a clean target register.
If we evaluate the program $$\frac{2}{3}$$ on input $$2^a3^b$$ we copy
the $$3^b$$ on top of $$2^a$$ and get $$2^{a+b}$$.

### Subtraction
After tackling addition, the obvious next trivial operation is
subtraction. If we have $$a\geq b$$ we want to feed our program
$$2^a3^b$$ and it should return $$2^{a-b}$$. This can be achieved with
the program $$\frac{1}{2\cdot 3}$$.
Note that if $$a\leq b$$ we get instead $$3^{a-b}$$. Thus we can also
use this to represent negative numbers.

### Fibonacci numbers
Let us write a nontrivial program. We want to compute Fibonacci
numbers. Let $$f_n$$ denote the $$n$$-th Fibonacci number. We want to
have a program, that given input $$2^{f_n}3^{f_{n+1}}5$$ returns
$$2^{f_{n+1}}3^{f_{n+2}}=2^{f_{n+1}}3^{f_n+f_{n+1}}$$. The $$5$$ in
the input is again for maintaining state.

First we copy the powers of $$2$$ to $$7$$ so that we have $$3^{f_{n+1}}
7^{f_n}$$. This can be achieved by the fraction $$\frac{7}{2}$$.

Next, we copy the powers of $$3$$ back to $$2$$ and $$7$$ so
that we are left with $$2^{f_{n+1}}7^{f_n+f_{n+1}}$$. This can be done
by $$\frac{2\cdot 7}{3}$$.

Lastly, we move the powers of $$7$$ back to $$3$$, leaving us with the
result $$2^{f_{n+1}}3^{f_n+f_{n+1}}$$.

If we account for the different states we receive the following
program.

$$
\begin{array}{ll}
\frac{7\cdot 11}{2\cdot 5},\frac{5}{11} & \text{copy the powers of $2$ to $7$} \\
\frac{13}{5} & \text{switch to the next state} \\
\frac{2\cdot 7\cdot 17}{3\cdot 13},\frac{13}{17} & \text{copy the powers of $3$ back to $2$ and $7$} \\
\frac{1}{13} & \text{switch state} \\
\frac{3}{7} & \text{move the powers of $7$ back to $3$}
\end{array}
$$

I am not fully happy with it, since this performs only one Fibonaccy
iteration. Actually, I wanted to have something that takes as input
$$5^k$$ and gives me back $$2^{f_k}$$ or something similar. Anyway,
I'll leave this as an open question for the readers.
We know that there is a solution due to the turing-completeness and I
conjecture that it can be realized by augmenting my program from above
with some more state bits.
And yeah, I know that googling would maybe yield a solution, but this
would take out all the fun.

## Conclusion
I hope I have given an understandable introduction into the magic of
fractran. If you would like to play with it, you may want to try to
implement multiplication (from repeated addition) or division with
remainder (from repeated subtraction).
