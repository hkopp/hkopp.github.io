---
layout: post
category : lesson
tags : [derivative pricing]
---
{% include math %}

##Introduction
In some of the last posts, we talked about how to price different
derivatives. But we had no unifying theme. In this post I am going to
describe the three general categories of how to price derivatives.
Remember, derivatives are assets whose value is dependent on the value
of another asset, such as an option or a future.

##Hedging
When hedging, we build a risk neutral portfolio. That
means we buy other assets, such that the risks cancel.  
Suppose we play the following game: I have a coin and if it shows
heads, you win 1$. If it shows tails, you get nothing in return. How
much would you pay to play this game?
In this case, the coin itself is the asset, and the return of our game
is the derivative.
When hedging, assume there is another possible game. Assume there is
the game where you win 1$ on tails and win nothing on heads. Obviously
this should have the same price as the previous game.  
But now, if you play both games, you get 1$ and this return does not
depend on which side the coin landed. So it is a riskless asset.
So both games together are worth 1$. But since each game is worth the
same, one game is worth half a dollar.    
That is how hedging works.

##Risk-neutral valuation
In risk-neutral valuation you disregard the risks and only look at the
expected value.  
For example in our coin-flip above, one game has a return expectation of
half a dollar. So it is worth half a dollar.
This seems like an obvious way of pricing instruments, but let us take
another example. Suppose I take a deck of cards, give it a good
shuffle, take one card out and if it is the ace of spades, you win 26
$. On all other cards, you gain nothing. For simplicity, let us assume
we have no jokers and it is a regular deck of cards, so it has 52
different cards in it. Then the expected value of your return is the
same. But the risk is higher.  
We can also throw a dice with 100 sides and if it lands on 5, you get
50$. On all other sides, you get nothing. The expected return is the
same, but again, risk has increased. So normally you expect to be
compensated for this additional risk. When you try to price assets in a
risk-neutral way, you are not compensated for risk.
So, now I may have convinced you that just looking at the expected
value is not a trivial and obvious wya to price assets.
But then, why does it work? There is a thing called the fundamental
theorem of asset pricing, which roughly says that in an arbitrage-free
market this works.

##Replication
Replication works by composing the derivative by "smaller" assets
which are easier to price. Let us take as example a forward contract
that I will next year sell 1000$ for euros at the exchange rate next
year. How much should this contract be worth? Well, I lose 1000$, so I
can instead sell a zero-coupon bond over 1000$ which expires next
year. I can proceed to exchange the amount I have gained in euros and
buy another zero-coupon bond which grants me euros with all the money
I have just made by selling the 1000$-bond.
Then my account is at zero again and next year, I lose
1000$ dollars and get some euros. So the effect is the same
and therefore the forward-agreement should be worth the price of the
euro-bond minus the price of the 1000$-bond.
You can do this more rigorously by computing the forward exchange
rate, if you want to.
The idea of replication is, that if there is a mispricing between the
current price of the derivative and the price of some replication
thereof which behaves in exactly the same way, then some clever
arbitrageurs will take care of it and the prices will again converge.
