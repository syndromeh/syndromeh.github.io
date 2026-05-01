---
layout: post
title: "The Notion of 'Almost Everywhere' and the Importance of Completeness"
date: 2026-05-01 20:29:00 +0900
categories: [Mathematics]
tags: [Measure Theory]
---
* TOC
{:toc}

As the very first post on this blog, I want to tackle a concept that will appear constantly in our future discussions: *almost everywhere* (or *almost surely* in probability theory). I will walk through why the 'almost everywhere' concept is so useful, the hidden problems it can cause, and how 'completeness' offers a natural fix.

## The Power of "Almost Everywhere"

Let's start by defining what "almost everywhere" actually means.

<div class="math-box box-def" markdown="1">
<span class="box-title">Definition (Almost Everywhere)</span>
Suppose a measure space $(X, \Sigma, \mu)$ is given. A property $P$ is said to hold *almost everywhere* (*a.e.*) in X if there exists $N \in \Sigma$ with $\mu(N) = 0$ and every $x \in X\setminus N$ has the property $P$. If $(X, \Sigma, \mu)$ is a probability space (*i.e.* $\mu(X) = 1$), the property $P$ is said to hold *almost surely* (*a.s.*).
</div>

The intuition here is quite clear: if a property holds at every point in a space except for a set of measure zero (a null set), we say it holds *almost everywhere*.

For instance, consider two functions $f$ and $g$ that are equal almost everywhere. The greatest strength of this concept is that two such functions are completely indistinguishable from the perspective of integration. This equivalence is detailed in the following proposition.

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 1</span>
Suppose $f:X \to \mathbb C$ and $g:X \to \mathbb C$ are integrable, *i.e.*, $f$ and $g$ are both measurable and $\int |f|$ and $\int |g|$ are finite. Then the following are equivalent:

1. $f = g$ *a.e.*
2. $\int \|f - g\| = 0$.
3. $\int_E f = \int_E g$ for all $E \in \Sigma$.
{: .thm-enum }

</div>

<details class="proof-container" markdown="1">
<summary>Proof of Proposition 1</summary>
<div class="proof-content" markdown="1">

**(a) $\Rightarrow$ (b)**

Let $N \in \Sigma$ be a null set such that $f(x) = g(x)$ for all $x \in X \setminus N$. Then

$$
\int |f - g| = \int_N |f-g| + \int_{X \setminus N} |f-g| = 0,
$$

since $N$ is a null set and $\|f - g\| = 0$ on $X \setminus N$.

**(b) $\Rightarrow$ (a)**

Let us define $E_n = \\{\|f - g\| > 1/n\\}$. Then

$$
0 \leq \frac{1}{n}\mu(E_n) \leq \int_{E_n} |f-g| \leq \int |f-g| = 0,
$$

which implies $\mu(E_n) = 0$ for all $n \in \mathbb N$. If we define $E = \bigcup E_n = \\{\|f - g\| > 0\\}$, then

$$
\mu(E) = \lim_{m \to \infty} \mu\left(\bigcup_{j=1}^m E_j\right) = 0,
$$

by the continuity of measure from below.

**(b) $\Rightarrow$ (c)**

For any $E \in \Sigma$,

$$
\left| \int_E f - \int_E g \right| = \left| \int f \mathbf 1_E  - \int g \mathbf 1_E \right| \leq \int \mathbf 1_E |f - g| \leq \int |f - g| = 0 .
$$

**(c) $\Rightarrow$ (a)**

Let $h := f - g$. By assumption, $\int_E h = 0$ for all $E \in \Sigma$. We can decompose $h$ into its real and imaginary parts, and further into positive and negative parts:

$$h = (\Re(h)^+ - \Re(h)^-) + i(\Im(h)^+ - \Im(h)^-)$$

To show $\Re(h)^+ = 0$ *a.e.*, let us choose $E = \\{ \Re(h) > 0 \\}$. Since $\int_E h = 0$, its real part must also be zero:

$$\Re \left( \int_E h \right) = \int_E \Re(h) = \int_E \Re(h)^+ = 0$$

Since $\Re(h)^+ \geq 0$ on $X$, by the same argument used in **(b) $\Rightarrow$ (a)**, we get $\Re(h)^+ = 0$ *a.e.*

