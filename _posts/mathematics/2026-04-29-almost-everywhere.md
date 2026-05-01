---
layout: post
title: "The notion of \"almost everywhere\" and the importance of completeness"
date: 2026-04-29 12:00:00 +0900
categories: [Mathematics]
tags: [Measure Theory, Probability Theory]
---

이 블로그의 첫 포스트로서, 앞으로 여러 글들에서 꾸준히 등장할 almost everywhere (확률론에서는 almost surely) 의 개념 및 중요성, 그리고 약간의 주의할 점을 가장 먼저 다루기로 한다.

가장 먼저 almost everywhere가 무엇인지부터 알아보자.

<div class="math-box box-def" markdown="1">
<span class="box-title">Definition (Almost Everywhere)</span>
Suppose a measure space $(X, \Sigma, \mu)$ is given. A property $P$ is said to hold *almost everywhere* (*a.e.*) in X if there exists $N \in \Sigma$ with $\mu(N) = 0$ and $x \in X\setminus N$ have the property $P$. If $(X, \Sigma, \mu)$ is a probability space (*i.e.* $\mu(X) = 1$), the property $P$ is said to hold *almost surely* (*a.s.*).
</div>

이것의 의미는 꽤나 분명하다; 어떤 성질이 null set을 제외한 공간 내 모든 점에서 성립한다면, 그것을 거의 모든 곳에서 (almost everywhere) 성립한다고 하겠다는 뜻이다. 

예를 들어 어떤 두 함수 $f$와 $g$가 거의 모든 곳에서 같은 상황을 생각할 수 있다. 이 개념이 가지는 가장 큰 강점은, 거의 모든 곳에서 같은 두 함수는 적분에 있어서 완전히 동등하다는 데에 있다. 이것은 다음의 Proposition을 통해 자세히 기술된다.

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

Let $N \in \Sigma$ be a null set s.t. $f(x) = g(x)$ for all $x \in X \setminus N$. Then

$$
\int |f - g| = \int_N |f-g| + \int_{X \setminus N} |f-g| = 0,
$$

since $N$ itself is a null set and $\|f - g\| = 0$ in $X \setminus N$.

**(b) $\Rightarrow$ (a)**

Let us define $E_n = \\{\|f - g\| > 1/n\\}$. Then

$$
0 \leq \frac{1}{n}\mu(E_n) \leq \int_{E_n} |f-g| \leq \int |f-g| = 0,
$$

so that $\mu(E_n) = 0$ for all $n \in \mathbb N$. Now define $E = \bigcup E_n = \\{\|f - g\| > 0\\}$, then

$$
\mu(E) = \lim_{m \to \infty} \mu\left(\bigcup_{j=1}^m E_j\right) = 0,
$$

by continuity from below.

**(b) $\Rightarrow$ (c)**

For any $E \in \Sigma$,

$$
\left| \int_E f - \int_E g \right| = \left| \int f \mathbf 1_E  - \int g \mathbf 1_E \right| \leq \int \mathbf 1_E |f - g| \leq \int |f - g| = 0 .
$$

**(c) $\Rightarrow$ (a)**

Let $h := f - g$. By assumption, $\int_E h = 0$ for all $E \in \Sigma$. We can decompose $h$ into its real and imaginary parts, and further into positive and negative parts:

$$h = (\Re(h)^+ - \Re(h)^-) + i(\Im(h)^+ - \Im(h)^-)$$

To show $\Re(h)^+ = 0$ *a.e.*, let us choose $E = \{ \Re(h) > 0 \}$. Since $\int_E h = 0$, its real part must also be zero:

$$\Re \left( \int_E h \right) = \int_E \Re(h) = \int_E \Re(h)^+ = 0$$

Since $\Re(h)^+ \geq 0$ on $X$, by the same argument used in **(b) $\Rightarrow$ (a)**, we get $\Re(h)^+ = 0$ *a.e.*

By similarly choosing appropriate sets (*e.g.*, $\{ \Re(h) < 0 \}$, $\{ \Im(h) > 0 \}$, $\{ \Im(h) < 0 \}$), we can easily show that $\Re(h)^- = 0$, $\Im(h)^+ = 0$, and $\Im(h)^- = 0$ *a.e.*

Therefore, $h = 0$ *a.e.*, which implies $f = g$ *a.e.*

</div>
</details>

우리는 어떤 함수에 대해 알아야 할 때, 그 함수로부터 얻어지는 적분값만이 잘 정의되면 충분한 경우가 많다.

- Banach space로서의 $L^p$ 공간을 다룰 때
- $\mathcal G$-measurable random variable로서의 conditional expectation $\mathbb E[X\|\mathcal G]$를 정의할 때
- ...

