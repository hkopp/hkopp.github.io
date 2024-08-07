---
layout: post
category: Quantitative Finance
tags: [programming, software design, trading, infrastructure]
---

## Introduction
In this post we will look at a basic system architecture of fully
automated trading systems.
An algorithmic trading system is a program that automatically trades
stuff on a market. This stuff could be anything ranging from
derivatives to forex.
Today nearly all brokers provide an API for automated trading.
Many proprietary trading desks plug their own software to these
brokers. For example you could have a VBA-macro in your excel file
that is plugged to an exchange. Then you see the current price in your
spreadsheet and are able to trade through excel.
In this post I want to talk about fully automated trading. So the
trading signals are not generated by humans but instead
algorithmically.    
You should know a little bit about object oriented programming to
understand this article. My approach is to explain the objects in the
order of information flow, except the logging facilities.

## Price Stream
The first important part of an algorithmic trading system is the price
feed. This is basically a separate thread which streams the current
prices from the exchange. Mostly this works via HTTP streams or the FIX
protocol.
A more advanced price handler could even provide the whole order book.
Sometimes these prices cover multiple financial instruments. On
these price signals the rest of the trading system depends.

## Trading Strategy
The trading strategy is the heart of a trading system. It receives the
prices and generates buy or sell signals. This module is perhaps the
most closely guarded secret of any fund.

## Risk Management module
Before executing the buy or sell signals the risk management module
determines if the orders are in line with risk management objectives.
The risk management module also determines the order size. This could
be according to any criterion, e.g., the (fractional) Kelly criterion.
When
trading multiple instruments a portfolio class could also be
incorporated in the risk management.
The risk management module creates other events which are processed by
the execution handler.

## Execution Handler
Finally we can execute the order. If we have a big order simply buying or
selling all in one go is a bad idea, since then we suffer
significant slippage. To this end we have an algorithmic execution
handler. Here we can offer more sophisticated execution algorithms
trying to match a benchmark like VWAP, TWAP oder POV.
If trading on multiple exchanges, for example in the case of arbitrage
trading, the execution handler also has to incorporate a routing
object which is responsible for routing the orders to the best
exchange.

## Logging facilities
Throughout all of these modules there have to be logging facilities
and perhaps a graphical user interface to see the current state of the
system. This should provide accounting to see and evaluate the
performance of the system. Furthermore it could help to restore the
current state of the system if it crashes. Another obvious reason to
have this module is to help in tracking down errors.
