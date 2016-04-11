---
layout: post
category : Lesson
tags : [stochastic, not that easy]
---
{% include math %}

#Introduction

Suppose you play a game. We model the outcome of the game with the
random variable $$X$$ where the probability of winning or losing is
$$p_X(1)=p_X(-1)=1/2$$.
So it is a fair game and $$\mathbb{E}(X)=0$$.  
Suppose now that we play multiple times. We denote by $$X_i$$ the
outcome of the $$i$$th game. Suppose we bet a piece of gold each time we
play. So after the first round we have $$X_1$$ goldpieces. After the
second one we would have $$X_1+X_2$$.  
More generally, after the $$t$$-th round we would have
$$
\begin{align*}
Y_t=\sum_{i=1}^t X_i
\end{align*}
$$
pieces of gold.  
The expected value of $$Y_i$$ in the $$(i-1)$$th round, i.e., after we
know $$Y_1$$, ..., $$Y_{i-1}$$ would be $$Y_{i-1}$$.
Since if we lose, we would have $$Y_{i-1}-1$$ and if we would win, we
would have $$Y_{i-1}+1$$.

In this example, the family of random variables $$Y_i$$ is called a
simple random walk.
This motivates the following more general definition.

##Definition
A family of random variables $$Y_0$$, $$Y_1$$, ... is called a
_discrete_ _martingale_ if
$$\mathbb{E}(Y_i)<\infty$$ for all $$i$$
and for the conditional expected value it holds that
$$\mathbb{E}(Y_{n+1}|Y_0,\dots,Y_n)=Y_n$$.

Note that the first assumption basically says that the expected value does exist.

##Towards continuity
So what is a continuous martingale?
Let us try to generalize our random walk. Let $$Y_i$$ be as before a
discrete random walk. Now define 
$$B_{j/N}=\frac{1}{\sqrt{N}} Y_j$$.  
We need a scaling factor, such that it works. This has to do with
bounding the variance and i do not want to explain it.
Note that if we let $$N\rightarrow\infty$$ then this stuff converges
to something which i will denote by $$B_t$$. It is also called
Brownian motion, therefore the symbol $$B$$.
Please also note that $$t\mapsto B_t$$ is almost surely a continuous function.
Furthermore because of the scaling we have that
$$B_\lambda-B_\mu \sim\mathcal{N}(0,\lambda-\mu)$$ is normally
distributed with mean $$0$$ and variance $$\lambda-\mu$$, the
differences between any two increments of Brownian motion are
independent and $$B_0=0$$.

##Definition
Let $$Y_i$$, $$i\geq 0$$ be a family of random variables,
$$\mathcal{F}_t=\{Y_i,\,0\leq i\leq t\}$$ a _filtration_.
Then $$Y_i$$, $$i\geq 0$$ is called a _martingale_ if
$$\mathbb{E}(Y_i)<\infty$$ for every $$i\leq 0$$
and furthermore $$\mathbb{E}(Y_i|\mathcal{F}_t)=Y_t$$ for every $$i>t$$.

##Integrals
Let us integrate this Brownian motion stuff. I mean, integrate as in
Riemann integration. You know, we just take a discrete integral of the
random walk and then do this limit stuff as explained above and then
get an integral of Brownian motion.  
Since Brownian motion is a stochastic process, the integral will also
be a random variable. Therefore we can characterize it by computing
its distribution. Obviously
$$I=\lim_{N\rightarrow\infty}\frac{1}{N}\sum_{i=1}^N B_{i/N}$$ should
be the integral $$I=\int_0^1 B_t dt$$, if it exists and if the last
part of that notation even makes sense. We have to hack around a bit, since only
Brownian increments are independent.  
$$
\begin{align*}
\sum_{i=1}^N Y_i=NX_1+(N-1)X_2+...+X_N=\sum_{i=1}^N
(N-i+1)X_i\sim\mathcal{N}\left(0,\frac{1}{N}\sum_{i=1}^N (N-i+1)^2\right)
\end{align*}$$  
Where the last part follows, since the $$X_i$$ are independent.
Moreover $$\frac{1}{N}\sum_{i=1}^N (N-i+1)^2=\frac{(N+1)(2N+1)}{6}$$
as one can show by induction or a search in your favorite collection
of mystic formulae.  
$$
\begin{align*}
I_n=\frac{1}{N}\sum_{i=1}^N
B_{i/N}\sim\mathcal{N}\left(0,\frac{(N+1)(2N+1)}{6N^2}\right)=\mathcal{N}\left(0,\frac{1}{3}+\frac{1}{2N}+\frac{1}{6N^2}\right)
\end{align*}$$  
Letting $$N\rightarrow\infty$$ we get $$\int_0^1 B_t
dt=I\sim\mathcal{N}(0,\frac{1}{3})$$.  
We just integrated Brownian motion, bitches.
