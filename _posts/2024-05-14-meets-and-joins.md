---
layout: post
category: Recreational Math
tagline: Exploring structures in preorders
tags: [math, theory, category theory]
---
{% include math %}

## Introduction
In this post, we will talk about meets and joins in preorders.  The
beauty here is, that this point of view allows to generalize multiple
different notions such as the minimum of two numbers, intersections of
sets, and greatest common divisors

## Preorders
First and foremost, let us define what we mean when we are talking
about preorders.
A preorder $$(A,\leq)$$ is a set $$A$$ together with a binary relation
$$\leq$$ such that the two following properties hold:

- **Reflexivity**: $$a\leq a$$ for all elements $$a\in A$$, i.e. every element is related to itself.
- **Transitivity**: if $$a\leq b$$ and $$b\leq c$$, then $$a\leq c$$ for all $$a, b, c\in A$$.

A preorder is nearly a partial order, but not quite. What is missing
is the antisymmetry, i.e., if $$a\leq b$$ and $$b\neq a$$ then
$$a=b$$. Preorders do not necessarily have that property.

## Meets and Joins
Next, let us define the **meet** of two elements $$a$$ and $$b\in A$$.
For $$a$$ and $$b\in A$$ we say that an element $$m\in A$$ is their
meet if both of the following conditions hold.

1. $$m\leq a$$ and $$m\leq b$$.
2. For all $$c\in A$$ with $$c\leq a$$ and $$c\leq b$$ we have $$c\leq m$$.

We write $$a\wedge b$$ for the meet.

Similarly, we define the **join**.
For $$a$$ and $$b\in A$$ we say that an element $$j\in A$$ is their
join if both of the following conditions hold.

1. $$a\leq j$$ and $$b\leq j$$.
2. For all $$c\in A$$ with $$a\leq c$$ and $$b\leq c$$ we have $$j\leq c$$.

We write $$a\vee b$$ for the meet.

Note that neither meet nor join need to exist. There are examples
where neither exists and we will see one later.

## Example: Real Numbers
One of the easiest examples of a preorder are the real numbers with
the usual comparison operator $$(\mathbb{R}, \leq)$$. This is clearly
a preorder, as reflexivity and transitivity hold.

Let us look at what the meet of two numbers is in that preorder.

From the first condition we get that $$a\wedge b\leq a$$ and $$a\wedge b\leq b$$.
Hence, the meet is smaller or equal than the minimum of a and b. Or as
formula: $$a\wedge b \leq \operatorname{min}(a,b)$$.

From the second condition we have that
for all $$c$$ with $$c\leq a$$ and $$c\leq b$$ we have $$c\leq a\wedge b$$.
This means that all other numbers that are smaller than $$a$$ and $$b$$ are
smaller then the join. Or in other words, the join is the largest
number that is smaller than both, $$a$$ and $$b$$. Or in other words
the greatest lower bound. Hence, the join
$$a\wedge b$$ is the minimum $$\operatorname{min}(a,b)$$.

An analogous investigation shows that the meet is the smallest number
that is greater than both $$a$$ and $$b$$ and hence the maximum: $$a\vee
b=\operatorname{max}(a,b)$$.

Note that $$a\wedge b + a\vee b=\operatorname{min}(a,b)+\operatorname{max}(a,b)=a+b$$

## Example: Sets with Inclusion
The second example of a preorder that i want to show you are sets.
The set that was previously called $$A$$ is now a set of some sets,
such as a powerset. For two sets $$a$$ and $$b$$ we say that $$a\leq
b$$ is a is a subset of b, i.e., if $$a\subseteq b$$.
Again, reflexivity and transitivity hold.

Let us again take a look at meets and joins in that preorder.
The join $$a\wedge b$$ is a set that is contained in both $$a$$ and
$$b$$. And for each set $$c$$ that is contained in both $$a$$ and $$b$$,
$$c$$ is contained in the join. That means that the join is the
largest set that is contained in both $$a$$ and $$b$$. Hence, the join
$$a\wedge b$$ is just the intersection of the sets $$a\cap b$$.

Similarly, the meet is the smallest set that contains both $$a$$ and
$$b$$. Consequently it is identical to the union of the sets $$a\cup
bb$$.

Note that $$\lvert a\wedge b\rvert + \lvert a\vee b\rvert =\lvert a\cap b\rvert +\lvert a\cup b\rvert = \lvert a\rvert + \lvert b\rvert$$.

## Example: Integers with Divisibility
Another example of a preorder are the integers $$(\mathbb{Z},\leq)$$,
where we define $$a\leq b$$ if and only if $$a\mid b$$, i.e. $$a$$
divides $$b$$.

Why is this a preorder?
Reflexivity holds trivially as every number $$a$$ is divisible by itself $$a\mid a$$
For transitivity, observe that if $$a$$ divides $$b$$ and $$b$$ divides $$c$$, then also $$a$$ divides $$c$$ or in formulas:
$$a\mid b$$ and $$b\mid c \rightarrow $$a\mid c$$.

This preorder may be more unfamiliar, as the ones above. For example
$$3\leq 9$$, but $$3\not\leq 8$$, as $$3$$ is not divisible by $$8$$.

What are joins and meets in that preorder?

The meet is a number that divides both $$a$$ and $$b$$. So, it is a
common divisor. Further, every other number that is a divisor of both
$$a$$ and $$b$$ is also a divisor of the meet due to the definition.
Hence, The meet of $$a$$ and $$b$$ is the greatest common divisor
$$a\wedge b =\operatorname{gcd}(a,b)$$.

Let us look at an example. We know that $$\operatorname{gcd}(10,30)=10$$.
For the second condition let us choose another divisor, e.g., $$5$$.
Then $$5\mid 10$$ and hence we see that the gcd fulfills the second
condition (at least in this example).

The join of $$a$$ and $$b$$ is an element that is divided by both
$$a$$ and $$b$$. So we could at first think that the join is the
product $$a\cdot b$$. But that is not the case due to the second
condition. The join divides all other numbers that are divided by both
$$a$$ and $$b$$. Hence, the join is the least common multiple
multiple $$a\vee b =\operatorname{lcm}(a,b)$$

Note that $$a\wedge b \cdot a\vee b=\operatorname{gcd}(a,b)\cdot\operatorname{lcm}(a,b)=a\cdot b$$.

## Example: Oriented Graphs and Paths
We have already seen a lot of example of preorders, meets and joins
that look very different. But to drive the point home further that
generalization can be very useful in order to treat different cases at
once, here is another example.

An oriented graph $$G$$ is a preorder, where the relationship $$a\leq
b$$ is defined to hold if there is a path from $$a$$ to $$b$$.
A path is defined as any sequence of arrows, such that the target of
one arrow is the source fo the next.

Now you have seen enough examples of preorders that you should be able
to verify by yourself, that this is a preorder. Take out paper and
pencil and try to verify this for yourself. For maximum learning
effect it is recommended to try to find what the join and meet in this
preorder are. I will spoil it to you in the next paragraph.

In contrast to the examples we have seen until now, in this preorder
the join or meet do not need to exist.
The join is the vertex that is the latest predecessor of both, $$a$$
and $$b$$. Further, the meet is the earliest sibling of $$a$$ and
$$b$$.

## Conclusion
We have seen that while preorders offer a very abstract structure,
they are the foundation of many different mathematical objects. The
notion of preorder allows us to treat all of these objects the same.
This is not too dissimilar to the way we can treat different instances
of numbers the same without worrying about the instantiation, e.g.,
two bananas or two apples.