이러한 상황에서 Proposition 1은 거의 모든 곳에서만 정의된 함수만으로도 적분에 대한 (거의 모든) 논의가 가능하게 해준다.

그러나 이러한 "거의 모든 곳에서의 같음"은 사실 만능은 아니다. 앞서 Proposition 1에서 우리는 $f$와 $g$의 적분 가능성, 즉 measurability를 가정하였다. 이것은 $f$가 measurable이고 $f=g$ a.e.더라도 $g$가 measurable임이 보장되지 않기 때문이다.

가장 간단한 예시는 역시 Cantor set의 활용일 것이다. Cantor set에 대해서는 다음과 같이 잘 알려진 사실이 있다. <span class="txt-gray">(Cantor set이 무엇인지와 아래 Proposition이 왜 성립하는 지에 대해서는 일단 받아들이도록 하자.)</span>

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 2</span>
Consider a measure space $([0, 1], \mathcal B([0, 1]), \lambda)$ where $\mathcal B$ implies Borel $\sigma$-algebra and $\lambda$ is Lebesgue measure. Suppose $C \subset [0, 1]$ is Cantor set. Then,

1. $\|C\| = \mathfrak{c}$.
2. $C \in \mathcal B([0, 1])$ and $\lambda(C) = 0$.
3. There exists $A \subset C$ s.t. $A \notin \mathcal B([0, 1])$.
{: .thm-enum }
</div>

Proposition 2로부터 우리는 자연스럽게 두 함수

$$
f=0, \quad g=\mathbf 1_A
$$

를 생각할 수 있다. 이렇게 정의된 $f$와 $g$는 $A$를 제외한 모든 점에서, 즉 거의 모든 곳에서 같지만, $f$와 다르게 $g$는 Borel measurable이 아님을 쉽게 알 수 있다.

<span class="txt-red">**이것은 단순히 병리적인 예시가 아니다.**</span> Measure theory의 근간은 measurable function을 다루는 데에 있다. 위 사실은 만약 우리가 어떤 함수에 대해서 거의 모든 곳에서의 정보만 알 수 있다면, 그 함수는 우리가 다룰 수 있는 대상인지 조차 알 수 없다는 큰 위험성을 내포한다.

특히 우리는 함수 또는 확률 변수의 극한에 관심이 있는 경우가 많다. 그러나 대부분의 상황에서, 우리가 말할 수 있는 것은 오직 거의 모든 곳에서의 수렴이 최선이다. 이 때 거의 모든 곳에서의 수렴은 극한 함수의 measurability 조차 보장해주지 못한다.

그렇다면 이러한 문제는 어떻게 해결해야 할까? 그 방법에 대해 알아보기 전에, 먼저 measure space의 completeness에 대해서 정의해보자.

<div class="math-box box-def" markdown="1">
<span class="box-title">Definition (Complete Measure Space)</span>
A measure $\mu$ is said to be *complete* if the domain of $\mu$ contains all subsets of null sets.
</div>

실제로 많은 measure space는 complete하지 않다. Borel $\sigma$-algebra를 가지는 measure space가 그 대표적인 예시이다.

이것에 대한 이해를 돕기 위해 probability zero인 event를 생각해보자. 우리는 자연스럽게 그러한 event의 subset또한 여전히 probability zero일 것이라고 생각한다. 그러나 위 정의상 만약 probability의 completeness가 주어지지 않았다면, 그러한 subset은 measurable인지 조차 불분명해진다.

이렇듯 completeness는 우리의 직관을 유지하는 데에 필요해 보인다. Completeness는 더 나아가 **거의 모든 점에서 같은 함수들의 measurability를 보존한다**는 매우 중요한 성질을 함의한다. 구체적으로는 다음과 같이 기술할 수 있다.

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 3</span>

The following are equivalent:
1. $\mu$ is complete.
2. If $f$ is measurable and $f = g$ $\mu$-*a.e.*, then g is measurable.
3. If $f_n$ are measurable and $\lim f_n = f$ $\mu$-*a.e.*, then $f$ is measurable.
{: .thm-enum }
</div>


<details class="proof-container" markdown="1">
<summary>Proof of Proposition 3</summary>
<div class="proof-content" markdown="1">

**(a) $\Rightarrow$ (c)**

Define $E = \\{ f_n \to f \\}$. Then $E \in \Sigma$ since $\mu(E^c) = 0$ and $\mu$ is complete by the assumption.

Let $g_n = f_n \mathbf 1_E$ and $g = f \mathbf 1_E$. Then $g_n$ are clealy measurable and $g$ is also measurable since pointwise limit conserves measurability.

Now consider a fixed Borel measurable set $B$. Then,