By similarly choosing appropriate sets (*e.g.*, $\\{ \Re(h) < 0 \\}$, $\\{ \Im(h) > 0 \\}$, $\\{ \Im(h) < 0 \\}$), we can easily show that $\Re(h)^- = 0$, $\Im(h)^+ = 0$, and $\Im(h)^- = 0$ *a.e.*

Therefore, $h = 0$ *a.e.*, which implies $f = g$ *a.e.*

</div>
</details>

Often, when working with functions, we only really care about their integrals being well-defined. Examples include:

- Treating $L^p$ spaces as Banach spaces.
- Defining the conditional expectation $\mathbb E[X\|\mathcal G]$ as a $\mathcal G$-measurable random variable.
- ...

In these contexts, Proposition 1 allows us to comfortably carry out our analysis using functions that are only defined *almost everywhere*.

## The Pitfall: The Loss of Measurability

However, this "almost everywhere equality" isn't a silver bullet. Note that in Proposition 1, we explicitly assumed the measurability (integrability) of both $f$ and $g$. If $f$ is measurable and $f = g$ a.e., it doesn't automatically guarantee that $g$ is measurable.

A classic example illustrating this involves the Cantor set. Let's recall a few well-known facts about it <span class="txt-gray">(we'll take these for granted for now, along with the definition of the Cantor set itself)</span>:

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 2</span>
Consider the measure space $([0, 1], \mathcal B([0, 1]), \lambda)$, where $\mathcal B([0, 1])$ denotes the Borel $\sigma$-algebra and $\lambda$ is the Lebesgue measure. Let $C \subset [0, 1]$ be the Cantor set. Then,

1. $\|C\| = \mathfrak{c}$.
2. $C \in \mathcal B([0, 1])$ and $\lambda(C) = 0$.
3. There exists a subset $A \subset C$ such that $A \notin \mathcal B([0, 1])$.
{: .thm-enum }
</div>

From Proposition 2, we can naturally construct two functions:

$$
f=0, \quad g=\mathbf 1_A
$$

It is easy to see that $f$ and $g$ are equal everywhere except on $A$ (which is a subset of a null set, hence they are equal almost everywhere). Yet, unlike $f$, the function $g$ is fundamentally not Borel measurable.

<span class="txt-red">**This is not just a pathological counterexample.**</span> Measure theory is nothing but about the manipulation of measurable functions. The danger here is that if we only know a function's behavior *almost everywhere*, we might not even be able to tell if it's a valid mathematical object in our space.

This becomes especially problematic when we look at limits of functions or random variables. In many cases, the best we can guarantee is pointwise convergence *almost everywhere*. But as we've seen, this type of convergence doesn't even guarantee the measurability of the limit function.

## The Importance of "Completeness"

So, how do we fix this? Before looking at the solution, let's establish the concept of a *complete* measure space.

<div class="math-box box-def" markdown="1">
<span class="box-title">Definition (Complete Measure Space)</span>
A measure $\mu$ is said to be *complete* if the domain of $\mu$ contains all subsets of null sets.
</div>

In reality, many measure spaces are incomplete. A standard measure space equipped with the Borel $\sigma$-algebra is a prime example.

To build intuition for the notion of completeness, think about an event with a probability of zero. Naturally, we expect that any sub-event (subset) should also have a probability of zero. However, without completeness baked into the definition, we can't even say whether that subset is an "event" (*i.e.*, measurable) in the first place. Completeness is essential for keeping our mathematical intuition intact.

Furthermore, it implies a very powerful property: **it preserves the measurability of functions that are equal almost everywhere.** Specifically, we can state the following:

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 3</span>

The following are equivalent:
1. $\mu$ is complete.
2. If $f$ is measurable and $f = g$ $\mu$-*a.e.*, then $g$ is measurable.
3. If $(f_n)$ is a sequence of measurable functions and $\lim f_n = f$ $\mu$-*a.e.*, then $f$ is measurable.
{: .thm-enum }
</div>

<details class="proof-container" markdown="1">
<summary>Proof of Proposition 3</summary>
<div class="proof-content" markdown="1">

**(a) $\Rightarrow$ (c)**

Define $E = \\{ f_n \to f \\}$. Then $E \in \Sigma$ since $\mu(E^c) = 0$ and $\mu$ is complete by assumption.

Let $g_n = f_n \mathbf 1_E$ and $g = f \mathbf 1_E$. Then each $g_n$ is clearly measurable, and $g$ is also measurable since the pointwise limit of measurable functions preserves measurability.

