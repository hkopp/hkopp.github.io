---
layout: post
category: Lesson
tags : [math, whirlwind]
---
{% include math %}

## Intro
This post is a very fast tour through elementary number theory. It
consists mainly of lecture notes shrinked to a cheat
sheet.

## Linear Diophantine Equations
$$ax+by=c,\;a\neq 0,\;b\neq 0$$ (solvable $$\Leftrightarrow gcd(a,b)\mid c$$)

Let $$d:=gcd(a,b)=ax_0+by_0$$. Then the solutions are 
$$x=\frac{c}{d}x_0+\frac{b}{d}n,\;y=\frac{c}{d}y_0+\frac{a}{d}n$$ for all $$n\in\mathbb{Z}$$.

## Perfect Numbers
$$n\in\mathbb{N}$$ is called perfect $$:\Leftrightarrow n=\sum\limits_{d\geq 1,\;d|n}^{n-1}$$.  
$$\sigma(n):=\sum\limits_{d|n}d\Rightarrow n$$ is perfect $$\Leftrightarrow 2n=\sigma(n)$$.

$$n=p_1^{\alpha_1}\cdots p_r^{\alpha_r},\;p_i\in\mathbb{P}$$ for all $$i \Rightarrow \sigma(n)=\sigma(\prod\limits_{i=1}^{r}p_i^{\alpha_i})
=\prod\limits_{i=1}^{r}\sigma(p_i^{\alpha_i})=\prod\limits_{i=1}^{r}\frac{p_i^{\alpha_i+1}-1}{p_i-1}$$

$$n=2^{s-1}b$$ for all $$s\geq 2$$, b odd $$\Rightarrow$$ (n perfect $$\Leftrightarrow b\in\mathbb{P}\wedge b=2^s-1$$)

$$M_s:=2^s-1$$ for all $$s\geq 1$$: $$M_s$$ prime $$\Leftrightarrow: M_s$$ Mersenne prime $$\Rightarrow s\in\mathbb{P}$$

$$F_s:=2^s+1$$ for all $$s\geq 0$$: $$F_s$$ prime $$\Leftrightarrow: F_s$$ Fermat prime $$\Rightarrow \exists t\in\mathbb{N}:s=2^t$$ (Proof through geometric series)

## The Totient Function
$$\varphi(n):=\lvert \mathbb{Z}_n^*\rvert$$

Euler's theorem: $$k,n\in\mathbb{N}_{>0},\;gcd(k,n)=1\Rightarrow k^{\varphi(n)}\equiv 1\;(mod\;n)$$
(Proof by observation in $$\mathbb{Z}_n^*$$)

Fermat's little theorem: $$k\in\mathbb{N}_{>0},\;p\in\mathbb{P}\Rightarrow k^p\equiv k\;(mod\;p)$$.

It follows that $$n\in\mathbb{P}\Rightarrow\forall k\in\{1,\cdots ,n-1\}:k^{n-1}\equiv 1\;(mod\;n)$$.

$$n\in\mathbb{Z}$$ is called Carmichael number $$:\Leftrightarrow\;(\forall a:gcd(a,n)=1\Rightarrow a^{n-1}\equiv 1\;(mod\;n))$$

## Computing $$\varphi(n)$$
$$n=\sum\limits_{d|n}\varphi(d)$$

$$p\in\mathbb{P}\Rightarrow\varphi(p^\alpha)=p^\alpha-p^{\alpha-1}$$ (Proof by Induction)

$$\varphi(n\cdot m)=\varphi(n)\cdot\varphi(m)$$ (Proof by induction over $$n\cdot m$$)

Similar to chinese remainder theorem: $$\mathbb{Z}_{nm}^*\cong\mathbb{Z}_{n}^*\times\mathbb{Z}_{m}^*$$

and thus $$\varphi$$ can be computed as $$\varphi(n)=n\cdot\prod\limits_{p\mid n,\;p\in\mathbb{P}}(1-\frac{1}{p})$$


## Multiplicative Functions
$$\psi:\mathbb{N}_{>0}\rightarrow\mathbb{R}$$ is called multiplicative $$:\Leftrightarrow \psi(nm)=\psi(n)\cdot\psi(m)$$
for all $$n,m\in\mathbb{N}$$ with $$gcd(n,m)=1$$

