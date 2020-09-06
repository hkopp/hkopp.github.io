---
layout: post
category : Lesson
tags : [stochastic, math, theory]
---
{% include math %}

# Scenario

Let's start from a very generic scenario:
We have some measurements $$(x_i,y_i)$$, $$i\in I=\{1,...,n\}$$, and a model $$f_\theta$$ for these measurements, where $$\theta$$ is a parameter.
However, these measurements are noisy, i.e., there are some
measurement errors. In formulas: $$f_\theta(x_i)=y_i+\epsilon$$ for all
$$i$$.
We now want to find $$\theta$$.

This scenario occurs lots of times. For instance, when measuring
physical constants, we usually know the physical model, and thus
$$f_\theta$$, but we are unaware of $$\theta$$. However, we can make
experiments to get a bunch of measurements.
In fact we can do this most of the time when we have a model and need
to choose parameters. We can simply do some experiments and then
choose the "best" parameters. Yeah, $$\theta$$ can be a vector of
parameters.

## Maximum Likelihood Estimation

Up to now we discussed **Maximum Likelihood Estimation**
in a [previous post]({% post_url 2017-12-20-maximum-likelihood-estimation-of-a-coin-flip %}), where the parameter $$\theta$$ is chosen in
such a way, that it is the most likely parameter to account for the
measurement results. More exactly, we defined the likelihood function
$$\mathcal{L}(p;x_1,...,x_n)$$ as the joint probability mass function

$$
\begin{equation}
\mathcal{L}(\theta;x_1,...,x_n)=F_\theta(x_1,...,x_n)=\prod\limits_i F_\theta(x_i)
\end{equation}
$$

where $$F_\theta(\cdot)$$ is the probability mass function from which the
$$y_i$$ are sampled.
Then, the parameter $$\theta$$ for which
$$\mathcal{L}(\theta;x_1,...,x_n)$$ is maximal is the "most likely"
$$\theta$$ to explain the measurements.

## Method of Least Squares

However, there is another way to choose the "best" parameter $$\theta$$, namely the **Method of Least Squares**.
The idea is that when we choose a fixed $$\hat\theta$$, then we still
have some error $$\epsilon$$ which is not explained by our model. This
error is exactly $$\epsilon=f_\theta(x_i)-y_i$$ for each measurement
$$i\in I$$. We want to have these errors as small as possible. That
is, we want to minimize the difference of our prediction and the real
measurement. In formulas, we want to minimize $$\sum\limits_{i\in I} \lvert
y_i-f_\theta(x_i)\rvert $$, where $$\lvert\cdot\rvert$$ denotes the
absolute value. As minimizing a function necessitates taking a
derivative it is easier (and customary) to instead minimize
$$RSS(\theta)=\sum\limits_{i\in I} (y_i-f_\theta(x_i))^2$$. This is the
so-called **residual sum of squares**.

## Some Math
So which of these methods -- maximum likelihood estimation or the
method of least squares -- is the best? Which one should you use?
Let me tell you that mostly it does no matter.
The point of this whole blog post is to show that

```
Maximum likelihood estimation assuming gaussian errors is exactly the
same as the method of least squares.
```

Let me repeat that in a different font style:
**Maximum likelihood estimation assuming gaussian errors is exactly the
same as the method of least squares.**

Now let me show you why that is the case.
Let us compute the maximum likelihood estimate $$\hat\theta$$.
Thus, assume that $$\epsilon$$ are identically and independently
gaussian distributed.

We know that $$\mathcal{L}(\hat\theta;x_1,...,x_n)$$ is maximal
exactly when $$\log\mathcal{L}(\hat\theta;x_1,...,x_n)$$ is maximal.
Taking the logarithm is another customary trick to ease the computation.

Plugging in the definition yields
$$
\begin{equation}
\log\mathcal{L}(\hat\theta;x_1,...,x_n)=\log\prod N(f_{\hat\theta}(x_i),\sigma^2) = \sum_i\log N(f_{\hat\theta}(x_i),\sigma^2)
\end{equation}
$$

As we assumed gaussian errors we get
$$
\begin{equation}
\sum_i\log f_{\hat\theta}(x_i) = \sum\limits_{i=1}^n \log\left(\frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{(y_i-f_\theta(x_i))^2}{2\sigma^2}}\right).
\end{equation}
$$

Here, $$\sigma$$ and $$\mu$$ are the parameters of the Gaussian model,
variance and the mean respectively. Simplifying further yields the
following.

$$
\begin{equation}
\sum\limits_{i=1}^n \log\left(\frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{(y_i-f_\theta(x_i))^2}{2\sigma^2}}\right)
= -n \log(\sigma\sqrt{2\pi}) -2\sigma^2 \sum\limits_{i=1}^n (y_i-f_\theta(x_i))^2.
\end{equation}
$$

Thus, in summary:
$$
\begin{equation}
\log\mathcal{L}(\hat\theta;x_1,...,x_n)= -n \log(\sigma\sqrt{2\pi}) -2\sigma^2 \sum\limits_{i=1}^n (y_i-f_\theta(x_i))^2.
\end{equation}
$$

As the first part only depends on $$\sigma$$ it is now pretty obvious that the last part is maximal if the
residual sum of squares $$RSS(\theta)=\sum\limits_{i\in I} (y_i-f_\theta(x_i))^2$$ is minimal.
But when the RSS is minimal then we retrieve the same parameters as
with the least squares method.
