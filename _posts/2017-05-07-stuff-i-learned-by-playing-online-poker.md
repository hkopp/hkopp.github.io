---
layout: post
category : lesson
tags : [poker, math]
---

{% include math %}

Recently I played a lot of online poker, so I thought I could write down
some observations. I played poker with
[Pokerth](http://www.pokerth.net) which is a nice poker client for
Linux. Pokerth, as the name implies is about (No-Limit) Texas Hold-em.

## Simple but Difficult
Poker has really easy rules, comparable to the board game Go. You can
learn it in about five minutes.
Nevertheless it takes years to master. In Go the better player gets a
handicap, to even the field. In Poker there is no comparable
mechanism, but since Poker involves more randomness, fishes also win
sometimes and that will bring them back to the table.

## Luck vs. Skill
Poker looks like a game of luck but is really about skill. Why else
are the people in the World Series of Poker finals always the same? A
single game is pure luck, depending on what cards you have been dealt.
But over many rounds the randomness evens out. Over sufficiently many
rounds all people get dealt the same cards. Then it depends on how you
play your cards.    
Interestingly if one thinks about it many things look like they are
about luck but are not: Sales, Women, and daytrading come to mind.

## Pot odds
One of the advanced basic concepts in poker is that of pot odds.
Assume you have a flush draw, i.e., one card is missing to a flush.
There are two more cards to come and assume we know that if you make
that flush you are certain to win. Further assume that there are 90$ in
the pot and your opponent raises 10$. Should you call?

This can be analyzed by computing the odds. The probability of 
making the flush is about $$1/3$$. If you call, however your share
on the pot is $$1/10$$, since there are 100$ in the pot and you have
to bet 10$. So you can win 10$ for every dollar you bet.
Thus you should call.

More exactly when we are three times in this situation, on average one
time we will make the flush and win at least 100$ (the 100$ this round
and perhaps future bets). The other two times we will not make the
flush and lose 10$ each. So our net profit of playing three times is 
$$100$ - 2\cdot 10$ = 80$ $$. That means that in one game, calling will
give us 27$ (80$ divided by 3) on average.

What if we raise by 10$?    
Assume our opponent folds with a probability of 50% if we raise. We do
not know this probability but can infer it approximately by watching
him over many plays. To analyze this assume we are playing that
situation six times.

On average three times he will fold and we will win 100$.    
One time he will not fold and we will make the flush, thus winning 110$ (90 which were in the pot and 20 from the opponent).    
Two times he will not fold and we will not make the flush. So we lose 20$ per play.    

Summing up we gain $$3\cdot 100$ + 110$ - 2\cdot 20$ $$. These are 370$. So
divided by six we see that we gain 61$ on average.

Thus we should better raise than call since calling only yields 27$ on
average.

## Influencing others by own actions
In poker the bets leak information. A player with good cards rather
raises or calls. He will not fold. This gives the other participants
information on which they will act. The betting patterns also leak
information.    
In trading a similar type of information leakage occurs. If a market
participant thinks a share is undervalued he will buy it. This will
show in the slippage, thus leaking information to the other market
participants who will act on the adjusted price.
In poker we also have imperfect information, but rather bet on the
estimated or perceived value of the cards instead of the perceived
value of some shares.

## Do not play every hand
There is a strong temptation to play too many cards. In fact in most
cases it is the best to directly fold. In the beginning it is only
about the small and big blind, so there is not much to gain anyway. In
fact, more hands should be played if the blinds are big or if there
are fewer players in the game.
Anyway, I think it is more of a psychological phenomenon. The thinking
goes "I will just take a quick peek at the flop and then I can fold".
This thinking will cost you money.

## Bluffing is overrated
Many people think poker is mainly about bluffing. It is not. Bluffing
is only a small part of the repertoire of a poker player to make a
weak hand look strong. It is almost as important as making a strong
hand look weak. This is called slowplaying or check-raising, where you
check in the first round of bets, someone else bets and then you
raise. This deceives players to bet in the first round, since they do
not know that you have a big hand.  
So, one could say that poker is about deception and indeed it is
important to hide the strength of your hand. But that is not all. As
mentioned above, it is also about pot odds.

## Poker culture
Poker players are one of the weirdest people on the planet. Perhaps it
is due to them being exposed to financial risks and learning how to
deal with it.
I have seen weird things. DDoS attacks on poker servers, even in the
small time I played. I watched poker players tilt and curse after
they went all-in. I have read chats about where the best hookers are
in Las Vegas, about people talking about traveling the world
while playing poker. These things are all somehow part of the poker
culture.
