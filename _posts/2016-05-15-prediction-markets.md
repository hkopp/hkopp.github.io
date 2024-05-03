---
layout: post
category: Quantitative Finance
tags: [trading]
---

## Introduction
My goal for this article is to explain prediction markets and what can
be done with them.

## Example
Suppose we want to know if Ron Paul will become the next president.
The traditional way to gain this knowledge would be to conduct surveys.
Since this is boring i want to explain another approach.    
Suppose we have contracts that grant the holder of that contract one
dollar if Ron Paul becomes president. Otherwise the contract is
naught.    
How much would you pay for such a contract?
The fair price of the contract is clearly the expectation that Ron
Paul becomes president. Suppose he becomes president with 20%
probability. Then with 20% the contract is worth one dollar, otherwise
zero. Thus the risk-neutral price is 0.2 dollars.    
The problem is that we do not know the probability in advance so
prediction markets work the other way around. We simply issue the
contract and let the price be influenced by demand and supply. If you
think the contract is too cheap and the fair price should be higher,
you can buy some contracts and make money if you were correct. If the
price then settles at 0.3 dollars, we can interpret this as the
probability of Ron Paul becoming president.

## Theory
Of course this not only works with presidency, but with any binary
event that eventually "expires". For example you could bet on the
weather tomorrow. By issuing multiple contracts, we can also bet on
discrete (non-binary) events.
But where does the money come from? Well, insiders are better informed
than the general public and thus they can make money with their
information. But by providing this information through buying or
selling such a contract, it becomes public in the price of the
contract. So the money gained is simply the price which was included
in that released information.

## Problems
Why aren't we all using prediction markets?
One problem is that often we have a lack of liquidity. Then the spread
becomes too large to make accurate predictions. For example, what does
it tell you, if you can buy a contract on an event at 0.6$ and sell it
at 0.3$? It only gives you an interval for the probability of that
event occuring.

Another issue occurs when people can issue their own bets. For example
they could bet on the date of the death of famous persons. This is
similar to how assassination markets work. 
In an assassination market one bets on the date of the death and if it
does not happen, the money spent is simply collected in a pot.
However, if one is correct about the date of death, then one receives
the pot.
This provides an incentive to murder the person in question, after
betting on the correct date of death which the murder knows, and
thereby collect the pot.
So, prediction markets in the worst case can degenerate to crowdfunded
murder by providing financial incentives to change the outcome of the
event underlying the bet.
