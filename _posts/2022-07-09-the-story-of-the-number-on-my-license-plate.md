---
layout: post
category: Recreational Math
tags: [math, easy]
---
{% include math %}


## Intro
Back in April, I bought my first car.
Here in Germany, you can pick your license plate and one part of it is
a number.
So, as the nerdy mathematician that I am I selected a special number.
First, I tried 6174 but in my region you can only pick numbers with three digits.
Next, I tried 257 but that one was already taken, probably by another
mathematician.
Finally, I picked 561 which was still available and is now on my
license plate.

In this blog post I will tell you about these favorite numbers of
mine. This post is not very technical, so you should be able to
follow along, even if you are not a mathematician.

## The Kaprekar Number 6174

Let us take a random number with four digits, say 3827.
Then we can sort the digits in ascending order and get 2378.
Sorting in descending order gives 8732.
If we subtract the larger number from the smaller number, we get:

$$
8732 - 2378 = 6354
$$

We can continue this process with the new number. Sorting it gives
3456 and 6543 and subtracting yields:

$$
6543 - 3456 = 3087
$$

What happens if we continue this process?
Do we get back to the starting number?
Do we hit all numbers with four digits?
Do we end up in a cycle and fluctuate between five numbers?
You can pause a moment here and think about what your intuition says.
But next, let's try it out.

$$
8730 - 378 = 8352\\
8532 - 2358 = 6174\\
7641 - 1467 = 6174\\
7641 - 1467 = 6174\\
...
$$

So for the starting number 3827 we end up with 6174.
What happens if we start from a different number, say 5553?

$$
5553 - 3555 = 1998\\
9981 - 1899 = 8082\\
8820 - 288 = 8532\\
8532 - 2358 = 6174
$$

And we are back at 6174.
It is a surprising fact, that for all numbers with four digits, except
1111, 2222, 3333, 4444, 5555, 6666, 7777, 8888, and 9999, we end up
with the Kaprekar number 6174.
That means that 6174 is a special number in some sense and appropriate
for a license plate.

Does this work for numbers with three digits? Or five digits? What
happens if we do not take numbers in base 10, but in a different base?
Fun questions to spice up your weekend.

## The Fermat Prime 257 
The number 257 is a prime number. That means that it has no divisors
except 1 and 257.
But, i hear you asking, what is so special about this prime number? 
Well, we can decompose it as $$257 = 2^{2^3}+1$$.

Any number of the form $$F_n = 2^{2^n}+1$$ is called Fermat
number.
If we look at these numbers, for the first few $$n$$, we get
$$1,3,17,267,65537$$.
These are all prime numbers.
But for $$n=5$$
we get $$2^{2^5}+1 = 4294967297 = 641 \cdot 6700417$$ which is not a
prime.
In fact, it is unknown if any Fermat number $$F_n$$ for $$n>4$$ is
prime.

Another nice fact about Fermat numbers, is that their binary
representation looks like $$1000...00001$$.
In particular, $$257$$ in binary is $$100000001$$.
This allows very fast exponentiation with 257.
As an example, if we want to compute $$a^{257}$$ for some number $$a$$
we would need 257 multiplications, in the naive case.
However, as $$a^{257} = a^{2^{2^4}+1} = (((a^2)^2)^2)^2 \cdot a$$, we
can compute it using only 5 multiplications!
This fact is often used in the signature and encryption algorithm RSA to
speed up the implementation.
The public key in the RSA algorithm is often set to $$65537=F_4$$.

All these facts make $$257$$ a nice prime number.
Whenever I wrote some number theoretic code and needed a
large prime number for a test case I used 257.

## The Smallest Carmichael Number 561
Fermat's little theorem says that for any prime number $$p$$ and any
number $$a$$ which is smaller than $$p$$ the following congruence relations holds:

$$a^{p-1} \equiv 1 \mod p$$

The modulo operation is just the usual remainder of the division with
$$p$$.
Let us make an example. As a prime we can use $$p=5$$, as $$a$$ we
can use any number, say $$a=2$$.
Then we get:

$$a^{p-1} = 2^{5-1} = 2^4 = 16 = 3\cdot 5 +1 \equiv 1\mod 5$$.
The last step is just division with remainder. 16 divided by 5 is 3 with
a remainder of 1.

How about the reverse way? Does Fermat's little theorem work for
numbers that are not prime?
Mostly not. (We will get to the exceptions later.) If we take $$9=3\cdot 3$$ as an example, and again $$a=2$$, then
$$2^{9-1} = 2^8 = 256 = 3\cdot 85 +2 \equiv 2 \mod 3$$.
For a prime number we would get 1 instead of 2. But 9 is not a prime,
and so we get 2.

This suggests that we could use Fermat's little theorem to create a
primality test. If we want to check if a number $$p$$ is prime, we could take some
$$a$$ and check if $$a^{p-1} \equiv 1 \mod p$$. The more different $$a$$ we take, the more certain we can be in the primality of the number $$p$$.

However, this does not always work. This test produces false
positives, i.e., there are numbers that are detected as primes by this
test, but which are not primes.
These are the exceptions I wrote about earlier.
These numbers are called Fermat pseudoprimes or simply Carmichael
numbers.
And -- you probably guessed it by the title of this section -- the smallest such number is 561.
The next Carmichael numbers are 1105, 1729, 2465, 2821, 6601, 8911,
10585, 15841, 29341, 41041, 46657, 52633. So one could get the
impression that they are sparse in some sense.
And as that number was not yet taken, it ended on my license plate.
