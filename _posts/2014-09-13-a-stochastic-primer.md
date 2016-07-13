---
layout: post
category : Lesson
tags : [stochastic, easy]
---
{% include math %}

# Introduction
I often stumbled across basic stochastic properties which I do not
remember exactly anymore, so here it is: A post just for me, where i
can search for stochastic stuff. This also means that this post will
not be very detailed, but if you had basic stochastic in school you
should be able to follow along.

# Measure Space
First, let us define what a measure space is.  
We have the set $$\Omega$$ of samples. You can think of it as a set
containing all possible outcomes.  
Then there is the set $$\mathcal{F}$$ which is a subset of the powerset
of $$\Omega$$. In fact, we require it to be a $$\sigma$$-algebra, which is
a fancy way of saying, that it consists of all sets, where we can
assign a "volume" such that it makes sense.  
Finally, we have a probability measure $$P$$ which is a function from
$$\mathcal{F}$$ to the interval $$[0,1]$$, where we require that it
"behaves like a volume function".
This means that $$P(\Omega)=1$$ and for _countably_ many pairwise disjoint
$$A_i\in\mathcal{F}$$ we require that $$P(\bigcup A_i)=\sum P(A_i)$$.
Furthermore $$P(\emptyset)=0$$.

The triple $$(\Omega, \mathcal{F}, P)$$ is now called a measure space.

# Random Variables
To make things easy, I only consider random real-valued variables. The
interested reader can certainly try to define random variables into any
measurable set (Sorry Vitali sets).  
A real-valued random variable $$X$$ is a function
$$X:\Omega\rightarrow\mathbb{R}$$.
To compute the probability of the event that $$X$$ is $$541$$ (which is the
hundredth prime, in case you didn't know) we take the preimage
$$X^{-1}(541)$$ which lies in $$\Omega$$ where we can apply our
probability measure $$P(X^{-1}(541))=P\{\omega:X(\omega)=541\}$$  
We will call this in the following $$p_X(541)$$ or $$P(X=541)$$.  
This thing $$p_X$$ is called the probability density of X. The
idea behind it is that we forget about our measure space $$\Omega$$
and define a new measure on $$\mathbb{R}$$.

Calculating with that stuff is straightforward. For example $$P(a\leq
X\leq b)=\int_a^b p_X(x)dx$$ which is fortunately exactly what I would
suspect.

If we have two random variables $$X$$ and $$Y$$ we say that they are
independent if and only if $$p_{XY}(x,y)=p_X(x)\cdot p_Y(y)$$.

# Distribution Functions
For every propability density $$p_X$$ there is the corresponding
distribution function $$F_X(z)=P(X\leq z)=\int_{-\infty}^z p_X(x)dx$$.

# Expected Value
The expected value is the value which you would expect "on average",
whatever that means exactly. It is defined as
$$\mathbb{E}(X)=\int_{-\infty}^\infty xp_X(x)dx$$ if it exists.  
This even makes sense intuitively, as we weight the events with their
corresponding probabilities and sum them up. Note that this thing does
not have to exist, since it could be possible that the integral does
not converge.

Sometimes it is also called the mean and denoted with $$\mu$$.

Fortunately it is linear, i.e.,
$$\mathbb{E}(aX+bY+c)=a\mathbb{E}(X)+b\mathbb{E}(Y)+c$$ for $$X$$,
$$Y$$ random variables and $$a$$, $$b$$, $$c$$ constants,
even when $$X$$ and $$Y$$ are not independent.

# Variance
Variance is a measure for deviation from the expected value. Much like standard
deviation. In fact it is the squared standard deviation.
$$Var(X)=\mathbb{E}((X-\mathbb{E}(X))^2)$$, and the positive
squareroot $$\sigma=\sqrt{Var(X)}$$ is called the standard deviation.
Note that you need the squares inside the computation of the variance,
since
$$\mathbb{E}(X-\mathbb{E}(X))=0$$.

You might find it useful to know that:

- $$Var(X+a)=Var(X)$$ for a random variable $$X$$ and a parameter
  $$a$$.
- $$Var(aX)=a^2 Var(X)$$ with $$X$$, $$a$$ as above.
- $$Var(X+Y)=Var(X)+Var(Y)$$ for _uncorrelated_ random variables $$X$$
  and $$Y$$. (see below for a explanation of uncorrelatedness)

# Covariance
Covariance measures how two random variables $$X$$ and $$Y$$ change
together.
$$Covar(X,Y)=\mathbb{E}((X-\mathbb{E}(X))(Y-\mathbb{E}(Y)))
=\mathbb{E}(XY)-\mathbb{E}(X)\mathbb{E}(Y)$$, where the last part
follows by linearity of the expected value and is a good exercise for the interested reader.  
Two random variables $$X$$ and $$Y$$ are called uncorrelated if
$$Covar(X,Y)=0$$.  
By the way, variance can be explained in terms of covariance as
$$Var(X)=Covar(X,X)$$.

# Normal Distribution
I should probably make a warning sign here, since there is a big
formula ahead. Normal distribution is just a special kind of
distribution. We say that a random variable $$X$$ has a normal distribution
with mean $$0$$ and variance $$1$$, if for the probability density it
holds that
$$
\begin{align*}
p_X(x)=\frac{1}{\sqrt{2\pi}}e^{-\frac{x^2}{2}}
\end{align*}
$$
for all $$x\in \mathbb{R}$$.  
One then often writes things like $$X\sim\mathcal{N}(0,1)$$ to denote
this special relationship.

Damn, why is this monster called the normal distribution? What on
earth is normal about that? It will become clear once I explain the
central limit theorem. But before doing this I want to show to you an
even bigger ugly formula, namely the normal distribution with mean
$$\mu$$ and variance $$\sigma^2$$.
For a random variable $$X$$ we write
$$X\sim\mathcal{N}(\mu,\sigma^2)$$ and say that $$X$$ has a normal
distribution with mean $$\mu$$ and variance $$\sigma$$ if
$$
\begin{align*}
p_X(x)=\frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{(x-\mu)^2}{2\sigma^2}}
\end{align*}
$$.

To get more familiar with this stuff, I urge you to do the following exercises:

- Show that a random variable $$X$$ with $$X\sim\mathcal{N}(0,1)$$ has mean $$0$$ and variance $$1$$. What terms do you have to compute to show this?
- Suppose again $$X\sim \mathcal{N}(0,1)$$. Let $$Y=\sigma X+\mu$$ be another random variable. Show that $$Y\sim\mathcal{N}(\mu,\sigma^2)$$.

# Central Limit Theorem
So, what is normal about the normal distribution?
Suppose we have random variables $$Y_1$$, $$Y_2$$,... , which are
independent and identically distributed with mean $$0$$ and variance
$$\sigma^2$$.
Now we can of course add finite subsets of them up. We then scale it,
so the terms do not grow infinitely.
$$\frac{1}{n}\sum_{i=1}^n Y_i$$
We now scale it with $$\frac{1}{\sqrt{n}}$$ and let $$n$$ grow to
infinity. Then we get a normal distribution.
I.e.
$$
\begin{align*}
\lim_{n\rightarrow\infty}\frac{1}{\sqrt{n}}(\frac{1}{n}\sum_{i=1}^n Y_i)\sim\mathcal{N}(0,\sigma^2)
\end{align*}
$$
Note that this is independent of the distributions of the $$Y_i$$. We
only demanded them to be equal.

# The End
Congratulations, you finally made it to the end. I suppose not many
people make it this far. I hope you enjoyed the journey and learned
something along the way.
