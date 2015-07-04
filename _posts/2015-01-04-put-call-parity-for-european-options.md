---
layout: post
category : lesson
tags : [trading, elementary, math, derivative pricing]
---
{% include math %}

##Introduction
In this post I am going to explain options and derive the put-call-parity for european options.

First of all, please imagine some en-dashes between "put" and "call", and "call" and "parity" in the title.
Unfortunately someone who wrote Jekyll decided to use hyphens as word separator in filenames for the titles of the blog post, so I am unable to insert any hyphens in titles of blog posts and I do not have fun when debugging such things. So you have to imagine some hyphens.

A (european) _call_ _option_ $$C$$ is a contract entered at time $$0$$, where the buyer has the option but not the obligation to buy at a future time $$T$$ an asset at a price $$E$$, called _strike_ _price_, which is known at time $$0$$.

A (european) _put_ _option_ $$P$$ is basically the same, but guarantees the right to sell assets at a predetermined price.

There are also american options. They also come in the two flavors _put_ and _call_ options.
The difference between european options and american options is, that european options can only be exercised at a predefined date $$T$$ in the future, whereas american options can always be exercised.
There are also hybrid types of options, which can only be exercised at many predetermined times.
In the following we will focus on european options.

By abuse of notation, we will call $$S(t)$$ the price of the underlying asset at time $$t$$.
$$C(t)$$ and $$P(t)$$ are the prices of the call, respectively the put option at time $$t$$.

What should now be a fair price for such an option?
Assume, that we buy a call option $$C$$ at time $$0$$ for $$C(0)$$.
Then at time $$T$$ two things could happen.

- If $$S(T)>E$$ we can buy the asset at price $$E$$ and then directly sell it at the greater price $$S(T)$$. So we effectively gain $$S(T)-C(0)$$.
- If $$S(T)\leq E$$ we will not exercise our option, since the asset is traded cheaper than $$E$$.

This means, that our profit is $$\max\{ 0, S(T)-E\}$$.

An analogous observation for put options is left as a small exercise.

##Put-call-parity
Now we want to establish a relationship between the price of a put option $$P$$ and a call option $$C$$.
Suppose we have two portfolios at time $$0$$.

1. The first one consists of $$E\cdot e^{-rT}$$ and $$C$$. Here $$e$$ is Euler's number. We need this stuff to account for interest rates over the time from $$0$$ up to the exercise time $$T$$.
2. The second portfolio consists of a put option $$P$$ and the asset $$S$$, which was bough at $$S(0)$$.

Then at time $$T$$, the first portfolio consists of $$E$$, because of the interest rate and $$\max\{ 0, S(T)-E\}$$ as explained above. This equals $$\max\{ E, S(T)\}$$.  
At time $$T$$ the second portfolio consists of $$\max\{ E-S(T),0\}$$ by the exercise above and $$S(T)$$. All in all, this amounts to $$\max\{ E, S(T)\}$$.

So the two portfolios yield the same value at time $$T$$. Since we did not trade anything in between they must have had the same value all the time, so
\begin{equation}
C+E\cdot e^{-rT} = P+S
\end{equation}
And this is the put-call-parity for european options mentioned in the title.

##Open Issues
We now know how to price a put option if we have the value of the call option and the other way around.
But we do not know either of them.
What we gained is that in further discussions of the price of options we can restrict ourselves w.l.o.g. to one type of option.
(That's "without loss of generality" if you are not into math geekspeak.)
If you are interested in the real price of an options you should learn some stochastic calculus and stochastic differential equations and then google the Black-Scholes model.
