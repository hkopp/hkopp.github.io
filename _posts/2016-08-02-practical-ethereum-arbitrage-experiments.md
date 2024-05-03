---
layout: post
category: Computer Stuff
tags: [trading, python]
---
{% include math %}

## Introduction
Inspired by [another
blogpost](http://jon.io/trading-ethereum-making-10-every-20-minutes.html)
I decided to experiment with trading arbitrage between different
exchanges.
Not so long ago there was a hardfork in the blockchain-based
cryptocurrency Ethereum. This means that we now have two Ethereums:
Ethereum (ETH) and Ethereum Classic (ETC). The exchanges decided to
also support the original cryptocurrency, i.e., ETC, and so we can now
trade ETH against ETC and the other way around. The initial idea was
that ETHETC is such a new pair, that nobody is doing arbitrage yet.

## First steps
There is an [API for the Kraken
exchange](https://github.com/veox/python2-krakenex) written in python
2 and an [API for Shapeshift](https://github.com/abitfan/shapeshiftio)
which helped me immensely. Since I like Python and Shapeshift and
already have an account at Kraken this was the way to go.

The idea is to check the price of ETHETC at Kraken, and ETCETH at
Shapeshift and check if it is profitable to buy at one exchange and
sell back at the other exchange. In Pseudocode:

{% highlight python %}
get fee_kraken
get fee_shapeshift
while True {
  get price_ethetc_shapeshift
  get price_etceth_kraken
  if(price_ethetc_shapeshift*price_etceth_kraken*(1-fee_kraken)*(fee_shapeshift)>1) {
    trade
  } else {
    wait
  }
}
{% endhighlight %}

Naturally we can also trade the other way around. For this we need
price_etceth_shapeshift instead of price_ethetc_shapeshift and for
kraken also the other way around. So the final pseudocode is as
follows.
{% highlight python %}
get fee_kraken
get fee_shapeshift
while True {
  get price_ethetc_shapeshift
  get price_etceth_kraken
  get price_etceth_shapeshift
  get price_ethetc_kraken
  if(price_ethetc_shapeshift*price_etceth_kraken*(1-fee_kraken)*(1-fee_shapeshift)>1) {
    trade
  } else if (price_etceth_shapeshift*price_ethetc_kraken*(1-fee_kraken)*(1-fee_shapeshift)>1) {
    trade the other way around
  } else {
    wait
  }
}
{% endhighlight %}


## The Kraken API
I wrote a file kraken.py which is a wrapper around the Kraken-API
exposing the relevant functions:

{% highlight python %}

import krakenex
import time

class Kraken():
    def __init__(self):
        self.__api = krakenex.API()

    def price_etceth(self):
        """1 ETC is worth how much ETH?"""
        try:
            print self.__api.query_public('Ticker', {'pair': 'XETCXETH'})
            return float(self.__api.query_public('Ticker', {'pair': 'XETCXETH'})['result']['XETCXETH']['b'][0])
        except Exception as e:
            print "An Error ocurred while fetching ETCETH@Kraken. "+ str(e) + " Trying again."
            time.sleep(1)
            return self.price_etceth()

    def price_ethetc(self):
        """1 ETH is worth how much ETC?"""
        try:
            print self.__api.query_public('Ticker', {'pair': 'XETCXETH'})
            return 1/float(self.__api.query_public('Ticker', {'pair': 'XETCXETH'})['result']['XETCXETH']['a'][0])
        except Exception as e:
            print "An Error ocurred while fetching ETHETC@Kraken. "+ str(e) + " Trying again."
            time.sleep(1)
            return self.price_ethetc()

{% endhighlight %}

Note how defensively the code is written. Somecould the API connection
just timed out or the JSON object could not be decoded. In this case
we wait a second and then simply try again.

## The Shapeshift API
Next I did the same for Shapeshift.

{% highlight python %}
from __future__ import print_function
from shapeshiftio import ShapeShiftIO
import time

class ShapeShift():

    def __init__(self):
        self.__api=ShapeShiftIO()

    def price_etceth(self):
        try:
            print(self.__api.rate('etc_eth'))
            return float(self.__api.rate('etc_eth')['rate'])
        except Exception as e:
            print("An Error ocurred while fetching ETCETH@Shapeshift: %s Trying again.", e)
            time.sleep(1)
            return self.price_etceth()

    def price_ethetc(self):
        try:
            print(self.__api.rate('eth_etc'))
            return float(self.__api.rate('eth_etc')['rate'])
        except Exception as e:
            print("An Error ocurred while fetching ETHETC@Shapeshift: %s Trying again.", e)
            time.sleep(1)
            return self.price_ethetc()

{% endhighlight %}

Here the errors from the API were much more varied. I got HTTP Error
503: Service Unavailable, HTTP Error 522: Origin Connection
Time-out, HTTP Error 525: Origin SSL Handshake Error, and also HTTP
Error 520: Origin Error. I did not even know those existed at all.
But on the second or third query they worked.

## Wrapping it all together
Now here the pseudocode from above again, but this time in Python 2.
And without the actual trading. We are just logging if arbitrage
opportunities exist.

{% highlight python %}
from __future__ import print_function
from kraken import Kraken
from shapeshift import ShapeShift
import time
from datetime import datetime

SHAPESHIFT_FEE = 0.0001 # 0.01%
KRAKEN_FEE = 0.001 # 0.1%
LOGFILE = 'log.txt'

def log(severity, message):
    """
    Log to a file. Severity is one of ['DEBUG', 'INFO', 'CRITICAL']
    """
    logfile = open(LOGFILE, 'a')
    logfile.write(datetime.isoformat(datetime.now())+ " [" + severity +"] - "+ message+"\n")
    logfile.close()

def run():
    k = Kraken()
    ss = ShapeShift()

    kraken_price_etceth = k.price_etceth()
    shapeshift_price_ethetc = ss.price_ethetc()

    log("DEBUG", "KrakenETCETH: "+str(kraken_price_etceth)+", SSETHETC: "+str(shapeshift_price_ethetc))

    trading_profit= kraken_price_etceth * shapeshift_price_ethetc * (1-SHAPESHIFT_FEE)*(1-KRAKEN_FEE)

    log("DEBUG", "returns when trading etc2eth@kraken + eth2etc@shapeshift (fees included): "+str(trading_profit))

    if trading_profit > 1:
        log("INFO", "ARBITRAGE OPPORTUNITY")

    #################################
    # Now test the other way around #
    #################################
    kraken_price_ethetc = k.price_ethetc()
    shapeshift_price_etceth = ss.price_etceth()

    log("DEBUG", "KrakenETHETC: "+str(kraken_price_ethetc)+", SSETCETH: "+str(shapeshift_price_etceth)+"\n")

    trading_profit= kraken_price_ethetc * shapeshift_price_etceth * (1-SHAPESHIFT_FEE)*(1-KRAKEN_FEE)

    log("DEBUG", "returns when trading eth2etc@kraken + etc2eth@shapeshift (fees included): "+str(trading_profit)+"\n")

    if trading_profit > 1:
        log("INFO", "ARBITRAGE OPPORTUNITY\n")
    
    time.sleep(5)


if __name__ == '__main__':
    while True:
      run()

{% endhighlight %}

Not exactly the most beautiful code, but it worked.  
The fees for Shapeshift and Kraken are straight from their websites.
Of course I could also have looked them up dynamically and if I would
plan to support more pairs, I would do that, but as a quick experiment
hardcoding sufficed.

## The Result
Yes, there were some arbitrage opportunities. Sometimes they lasted
about half a minute which was a real surprise for me. On the other
hand the profit margins were really small. You would get around 0.6%
at most. Everything else I saw was around 0.1%. This was the reason I
did not continue the experiment, since these profit margins are too
small for me. There is also a timing risk involved, since you need to
make the deposit on both exchanges, but the price may have changed in
the meantime. You also need enough money, such that the profit will
not simply vanish as a rounding error. But the more money you use the
higher is your slippage which will also make the strategy
unprofitable.
