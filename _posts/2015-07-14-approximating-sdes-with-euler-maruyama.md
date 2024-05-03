---
layout: post
category: Math
tags: [math, stochastic, numerical analysis]
---
{% include math %}

## Introduction
In this post I am going to explain the Euler-Maruyama method for
approximating stochastical differential equations.

## Euler method
First, the Euler method. Suppose you have a (deterministic) ordinary differential
equation
$$
\begin{align*}
X'(t)=f(X(t),t)
\end{align*}
$$
where $$X'$$ is the derivative of $$X$$, $$f$$ is a computable
function and the domains of definition are so that everything
"works fine".
Suppose we want to approximate the solution on the closed interval
$$[a,b]$$.
We also need to have a boundary condition, e.g., that $$X(a)=X_0$$
to ensure uniqueness of the solution.

Let us now approximate the result. First choose a partitioning of the
interval $$[a,b]$$. We take contant stepsizes, but you can also use any
other partitioning. So choose a $$n>0$$ "big" and then define the
stepsize as $$\Delta t:=\frac{b-a}{n}$$ and the different steps as
$$t_i:=a+i\cdot \Delta t$$ for $$0\leq i\leq n$$.

We know from our boundary condition that $$X(t_0)=X(a)=X_0$$.  
What is now a meaningful value for $$X(t_1)=X(t+\Delta t)$$?  
We know that $$X'(t_0)=f(X(t_0),t_0)=f(X_0,t_0)$$ which we can
compute. Thus we can approximate the "real" solution $$X(t)$$ in a
neighborhood of $$t_0$$ by a line.
This works in a practical sense if $$n$$ was chosen "big enough",
since then $$\Delta t$$ is small.

So a good guess for $$X(t_1)$$ would be
$$X(t_1)=X(t_0)+f(X_0,t_0)\cdot\Delta t$$.
We take the correct value and gradient at $$t_0$$ and walk $$\Delta
t$$.
More generally we can define an approximation $$Y(t)$$ of $$X(t)$$ via

$$
\begin{align*}
Y(t_0)=X_0 \text{ and }
Y(t_i)=Y(t_{i-1})+f(Y(t_{i-1}),t_{i-1})\cdot\Delta t
\end{align*}
$$

If this is not clear to you I suggest you stop here and draw a
picture.

## Euler-Maruyama method
The Euler-Maruyama method is for SDEs the same as the Euler method is
for ODEs. Suppose we have the SDE

$$
\begin{align*}
d X(t)= f(X(t)) dt + g(X(t)) dB(t)
\end{align*}
$$

where $$B(t)$$ is
[brownian motion]({% post_url 2015-03-25-martingales %}).

We want to do the same, namely approximate $$X(t)$$ on the closed
interval $$[a,b]$$.
Again we need a boundary condition like $$X(a)=X_0$$.
And we also take a steplength $$\Delta t:=\frac{b-a}{n}$$ and define
the different points as $$t_i:=a+i\cdot \Delta t$$ for $$0\leq i\leq
n$$.
Basically we do everything exactly as before.
We can also again approximate $$X(t_i)$$ in a small neighborhood by a
line but this time the gradient of the line is stochastical in nature.
So we get as approximation the markov chain

$$
\begin{align*}
Y(t_0)=X_0 \text{ and }
Y(t_i)=Y(t_{i-1})+f(X(t_i))\cdot\Delta t + g(X(t_i))\cdot\Delta B_i
\end{align*}
$$

where $$\Delta B_i=B(t_i)-B(t_i-1)$$ which are i.i.d. random variables
which follow a $$\mathcal{N}(0,\Delta t)$$ distribution.

What does Markovian mean in this context? It means that for the
conditional expected values $$E(Y(t_i)|Y(t_l),l\leq
i)=E(Y(t_i)|Y(t_{i-1}))$$ so the next expected value depends only on
the previous one.