$$\sigma,\varphi$$ multiplicative

$$
\begin{equation}
o(n):=\begin{cases}
    1,   & n=1\\
    0,   & else\\
  \end{cases}
\end{equation}
$$ is multiplicative

$$e(n):=1$$ multiplicative, $$i(n):=n$$ multiplicative

$$f$$ multiplicative $$\Rightarrow f(1)=1\vee \forall n\in\mathbb{N}:f(n)=0$$

$$f, g$$ multiplicative $$\Rightarrow (f\cdot g)(n):=f(n)\cdot g(n)$$ multiplicative

We define the Dirichlet convolution for $$f,g:\mathbb{N}\rightarrow\mathbb{R}$$ as $$(f\star g)(n):=\sum\limits_{d\mid n}f(d)\cdot g(\frac{n}{d})$$

Dirichlet convolution: commutative, associative, $$o$$ is the neutral element

$$f, g$$ multiplicative $$\Rightarrow f\star g$$ multiplicative

$$
\mu:\mathbb{N}\Rightarrow\mathbb{R},\;
\begin{equation}
\mu(n):=\begin{cases}
  (-1)^r, & n=p_{1}\cdots p_{r} \text{ is squarefree} \\
  0,      & \text{ else} \\
\end{cases}
\end{equation}
$$ is multiplicative.

$$f:\mathbb{N}_{>0}\rightarrow\mathbb{R},\;F(n):=\sum\limits_{d\mid n}f(d)=(f\star e)(n)$$ summation function of $$f$$

We have the following identities: 
$$\varphi\star e=i,\quad i\star e=\sigma,\quad\mu\star e=o,\quad o\star e=e,\quad \frac{\mu}{i}\star e=\frac{\varphi}{i}$$

$$e\star e=\tau,\;\tau(n):=\sum\limits_{d \mid n} 1$$ the number of factors

## The Diophantine Equation $$x^2+y^2=p$$
Every polynomial that is not constant has roots modulo infinitely many primes (Idea of proof $$f(p_{1}\cdots p_{r}a_0x)=a_0g$$)

$$p\in\mathbb{P}$$ odd: $$x^2+1\equiv 0\;(mod\;p)$$ is solvable $$\Leftrightarrow p\equiv 1\;(mod\;4)$$
(Idea of proof: $$1\equiv(\alpha^2)^{\frac{p-1}{2}}\;(mod\;p)$$)

It follows that there are infinitely many primes of the form $$p\equiv 1\;(mod\;4)$$.

## Wilson's Theorem
Let $$K$$ be a field, $$\alpha$$ a zero of $$f\in K[X]$$. Then $$\Rightarrow (\alpha-x)\mid f$$ (Idea of proof: $$f=q(x-\alpha)+r,\;deg(r)<deg(x-\alpha)$$)

$$f\in K[X],\;deg(f)=n\Rightarrow f$$ has at most $$n$$ zero points.

Wilson: $$p\in\mathbb{P}\Rightarrow (p-1)!\equiv -1\;(mod\;p)$$ (Idea of proof: $$x^{p-1}-1\in\mathbb{Z}_p[X]$$ and Euler)

## Primes as Sum of Two Square
$$x,y\in\mathbb{Z}$$ with $$x^2+y^2=p\in\mathbb{P}\setminus\{2\}\Rightarrow gcd(x,p)=gcd(y,p)=1$$