$$
\begin{align}
f^{-1}(B) &= (f^{-1}(B) \cap E) \sqcup (f^{-1}(B) \cap E^c) \\
&= \underbrace{(g^{-1}(B) \cap E)}_{g \text{ measurable } \rightarrow \text{ measurable}} \sqcup \underbrace{(f^{-1}(B) \cap E^c)}_{\mu \text{-null set} \rightarrow \text{ measurable}}.
\end{align}
$$

Therefore, $f$ is measurable.

**(c) $\Rightarrow$ (b)**

Obvious from the sequence $f$, $f$, $f$, ... which converges to $g$.

**(b) $\Rightarrow$ (a)**

Suppose $F \subset E$ for a measurable $E$ which is of measure zero. Putting $f=0$ and $g=\mathbf 1_F$ concludes the proof.

</div>
</details>

<span class="txt-gray">
완비성은 확률 과정에 대한 이론에서도 매우 중요한 역할을 한다. 구체적으로, 주어진 filtration $(\mathcal F_t)$가 complete할 때 얻을 수 있는 중요한 성질은 다음과 같다:</span>

- Stochastic integral을 확률 과정으로서 다룰 수 있다.
- Hitting time이 stopping time이 된다.
{: .txt-gray }

<span class="txt-gray">이에 대해서는 차후에 다루도록 한다. </span>

<!-- 그러나 measure space의 completeness는 반드시 주어지는 것은 아니다. Completenes는 그 자체로 measure에 dependent하다. 이에 따라 다뤄야 하는 measure가 여러 개인 경우에는 어떤 measure롤 기준으로 completeness를 정해야 하는지에 대한 모호함이 남는다.
 -->

이렇듯 completeness는 우리가 거의 모든 곳에서 정의된 함수를 다루기 위해서 필수적이다. 그러나 measure space나 probability space를 다룰 때, 처음부터 completeness가 주어진다고 일반적으로 기대하기는 어렵다.

가장 중요한 이유는 completeness가 measure-dependent한 개념이기 때문이다. Null set의 subset을 포함하기 위해서는 먼저 null set이 무엇인지부터 명확히 해야 하는데, measure가 정해지지 않았다면 null set이 무엇인지 조차 알 수 없기 때문이다. 그리고 당연하게도, measure가 바뀌면 null set과 completeness가 모두 바뀌게 된다.

그러나 (모두는 아니지만) 많은 경우에 completeness는 문제가 되지 않는다. 다음 결과를 보자.

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 4</span>

Suppose that $(X, \Sigma, \mu)$ is a measure space. Let $\mathcal N = \\{ N \in \Sigma: \mu(N)=0 \\}$ and $\overline{\Sigma} = \\{E \cup F: E \in \Sigma \text{ and } F \subset N \text{ for some } N \in \mathcal N \\}$. Then $\overline{\Sigma}$ is a $\sigma$-algebra, and there is a unique extension $\overline \mu$ of $\mu$ to a complete measure on $\overline{\Sigma}$.

</div>

<details class="proof-container" markdown="1">
<summary>Proof of Proposition 4</summary>
<div class="proof-content" markdown="1">

**$\overline \Sigma$ is a $\sigma$-algebra.**

Consider $E, E_n \in \Sigma$ and $F, F_n \subseteq X$ such that there exist $N, N_n \in \mathcal N$ where $F \subseteq N$ and $F_n \subseteq N_n$. 

1. $\overline \Sigma$ is closed under countable unions.

    $$
    \bigcup_n (E_n \cup F_n) = (\bigcup_n E_n) \cup (\bigcup_n F_n) \text{, where } \bigcup_n F_n \subseteq \bigcup_n N_n \in \mathcal N.
    $$

2. $\overline \Sigma$ is closed under complements.

    $$
    (E \cup F)^c = E^c \cap F^c = \underbrace{(E^c \cap N^c)}_{\in \Sigma} \cup \underbrace{(N \setminus (E \cup F))}_{\subseteq N \in \mathcal N}.
    $$

Therefore, $\overline \Sigma$ is a $\sigma$-algebra.

**There exists an extension $\overline \mu$ of $\mu$ on $\overline \Sigma$.**

We now can define a measure on $(X, \overline \Sigma)$, namely, we can define $\overline \mu$ by

$$
\overline \mu(E \cup F) = \mu(E).
$$

Then it is clearly an extension of $\mu$ on $\overline \Sigma$.

We first need to verify that $\overline \mu$ is well-defined and is a measure on $\overline \Sigma$. Suppose $E \cup F = E' \cup F'$ where $E' \in \Sigma$ and $F' \subseteq N' \in \mathcal N$. Then

