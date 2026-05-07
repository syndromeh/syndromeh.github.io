---
layout: post
title: "A Distributional Helmholtz Decomposition for Probability-Weighted Velocities"
date: 2026-05-07 13:58:00 +0900
categories: [Mathematics]
tags: [Functional Analysis, Mathematical Physics]
---
* TOC
{:toc}

Suppose a random process $(X_t)_{t \in [0, T]}$ is governed by the following ODE:

$$
\dot X_t = v_t(X_t).
$$

Let $\\{ p_t \\}_{t \in [0, T]}$ be its marginal density flow. It is a well-known fact that the evolution of this marginal flow is tied to the drift $v_t$ via the PDE:

$$
\partial_t p_t + \nabla \cdot (p_t v_t) = 0,
$$

which is known as the *continuity equation*.

Interestingly, the drift $v_t$ is not uniquely determined by the marginal flow $\\{p_t\\}$. In fact, any alternative drift $\tilde v_t$ will yield the exact same $\\{p_t\\}$ as long as $\nabla \cdot (p_t (v_t - \tilde v_t)) = 0$.

In this note, we will explore this non-uniqueness of $v_t$ and discuss its canonical (in some sense) representation.

## Preliminaries: Function Spaces and $\rho$-Solenoidal Fields
### Density-weighted $L^2$ space

Let $\Omega \subseteq \mathbb R^d$ be an open set. Let

$$
\rho: \Omega \to [0, \infty)
$$

be a measurable function such that

$$
0 < \int_\Omega \rho(x) dx < \infty.
$$

In probability applications, one ususally has

$$
\int_\Omega \rho(x)dx = 1.
$$

Define the density-weighted velocity space

$$
L^2_\rho (\Omega; \mathbb R^d) := \left\{ v: \Omega \to \mathbb R^d : \int_\Omega |v(x)|^2 \rho(x) dx < \infty \right\}.
$$

Conventionally, two vector fields are identified if they agree $\rho(x)dx$-a.e.

The inner product on $L^2_\rho (\Omega; \mathbb R^d)$ is

$$
\langle v, w \rangle_\rho = \int_\Omega v(x) \cdot w(x) \rho(x) dx.
$$

The induced norm is

$$
\| v \|^2_{L^2_\rho} = \int_\Omega |v(x)|^2 \rho(x) dx.
$$

Equivalently, if $\mu(dx) = \rho(x) dx$, then

$$
L^2_\rho(\Omega; \mathbb R^d) = L^2(\mu; \mathbb R^d).
$$

Thus $L^2_\rho$ is a Hilbert space.

### The gradient-closure space

Define the *test-gradient space*

$$
\mathcal G_{\rho,0}
:=
\left\{
\nabla \varphi : \varphi \in C_c^\infty(\Omega)
\right\}
\subseteq
L_\rho^2(\Omega; \mathbb R^d),
$$

where $C^\infty_c (\Omega)$ denotes the space of smooth and compactly supported functions.

This inclusion is well-defined. Indeed, if $\varphi \in C_c^\infty(\Omega)$, then $\nabla \varphi$ is bounded and compactly supported. Therefore,

$$
\int_\Omega \rho |\nabla \varphi|^2 dx
\le
\|\nabla \varphi\|_\infty^2
\int_\Omega \rho dx
< \infty.
$$

Thus $\nabla \varphi \in L^2_\rho(\Omega; \mathbb R^d)$.

The space $\mathcal G_{\rho,0}$ is a vector space, since $C_c^\infty(\Omega)$ is a vector space and the gradient operator is linear.

Now define

$$
\mathcal G_\rho
:=
\overline{\mathcal G_{\rho,0}}^{L^2_\rho},
$$

where the closure is taken with respect to the $L^2_\rho$ norm.

Equivalently, $g \in \mathcal G_\rho$ if and only if there exists a sequence $\varphi_n \in C_c^\infty(\Omega)$ such that

$$
\nabla \varphi_n \to g
\quad \text{in } L_\rho^2.
$$

Since $\mathcal G_\rho$ is the closure of a vector subspace in $L_\rho^2$, it is also a closed vector subspace of $L^2_\rho(\Omega; \mathbb R^d)$.


### The $\rho$-solenoidal fields

In this section, we want to make precise what it means for $u \in L^2_\rho(\Omega; \mathbb R^d)$ to be $\rho$-solenoidal, that is, what it means to write

$$
\nabla \cdot (\rho u) = 0.
$$

