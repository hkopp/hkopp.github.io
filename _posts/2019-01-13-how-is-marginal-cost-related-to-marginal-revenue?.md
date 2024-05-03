---
layout: post
category: Quantitative Finance
tags: [economics, theory, easy, rant]
---
{% include math %}

## Introduction
I have often heard that marginal cost should equal marginal revenue,
but never really understood why. The posts of economists on the
internet treat this as an axiom and often abbreviate it to $$MC=MR$$.
Well, this did not help me either. Until I thought really hard about
it and now I have to write this down, before I forget it again.

## The Setting
Let's say we produce and sell apples. Then we have some profit $$P$$
per apple, which depends on the quantity $$q$$ of sold apples. So we can regard
this as a function $$P(q)$$.

The profit can be divided into two parts, the revenue $$R(q)$$ per
sold apple and the cost $$C(q)$$ per sold apple. The revenue is simply
the price we get for one apple if we sell $$q$$ of them. $$C(q)$$ is
the cost per apple of producing and selling $$q$$ apples. Then
$$P(q)=R(q)-C(q)$$.

Now, you may think that the profit per single apple $$R(q)$$ does not
depend on the number $$q$$ of sold apples.
Well, that is not really the case.
Actually, if you sell enough apples, you will find out that people
are fed up with apples and stop buying them. Thus, the revenue per
apple decreases when more apples are sold.
Likewise, the cost $$C(q)$$ per sold apple changes, due to cheaper shipping
and packaging of large quantities of apples.

## Playing around with the formulas

Actually, since we are in a capitalist society we want to maximize our
profit. Finding a maximum of something can be done by setting the
derivative to zero.

$$
\begin{equation}
\frac{dP(q)}{dq}=\frac{dR(q)}{dq}-\frac{dC(q)}{dq}\stackrel{!}{=}0
\end{equation}
$$

But this is the case only if $$\frac{dR(q)}{dq}=\frac{dC(q)}{dq}$$.
Economists call the left side marginal revenue and the right side
marginal cost. And this is where $$MC=MR$$ comes from. It is simply a
result of maximizing the profit.
