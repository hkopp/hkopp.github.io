---
layout: post
category: Recreational Math
tags: [brainteaser, stochastic, trading, math]
---
{% include math %}

# Intro
In this post I am going to talk a bit about Kelly betting, since today
I was flabbergasted that I did not write anything about it yet. Kelly
betting is a strategy how to allocate money to bets.

## Problem
Assume we are playing a game. I am going to flip a coin. You can bet
an amount $$x$$$ on the result of the coinflip. If you are right, you
win $$x$$$. If you are wrong, you lose your $$x$$$. Let us say that
you know that my coin is nonrandom and lands on head with probability
$$p>0.5$$. To further ease the problem assume that you are always
betting the same fraction $$f$$ of your capital. Now, how should you
choose $$f$$ to get returns bigger than any other strategy? Another
way of formulating the problem would be to maximize the expected
return.

Get out and try it before reading on. And I hope you will come back
with the right solution or at least utterly frustrated and nearly
crying.

## Solution
The expected return is $$(1+f)$$ each time we win and $$(1-f)$$ each
time we lose. Now putting those nasty probabilities in we get a return
of $$(1+f)^p+(1-f)^{1-p}$$. We need to find the maximal value of that
expression. To make things easier we can also find the maximal value
of the logarithm of that expression

$$
\begin{align*}
\log ((1+f)^p+(1-f)^{1-p})=
p\log(1+f) + (1-p) \log(1-f)
\end{align*}
$$

since the logarithm is increasing monotonically. So we have to set teh
derivative to zero. Observe that

$$
\begin{align*}
\frac{\partial}{\partial f} p\log(1+f) + (1-p) \log(1-f) &= 0\\
\Leftrightarrow \frac{p}{1+f}+\frac{p-1}{1-f} &= 0\\
\Leftrightarrow \frac{p}{1+f} &= \frac{1-p}{1-f}\\
\Leftrightarrow p(1-f) &= (1-p)(1+f)\\
\Leftrightarrow f &= 2p-1
\end{align*}
$$

You can plug some sensible values for $$p$$ in it to check
correctness.

Now, let us generalize this problem because of mathematical elitism.
Let us say if you win with probability $$p$$, you win a proportion
$$a$$ of your initial bet and if you loose, then you win a proportion
$$b$$, where $$b$$ is possibly negative.
So our returns are $$(1+fa)^p+(1+fb)^{1-p}$$. Note that this is
essentially the same as before if we take $$a=1$$ and $$b=-1$$.
Now let us again maximize this, more exactly let us maximize the
logarithm. So we again set the derivative to zero.

$$
\begin{align*}
\frac{\partial}{\partial f} \log[(1+fa)^p+(1+fb)^{1-p}]\\
= \frac{\partial}{\partial f} p\log(1+fa)+(1-p)\log(1+fb)\\
= \frac{pa}{1+fa}+\frac{(1-p)b}{(1+fb)}
\end{align*}
$$

This is equal to zero if and only if

$$
\begin{align*}
\frac{pa}{1+fa}+\frac{(1-p)b}{(1+fb)} &= 0\\
\Leftrightarrow pa+pafb+(1-p)b+(1-p)bfa &= 0\\
\Leftrightarrow pa+(1-p)b+bfa &= 0\\
\Leftrightarrow fab=-pa+(p-1)b &= 0\\
\Leftrightarrow f=\frac{-pa+(p-1)b}{ab} &= 0
\end{align*}
$$

If we would now plug in the values for our first exercise $$a=1$$ and
$$b=-1$$, we would get the same result.
