---
layout: post
category : Recreational Math
tags : [math, theory]
---
{% include math %}

## Motivation
Sometimes people mistake distributions for functions. This is due to
the fact, that the notation sucks and often people use the same
symbols for both. In this post I want to clarify the intuition behind
distributions.

Take as an example the expected value of a random variable $$X$$ in
$$\mathbb{R}$$ with
probability density function $$p_X$$. Then the expected value is
$$E(X)=\int_\mathbb{R} x p_X(x) dx$$.
But does any random variable admit a probability density function
$$p_X$$? Of course not. Take as a counterexample the random variable
$$Y$$ with absolutely no randomness which is always $$0$$. Can we
write this as $$0=E(Y)=\int_\mathbb{R} x p_Y(x) dx$$ for some function
$$p_Y$$, which is not zero everywhere?

## Distributions
It turns out we can kind of can do this. There is the so-called dirac
distribution (often wrongly called dirac function) $$\delta$$ which is a gadget which is zero everywhere,
except at $$1$$ where its value is infinity. Further, integrating
yields $$\int_\mathbb{R} \delta(x) dx=1$$.
This $$\delta$$ would have the properties we require of $$p_Y$$ from
above.

But why should $$\delta$$ exist at all? The trick is, that it is not a
function but a special type of generalized function. These generalized
functions are called distributions.

Let me introduce some more
notation. Let $$D(\mathbb{R})$$ be the set of infinitely
differentiable (smooth) functions
$$\phi:\mathbb{R}\rightarrow\mathbb{R}$$ which are zero outside some
bounded interval (have compact support). We call those functions test
functions.
We now define a distribution $$T:D(\mathbb{R})\rightarrow\mathbb{R}$$ to be a linear mapping from the space of
test functions $$D(\mathbb{R})$$ to $$\mathbb{R}$$. Usually one writes
$$\langle T, \phi\rangle$$ instead of $$T(\phi)$$.

As you may have guessed every function $$f$$ induces a distribution
$$T_f$$. This should be intuitive, since I have written above that a
distribution is a generalized function, so a usual function should of course
also be a distribution.
In particular we define $$T_f$$ by its action on the test functions
$$\phi$$ through $$\langle T_f, \phi\rangle=\int_\mathbb{R} f(x)
\phi(x)dx$$.

The Dirac distribution $$\delta$$ on the other hand is not induced by
a function. It is defined by $$\langle \delta, \phi\rangle=\phi(0)$$.
I.e., it maps a function to its value at zero.
We wrote above $$0=E(Y)=\int_\mathbb{R} x p_Y(x) dx$$, even though we
did not yet know that $$p_Y(x)$$ in this case is the dirac
distribution and not a function.
In the literature stuff like this is often written as
$$0=\int_\mathbb{R} x \delta(x) dx$$ to mean $$\langle\delta,
x\rangle=0$$. There is similar weird notation floating around, like
$$\int_\mathbb{R} \delta(x) dx=1$$, which is very misleading.
This is not an integral of a function, since $$\delta$$ is not a
function. Don't get confused. The last equation can be translated as
$$\int_\mathbb{R} \delta(x) dx=\langle \phi, 1\rangle$$. And then it
makes sense again, at least from a notational point.

And that is already everything you need to know to understand
distributions. One can take a more sophisticated space of test
functions to arrive at more sophisticated distributions. One can
define derivatives of distributions, such that it feels natural. One
can add and multiply distributions. But the underlying intuition stays
the same as above, namely to generalize functions as inducing a
linear mapping from a space of test functions to $$\mathbb{R}$$.
