---
layout: post
category: Quantitative Finance
tags: [trading, infrastructure]
---
{% include math %}

Today, I want to write about order book matching algorithms. These
algorithms are scratching the surface of HFT and normally you do not
need to know much about them except if you trade fully automated
intraday or if you want to incorporate a more realistic market
simulation in your backtester. Nevertheless I think they provide an
interesting glimpse into the mechanics of an exchange.

## What is an orderbook?
First i want to explain what an orderbook is. When the limit orders
for a specific asset
arrive at an exchange, they are collected in a list. This list is
termed the orderbook. There is one orderbook per asset. This is divied
into the sell orders an the buy orders.
It looks roughly as follows:

|Buy|      |    |     ||Sell |    |       |  |
|---------------------||---------------------|
|ID| Time  |Size|Price||Price|Size| Time  |ID|
|:-|-------|----|-----||-----|----|-------|-:|
| 4|8:00:04|250 |  100||101  |750 |8:00:01| 1|
| 6|8:00:10|500 |  100||101  |500 |8:00:05| 5|
| 2|8:00:01|750 |  97 ||101  |750 |8:00:30| 8|
| 7|8:00:10|150 |  96 ||102  |250 |8:00:02| 3|

Of course it is in a database inside a computer, so the above is only
a representation.
On the left hand side I have grouped the buy orders, whereas on the
right side i have the sell orders. Here the price of the buy orders
is lower than the price of the sell orders. There are special cases
where this is not the case, but for clarity we can disregard them. The
size is the amount of the asset which should be traded. Time marks the
time when the order arrived at the exchange. The field id is an
identifier for the order. There are also other
fields like the participants, but the ones I
have written above are the most important ones. Note that orders are
sorted with respect to price and when they have the same price they are
sorted wrt. their arrival time at the exchange.
When a market order is routed to the exchange, the exchange has to
decide which orders are filled. This decision algorithm is called an
order book matching algorithm.

## Price-Time-Priority/FIFO
The most simplest order book matching algorithm is a
price-time-priority algorithm. That means that the matching priority
is first price and then time. The participants are rewarded for
offering the best price and coming early.
Let us assume we have the same order book as above and a buy order for
1000 assets comes in. The relevant parts from above is the sell side.

|Sell                 |
|---------------------|
|Price|Size| Time  |ID|
|-----|----|-------|-:|
|101  |750 |8:00:01| 1|
|101  |500 |8:00:05| 5|
|101  |750 |8:00:30| 8|

When assuming price-time-priority, the orders are simply filled from
above. So the first order of 750 assets is filled and the remaining
250 assets are filled from order id 5. So the order book after the buy
order looks like the following.

|Sell                 |
|---------------------|
|Price|Size| Time  |ID|
|-----|----|-------|-:|
|101  |250 |8:00:05| 5|
|101  |750 |8:00:30| 8|


## Price Pro-rata
Price Pro-rata matching is often found in futures market and means that
priority is first given to the price and then the orders are filled
proportionally to their size. Assume again the order book from above.
The motivation is, that order id 8 has a large size and should be
preferred. We buy again 1000 assets. The total of assets with the best price of 101 is 2000, so
each of the limit orders 1, 5, 8 is half filled. The order book after
the execution of the buy order looks as follows:

|Sell                 |
|---------------------|
|Price|Size| Time  |ID|
|-----|----|-------|-:|
|101  |375 |8:00:01| 1|
|101  |250 |8:00:05| 5|
|101  |375 |8:00:30| 8|


## Conclusion
There are many more order book matching algorithms, so I only covered
the most important and widespread ones. They have different features
like being good for small liquidity, but unfortunately I did not find
a comprehensive reference.

The order book matching algorithm depends on the exchange, so when in
doubt, consult the documentation of your exchange. Especially if you
wonder why you are not filled when you joined too late at a price
level.
