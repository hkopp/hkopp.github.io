---
layout: post
category : Lesson
tags : [trading, derivative pricing]
---
{% include math %}

## Introduction
In this post I am going to explain futures and talk about the spot
future parity which can be used to find the fair price of a future.
If you want to, you can first read about the
[put-call-parity of options]({% post_url 2015-01-04-put-call-parity-for-european-options %})
Perhaps it makes sense to read that post first, I do not know. Options
and Futures are similar, but Futures are easier, since they have a
linear payoff.

As in the post about the put-call parity of options, you have to
imagine en-dashes between "put" and "call" in the title, since I did
not find out how I can insert en-dashes in filenames in jekyll.

A _future_ $$F$$ is a contract entered at time $$0$$, where the buyer
has the obligation (not the option) to buy at a future time $$T$$ an
asset $$S$$ at the current price  which we will also denote by $$S$$.

You can also sell a future, then you have the duty to sell assets at
the price $$S$$ at time $$T$$.

The asset $$S$$ is also called the _underlying_.

The great thing about futures is that they lock in a fixed price.
Assume you think that the value of some company will rise in the near
future. So what can you do? You can buy futures on the stocks of the
company. When the future expires two possible scenarios can happen:

* The price of the stock rises and you have to buy it at the cheap old
  price. Then you can directly sell the stocks and make money like a
  boss.
* Or the stock can fall in value and you lose big. If you are clever
  you sell the futures to some other sucker and only lose the
  difference between the price at which you have bought the future and
  the price when you sell the future.

Note that the future should always mimic the price at the spot market,
that is the price of the underlying. In particular when the spot price
is rising, then the future price should also rise and vice versa.

Also note that futures are a zero-sum game. For every future you buy
there has to be another guy on the other side of the trade who sells
the future. So in future trading the sum of all bought and all sold
futures is always zero. In stocks this is not the case, since a
positive amount of stocks exists.

Why do you want to trade futures in the first place and not only
stocks? Well, futures provide leverage, meaning you only need some
margin to buy futures. Also futures can be on an index and so the risk
is diversified. As a third point, stocks can only be shorted intraday,
whereas futures can be shorted on longer timeframes.

## Spot-Future Parity
As you probably already know from my article about options, there is a
put-call parity which establishes a relationship between the price of
a put option and a call option.
As I have said futures are significantly easier. The spot-future
parity gives you a relation between the price of the underlying $$S$$
in the spot market and the price of the future $$F$$.

The fair price of the future should reflect the price of the
underlying. So I give you the formula and then talk about it.
Let $$r$$ be the risk-free interest rate, $$x$$ the days until
expiration and $$d$$ the dividends of the underlying occuring between
now and the expiry date. Then
\begin{equation}
F=S\cdot \left(1+r\frac{x}{365}-d\right)
\end{equation}
If you look close, you see the this is exactly what the price should
be, namely the risk-free price of the stock at the current time
without the dividends.
Often the price of the future is near this theoretical price of the
model, but not always, due to "market inefficiencies". In fact I have
not really found out why this occurs. I think it is in part due to
stuff like broker fees and liquidity which provide friction when
trading and so prevents clever people from arbitrage trading.
