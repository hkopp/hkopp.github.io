---
layout: post
category : Lesson
tags : [stochastic, easy]
---
{% include math %}

## Introduction
Today, I will explain easy things in a complex way. I will give a
simple example of maximum likelihood estimation of the probabilities
of a biased coin toss.

## Bernoulli trial
A Bernoulli trial is a random experiment with two outcomes. The
standard example is the flip of a probably biased coin. Let us first
denote the outcomes with $$0$$, and $$1$$ instead of head and tail, since that
sounds a lot more professional.
The coin will land on one side, say $$1$$ with probability of $$p$$.
It will land on the other side $$0$$ with a probability of $$1-p$$,
since the probabilities need to sum to one.
In mathematical formulation we get the probability 
$$P(X=1)=p$$ and $$P(X=0)=1-p$$.
Now we can define the probability mass function $$f(x;p)$$.

$$
\begin{equation}
f(x;p)=\begin{cases}
    p,   & x=1\\
    1-p, & x=0\\
  \end{cases}
\end{equation}
$$

In case you only know continuous probability distributions, the
probability mass function is similar to the probability density function,
but in the discrete case.

## Maximum Likelihood Estimation
Let us now assume, that we have flipped the coin a few times and got
the results $$x_1,...,x_n$$ which are either $$0$$ or $$1$$. In
stochastic parlance these are called observations.
The question is what the probability $$p$$ is. Intuitively one could
assume that it is the number of ones we got divided by the total
number of coin throws. For example if we threw the coin a hundred
times and 30 times we got a one, then we would maybe guess
$$\hat{p}=30/100$$.
Actually this is the interpretation from a frequentist perspective,
but let us not digress into that territory. I will prove in the
following that the intuition in this case is correct, by proving that
the guess $$\hat{p}=\sum x_i/n$$ is the "most likely" value for the
real $$p$$.

First, let us reformulate our probability mass function as
$$f(x;p)=p^x(1-p)^{1-x}$$, which makes it easier to calculate with it.
If we have the (independent) observations $$x_1,...,x_n$$, then their
joint probability mass function is

$$
\begin{equation}
f(x_1,...,x_n;p)=\prod\limits_i f(x_i;p)=p^{\sum x_i} (1-p)^{n-\sum x_i}.
\end{equation}
$$

If we interpret this as a function not in the observations with the
fixed parameter $$p$$, but instead as a function in the model
parameter with fixed observations, we get what is called the likelihood
function $$\mathcal{L}(p;x_1,...,x_n)=f(x_1,...,x_n;p)$$.

We now want to find the $$p$$ with the highest likelihood given the
observation $$x_1,...,x_n$$, that is, we want to maximize our
likelihood function $$\mathcal{L}(\cdot;x_1,...,x_n)$$.
In this case it is easier to maximize the log of the likelihood
$$\log\mathcal{L}(p)=\sum x_i\log p + (n-\sum x_i)\log(1-p)$$
, which yields the same result, since the log is monotonously increasing.  

As you may remember, maximizing a function means setting its
first derivative to zero.

$$
\begin{align*}
\frac{\partial\log\mathcal{L}(p)}{\partial p}
= \frac{\sum x_i}{p} - \frac{n-\sum x_i}{1-p}
= \frac{(1-p)\sum x_i -pn +p \sum x_i}{p(1-p)}
= \frac{\sum x_i -pn}{p(1-p)}
\stackrel{!}{=}0
\end{align*}
$$

Multiplying by $$p(1-p)$$ yields that the maximum is reached at
$$p=\frac{\sum x_i}{n}$$. This is the most likely value for $$p$$
given our observation which confirms our intuition.