$$x,y\in\mathbb{Z}$$ with $$x^2+y^2=p\in\mathbb{P}\setminus\{2\}\Leftrightarrow p\equiv 1\;(mod\;4)$$ (Idea of proof: Fermat's theorem)

Thue's lemma: $$a\in\mathbb{Z}\;,n\in\mathbb{N}$$ is not square $$\Rightarrow ax\equiv y\;(mod\;n)$$ has a solution $$(x,y)\neq(0,0)$$ with $$\lvert x\rvert,\;\lvert y\rvert <\sqrt{(n)}$$

## The Group of Units in $$\mathbb{Z}_n$$
$$\lvert G\rvert=n$$

$$\forall d\mid n\exists\varphi(d)$$ elements of order d $$\Leftrightarrow$$ G is cyclic.

$$K$$ field, $$G\leq K^*$$ subgroup, $$\lvert G\rvert=n<\infty\Rightarrow$$ G is cyclic (Idea of proof: $$x^d-1$$)

$$a \in\mathbb{N}$$ is a primitive root modulo $$n:\Leftrightarrow\overline{a}\in\mathbb{Z}_n^*$$ with $$o(\overline{a})=\varphi(n)$$, 
therefore $$\mathbb{Z}_n^*=\langle\overline{a}\rangle$$

$$\alpha\geq 3 \Rightarrow$$ there is no primitive root modulo $$2^\alpha$$, since $$a^{2^{\alpha-2}}\equiv1\;(mod\;2^\alpha)$$

$$\forall p\in\mathbb{P}\setminus\{2\}:\;\mathbb{Z}_{p^\alpha},\;\mathbb{Z}_2,\;\mathbb{Z}_4\;\mathbb{Z}_{2p^\alpha}$$ is cyclic.

## The Legendre Symbol
$$a\in\mathbb{Z},\;gcd(a,n)=1$$ is called quadratic residue modulo $$n:\Leftrightarrow x^2\equiv a\;(mod\;n)$$ is solvable

$$\mathfrak{R}:=\{\overline{a}\in\mathbb{Z}_p^*:\, a$$ is a quadratic residue mod p$$\}\leq\mathbb{Z}_n^* \quad\lvert \mathfrak{R}\rvert=\frac{p-1}{2}$$

$$2\neq p\in\mathbb{P},\;a\in\mathbb{Z}$$

Definition of the Legendre symbol
$$
\begin{equation}
\left(\frac{a}{p}\right):=\begin{cases}
  1,&gcd(a,p)=1,\text{ a is a quadratic residue mod p}\\
  -1,&gcd(a,p)=1,\text{ a is a quadratic nonresidue mod p}\\
  \end{cases}
\end{equation}
$$

The Legendre symbol has the following properties:

- $$a\equiv b\;(mod\;p)\Rightarrow \left(\frac{a}{p}\right)=\left(\frac{b}{p}\right)$$
- $$\left(\frac{a\cdot b}{p}\right)=\left(\frac{a}{p}\right)\cdot\left(\frac{b}{p}\right)$$
- $$\left(\frac{a^2}{p}\right)=1\Leftrightarrow p\nmid a$$
- $$\left(\frac{2}{p}\right)=1\Leftrightarrow p=\pm 1\;(mod\;8)$$
- Euler: $$\left(\frac{a}{p}\right)=a^{\frac{p-1}{2}}$$

Law of Quadratic Reciprocity:
Let $$2\neq p,q\in\mathbb{P},\;p\neq q:$$. Then

$$
\begin{equation}
\left(\frac{p}{q}\right)\cdot\left(\frac{q}{p}\right)=(-1)^{\frac{p-1}{2}\cdot\frac{q-1}{2}}
=\begin{cases}
  -1,	&	p\equiv q\equiv 3\;(mod\;4)	\\
  1,	&	else \\
\end{cases}
\end{equation}
$$

$$\left(\frac{-1}{p}\right)=(-1)^{\frac{p-1}{2}}$$

$$
\begin{equation}
\left(\frac{2}{p}\right)=(-1)^{\frac{p^2-1}{8}}
=\begin{cases}
  1,	&	p=\pm1\;(mod\;8)	\\
  -1,	&	p=\pm3\;(mod\;8) \\
\end{cases}
\end{equation}
$$

## The Diophantine Equation $$x^2-my^2=\pm p$$
$$gcd(p,k,m)=1,\;2\neq p\in\mathbb{P},\;x^2-my^2=kp$$ solvable $$\Rightarrow(\frac{m}{p})=1$$

$$x^2+2y^2=p,\;2\neq p\in\mathbb{P}$$ solvable $$\Leftrightarrow p\equiv1,\;3\;(mod\;8)\Leftrightarrow(\frac{-1}{p})=1$$
