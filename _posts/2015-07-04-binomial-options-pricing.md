---
layout: post
category : lesson
tags : [derivative pricing, python, stochastic, math, not that easy]
---
{% include math %}

##Introduction
In this post, I am going to explain how one can price options via
binomial trees.
As you perhaps know, a (european) _call_ _option_ $$C$$ grants you the
right to buy at a future time $$T$$ an _underlying_ $$S$$ at a price
$$E$$, called _strike_ _price_, which is known at time $$0$$.
I have already written
[something about options]({% post_url 2015-01-04-put-call-parity-for-european-options %})
before.
It is not necessary to read it, you just have to know what an option
is. Also it would be good to know, that via the put-call-parity for
european options, it is possible to price _put_ options, assuming we know
the value of the according _call_ option.
Well, that means that we can restrict ourselves in the following to
the pricing of call options, since with the put-call-parity, the price
of put options follows trivially.

##Binomial trees
The Cox-Ross-Rubinstein model, or more simply, the binomial options
pricing model assumes that we can model the price of the underlying
$$S$$ with a binomial tree.
Normally the underlying would be something like a stock.
Assume that the price of the underlying currently is $$S$$. Then in a
next step it can either go up by a factor of $$e^\sigma$$, or it can
go down by $$e^{-\sigma}$$. In other explanations you will often see
an increase by some constant $$\Delta$$. This sucks, since then you
have to take care that the price never drops below zero. For those of
you in the know, I am using geometric Brownian motion instead of
"regular" Brownian motion.
In ASCII, that would be akin to the following:

      S*e^sigma
     /
    S
     \
      S*e^(-sigma)


Now, why not simply take more branches in the tree? Let
$$u=e^{\sigma\sqrt{\Delta t}}$$, where $$\Delta t$$ is a "sufficiently
small" time difference and $$d=1/u$$ to make the notation
easier. Then we can have an even bigger tree.

      S*u^2
       /
      S*u
     /  \
    S    S
     \  /
      S*d
       \
      S*d^2

and so on. I think you can imagine the principle behind the tree.

##Options pricing
Now, what does this have to do with options pricing? Well, each of the
paths in the tree correnspond to a possible development of the
underlying. And we know that we can model the payoff at expiry time
$$T$$ of a call option as $$\max\{ 0, S(T)-E\}$$, where $$S(T)$$ is
the price of the underlying at time $$T$$ and $$E$$ is the strike
price. In the regular Cox-Ross-Rubinstein model you would now look at
all paths and then go backwards.
We are doing a more sloppy approach.
We can simply observe enough possible developments of the
underlying, via picking enough paths in the binomial tree. Now for
each possible development of the underlying we have the according
price of the option. That means the mean of the option at the possible
outcomes converges to the "fair price" of the option. Note that the
price of the option has to exist in the first place.

Why does this work? Because we simulate the expected payoff of the option.
At least nearly. In a real-life scenario you would have to take care
of the risk free interest rate, but for now we leave this out of the
equation.
Computing stuff by hand is difficult, so I have written a simulation
in Python which computes the price of a european call option.

{% highlight python %}

import numpy as np
import random
 
#Parameters
tbegin = 0
tend = 1
deltat = .0001
t = np.arange(tbegin, tend, deltat)

E=1.0

sigma = 0.3
S0 = 1

sqrtdt = np.sqrt(deltat)

N=np.zeros(10000) #do 10000 simulations
for i in xrange(0,N.size):
  y = S0
  for j in xrange(1, t.size):
      y = y * np.exp(random.choice([-1,1]) * sigma * sqrtdt)
  N[i]=y

N=np.maximum(0,N-E) #payoff for call option
print(N.mean())
{% endhighlight %}

If you want to see the answer to the option price, then you need to have
python installed. And run my code. Learning experience is maximized if
you hack around with it and try different parameters.

Hope you had fun.