If $\rho u$ were sufficiently smooth, this equation could be understood in the classical pointwise sense. But in our setting, $u$ is only an element of the Hilbert space $L^2_\rho$, so neither $u$ nor $\rho u$ should be expected to have classical derivatives. Hence the expression $\nabla \cdot (\rho u)$ must be understood weakly, or more precisely, in the [distributional](#rmk-distribution) sense.

Since $u \in L_\rho^2$ and $\int_\Omega \rho < \infty$, we have $\rho u \in L^1 (\Omega; \mathbb R^d)$ by Cauchy-Schwarz:

$$
\int_\Omega \rho |u| dx \leq \left( \int_\Omega \rho |u|^2 dx \right)^{1/2} \left( \int_\Omega \rho dx \right)^{1/2} < \infty.
$$

From this result, we can define the distributional divergence of $\rho u$ by duality:

$$
\langle \nabla \cdot (\rho u), \varphi \rangle := - \langle \rho u, \nabla \varphi \rangle = - \int_\Omega \rho u \cdot \nabla \varphi dx
$$

for every scalar test function $\varphi \in C^\infty_c (\Omega)$. We are now ready to define $\rho$-solenoidal fields.

<div class="math-box box-def" id="def-solenoidal" markdown="1">
<span class="box-title">Definition. ($\rho$-Solenoidal Fields)</span>
Let $u \in L^2_\rho (\Omega; \mathbb R^d)$. We say that $u$ is $\rho$-solenoidal if

$$
\int_\Omega \rho(x) u(x) \cdot \nabla \varphi(x) dx = 0
$$

for every test function $\varphi \in C^\infty_c (\Omega)$.
</div>

This means exactly $\nabla \cdot (\rho u) = 0$ in the distributional sense. 

Note that <span class="txt-red">**the $\rho$-solenoidality of $u$ naturally implies that $u$ is orthogonal to $\mathcal G_{\rho, 0}$ in $L_\rho^2$**</span>. By the continuity of the inner product, <span class="txt-red">**this orthogonality readily extends to the closure $\mathcal G_\rho$**</span>. This observation forms the core of this note, and we will revisit it shortly.

<div class="math-box box-note" id="rmk-distribution" markdown="1">
<span class="box-title">Remark 1. (What does *distribution* mean?)</span>

The object $\nabla \cdot (\rho u)$ does not seem to be a standard (probability) distribution function induced by some probability measure. Here, the term *distribution* means a generalized function defined by how it acts on smooth test functions that are compactly supported. 

Specifically, distributions are linear functionals that map a set of *test functions* into the real numbers. In our case, the test function space of interest is $C_c^\infty (\Omega)$, whose members are smooth and compactly supported.

In this sense, we take $\Omega \subseteq \mathbb R^d$ to be open. This is convenient for the following reasons:

1. Derivatives of test functions are well-defined inside $\Omega$.
2. We do not need to specify how test functions behave on the boundary $\partial \Omega$.

Therefore, when we write $\nabla \cdot (\rho u)=0$ in the distributional sense, we mean that it vanishes after being tested against every function in $C_c^\infty(\Omega)$.

</div>

<div class="math-box box-note" id="rmk-classical-divergence" markdown="1">
<span class="box-title">Remark 2. (Relation to classical divergence-free fields)</span>
If sufficient regularity of $\rho u$ is further assumed, then **the $\rho$-solenoidality of $u$ precisely implies**

$$
\nabla \cdot (\rho u) = 0 \quad \text{a.e.}
$$

in the usual sense. For example, suppose 

$$
\rho u \in W_\text{loc}^{1, 1}(\Omega; \mathbb R^d),
$$

that is, $\rho u$ and its weak derivatives are locally integrable. Then $\nabla \cdot (\rho u)$ is not just an element of $(C_c^\infty (\Omega))^\ast$, the dual space of $C_c^\infty(\Omega)$; it is indeed represented by a function in $L_\text{loc}^1$. 

Thus, in this case, we can identify the distributional condition with the *a.e.* statement $\nabla \cdot (\rho u)=0$.
</div>

## Orthogonal Projections in Hilbert Spaces

Before proceeding, it will be helpful to recall a well-known property about Hilbert spaces.

<div class="math-box box-thm" id="lem-orthogonal-projection" markdown="1">
<span class="box-title">Lemma 1. (Orthogonal Projection onto a Closed Subspace)</span>
Let $H$ be a Hilbert space and $M$ be a closed linear subspace. Then for every $v \in H$, there exist unique elements

$$
g \in M, \quad u \in M^\perp
$$

such that 

$$
v = g + u.
$$

Moreover,

$$
g =  \underset{h \in M}{\text{argmin}}\| v - h \|_H.
$$

</div>

<details class="proof-container" markdown="1">
<summary>Proof of Lemma 1</summary>
<div class="proof-content" markdown="1">

**Step 1. Existence of a minimizer**

Let $\delta := \inf_{h \in M} \\| v - h \\|_H$ and choose a minimizing sequence $(h_n) \subseteq M$ such that

$$
\| v - h_n \|_H \to \delta.
$$

Our claim is that $(h_n)$ is Cauchy. Specifically, the following equalities are true:

$$
\|h_n - h_m\|_H^2 = \|v - h_n\|_H^2 + \|v - h_m\|_H^2 - 2 \langle v - h_n, v - h_m \rangle_H
$$

and

$$
4 \left\| v - \frac{h_n + h_m}{2} \right\|_H^2 = \|v - h_n\|_H^2 + \|v - h_m\|_H^2 + 2 \langle v - h_n, v - h_m \rangle_H.
$$

Therefore,

$$
\|h_n - h_m\|_H^2 = 2\|v - h_n\|_H^2 + 2\|v - h_m\|_H^2 - 4 \left\| v - \frac{h_n + h_m}{2} \right\|_H^2.
$$

<span class="txt-gray">This identity is also referred to as the *parallelogram law*.</span>

Since $M$ is a vector space,

$$
\frac{h_n + h_m}{2} \in M.
$$

Therefore,

$$
\left\| v - \frac{h_n + h_m}{2} \right\|_H \geq \delta.
$$

Hence,

$$
\|h_n - h_m\|_H^2 \leq 2\|v - h_n\|_H^2 + 2\|v - h_m\|_H^2 - 4\delta^2.
$$

Letting $n, m \to \infty$, the right-hand side tends to $0$. Thus $(h_n)$ is Cauchy.

Since $H$ is complete and $M$ is closed, there exists $g \in M$ such that

$$
h_n \to g \quad \text{in } H.
$$

By continuity of the norm,

$$
\|v - g\|_H = \delta.
$$

Thus $g$ is a minimizer.

**Step 2. Orthogonality of the remainder**

Define $u := v - g$.

Our claim is that $u \in M^\perp$. For any $h \in M$ and $t \in \mathbb R$, we have $g + th \in M$. Since $g$ minimizes $h \mapsto \\| v-h \\|_H^2$ over $M$,

$$
\|v - g - th\|_H^2 = \|u - th\|_H^2 = \|u\|_H^2 - 2t\langle u, h \rangle_H + t^2\|h\|_H^2
$$

has a minimum at $t = 0$. Therefore,

$$
\langle u, h \rangle_H = 0.
$$

Since $h \in M$ was arbitrary,

$$
u \in M^\perp.
$$

Thus

$$
v = g + u, \quad g \in M, \quad, u \in M^\perp.
$$

**Step 3. Uniqueness of the minimizer**

Suppose

$$
v = g_1 + u_1 = g_2 + u_2,
$$

where $g_1,\,g_2 \in M$ and $u_1,\,u_2 \in M^\perp$. Then

$$
g_1 - g_2 = u_1 - u_2.
$$

The left-hand side belongs to $M$ and the right-hand side belongs to $M^\perp$. Hence

$$
g_1 - g_2 \in M \cap M^\perp = \{0\}.
$$

This completes the proof.

</div>
</details>

This result is also referred to as the *Hilbert projection theorem*.

## Distributional Helmholtz Decomposition

We now apply [Lemma 1](#lem-orthogonal-projection) to the density-weighted velocity space $L_\rho^2(\Omega; \mathbb R^d)$.

<div class="math-box box-thm" id="thm-helmholtz-decomposition" markdown="1">
<span class="box-title">Theorem 2. (Distributional Helmholtz Decomposition)</span>

Let $\Omega \subseteq \mathbb R^d$ open and let $\rho \in L^1 (\Omega)$ satisty

$$
\rho \geq 0, \quad 0 < \int_\Omega \rho(x) dx < \infty.
$$

Define

$$
\mathcal G_{\rho, 0} = \{\nabla \varphi: \varphi \in C_c^\infty (\Omega)\}, \quad \mathcal G_\rho = \overline{\mathcal G_{\rho, 0}}^{L_\rho^2}.
$$

Then for every

$$
v \in L_\rho^2 (\Omega; \mathbb R^d),
$$

there exist unique vector fields

$$
g \in \mathcal G_\rho, \quad u \in \mathcal G_\rho^\perp
$$

such that $v = g + u$.

Moreover,

$$
\int_\Omega \rho u \cdot \nabla \varphi dx = 0 \quad \forall \varphi \in C_c^\infty (\Omega).
$$

Equivalently, $\nabla \cdot (\rho u) = 0$ in the distributional sense.
</div>

<details class="proof-container" markdown="1">
<summary>Proof of Theorem 2</summary>
<div class="proof-content" markdown="1">

The space

$$
L_\rho^2 (\Omega; \mathbb R^d)
$$

is a Hilbert space. By definition,

$$
\mathcal G_\rho = \overline{\mathcal G_{\rho, 0}}^{L_\rho^2}
$$

is a closed linear subspace.

By applying [Lemma 1](#lem-orthogonal-projection), we obtain unique

$$
g \in \mathcal G_\rho, \quad u \in \mathcal G_\rho^\perp
$$

such that $v = g + u$. Since $u \in \mathcal G_\rho^\perp$, we have

$$
\langle u, h \rangle_\rho = 0 \quad \forall h \in \mathcal G_\rho.
$$

In particular, for every $\varphi \in C_c^\infty (\Omega)$, $\nabla \varphi \in \mathcal G_\rho$. Therefore,

$$
\int_\Omega \rho u \cdot \nabla \varphi dx = \langle u, \nabla \varphi \rangle_\rho = 0.
$$

This completes the proof.

</div>
</details>

The implication of [Theorem 2](#thm-helmholtz-decomposition) is that any vector field $v \in L_\rho^2$ admits a unique decomposition $v = g + u$, where $g$ is a gradient-like component and $u$ is a divergence-free component in the distributional sense.

If sufficient regularity is available, as noted in [Remark 2](#rmk-classical-divergence), then $\nabla \cdot (\rho u)$ is represented by a function in $L_\text{loc}^1$ and the condition $\nabla \cdot (\rho u)=0$ holds *a.e.*

## Orthogonality and Kinetic Energy Splitting

In the decomposition

$$
v = g + u, \quad g \in \mathcal G_\rho, \quad u \in \mathcal G_\rho^\perp,
$$

$g$ and $u$ are orthogonal in $L_\rho^2$. Therefore,

$$
\langle g, u \rangle_\rho = 0.
$$

Hence the Pythagorean theorem holds:

$$
\|v\|_{L_\rho^2}^2 = \|g\|_{L_\rho^2}^2 + \|u\|_{L_\rho^2}^2.
$$

In density notation,

$$
\int_\Omega \rho |v|^2 dx = \int_\Omega \rho |g|^2 dx + \int_\Omega \rho |u|^2 dx.
$$

We refer to $\int_\Omega \rho \|v\|^2 dx$ as *kinetic energy* (yes, just like in physics), omitting the convensional factor of $1/2$. The decomposition partitions the kinetic energy of $v$ into two parts: one from the gradient-like component $g$, and the other from the $\rho$-solenoidal component $u$.

## Solenoidal Equivalence and Minimum-Energy Representative

[Theorem 2](#thm-helmholtz-decomposition) induces an equivalence relation on $L_\rho^2 (\Omega; \mathbb R^d)$.

<div class="math-box box-def" id="def-solenoidal-equivalence" markdown="1">
<span class="box-title">Definition. ($\rho$-Solenoidal Equivalence)</span>
For $v,\,\tilde v \in L_\rho^2 (\Omega; \mathbb R^d)$, we define

$$
v \sim_\rho \tilde v
$$

if 

$$
\int_\Omega \rho (v - \tilde v) \cdot \nabla \varphi dx = 0 \quad \forall \varphi \in C_c^\infty (\Omega).
$$

Equivalently, this means that $\nabla \cdot (\rho (v - \tilde v)) = 0$ in the distributional sense.
</div>

The relation $\sim_\rho$ on $L_\rho^2$ is an equivalence relation, as verified below:

1. $v \sim_\rho v$ for all $v \in L_\rho^2$.
2. $v \sim_\rho \tilde v \iff \tilde v \sim_\rho v$.
3. $v_1 \sim_\rho v_2$ and $v_2 \sim_\rho v_3$ imply $v_1 \sim_\rho v_3$ due to the linearity of the integral.

The kinetic energy splitting provides a canonical representative among all velocity fields within the same $\rho$-solenoidal equivalence class.

<div class="math-box box-thm" id="thm-minimum-energy" markdown="1">
<span class="box-title">Theorem 3. (Minimum-Energy Representative)</span>
Let $v = g + u$ be the decomposition from [Theorem 2](#thm-helmholtz-decomposition), where

$$
g \in \mathcal G_\rho, \quad u \in \mathcal G_\rho^\perp.
$$

Then $g$ is the unique kinetic energy minimizer in the $\rho$-solenoidal equivalence class of $v$, that is,

$$
\| g \|_{L_\rho^2} \leq \| \tilde v \|_{L_\rho^2}
$$

for every $\tilde v \in L_\rho^2$ satisfying

$$
\tilde v \sim_\rho v.
$$

Moreover, equality holds if and only if $\tilde v$ and $g$ agree $\rho(x)dx$-*a.e.*
</div>

<details class="proof-container" markdown="1">
<summary>Proof of Theorem 3</summary>
<div class="proof-content" markdown="1">

Let $\tilde v \sim_\rho v$. Then

$$
\int_\Omega \rho (\tilde v - v) \cdot \nabla \varphi dx = 0 \quad \forall \varphi \in C_c^\infty (\Omega).
$$

Now suppose $h \in \mathcal G_\rho$. Equivalently, there exists a sequence $(\varphi_n) \subseteq C_c^\infty (\Omega)$ such that

$$
\nabla \varphi_n \to h \quad \text{in } L_\rho^2.
$$

By Cauchy-Schwarz:

$$
| \langle w, h \rangle_\rho - \langle w, \nabla \varphi_n \rangle_\rho| = |\langle w, h - \nabla \varphi_n \rangle_\rho| \leq \|w\|_{L_\rho^2}\|h - \nabla \varphi_n\|_{L_\rho^2} \quad \forall w \in L_\rho^2 (\Omega; \mathbb R^d),
$$

we get

$$
\langle \tilde v - v, h \rangle_\rho = \lim_{n \to \infty} \langle \tilde v - v, \nabla \varphi_n \rangle_\rho = 0.
$$

Therefore, $\tilde v - v \in \mathcal G_\rho^\perp$.

Now define 

$$
w := u + \tilde v - v \in \mathcal G_\rho^\perp.
$$

Then $\tilde v = g + w$ is the Helmholtz decomposition of $\tilde v$. By the kinetic energy splitting,

$$
\| \tilde v \|_{L_\rho^2}^2 = \| g \|_{L_\rho^2}^2 + \| w \|_{L_\rho^2}^2.
$$

Therefore,

$$
\| \tilde v \|_{L_\rho^2} \geq \| g \|_{L_\rho^2}.
$$

Furthermore, equality holds if and only if $$\| w \|_{L_{\rho}^2}=0$$, which means $\tilde v = g$ $\rho(x)dx$-*a.e.*
</div>
</details>

An important consequence of [Theorem 3](#thm-minimum-energy) is that this specific representative $g$ is exactly the one that minimizes the kinetic energy. This energy-minimizing perspective naturally bridges our discussion to the theory of optimal transport, particularly the Benamou-Brenier formulation.

## Consequence for the Continuity Equation

The *continuity equation* for a density $p_t$ and a velocity field $v_t$ is

$$
\partial_t p_t + \nabla \cdot (p_t v_t) = 0.
$$

This equation can (and should) be understood in the distributional sense. Specifically, by viewing $p$ as an element of $L_\text{loc}^1([0, T] \times \Omega)$, we interpret $\partial_t p$ as the *distributional derivative* of $p$ in the sense that

$$
\langle \partial_t p, \varphi \rangle = - \int_0^T \int_\Omega p(t, x) \partial_t \varphi(t, x) dxdt
$$

for all $\varphi \in C_c^\infty((0, T) \times \Omega)$.

At a fixed time $t$, we can decompose $v_t$ with respect to $L_{p_t}^2$:

$$
v_t = g_t + u_t, \quad g_t \in \mathcal G_{p_t}, \quad u_t \in \mathcal G_{p_t}^\perp.
$$

Then, since $\nabla \cdot (p_t u_t) = 0$, it follows that

$$
\nabla \cdot (p_t v_t) = \nabla \cdot (p_t g_t).
$$

Hence, $v_t$ and $g_t$ induce the same marginal density evolution.[^1]

[^1]: Strictly speaking, this argument is *pointwise* in time. To formulate the whole time-dependent decomposition rigorously, one also needs suitable measurability and integrability of $t\mapsto g_t,u_t$.

The $p_t$-solenoidal component $u_t$ may alter the trajectory-level probability distribution, but it does not change the marginal density evolution. Therefore, the canonical component

$$
g_t = P_{\mathcal G_{p_t}} v_t
$$

is the minimum-energy representative among all velocities inducing the same marginal flow.

<div class="bibliography" markdown="1">

## References
{: .no_toc }

1. Ambrosio, L., Gigli, N., & Savaré, G. (2008). *Gradient flows: in metric spaces and in the space of probability measures*. Springer Science & Business Media.
2. Benamou, J.-D., & Brenier, Y. (2000). A computational fluid mechanics solution to the monge-kantorovich mass transfer problem. *Numerische Mathematik*, 84(3).

</div>