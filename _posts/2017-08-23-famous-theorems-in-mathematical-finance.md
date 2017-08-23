---
layout: post
category : Lesson
tags : [derivative pricing, stochastic, math, theory]
---
{% include math %}

## Introduction
In this post, I want to explain the intuition behind some famous
theorems in mathematical finance.
I will not explain any proofs, since you can find these in
books, but rather for what the theorems can be used.

## Girsanov's theorem
Let us start with Girsanov's theorem. We need a Brownian motion
$$W_t$$ under measure $$\mathbb{P}$$, and a "sufficiently reasonable"
function $$v$$. Then there is an equivalent measure $$\mathbb{Q}$$
such that $$\bar{W_t}=W_t-vt$$ is Brownian motion under
$$\mathbb{Q}$$.  In fact Girsanov's theorem is even more general,
since it gives us a way to compute $$\mathbb{Q}$$.

This theorem mostly comes in handy if you want to have Brownian
motion, but currently have Brownian motion with a drift.  E.g., you
want to model your stock prices as a Brownian motion, but care about
the interest rate. With Girsanov's theorem you just change the
measure and your drift vanishes.

## Fokker-Planck Equation
Also called the Kolmogorov Forward Equation. This one is pretty
straightforward but looks scary.
We have a stochastic process $$dX_t=\mu(X_t,t) dt+\sigma(X_t,t) dW_t$$
and are interested in the probability distribution at a future point
in time. Fokker-Planck says that for the probability distribution
$$p(x,t)$$ it holds that
$$
\begin{align*}
\frac{\partial}{\partial t}p(x,t)=
-\frac{\partial}{\partial x}(\mu(x,t)p(x,t))+
\frac{\partial^2}{\partial x^2}(D(x,t)p(x,t))
\end{align*}
$$
, where $$D(x,t)=\sigma^2(X_t,t)/2$$ is the diffusion.
Think of $$X_t$$ as the price of an asset. Then the Fokker-Planck
equation gives us the probability distribution of the price of the
asset at the future time $$t$$.

## Black-Scholes equation
The famous Black-Scholes equation is a partial differential equation
which describes the price of a financial derivative. The idea is that
we have a stock with according price $$S(t)$$ at time $$t$$ and a
derivative with price $$V(S_t)=V(S,t)$$. Depending on the boundary
condition, e.g., the price at expiry, this may be a put or call
option, or something totally different.
The idea is that we can hold $$V$$ and then buy and sell $$S$$ in such
a fashion that our account balance stays at zero. This is called
Delta-hedging. Thus we can describe the price of $$V$$, because of
"no-arbitrage".
The Black-Scholes formula is as follows:
$$
\begin{align*}
\frac{\partial V}{\partial t} + \frac{1}{2}\sigma^2 S^2
\frac{\partial^2 V}{\partial S^2} + rS\frac{\partial V}{\partial S} -
rV = 0
\end{align*}
$$

, where $$r$$ is the risk-free interest rate, $$\sigma$$ is standard
deviation and $$\mu$$ is drift.

Behold! This was at a time when martingales were not invented yet. 
Differential equations like the one above were used to price options.
Scholes and Merton, who also had something to do with this equation
received the Nobel Prize in economics 1997 for their work. Black
unfortunately died in 1995.

Today the Black-Scholes equation has many drawbacks and I nearly never
use it, since I like the martingale approach better. I consider the
Black-Scholes equation
like the Sex Pistols. They were very influential at their time and
defined a generation. But today I do not often listen to them. There
is better stuff out there.

## Martingale decomposition theorem
The differential equation approach to pricing derivatives relied on
having a portfolio containing the derivative, say $$M_t$$ and then
delta-hedging. In particular one could obtain a constructive hedging
strategy. When pricing with the martingale approach this is often not
the case. Martingales heavily rely on being risk-neutral.
This is where the Martingale representation theorem comes into play.
Assume we have some Brownian motion $$W_t$$ and a continuous
martingale $$M_t$$ both adapted to the same filtration. Then the
theorem says that there exists a predictable process $$\phi(t)$$ such
that
$$
\begin{align*}
dM_t=\phi(t)dW_t
\end{align*}
$$.
In an application $$W_t$$ is the price of the underlying, $$M_t$$ is
the price of the derivative and $$\phi(t)$$ is the hedging strategy.
Note that this is not a constructive theorem and only yields the
existence of such a hedging strategy.

Intuitively, all the information about the ups and downs of the
Brownian motion are included in the filtration. Since $$M$$ is adapted
regarding the same filtration as $$W$$, it is only natural that they
move together.

Another way I think of this theorem is that every martingale is just
scaled Brownian motion. And we know a lot of theorems about Brownian
motion. Maybe this will come in handy sometimes.

## Feynman-Kac formula
When I first read the description of the formula I was pretty aghast.
I did not know much about differential equations at the time and still
consider myself more of a pure mathematician.
You can look up the formula for yourself on
[wikipedia](https://en.wikipedia.org/wiki/Feynman%E2%80%93Kac_formula).
The setting of the formula is the Black-Scholes equation, with a
terminal condition. In theory this describes the price of the
financial derivative at expiry. Feynman-Kac says that we can also write the
solution as an expectation.

This is precisely the link between the differential equation approach
to derivative pricing using Black-Scholes, and the martingale
approach. In the last one, we look at the expectation of the price of
the derivative assuming the underlying evolves according to Brownian
motion.
Feynman-Kac says that the theoretical prices received from the two
approaches are the same.

## References
I had to look up some of this on wikipedia, as well as my current
favorite financial math book, "The Concepts and Practice of
Mathematical Finance" by Joshi.
