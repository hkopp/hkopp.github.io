---
layout: post
category: Quantitative Finance
tags: [pairs trading, python, programming, math, stochastic]
---
{% include math %}

This time I will give a short introduction to time series models, since I find myself
looking up the same things over and over, because I forget them in the
meantime.
I will cover the AR(p)-model and quickly define the MA(q)-model.
These are the basic models, on which the more sophisticated ones
are built.

## Useful definitions
In the following $$X_t, t\in\mathbb{N}$$ will always be a time series,
i.e. a series of random variables.    
We define the _mean_ $$\mu(X_t)$$ of the time series $$X_t$$ to be
simply the expectation $$\mu(t)=E(X_t)$$.    
We say that a time series $$X_t$$ is _stationary in the mean_ if the mean is a
constant, i.e., $$\mu(t)=\mu=const$$.    
We define the _variance_ of a time series $$X_t$$ to be
$$\sigma^2(t)=E((X_t - E(X_t))^2)$$. If the time series is stationary in
the mean this is just $$\sigma^2(t)=E((X_t - \mu)^2)$$.    
We say that a time series $$X_t$$ is _stationary in the variance_ if, you guessed it, the variance is a constant, i.e., $$\sigma^2(t)= const$$.    
If a time series is stationary in the mean and stationary in the
variance we say that it is _second order stationary_. Then the
correlation of shifted observations is only a function of the shift.

There is also the notion of strong stationarity, which basically means
that if you shift the time series, the cumulative probability
distribution does not change. If you are interested in that, you may
want to look it up. I am not going to split hairs over this.

The problem with all these definitions is that you normally do not
know any of these terms, since you only have one realization of the
time series. Thus you cannot know the mean, since this is a function
of time. Also you cannot compute the variance, since the variance also
depends on the time.    
Normally you assume some properties like stationarity in the mean, if
it makes sense for your observations and then go on to compute what
you are allowed to compute.

If a series $$X_t$$ is second order stationary then we can define the
autocovariance of lag $$k$$ as
$$C_k=cov(X_t,X_{t+k})=E((X_t-\mu)(X_{t+k}-\mu))$$. Note that
$$\mu$$ exists since $$X_t$$ is second order stationary.

The autocorrelation $$\rho_k$$ of lag $$k$$ of a second order
stationary time series $$X_t$$ is the covariance of $$X_t$$ and
$$X_{t+k}$$ divided by its variance. Or the autocovariance of lag
$$k$$ of $$X_t$$ divided by its variance. I.e.
$$\rho_k=C_k/\sigma^2$$. Alternatively it can be defined as the
correlation between $$X_t$$ and $$X_{t+k}$$.

In the following $$\epsilon_t$$ will always denote white noise. That
means that $$\epsilon_t\sim \mathcal{N}(\mu,\sigma^2)$$ are
independent and identically distributed.

We denote the _backward shift_ operator by $$B$$. The backward shift
operator takes a time series element and returns the previous element.
$$BX_t=X_{t-1}$$. In other references it is sometimes denoted by
$$L$$ and called the _lag operator_.

There is also the _difference operator_ $$\Delta$$ which is defined as
$$\Delta=(1-B)$$. This is an aesthetically pleasing way to write
$$\Delta X_t=X_t-X_{t-1}$$.

## AR model
A time series model $$X_t$$ is an _autoregressive model of order p_, AR(p), if

$$
\begin{align}
X_t = c + \sum\limits_{i=1}^p \phi_i X_{t-i} + \epsilon_t
\end{align}
$$

where $$\phi_1,...,\phi_p$$ are the parameters of the model, $$c$$ is a constant and $$\epsilon_t$$ is white noise.
We can rewrite this as $$\Phi(B)X_t - c=\epsilon_t$$, where $$\Phi(B)$$ is the
polynomial $$\Phi(x)= 1-\phi_1 x - \phi_2 x^2 - ... - \phi_p x^p$$
evaluated at the backward shift operator $$B$$.

That means, that the next value is always a linear combination of the
old value and some shock $$\epsilon$$.
These models are not always stationary.

