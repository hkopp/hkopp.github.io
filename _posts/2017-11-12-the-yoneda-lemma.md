---
layout: post
category : Lesson
tags : [math, theory, category theory]
---
{% include math %}

## Introduction
In this post I am going to explain my favorite theorem: The Yoneda lemma.
If you do not know what category theory is, you should maybe read my
[previous post]({% post_url 2017-10-07-introduction-to-category-theory %}) where I explain some of the basics.

The thing that fascinates me about the Yoneda lemma is that depending
on the concrete category you are working with, it provides a theorem
which is mostly nontrivial. Previously, these theorems were proven
independently since it was unknown that they can be generalized with
category theory.
One way to think of the Yoneda lemma is through particle physics. In
particle physics, it suffices to take a particle and shoot other
particles at it. If one knows all reactions of a special particle with
all possible particles, then one knows everything there is to know
about the special particle. The Yoneda lemma formalizes this
intuition.
In a [previous post]({% post_url 2017-01-09-distributions %}) I have generalized functions by
defining generalized functions (distributions) by their action on
other functions, so-called test functions. This was also an
application of the Yoneda-embedding.

Depending on the concrete category it provides the following theorems:

- In a poset every element can be determined by the set of all
  elements it is greater than or equal to. In particular, every
  element is equivalent to a Dedekind cut. Or alternatively, every
  element of a partially ordered set can be included in some powerset.
- Cayley's theorem: Every group is isomorphic to a subgroup of a
  permutation group.
- Erdös' lemma: Every graph is isomorphic to a family of subsets
  $$S_i$$ of a set $$S$$ such that $$v_i$$ and $$v_j$$ are joined by
  an edge iff $$S_i\cap S_j\neq\emptyset$$
- Every ring has a faithful module.

## Presheaves
First, we are going to generalize elements in sets.
If we have a set $$M$$ containing some elements $$m_1,...$$ we may
recognize that we can instead of looking at the elements look at
the projections $$\{*\}\rightarrow M$$.
Here, $$\{*\}$$ denotes a set which contains only one element. Instead
of taking the elements directly we identify them by a "choosing function"
which picks single elements.
Thus, each projection $$\{*\}\rightarrow M$$ corresponds to a single
elements. To generalize elements in sets we can generalize this notion
of "picking" by allowing other sets as the singleton set
$$\{*\}$$ as the domain.
Let us define this for general categories, not only for sets:

Let $$\mathcal{C}$$ be a locally small category, $$A$$ an object in $$\mathcal{C}$$.
Then we define the contravariant Hom-functor of A by
$$
\begin{align*}
A^\circ: \mathcal{C}&\rightarrow Set\\
B&\mapsto Hom(B,A)\\
(B\stackrel{f}{\rightarrow} B')&\mapsto (Hom(B',A)\stackrel{\_\circ f}{\rightarrow}Hom(B,A))
\end{align*}
$$\\
where Set is the category of sets.

This is a contravariant functor as you may want to prove by checking the
corresponding functor laws. Go grab a pen, it is really not that
difficult and will help your understanding. I wait here for you.

Locally small means that the morphisms between each two objects form a
set. If this is not the case we get problems pertaining the
well-definedness.

I will occasionally write $$Hom(\_,A)$$ instead of $$A^\circ$$.

A presheaf on a category $$\mathcal{C}$$ is any functor
$$F:\mathcal{C}^{op}\rightarrow Set$$.
A presheaf is called representable if it is naturally isomorphic to
the contravariant Hom-functor $$A^\circ$$ for some object $$A$$.

## The Yoneda lemma
The Yoneda lemma tells us that we can get all presheaves from
Hom-functors through natural transformations and how to do this. It
explicitly enumerates all these natural transformations.

If you look into literature, what I am going to explain is often
called the contravariant Lemma of Yoneda. I think it is easier to
understand than the usual Yoneda, but you can get the other one by
applying the contravariant Yoneda in the opposite category.