Now consider any Borel set $B$. Then,

$$
\begin{align}
f^{-1}(B) &= (f^{-1}(B) \cap E) \sqcup (f^{-1}(B) \cap E^c) \\
&= \underbrace{(g^{-1}(B) \cap E)}_{g \text{ measurable } \rightarrow \text{ measurable}} \sqcup \underbrace{(f^{-1}(B) \cap E^c)}_{\mu \text{-null set} \rightarrow \text{ measurable}}.
\end{align}
$$

Therefore, $f$ is measurable.

**(c) $\Rightarrow$ (b)**

This is obvious by considering the constant sequence $f_n = f$, which converges to $g$ $\mu$-*a.e.*

**(b) $\Rightarrow$ (a)**

Let $E \in \Sigma$ be a set of measure zero, and suppose $F \subset E$. Setting $f=0$ and $g=\mathbf 1_F$ concludes the proof.

</div>
</details>

As we can see, completeness is indispensable when dealing with functions defined almost everywhere. However, we can't always expect a measure or probability space to be complete out of the box.

The main reason is that completeness is heavily measure-dependent. To include all subsets of null sets, we first need to identify what the null sets are. If the measure hasn't been fixed, the null sets—and thus the requirements for completeness—remain unknown. Naturally, if the measure changes, the null sets and completeness properties change with it.

<div class="math-box box-note" markdown="1">
<span class="box-title">Remark</span>
Completeness also plays a pivotal role in the theory of stochastic processes. Specifically, when a given filtration $(\mathcal F_t)$ is complete, we gain crucial properties:

- Stochastic integrals can be rigorously treated as stochastic processes.
- Hitting times become valid stopping times.

We will explore these topics in future posts.
</div>

## A Natural Fix: Measure Completion

Fortunately, in many (though not all) situations, this lack of initial completeness isn't a dealbreaker. Consider the following result:

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 4</span>

Let $(X, \Sigma, \mu)$ be a measure space. Let $\mathcal N = \\{ N \in \Sigma: \mu(N)=0 \\}$ and define $\overline{\Sigma} = \\{E \cup F: E \in \Sigma \text{ and } F \subset N \text{ for some } N \in \mathcal N \\}$. Then $\overline{\Sigma}$ is a $\sigma$-algebra, and there exists a unique extension $\overline \mu$ of $\mu$ to a complete measure on $\overline{\Sigma}$.

</div>

<details class="proof-container" markdown="1">
<summary>Proof of Proposition 4</summary>
<div class="proof-content" markdown="1">

**$\overline \Sigma$ is a $\sigma$-algebra.**

Let $E, E_n \in \Sigma$ and $F, F_n \subseteq X$ such that there exist $N, N_n \in \mathcal N$ satisfying $F \subseteq N$ and $F_n \subseteq N_n$. 

1. $\overline \Sigma$ is closed under countable unions.

    $$
    \bigcup_n (E_n \cup F_n) = \left(\bigcup_n E_n\right) \cup \left(\bigcup_n F_n\right) \text{, where } \bigcup_n F_n \subseteq \bigcup_n N_n \in \mathcal N.
    $$

2. $\overline \Sigma$ is closed under complements.

    $$
    (E \cup F)^c = E^c \cap F^c = \underbrace{(E^c \cap N^c)}_{\in \Sigma} \cup \underbrace{(N \setminus (E \cup F))}_{\subseteq N \in \mathcal N}.
    $$

Therefore, $\overline \Sigma$ is a $\sigma$-algebra.

**There exists an extension $\overline \mu$ of $\mu$ on $\overline \Sigma$.**

We define a set function $\overline \mu$ on $\overline \Sigma$ by

$$
\overline \mu(E \cup F) = \mu(E).
$$

By setting $F = \emptyset$, it is clear that $\overline \mu$ is an extension of $\mu$ to $\overline \Sigma$.

We must first verify that $\overline \mu$ is well-defined. Suppose $E \cup F = E' \cup F'$ for some $E' \in \Sigma$ and $F' \subseteq N' \in \mathcal N$. Then

