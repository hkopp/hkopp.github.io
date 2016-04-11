---
layout: post
category : Lesson
tags : [math, stochastic, numerical analysis, python, derivative pricing]
---
{% include math %}

##Introduction
In this post I am going to explain option pricing via
[Euler-Maruyama]({% post_url 2015-07-14-approximating-sdes-with-euler-maruyama %}).
As discussed [before]({% post_url 2015-01-04-put-call-parity-for-european-options %})
we only need to be able to price call options to get the price of put
options.

In [another post before]({% post_url 2015-07-04-binomial-options-pricing %})
we used a binomial tree for option pricing. This time we will
basically do the same, but start from an opposite direction.

Assume the price of the underlying $$S(t)$$ is described by the
stochastical differential equation of geometric brownian motion:
$$
\begin{align*}
dS(t)=\mu S(t)dt + \sigma S(t) dB(t)
\end{align*}
$$
where $$B(t)$$ is
[brownian motion]({% post_url 2015-03-25-martingales %}).
The variable $$\mu$$ corresponds to drift and $$\sigma$$ behaves like
volatility.
We also need a known $$S(0)$$.

This is slightly more general than in
[the post before]({% post_url 2015-07-04-binomial-options-pricing %}).

We can now simulate some paths of the underlying on the time interval
up to expiry via Euler-Maruyama and compute the expected payoff as the
mean, if it exists.

##Coding
To be more precise we want to approximate the SDE on the interval
$$[0,T]$$. The strike price is denoted with $$E$$.
If we discretize the SDE $$dS(t)=\mu S(t)dt + \sigma S(t) dB(t)$$ we
get an approximation with
$$
\begin{align*}
Y(0)=S(0) \text{ and }
Y(t_i)=Y(t_{i-1})+Y(t_{i-1})\cdot\mu\cdot\Delta t + Y(t_{i-1})\cdot \sigma\cdot\Delta B_i
\end{align*}
$$
where $$\Delta B_i$$ are i.i.d and normally distributed with mean
$$0$$ and variance $$\Delta t$$.
So let us write this in slow Python code.

{% highlight python %}

import numpy as np
import random
 
#Parameters
tbegin = 0
tend = 1
deltat = .0001
t = np.arange(tbegin, tend, deltat)

E=1.0

sigma = 0.8
mu = 0.4
S0 = 1

sqrtdt = np.sqrt(deltat)


N=np.zeros(10000) #do 10000 simulations
for i in xrange(0,N.size):
  y = S0
  for j in xrange(1, t.size):
      y = y + y * mu * deltat + y * sigma * random.gauss(0, np.sqrt(deltat))
  N[i]=y

N=np.maximum(0,N-E) #payoff for call option
print(N.mean())
{% endhighlight %}

##Outlook
If you are clever or if you can use google, then you know, that the
price of european options can also be computed explicitly. Yes, that
is right, but that was not the point of my post. The point of my post
was to show an easy example of how to price derivatives.  
According to the same method you can price any kind of exotic option,
where the payoff is not only dependant on the price of the asset at
expiration but on other stuff. Or you can price options with multiple
possible expiration times, like American or Bermudan options.

You may also argue that we do not know all those parameters like the
volatility and mean of the underlying. Yes, that is also right, but we
can guess them, for example via maximum-likelihood estimation, if we
have historical data of the asset.

Another interesting argument would be, that normal assets are unlikely
to follow geometric brownian motion. That is also right.
Distributions of returns have fat tails. But in the code above you can
plug in any stochastic process, instead of the geometric brownian
motion we used. You can also use a stochastic volatility model, i.e.,
a model, where the volatility itself is another stochastical process.
I, as an arrogant mathematician would say that this is an easy
exercise. If you do not trust me, try it yourself.
