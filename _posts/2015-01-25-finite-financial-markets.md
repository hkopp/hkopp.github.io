---
layout: post
category : Lesson
tags : [beginner, math, trading, stochastic]
---
{% include math %}

##Introduction
In this post I am going to explain how finite financial markets are modelled in math.

##Notation
Throughout the remainder of this post, let $$\Omega$$ be a finite probability space with measure $$\mu$$.
We also have a filtration $$\mathcal{F}=\{\mathcal{F}_t\}_{t=0,...,T}$$, where $$T\in\mathbb{N}$$ is fixed.
That means that the $$\mathcal{F}_t$$ are $$\sigma$$-algebras on $$\Omega$$, where $$\mathcal{F}_t\subset \mathcal{F}_{t+1}$$.
Alternatively the partition induced by the $$\sigma$$-algebras on $$\Omega$$ gets finer as $$t$$ increases.
Intuitively this means that more information is available as the time $$t$$ increases.
We furthermore let $$\mathcal{F}_0=\{\emptyset,\Omega\}$$, i.e., at the beginning we have no information, and $$\mathcal{F}_T=\mathcal{P}(\Omega)$$ the powerset of $$\Omega$$.

##Markets
In this setting a finite financial market is a $$\mathbb{R}^{d+1}$$-valued $$\mathcal{F}$$-adapted stochastic process $$S=\{S(t)\}_{t=0,...,T}=\{(S_0(t),S_1(t),...,S_d(t))\}_{t=0,...,T}$$ such that $$S_0(0)=1$$ and $$S_0(t)>1$$ for $$t=1,...,T$$.  
What do all those complicated words mean?

* stochastic process means, that each vector $$S(t)$$ is a random variable $$S(t):\Omega\rightarrow\mathbb{R}^{d+1}$$.
* $$\mathcal{F}$$-adapted means that each random variable $$S(t)$$ is $$\mathcal{F}_t$$-measurable.

That special stuff about $$S_0$$ is because we want to have a point of reference if we have portfolios where we hold a linear combination of the $$S_i$$ and want to compare them.

##Strats
We now have modelled a market with different assets $$S_i$$.
Now we want to model some trading strategies.

We say that a stochastic process $$\phi=\{\phi(t)\}_{t=1,...,T}$$ is predictable, if $$\phi(t)$$ is $$\mathcal{F}_{t-1}$$-measurable for all $$t=1,...,T$$.
That means intuitively, that $$\phi(t)$$ is known at time $$t-1$$, but not before.
This is how we want to allocate our cash.

A trading strategy is now a $$\mathbb{R}^{d+1}$$-valued predictable stochastic process
$$
\begin{align*}
\phi=\{\phi(t)\}_{t=0,...,T}=\{(\phi_0(t),...,\phi_d(t))\}_{t=0,...,T}
\end{align*}
$$.
Now $$\phi_i$$ represents how much of the asset $$S_i$$ we want to hold between time $$t-1$$ and $$t$$.
The filtration of $$\phi$$ is shifted, because we can decide how much of $$S_i$$ we want to hold between time $$t-1$$ and $$t$$ only based on the knowledge at time up to $$t-1$$.
We cannot see into the future.

We now want that no additional money comes out of nowhere.
That means that
$$
\begin{align*}
\langle \phi(t), S(t)\rangle=\langle \phi(t+1), S(t)\rangle
\end{align*}
$$.
If this is the case, then we call the strategy $$\phi$$ self-financing.
Note that the strange brackets denote the dot-product, i.e.,
$$
\begin{align*}
\langle \phi(t), S(t)\rangle=\sum_{i=0}^d \phi_i(t)\cdot S_i(t)
\end{align*}
$$.
Also the stochastic process $$X$$ defined by $$X(t)= \langle \phi(t), S(t)\rangle$$ denotes the wealth process, where $$X(0)=\langle \phi(1),S(0)\rangle$$ is the seed capital.
This directly models our wealth at each point $$t$$ in time.

We can also define the wealth by the sum of the gains.
$$
\begin{align*}
X(t)=\langle \phi(t), S(t)\rangle=\sum_{\tau=1}^t \langle \phi(\tau), \Delta S(\tau)\rangle=\sum_{\tau=1}^t \langle \phi(\tau), S(\tau)-S(\tau -1)\rangle
\end{align*}
$$.
In case you did not guess it already, that last part is a discrete stochastic integral.
Meditate on this if you never saw a stochastic integral before.