Let us consider a random walk. With our new notation, we can define
this as an AR(1) process where $$\phi_1=1$$ and $$c=0$$. This is not a
stationary process.

In fact, if you want to check if your process is stationary, you have
to compute the roots of the polynomial $$\Phi$$. If all of them are
bigger than one in absolute value the process is stationary.
If there are some unit roots, i.e., roots with absolute value 1, like
in the case above with the random walk, then your process is not
stationary. You can check this by computing it for an AR(1) process.
The proof of the general statement is just a generalization of an
AR(1) model.

If you are looking for a statistical test for the presence of a unit
root, there is the Augmented Dickey-Fuller test.

### Example code
In the following example we will take random walk and fit an
AR-process on it.
Then we will check if it is stationary.

{% highlight Python %}
import pandas as pd

import numpy as np
import statsmodels.tsa.ar_model as ar

randomwalk = np.random.randn(1000).cumsum()
armodel = ar.AR(randomwalk)
# We specify the max lag as 1
result=armodel.fit(1)
print(results.conf_int())
{% endhighlight %}

The result of running the code is as follows:
{% highlight Python %}
[[-0.05228426  0.12246102]
 [ 0.98979846  1.00183547]]
{% endhighlight %}

These are the estimated parameters within a 5% confidence interval.
The second row says, that our $$\phi_1$$ which should be one is
estimated to be between 0.98979846 and 1.00183547. Quite close.    
I am not quite sure what the first pair is good for. First I thought
this is an estimate for the constant term c. But it did not change
when I estimated the parameters for randomwalk+100, so I simply do not
know.

Let us now check if our random walk is stationary. For this, we employ
the Augmented Dickey Fuller test. There are also other tests for
stationarity, but for our purposes, ADF suffices.

ADF does not know about conditional heteroskedasticity, which is a
mathematical way of saying that the volatilities are clustered. So if
you have a heteroskedastic time series and use ADF, you are doing it
wrong. ADF internally estimates the parameters assuming we have an
AR(p) process and then checks for the unit root.

Our code continues from above
{% highlight Python %}
import statsmodels.tsa.stattools as ts
ts.adfuller(randomwalk,1)
{% endhighlight %}

If we do not provide the 1, which specifies the order of our model, it
estimates the order.
The output is as follows:

{% highlight Python %}
(-2.1121577063909767,
 0.2396541120037593,
 0,
 999,
 {'1%': -3.4369127451400474,
  '10%': -2.568312754566378,
  '5%': -2.864437475834273},
 2827.700663064752)
{% endhighlight %}

The first value is the test statistic. Next is the p-value. The real
p-values from the literature [MacKinnon (1994)] at the critical values
of 1, 5, and 10%, are in the dictionary.

The null hypothesis of the ADF is that there is a unit
root. Since our p-value is big, we cannot reject the null hypothesis.
Alternatively we can also see that our statistic is bigger than the
statistic at MacKinnons values.

This is in accord with our intuition, since we provided a
nonstationary process as input, i.e., a process with a unit root.

## MA model
A time series model $$X_t$$ is a _moving average model of order q_, MA(q), if

$$
\begin{align}
X_t = \mu + \epsilon_t + \sum\limits_{i=1}^q \theta_i \epsilon_{t-i}
\end{align}
$$

where $$\theta_1,...,\theta_p$$ are parameters, $$\mu$$ is a constant, namely the expectation of $$X_t$$ and $$\epsilon_t,...,\epsilon_{t-q}$$ is white noise.
We can rewrite this using a polynomial $$\Theta$$ as $$X_t = \mu +
\Theta(B)\epsilon_t = \mu + (1 + \theta_1 B+ \theta_2 B^2 + ... + \theta_q
B^q) \epsilon_t$$.

In MA-models, the next value of the series is a linear combination of
the previous shocks.
MA-models are always stationary.

## Conclusion
We have seen the basic definitions of time series and how to test for
stationarity of an AR(p) time series. I will probably reference this
from future articles.