$$
\mu(E) \leq \mu(E' \cup N') \leq \mu(E') + \mu(N') = \mu(E').
$$

The symmetry gives that $\mu(E) = \mu(E')$, *i.e.*, $\overline \mu$ is well-defined.

Clearly $\overline \mu(\phi) = 0$. For disjoint $E_n \cup F_n$, it is clear that the following hold:

$$
\overline \mu(\bigcup_n (E_n \cup F_n)) = \mu(\bigcup_n E_n) = \sum_n \mu(E_n) = \sum_n \overline \mu(E_n \cup F_n).
$$

Thus, $\overline \mu$ is indeed a measure on $\overline \Sigma$.

**The extension is unique.**

Further, suppose $\nu$ is another extension. Then

$$
\mu(E) = \nu(E) \leq \nu(E \cup F) \leq \nu(E \cup N) \leq \nu(E) + \nu(N) = \nu(E) = \mu(E),
$$

which implies $\nu = \overline \mu$. Therefore, the extension is unique.

</div>
</details>

위 proposition은 임의의 measure space에는 그에 대응되는 completion이 존재함을 보여준다. 그러나 이것만으로는 우리가 원래 다루고자 했던 space에서 measurable인 함수를 찾는 데에는 약간 부족하다.

다음 결과는 더 나아가, completion $(X, \overline \Sigma, \overline \mu)$에 대해 measurable인 모든 함수는 original space $(X, \Sigma, \mu)$ 에서 measurable인 version이 존재함을 보여준다.

<div class="math-box box-thm" markdown="1">
<span class="box-title">Proposition 5</span>

Let $(X, \Sigma, \mu)$ be a measure space and $(X, \overline \Sigma, \overline \mu)$ be its completion. If $f$ is a $\overline \Sigma$-measurable function on $X$, then there is a $\Sigma$-measurable function $g$ such that $f=g$ $\mu$-a.e.

</div>

<details class="proof-container" markdown="1">
<summary>Proof of Proposition 5</summary>
<div class="proof-content" markdown="1">

It is obvious by definition of $\overline \Sigma$ when $f$ is an indicator function and so is for a simple $f$.

Now suppose $\(\phi_n\)$ is a sequence of $\overline \Sigma$-measurable simple functions converges to $f$. Then we can define $\(\tilde \psi_n \)$ where each $\tilde \psi_n$ is $\Sigma$ measurable and $\phi_n = \tilde \psi_n$ except on $F_n$ where $\overline \mu(F_n) = 0$.

Now choose $N\in \Sigma$ such that $\bigcup_n F_n \subseteq N$ and $\mu(N) = 0$. We can define a sequence of simple functions $\(\psi_n\)$ by

$$
\psi_n = \tilde \psi_n \mathbf 1_{N^c}.
$$

Define $g = \lim_n \psi_n$, then $g$ is $\Sigma$-measurable and $f = g$ a.e.

</div>
</details>

Proposition 4와 5는 completeness에 관련된 문제를 회피할 수 있는 방법이 된다. 예를 들어 measure space $(X, \Sigma, \mu)$가 (not necessarily complete) 주어져있고, 함수열 $(f_n)$ 이 $\Sigma$-measurable이며, $f_n \to f$ *a.e.* 라고 하자. 이 때 $f$는 $\Sigma$-measurable이 아닐 수 있다. 그러나 Proposition 4와 5를 통해서 우리는 자연스럽게 다음과 같이 문제를 회피할 수 있다.

1. $(X, \Sigma, \mu)$에 대한 completion $(X, \overline{\Sigma}, \overline{\mu})$을 생각한다. (Proposition 4)
2. $(X, \overline{\Sigma}, \overline{\mu})$ 에서 극한 함수 $\tilde f$를 생각한다. (극한이 ) 이 때 complete이므로 $\tilde f$는 $\overline{\Sigma}$-measurable이다.
3. $\tilde f$와 거의 모든 곳에서 같으면서 $\Sigma$-measurable인 함수 $f$를 구한다. (Proposition 5)

이러한 일련의 과정을 통해 우리는 극한 함수의 measurability를 크게 걱정하지 않고도 많은 논의를 이어갈 수 있다.

그러나 completion이 항상 정답인 것은 아니다. 예를 들어 우리는 주어진 함수와 거의 모든 곳에서 같으면서 연속인 함수를 구하고 싶을 때가 많다. 연속성은 각 점마다 만족해야 하는 성질로, 이것은 그 함수를 거의 모든 곳에서 아는 것만으로는 판단할 수 없다. 이와 같은 점별 논의가 필요한 상황에서는 completeness를 조심해서 다뤄야 할 필요가 있다.




<!-- <div class="math-box box-note" markdown="1">
<span class="box-title">Remark</span>
Note that the change of measure must satisfy the condition.
</div> -->