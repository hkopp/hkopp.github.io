---
layout: post
category : Brainteaser
tagline: "a quiz with coins"
tags : [brainteaser, easy, stochastic]
---
{% include math %}

#Intro
I have found this brainteaser in Max Damas "Automated trading", but
without a solution. I think my solution should be correct.

##Problem
You have 5 quarters on the table in front of you. Four of them are
fair, i.e., one side is heads the other tails, and one coin has heads
on both sides.  
You pick one at random and flip it five times. Each time you get
heads.
What is the probability, that you picked up the coin with two heads?

I suggest you try this for yourself before reading on.

##Solution
There is a thing called Bayes' theorem, which makes this seemingly
difficult brainteaser a simple exercise, as we mathematicians like to
call it.  
Bayes' theorem is about relating conditional probabilities. Let
$$P(A)$$ be the probability of the event $$A$$ and $$P(A|B)$$ the
probability of the event $$A$$ given the event $$B$$.  
Then Bayes' says that
$$
\begin{align*}
P(A|B)=\frac{P(B|A)P(A)}{P(B)}
\end{align*}
$$

So we just need to define the events accordingly, plug in the values
and whoosh, we are finished.
So, let $$HH$$ be the event, that we picked the coin with two heads,
$$5H$$ the event, that we got five heads.
The question is now, what is $$P(HH|5H)$$?

According to Bayes:
$$
\begin{align*}
P(HH|5H)=\frac{P(5H|HH)P(HH)}{P(5H)}
\end{align*}
$$

Obviously $$P(5H|HH)=1$$, since we can only get heads from the coin
with two heads.
$$P(HH)=1/5$$, since we have four fair coins and one head-head-coin.

So it remains to compute $$P(5H)$$. This is $$4/5\cdot1/2^5$$ for
the fair coins and $$1/5\cdot 1$$ for the rigged coin. Summing up
yields
$$
\begin{align*}
P(5H)=1/2^5\cdot 4/5+1/5\cdot 1=0.025+0.2=0.225
\end{align*}
$$

All in all
$$
\begin{align*}
P(HH|5H)=\frac{P(5H|HH)P(HH)}{P(5H)}\approx 0.889
\end{align*}
$$