Since we can define a presheaf $$A^\circ$$ for each object $$A$$ in a
category we can define an embedding $$\_^\circ$$. This is called the
Yoneda embedding and is a (co-variant) functor

$$
\begin{align*}
\mathcal{Yon}: \mathcal{C}&\rightarrow Fun(\mathcal{C}^{op}, Set)\\
A&\mapsto A^\circ \\ 
(A\stackrel{f}{\rightarrow} A')&\mapsto (A^\circ\stackrel{f\circ\_}{\rightarrow}(A')^\circ))
\end{align*}
$$

The Yoneda embedding embeds a category into a category of functors.
The question the Yoneda lemma answers is how the elements behave in
this new category. How do they interact with the other elements?
What are the morphisms $$Mor(A^\circ, F)$$ for some $$F$$ from the
bigger category $$Fun(\mathcal{C}^{op}, Set)$$?

**Lemma(Yoneda):**
Let $$F:\mathcal{C}^{op}\rightarrow Set$$ be an arbitrary functor.
Then for all objects $$A\in Ob(\mathcal{C})$$ there is a bijection.
\begin{equation}
F(A)\simeq Mor(A^\circ, F)
\end{equation}

On the left side of the equation there is a set, say $$X$$. On the
right side of the equations, we have morphisms between two functors
$$A^\circ$$ and $$F$$, i.e., natural transformations $$\eta_X$$.

To prove this, let us first take a natural transformation from the
right set $$Mor(A^\circ, F)$$. Let's say $$\eta$$.
This looks as follows:\\
$$\require{AMScd}
\begin{CD}
Hom(\_,A) \\
@VV\eta V \\
F(\_) \\
\end{CD}
$$

I want to show that $$\eta$$ is completely determined by some object
in $$F(A)$$. This would give us a bijection $$F(A)\simeq Mor(A^\circ,
F)$$ and we are done.

So, take some morphism $$f:X\rightarrow A$$ for some
arbitrary object $$X$$ in $$\mathcal{C}$$.
Since $$\eta$$ is a natural transformation, we can draw the following
commutative diagram by applying $$\eta$$ at the components $$A$$ and
$$X$$.

$$\require{AMScd}
\begin{CD}
Hom(A,A)        @>\_\circ f>>   Hom(X,A) \\
@VV\eta_AV             @VV\eta_XV \\
F(A)        @>F(f)>>   F(X) \\
\end{CD}
$$

We do not know a lot about $$Hom(A,A)$$, but we know that it contains the identity $$id_A$$.
Thus we can trace the functions on the element $$id_A$$ and get the
following:

$$\require{AMScd}
\begin{CD}
id_A        @>\_\circ f>>  f \\
@VV\eta_AV       @VV\eta_XV \\
u           @>F(f)>>   \eta_X(f)=(F(f))(u) \\
\end{CD}
$$

I take the symbol $$u$$ for $$\eta_A(id_A)$$.
The equation on the lower right $$\eta_X(f)=(F(f))(u)$$ follows from
commutativity. One hand is given by going through the upper right
corner, the other hand is given by going through the lower left
corner.

Note that we started with any $$\eta$$ and wanted to find some object
in $$F(A)$$ which completely determines $$\eta$$. But hey, we are
done. $$u=\eta_A(id_A)$$ is such an object. It determines $$\eta$$
completely since if we know $$u$$, we know $$\eta_X(f)$$ for each
$$X$$, since this is exactly $$(F(f))(u)$$ and thus only depends on
$$u$$. Thus, the proof is complete.

## Conclusion
Even if you do not share my enthusiasm for my favorite theorem I hope
you found this post helpful. The important thing to remember is that
things are fully determined by their interactions with everything
else.
Even if you did not follow each of the steps in the proof I hope that
I could convince you that the Yoneda lemma is a very powerful tool.
And even if you did not understand anything at all, which I doubt,
since then you would not be reading to the bottom of the page, I may
have convinced you that there are areas of math that have nothing to
do with boring computations and numbers.
