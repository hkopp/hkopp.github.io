---
layout: post
category : Lesson
tags : [math, theory, category theory]
---
{% include math %}

## Introduction
In this post I am going to explain the fundamentals of category theory
in order to explain my favorite theorem in a later post.
Category theory is a really pure and fundamental part of mathematics,
comparable to set theory.
Thus it sometimes is dry since there is no immediate application. But
constructions from category theory recently found applications like in
programming languages, especially Haskell.
In a nutshell, category is the study of things together with its structure.
It can be used as a foundation for all of mathematics, like set
theory.

Another motivation to learn category theory is that sometimes the same
concepts surface in different areas of mathematics. For example a
product. There is a product of integers, of vector spaces, of
topologies, of groups, ... So, why not define a product on a much more
general structure? The same is true with isomorphisms, or quotients,
...

## Category, a definition
We start with the basic thing in category theory: a category. A
category is a thing together with structure, as motivated above.
More precise, a category $$\mathcal{C}$$ consists of

  - objects $$Ob(\mathcal{C})$$, like $$A, B, C, ...$$, i.e., things
  - morphisms (or arrows)  $$Mor(\mathcal{C})$$, like $$f, g, h,
    ...$$. One can think of these as functions, but they can also be
    relations or orders, i.e., structure.
  - For all morphisms $$f$$ there  are objects $$dom(f)$$ and
    $$codom(f)$$, called domain and codomain. We write this as
    $$f:dom(f)\rightarrow codom(f)$$.
  - For morphisms $$f, g$$ such that $$f:A\rightarrow B$$,
    $$g:\rightarrow C$$ there is a morphism $$g\circ f:A\rightarrow
    C$$. This amounts to composition of morphisms.
  - For all objects $$A$$ there is the identity morphism
    $$id_A:A\rightarrow A$$.

Additionally we require

  - Associativity: $$h\circ (g\circ f)=(h\circ g)\circ f$$, for all
    $$f,g,h$$, such that this expression is defined.
  - Unity: $$f\circ id_A = f = id_B \circ f$$ for all $$f:A\rightarrow
    B$$.

### Examples
The fun part in category theory is always the examples. The category
above looks like a set with functions, but indeed it is more general.

1. As mentioned, any set is a category. E.g., take $$S=\{A,B,C\}$$ and
   only the identity morphisms $$id_A, id_B, id_C$$.
2. Any ordered set is a category. Let $$(D,\leq)$$ be an ordered set.
   Then we define $$f:A\rightarrow B$$ if $$A\leq B$$. Function
   composition in this respect means transitivity, i.e., if $$A\leq
   B$$ and $$B\leq C$$, then $$A\leq C$$. So, categories can be
   used to express order relations.
3. The set of all Haskell types is a category, i.e., Integer, Char,
   Lists of Chars, ... (Well, nearly. There are some exceptions, which
   have to do with undefinedness.) The morphisms are just the usual
   functions you can write in Haskell.
4. The "set of all sets" is a category. If you take all sets together,
   this is not a set, due to Russel's paradox, but in a sufficiently
   generalized manner, this works. Note that in this case the objects
   do not form a set. This can be formalized, but I am skipping this
   part.
5. For each category $$\mathcal{C}$$ we get a dual category
   $$\mathcal{C}^{op}$$ by reversing all the arrows.


## Functors
After looking at categories, the next thing are functions between
categories. These are called functors and preserve the structure of
the category. Thus, if we take categories as objects and functors as
their morphisms, we even get a category of categories.

### Definition
We call a function $$F$$ between two categories $$\mathcal{C}$$ and
$$\mathcal{D}$$, i.e.,

$$
\begin{align*}
F:\mathcal{C}&\rightarrow\mathcal{D}\\
A&\mapsto F(A) \\
(A\stackrel{f}{\rightarrow}B)&\mapsto(F(A)\stackrel{F(f)}{\rightarrow}F(B))
\end{align*}
$$

a (covariant) functor, if for all objects $$A$$ we have $$F(id_A)=id_{F(A)}$$ and
$$F(f\circ g)=F(f)\circ F(g)$$, for $$f, g$$ suitable.

We call a functor contravariant if all of the above is satisfied
except we have $$F(f\circ g)=F(g)\circ F(f)$$, for $$f, g$$ suitable,
instead of $$F(f\circ g)=F(f)\circ F(g)$$, i.e., they switch places.

Note that we define the action of the functor on the objects, as well
as the morphisms. The requirements to preserve the identity and the
compositions are the preservation of the structure i have mentioned
above.

### Examples

1. Lists in Haskell are a functor. I.e., the morphisms which maps
   types to a list of types, also Maybe is a functor in Haskell.
2. Let us take another Haskell example. Let us define a tree
   ```data Tree a = Leaf a | Branch (Tree a) (Tree a)```
   This is also a functor mapping types into trees containing these
   types. The composition is preserved, since if we have a tree of
   things of type $$A$$, and a function $$f:A\rightarrow B$$, then we
   can apply $$f$$ to all things in the tree (this is called $$F(f)$$)
   and get a tree of things with type $$B$$.
3. Taking a set to its powerset is a functor from the category of sets
   to the category of sets.
4. The identity functor is also a functor which maps any category to
   itself. Thus, as stated above, if we take categories as objects and
   functors as the morphisms, we can see that all categories together
   form a category.
5. For each contravariant functor
   $$F:\mathcal{C}\rightarrow\mathcal{D}$$, there is a unique
   covariant functor
   $$\bar{F}:\mathcal{C}^{op}\rightarrow\mathcal{D}$$. Okay, this is
   not really an example, but more an exercise. Go ahead, Take a
   pencil and try to define $$\bar{F}$$.

## Natural transformations
Another fun part of category theory is that one always goes a step
further. We have categories. We have functors between categories. We
have seen that there is a category of categories. But what are the
functors there? Turns out, these are interesting to study on its own.
We call then natural transformations.
With the natural transformations as functors and the functors as
morphisms, we even get the category of morphisms.

### Definition
A natural transformation $$\eta$$ between Functors
$$F,G:\mathcal{C}\rightarrow\mathcal{D}$$ is a family of functors
$$\eta=\{\eta_A\}_{A\in ob(\mathcal{C})}$$ such that the following
diagram commutes for all $$f:A\rightarrow B$$.

$$\require{AMScd}
\begin{CD}
F(A)        @>F(f)>>   F(B) \\
@VV\eta_AV             @VV\eta_BV \\
G(A)        @>G(f)>>   G(B) \\
\end{CD}
$$

And with these definitions I will end my expositions. Next time, we
can take a look at my favorite theorem: The Yoneda Lemma.

## References
If you are interested in category theory, there is "Categories for the
working mathematicians" which started it all. It is primarily written for a
mathematical audience.

Then there is "Catgory Theory" by Awodey which is also a good book. He
is a little bit more on the computer science side, since he, e.g.,
introduces lambda calculus as a category.

For pure programmers there is no real resource except the blog, by
[Bartosz Milewski](https://bartoszmilewski.com/). This guy is a
genius, despite his habit of sometimes drawing objects as pigs and
morphisms as fireworks. (Yeah, you read that right. This is also a
category.)