$$
\mu(E) \leq \mu(E' \cup N') \leq \mu(E') + \mu(N') = \mu(E').
$$

By symmetry, we also have $\mu(E') \leq \mu(E)$, yielding $\mu(E) = \mu(E')$. Thus, $\overline \mu$ is well-defined.

Clearly, $\overline \mu(\emptyset) = 0$. For a sequence of pairwise disjoint sets $(E_n \cup F_n)$ in $\overline \Sigma$, the sets $E_n$ are also pairwise disjoint, yielding:

$$
\overline \mu\left(\bigcup_n (E_n \cup F_n)\right) = \mu\left(\bigcup_n E_n\right) = \sum_n \mu(E_n) = \sum_n \overline \mu(E_n \cup F_n).
$$

Thus, $\overline \mu$ is indeed a measure on $\overline \Sigma$.

**The extension is unique.**

Suppose $\nu$ is another complete measure on $\overline \Sigma$ that extends $\mu$. Then

$$
\mu(E) = \nu(E) \leq \nu(E \cup F) \leq \nu(E \cup N) \leq \nu(E) + \nu(N) = \nu(E) = \mu(E),
$$

which implies $\nu(E \cup F) = \mu(E) = \overline \mu(E \cup F)$. Therefore, the extension is unique.

</div>
</details>

This proposition shows that any arbitrary measure space can be uniquely extended to a completion. But this alone doesn't entirely help us find measurable functions in our *original* space.

The next result bridges that gap, demonstrating that any function measurable in the completed space has a "version" that is measurable in the original space.

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 5</span>

Let $(X, \Sigma, \mu)$ be a measure space and $(X, \overline \Sigma, \overline \mu)$ be its completion. If $f$ is a $\overline \Sigma$-measurable function on $X$, then there exists a $\Sigma$-measurable function $g$ such that $f=g$ $\mu$-*a.e.*

</div>

<details class="proof-container" markdown="1">
<summary>Proof of Proposition 5</summary>
<div class="proof-content" markdown="1">

The claim follows directly from the definition of $\overline \Sigma$ when $f$ is an indicator function, and consequently, it also holds for any simple function.

Now, suppose $(\phi_n)$ is a sequence of $\overline \Sigma$-measurable simple functions converging pointwise to $f$. By the result for simple functions, for each $n$, we can find a $\Sigma$-measurable simple function $\tilde \psi_n$ such that $\phi_n = \tilde \psi_n$ except on a set $F_n$ with $\overline \mu(F_n) = 0$.

By the definition of the completion, we can choose a null set $N \in \Sigma$ such that $\bigcup_n F_n \subseteq N$ and $\mu(N) = 0$. We define a new sequence of $\Sigma$-measurable functions $(\psi_n)$ by

$$
\psi_n = \tilde \psi_n \mathbf 1_{N^c}.
$$

Define $g = \lim_{n \to \infty} \psi_n$. Then $g$ is clearly $\Sigma$-measurable. Since $\psi_n = \phi_n$ on $N^c$ and $\phi_n \to f$, it follows that $g = f$ on $N^c$. Thus, $f = g$ $\mu$-*a.e.*

</div>
</details>

Propositions 4 and 5 give us a robust workaround for completeness issues. For example, suppose we have a (not necessarily complete) measure space $(X, \Sigma, \mu)$ and a sequence of $\Sigma$-measurable functions $(f_n)$ such that $f_n \to f$ *a.e.* The limit function $f$ might not be $\Sigma$-measurable. But we can seamlessly bypass this limitation:

1. Consider the completion $(X, \overline{\Sigma}, \overline{\mu})$ of our original space (via Proposition 4).
2. Treat the limit function $f$ within this completed space. Because the space is complete, $f$ is guaranteed to be $\overline{\Sigma}$-measurable.
3. Find a $\Sigma$-measurable function $g$ that is almost everywhere equal to $f$ (via Proposition 5).

Through this sequence of steps, we can carry out most of our analysis without constantly worrying about the measurability of limit functions.


<div class="math-box box-note" markdown="1">
<span class="box-title">Caveat</span>
Completion isn't a universal remedy. For instance, we often want to find a **continuous function** that is equal to a given function almost everywhere. Continuity is a pointwise property; it cannot be determined solely by looking at a function's behavior *almost everywhere*. In situations where pointwise properties matter, we must handle completeness and almost-everywhere equivalence with extreme care.
</div>

<div class="bibliography" markdown="1">

## References
{: .no_toc }

1. Folland, G. B. (1999). *Real Analysis: Modern Techniques and Their Applications* (2nd ed.). John Wiley & Sons.
2. Rudin, W. (1987). *Real and Complex Analysis* (3rd ed.). McGraw-Hill.

</div>