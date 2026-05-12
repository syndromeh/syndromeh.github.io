---
layout: post
title: "The Non-Uniqueness of Probability Flows"
date: 2026-05-12 23:59:59 +0900
categories: [Machine Learning]
tags: [Generative Models, Flow Matching Models]
---
* TOC
{:toc}

## Introduction

The fundamental philosophy of generative models is to transport a prior distribution $p_\text{prior}$ to a data distribution $p_\text{data}$.
There are various ways to achieve this. For example, in continuous-time diffusion models, this transport is obtained through a reverse-time Stochastic Differential Equation (SDE).[^1]

[^1]: This is guaranteed by Anderson (1982). We will revisit this in a later post.

However, discrete SDE sampling methods (*e.g.*, Euler-Maruyama discretization) tend to require many time steps and can be computationally unstable. Fortunately, what we are generally interested in when dealing with generative modeling is not the trajectory-level distribution, but only the marginal-level probability density flow $\\{ p_t \\}_{t \in [0, T]}$.

Of course, the dynamics that produce a specific marginal flow are not unique. Therefore, it is highly beneficial to find alternative dynamics that can realize the exact same marginal flow more stably and rapidly. 

From this perspective, *Ordinary Differential Equations (ODEs)* serve as a good approach. They are appealing because they admit faster and stable deterministic solvers, motivating a search for ODEs that realize a given marginal flow.

For instance, a reverse-time SDE can be converted into an ODE that shares the same marginal flow as follows:

$$
\dot X_t = f(X_t, t) - \frac{1}{2}g^2(t) \nabla \log p_t (X_t).
$$

This is known as the *Probability Flow ODE (PF-ODE)*.

Alternatively, rather than fixing the marginal flow, there is an approach where the velocity vector field is learned so that marginal endpoints act as well-defined boundary conditions:

$$
\dot X_t  = v_t (X_t).
$$

This is known as *Flow Matching (FM)*.

In both frameworks, the core challenge of generative modeling shifts to finding an appropriate deterministic vector field (or *drift*). This naturally raises the following questions:

> Given a fixed marginal flow $\\{ p_t \\}_{t \in [0, T]}$, is the ODE drift that generates it unique? 
> 
> If it is not unique, what is the most "canonical" choice?
{: #bq-core-questions }

In this note, we aim to answer these questions.

<div class="math-box box-note" id="rmk-fm-fixed-marginal" markdown="1">
<span class="box-title">Remark 1. (FM and fixed marginal flows)</span>

Although FM primarily focuses on learning the drift given fixed marginal endpoints—without explicitly targeting the intermediate marginal flows—the concept of *drift under a fixed marginal flow* remains highly relevant to FM from two perspectives:

1. **Guiding the Learning Process** 

    If the most desirable velocity field for a specific marginal flow is known, we naturally want to guide the learning process toward it. Therefore, it is beneficial to design model architectures that inherently satisfy this criterion. Furthermore, when incorporating system-specific priors (*e.g.*, periodicity in cell cycles), we are required to select a velocity field that aligns with these reference dynamics.

2. **Modifying the Learned Field** 
    
    Once the velocity field is trained, we may want to modify it to achieve specific objectives (*e.g.*, distilling it into a simpler model or transforming it for computational efficiency). In these scenarios, preserving the marginal flow serves as a natural constraint to maintain the model's generative performance.
</div>

## The Continuity Equation

Before moving on, it is worth observing the relationship between a marginal flow and the velocity field that generates it. In many physical settings involving a continuous medium, the time-dependent density $p_t$ and the velocity field $v_t$ satisfy a single fundamental equation.

<div class="math-box box-thm" id="thm-continuity-equation" markdown="1">
<span class="box-title">Theorem 1. (Continuity Equation)</span>

Let $v_t : \mathbb{R}^d \to \mathbb{R}^d$ be a smooth, sufficiently well-behaved time-dependent vector field, and consider the ODE

$$
\dot X_t = v_t(X_t), \qquad X_0 \sim p_0.
$$

Let $p_t$ denote the density of $X_t$ at time $t$. Assuming $p_t$ is sufficiently regular[^2], the pair $(p_t, v_t)$ satisfies the *continuity equation*

$$
\partial_t p_t + \nabla \cdot (p_t v_t) = 0.
$$

Furthermore, if $\(q_t, v_t\)$ also satisfies the equation with $q_0 = p_0$, then $q_t = p_t$ pointwise.

</div>

[^2]: The smoothness assumption can be substantially relaxed; see DiPerna and Lions (1989) and Ambrosio (2004) for the precise function space requirements.

<details class="proof-container" markdown="1">
<summary>Proof of Theorem 1</summary>
<div class="proof-content" markdown="1">

**Continuity Equation**

Take a test function $\phi \in C_c^\infty(\mathbb{R}^d)$ and consider its expectation under $X_t$:

$$
\mathbb{E}[\phi(X_t)] = \int \phi(x)\, p_t(x)\, dx.
$$

Differentiating both sides in $t$, the left-hand side gives, by the chain rule applied to $\dot X_t = v_t(X_t)$,

$$
\frac{d}{dt}\mathbb{E}[\phi(X_t)] = \mathbb{E}[\nabla \phi(X_t) \cdot v_t(X_t)] = \int \nabla \phi(x) \cdot v_t(x)\, p_t(x)\, dx.
$$

The right-hand side gives

$$
\frac{d}{dt}\int \phi(x)\, p_t(x)\, dx = \int \phi(x)\, \partial_t p_t(x)\, dx.
$$

Equating the two and integrating the first by parts <span class="aside">(the boundary term vanishes since $\phi$ has compact support)</span>, we obtain

$$
\int \phi(x)\, \partial_t p_t(x)\, dx = -\int \phi(x)\, \nabla \cdot (p_t v_t)(x)\, dx.
$$

Since this holds for every $\phi \in C_c^\infty$, we conclude

$$
\partial_t p_t + \nabla \cdot (p_t v_t) = 0
$$

in the distributional sense, and pointwise whenever $p_t$ and $v_t$ are $C^1$ functions.

**Uniqueness of $p_t$ given $v_t$**

Assume $v_t$ is Lipschitz in $x$ uniformly in $t$ with sublinear growth. The Picard-Lindelöf theorem then gives a flow $$\Phi_t : \mathbb{R}^d \to \mathbb{R}^d$$ satisfying

$$
\dot\Phi_t(x) = v_t(\Phi_t(x)), \qquad \Phi_0(x) = x,
$$

which is a diffeomorphism for each $t$ <span class="aside">(run the ODE forward and backward in time, using Picard-Lindelöf uniqueness in both directions)</span>.


Define $$p_t := (\Phi_t)_\# p_0$$. We verify that this satisfies the continuity equation. For any test function $\phi \in C_c^\infty(\mathbb{R}^d)$,

$$
\int \phi(x)\, p_t(x)\, dx = \int \phi(\Phi_t(y))\, p_0(y)\, dy
$$

by the change-of-variables formula for pushforward measures. Differentiating in $t$ and using $\dot\Phi_t(y) = v_t(\Phi_t(y))$,

$$
\frac{d}{dt}\int \phi(x)\, p_t(x)\, dx = \int \nabla \phi(\Phi_t(y)) \cdot v_t(\Phi_t(y))\, p_0(y)\, dy = \int \nabla \phi(x) \cdot v_t(x)\, p_t(x)\, dx,
$$

where the last equality applies the pushforward change of variables in reverse. Integrating by parts <span class="aside">(boundary term vanishes since $\phi$ has compact support)</span>,

$$
\frac{d}{dt}\int \phi(x)\, p_t(x)\, dx = -\int \phi(x)\, \nabla \cdot (p_t v_t)(x)\, dx.
$$

On the other hand,

$$
\frac{d}{dt}\int \phi(x)\, p_t(x)\, dx = \int \phi(x)\, \partial_t p_t(x)\, dx.
$$

Equating the two and noting that $\phi \in C_c^\infty$ was arbitrary,

$$
\partial_t p_t + \nabla \cdot (p_t v_t) = 0.
$$

Now suppose $q_t$ is another $C^1$ solution with $q_0 = p_0$, and set $r_t := q_t - p_t$. By linearity of the continuity equation,

$$
\partial_t r_t + \nabla \cdot (r_t v_t) = 0,
$$

with $r_0 = 0$.

Fix any $\phi \in C_c^\infty(\mathbb{R}^d)$ and $\tau \in [0, T]$, and define

$$
\psi(t, x) := \phi\bigl(\Phi_\tau \circ \Phi_t^{-1}(x)\bigr).
$$

Then $\psi(\tau, x) = \phi(x)$ by construction. Moreover,  for any $y \in \mathbb R^d$,

$$
\psi(t, \Phi_t(y)) = \phi(\Phi_\tau(y)),
$$

which is independent of $t$. Differentiating in $t$ via the chain rule and using $\dot\Phi_t(y) = v_t(\Phi_t(y))$,

$$
0 = \frac{d}{dt} \psi(t, \Phi_t(y)) = \partial_t \psi(t, \Phi_t(y)) + v_t(\Phi_t(y)) \cdot \nabla \psi(t, \Phi_t(y)).
$$

Since $\Phi_t$ is surjective, $\psi$ satisfies the following equation:

$$
\partial_t \psi(t, x) + v_t(x) \cdot \nabla \psi(t, x) = 0.
$$

Now differentiate $\int \psi(t, x)\, r_t(x)\, dx$ in $t$. Using $\partial_t r_t = -\nabla \cdot (r_t v_t)$ and integration by parts <span class="aside">(boundary terms vanish since $\Phi_t$ being a diffeomorphism, hence $\psi(t, \cdot)$ has compact support)</span>,

$$
\frac{d}{dt}\int \psi(t, x)\, r_t(x)\, dx = \int \bigl[\partial_t \psi + v_t \cdot \nabla \psi\bigr]\, r_t\, dx = 0.
$$

So this integral is constant in $t$. At $t = 0$ it equals zero (since $r_0 = 0$), hence at $t = \tau$,

$$
\int \phi(x)\, r_\tau(x)\, dx = 0.
$$

Since this holds for every $\phi \in C_c^\infty$, we have $r_\tau = 0$, so $q_\tau = p_\tau$ pointwise. As $\tau \in [0, T]$ was arbitrary, $q_t = p_t$ for all $t$.

</div>
</details>

Although the proof is quite dense and heavy, the implication of [Theorem 1](#thm-continuity-equation) is striking: the dynamics of the marginal flow $p_t$ depends on the velocity field $v_t$ only through the divergence $\nabla \cdot (p_t v_t)$. Many distinct velocity fields can therefore induce the same $p_t$---if any exist---as long as they share the same $\nabla \cdot (p_t v_t)$.


<div class="math-box box-note" id="rmk-uniqueness" markdown="1">
<span class="box-title">Remark 2. (The direction of the uniqueness)</span>
The uniqueness is in the direction of $\(v_t,\,p_0\) \mapsto p_t$. The reverse direction $p_t \mapsto v_t$ is <span class="txt-red">**not unique**</span>, which is the main topic of this note.
</div>

## A Simple Example: Isotropic Gaussian Marginal Flow

### Constructing affine drifts for Gaussians

In this section, we demonstrate that the non-uniqueness is not merely a theoretical possibility, but a concrete reality. Specifically, we will explicitly construct multiple distinct velocity fields $v_t$ that generate the exact same marginal flow $p_t$.

To illustrate this, we first observe that for any isotropic Gaussian marginal flow, there always exists an affine velocity field that generates it.

<div class="math-box box-thm" id="prop-gaussian-affine-drift" markdown="1">
<span class="box-title">Proposition 2. (Affine Drift for an Isotropic Gaussian Marginal Flow)</span>

Let $\\{ p_t \\}_{t \in [0, T]}$ be a marginal flow of isotropic Gaussians,

$$
p_t = \mathcal{N}(\mu_t, \sigma_t^2 I),
$$

with smooth $\mu_t \in \mathbb{R}^d$ and $\sigma_t > 0$. Then there exists an affine drift $v_t$ inducing this flow which takes the form

$$
v_t(x) = \dot\mu_t + A_t(x - \mu_t),
$$

for some matrix $A_t$ satisfying

$$
A_t + A_t^\top = 2 \frac{\dot\sigma_t}{\sigma_t} I.
$$

Furthermore, when $A_t$ is constrained to be symmetric, it is uniquely determined as $A_t = \frac{\dot\sigma_t}{\sigma_t} I$, giving

$$
v_t(x) = \dot\mu_t + \frac{\dot\sigma_t}{\sigma_t}(x - \mu_t).
$$

</div>

<details class="proof-container" markdown="1">
<summary>Proof of Proposition 2</summary>
<div class="proof-content" markdown="1">

Suppose we assume an affine structure for the drift, given by $v_t(x) = b_t + A_t x$, for some time-dependent vector $b_t \in \mathbb{R}^d$ and matrix $A_t \in \mathbb{R}^{d \times d}$. 

**(a) Matching the mean**

First, we track the evolution of the mean. Taking the time derivative yields:

$$
\dot{\mu}_t = \frac{d}{dt}\mathbb{E}[X_t] = \mathbb{E}[\dot{X}_t] = \mathbb{E}[b_t + A_t X_t] = b_t + A_t \mu_t.
$$

By rearranging this, we can express the translation term as $b_t = \dot{\mu}_t - A_t \mu_t$. Substituting this back into the original drift equation gives:

$$
v_t(x) = \dot{\mu}_t + A_t(x - \mu_t).
$$

**(b) Matching the covariance**

Let us define the centered random variable $Y_t := X_t - \mu_t$. It follows that $\mathbb{E}[Y_t] = 0$, and its time derivative is $\dot{Y}_t = \dot{X}_t - \dot{\mu}_t = A_t Y_t$. We can then compute the time derivative of the covariance matrix:

$$
\begin{aligned}
2\sigma_t \dot{\sigma}_t I = \frac{d}{dt}\bigl(\sigma_t^2 I\bigr) &= \frac{d}{dt}\mathbb{E}[Y_t Y_t^\top] \\\\
&= \mathbb{E}[\dot{Y}_t Y_t^\top + Y_t \dot{Y}_t^\top] \\\\
&= \mathbb{E}[A_t Y_t Y_t^\top + Y_t Y_t^\top A_t^\top] \\\\
&= \sigma_t^2 \bigl(A_t + A_t^\top\bigr).
\end{aligned}
$$

Rearranging this result, we obtain a constraint on the symmetric part of $A_t$:

$$
A_t + A_t^\top = 2 \frac{\dot{\sigma}_t}{\sigma_t} I.
$$

This equation admits at least one trivial solution: the symmetric choice $A_t = \frac{\dot{\sigma}_t}{\sigma_t} I$. More generally, any matrix $A_t$ satisfying this constraint can be decomposed as $A_t = \frac{\dot{\sigma}_t}{\sigma_t} I + K_t$, where $K_t$ is a matrix such that $K_t + K_t^\top = 0$ <span class="aside">(*i.e.*, skew-symmetric)</span>. This demonstrates that while $A_t$ itself is not strictly unique, its symmetric part is uniquely determined by the marginal covariance.

**(c) $v_t$ generates $p_t$**

It remains to verify that the drift $v_t(x) = \dot{\mu}_t + A_t(x - \mu_t)$, for any such matrix $A_t$, indeed induces the specified marginal flow. We can demonstrate this by directly evaluating the continuity equation. First, we compute the score:

$$
\nabla \log p_t(x) = -\frac{1}{\sigma_t^2}(x - \mu_t).
$$

This yields:

$$
\nabla p_t(x) \cdot v_t(x) = p_t(x) \nabla \log p_t(x) \cdot v_t(x) = -\frac{p_t(x)}{\sigma_t^2} (x - \mu_t) \cdot \bigl[\dot{\mu}_t + A_t(x - \mu_t)\bigr].
$$

By applying the covariance constraint to the symmetric part of $A_t$, the divergence of $v_t$ evaluates to:

$$
\nabla \cdot v_t(x) = \mathrm{Tr}(A_t) = \frac{1}{2}\mathrm{Tr}(A_t + A_t^\top) = d \frac{\dot{\sigma}_t}{\sigma_t}.
$$

Consequently, we have:

$$
\nabla \cdot (p_t v_t) = (\nabla p_t) \cdot v_t + p_t (\nabla \cdot v_t) = -\frac{p_t}{\sigma_t^2}(x - \mu_t) \cdot \bigl[\dot{\mu}_t + A_t(x - \mu_t)\bigr] + d \frac{\dot{\sigma}_t}{\sigma_t} p_t.
$$

Next, we compute the explicit time derivative of the density. Starting from the log-density,

$$
\log p_t(x) = -\frac{1}{2\sigma_t^2}\|x - \mu_t\|^2 - d \log \sigma_t + \text{const},
$$

we differentiate with respect to $t$ to obtain:

$$
\partial_t \log p_t = \frac{1}{\sigma_t^2} (x - \mu_t) \cdot \dot{\mu}_t + \frac{\dot{\sigma}_t}{\sigma_t^3} \|x - \mu_t\|^2 - d \frac{\dot{\sigma}_t}{\sigma_t}.
$$

Since $\partial_t p_t = p_t \partial_t \log p_t$, it follows that:

$$
\partial_t p_t = p_t \left[ \frac{1}{\sigma_t^2} (x - \mu_t) \cdot \dot{\mu}_t + \frac{\dot{\sigma}_t}{\sigma_t^3} \|x - \mu_t\|^2 - d \frac{\dot{\sigma}_t}{\sigma_t} \right].
$$

Summing $\partial_t p_t$ and $\nabla \cdot (p_t v_t)$, we observe that the terms involving $\dot{\mu}_t$ and $d \frac{\dot{\sigma}_t}{\sigma_t}$ perfectly cancel out. The remaining quadratic terms in $(x - \mu_t)$ combine to:

$$
\frac{p_t}{\sigma_t^2} \left[ \frac{\dot{\sigma}_t}{\sigma_t} \|x - \mu_t\|^2 - (x - \mu_t)^\top A_t (x - \mu_t) \right].
$$

Using the covariance constraint again, we can rewrite the quadratic form involving $A_t$ as:

$$
(x - \mu_t)^\top A_t (x - \mu_t) = \frac{1}{2} (x - \mu_t)^\top (A_t + A_t^\top) (x - \mu_t) = \frac{\dot{\sigma}_t}{\sigma_t} \|x - \mu_t\|^2.
$$

This implies that the bracketed expression vanishes entirely. Thus, we conclude that

$$
\partial_t p_t + \nabla \cdot (p_t v_t) = 0,
$$

which completes the verification.
</div>
</details>

It is worth noting that even if we restrict $v_t$ to be affine, the choice of drift is not uniquely determined. A systematic classification of this non-uniqueness is a fascinating topic that we will save for the joy of the next chapter. For now, we will explicitly construct three distinct drifts that all generate the exact same marginal flow: (1) a symmetric affine drift, (2) an asymmetric affine drift, and (3) a nonlinear drift.

### Three drifts, one marginal flow

Let us consider a 2D state space $\mathbb{R}^2$ and fix an isotropic Gaussian marginal flow $p_t$ governed by the following parameters:

$$
\mu_t = -2 + 4t, \quad \sigma_t = (\sqrt{0.5} - \sqrt{0.05})t + \sqrt{0.05}, \quad 0 \leq t \leq 1.
$$

This describes an expanding Gaussian whose mean translates from $-2$ to $2$, and whose standard deviation increases linearly from $\sqrt{0.05}$ to $\sqrt{0.5}$ over the given time interval.

**(1) Symmetric affine drift $v_t^{(1)}$**

This is the choice derived directly from [Proposition 2](#prop-gaussian-affine-drift).

**(2) Asymmetric affine drift $v_t^{(2)}$**

Let us define a standard skew-symmetric matrix $K \in \mathbb{R}^{2 \times 2}$ as follows:

$$
K = \begin{pmatrix}
0 & -1 \\
1 & 0
\end{pmatrix}.
$$

According to the proof of [Proposition 2](#prop-gaussian-affine-drift), any drift of the form

$$
v_t^{(2)}(x) = v_t^{(1)}(x) + \omega K (x - \mu_t)
$$

induces exactly the same marginal flow $p_t$. While any scalar $\omega$ preserves this equivalence, we set $\omega = 8.0$ for our simulation.

**(3) Nonlinear drift $v_t^{(3)}$**

Lastly, we introduce a nonlinear drift component. Specifically, let us denote the normalized spatial variable as:

$$
z = \frac{x - \mu_t}{\sigma_t} = \begin{pmatrix}
z_1 \\
z_2
\end{pmatrix}.
$$

With respect to $z$, we define the following nonlinear vector field:

$$
u_\text{norm}(z) = \begin{pmatrix}
- z_1 z_2 \\
z_1^2 - 1
\end{pmatrix}.
$$

Our third drift vector field is then defined by appending this nonlinear term to the symmetric affine drift:

$$
v_t^{(3)}(x) = v_t^{(1)}(x) + \omega \sigma_t u_\text{norm} \left( \frac{x - \mu_t}{\sigma_t} \right).
$$

<span class="aside">(This specific form might seem arbitrary right now, but we will unravel how this is derived shortly.)</span>

For this simulation, we set $\omega = 2.0$.

Simulating the particle trajectories under these three distinct vector fields yields the following visualization.

<video width="100%" autoplay loop muted playsinline style="border-radius: 8px; margin: 1.5em 0;">
  <source src="https://github.com/user-attachments/assets/fc34a9f1-f8f8-4cd1-a33a-7685dd7753ed" type="video/mp4">
</video>

As the visualization clearly confirms, <span class="txt-red">**all three distinct vector fields successfully preserve the exact same marginal flow**</span>. However, if we look closely at the individual particle trajectories, we see that the three vector fields induce substantially different motions. More specifically:

1. The symmetric affine drift $v_t^{(1)}$ appears to transport particles along the most direct, *shortest paths*.
2. In contrast, under the asymmetric affine drift $v_t^{(2)}$, the particles exhibit a distinct tendency to *rotate*.
3. The nonlinear drift $v_t^{(3)}$ appears to follow neither of these simple patterns.

This brings us back to our core question: given a fixed marginal flow $p_t$, what is the most *canonical* choice among these valid candidates? 

While our geometric intuition strongly hints at an obvious answer <span class="aside">(the straightest, non-rotating path)</span>, we cannot rely on visual intuition alone. In the following section, we will take a deep dive into the theoretical framework that rigorously justifies this canonical choice.

## Vector Field Decomposition

To systematically understand this non-uniqueness, we now introduce a general framework for classifying these vector fields. <span class="txt-red">**The core idea is to decompose the vector field into two distinct components**</span>: one that actually drives the evolution of the marginal flow, and another that leaves the marginal flow completely unaffected. This is precisely stated in the following theorem.

<div class="math-box box-thm" id="thm-helmholtz-informal" markdown="1">
<span class="box-title">Theorem 3. (*Informal*, Helmholtz Decomposition)</span>

**(1) Unique Decomposition**

Suppose $v_t: \mathbb{R}^d \to \mathbb{R}^d$ is a vector field that induces a marginal flow $p_t$. Then, $v_t$ can be uniquely decomposed as:

$$
v_t = g_t + u_t,
$$

where *the gradient field* $g_t: \mathbb{R}^d \to \mathbb{R}^d$ is characterized by $g_t = \nabla \phi_t$ for some scalar potential function $\phi_t: \mathbb{R}^d \to \mathbb{R}$, and *the solenoidal field* $u_t: \mathbb{R}^d \to \mathbb{R}^d$ is constrained to be $p_t$-solenoidal, meaning it satisfies:

$$
\nabla \cdot (p_t u_t) = 0.
$$

**(2) Equivalence of Drifts**

Two velocity fields $v_t$ and $\tilde{v}_t$ generate the exact same marginal flow $p_t$ if and only if their difference $v_t - \tilde{v}_t$ is $p_t$-solenoidal.

**(3) Energy Minimization**

The decomposition $v_t = g_t + u_t$ satisfies the following equality:

$$
\int_{\mathbb{R}^d} \| v_t(x) \|^2 p_t(x) \, dx = \int_{\mathbb{R}^d} \| g_t(x) \|^2 p_t(x) \, dx + \int_{\mathbb{R}^d} \| u_t(x) \|^2 p_t(x) \, dx.
$$

Consequently, the gradient field $g_t$ is the unique minimizer of the *kinetic energy* $$\int \|v_t\|^2 p_t \, dx$$ among all possible velocity fields that yield the exact same marginal flow.

</div>

For the rigorous mathematical statements and their accompanying proofs, please refer to [Theorem 2]({% post_url mathematics/2026-05-07-helmholtz-decomposition %}#thm-helmholtz-decomposition) and [Theorem 3]({% post_url mathematics/2026-05-07-helmholtz-decomposition %}#thm-minimum-energy) in [our previous post]({% post_url mathematics/2026-05-07-helmholtz-decomposition %}).

To understand the practical implications of this theorem for generative modeling, we can distill it into three key insights:

1. **Unique Splitting** 
   
   Any vector field $v_t$ can be uniquely separated into two distinct parts: a gradient field $g_t = \nabla \phi_t$ <span class="aside">(the component that governs the flow)</span> and a $p_t$-solenoidal field $u_t$ <span class="aside">(meaning $\nabla \cdot (p_t u_t) = 0$)</span>.
2. **Freedom of Modification** 
   
   The solenoidal field $u_t$ is precisely the component that has no effect on the marginal dynamics. We can freely modify our vector field by adding any arbitrary $p_t$-solenoidal field $\tilde{u}_t$, and the resulting marginal flow $p_t$ will remain unchanged.
3. **The Canonical Choice** 
   
   Because of this freedom, we can construct infinitely many distinct velocity fields that generate the exact same marginal flow. However, among all these valid candidates, the pure gradient field $g_t$ emerges as the most natural and canonical choice, as it is the unique vector field that minimizes the kinetic energy.

These insights provide a complete answer to [our core questions](#bq-core-questions). We now understand exactly <span class="txt-red">**why the vector field is not unique, and how we can modify the generative dynamics without altering the underlying marginal flow**</span>. With this solid foundation in place, let us first revisit our isotropic Gaussian example through this new lens, and then dive into the case studies to see how these principles are being utilized in recent generative modeling research.

<div class="math-box box-note" id="rmk-solenoidal" markdown="1">
<span class="box-title">Remark 3. (Why *solenoidal*?)</span>
A vector field is called *solenoidal* if its divergence vanishes. The term originally stems from electromagnetism: a *solenoid* is a coil-shaped electromagnet, and the magnetic field $\mathbf{B}$ it generates satisfies $\nabla \cdot \mathbf{B} = 0$ <span class="aside">(Gauss's law for magnetism, meaning there are no magnetic monopoles)</span>. Over time, the term broadened to describe *any* divergence-free field. 

For our purposes, the relevant condition is the weighted version: $u_t$ is *$p_t$-solenoidal* if $\nabla \cdot (p_t u_t) = 0$. When $p_t$ is uniform, this reduces to the standard solenoidal condition. For non-uniform $p_t$, the probability density acts as a weighting factor, ensuring that the *probability mass* is conserved pointwise.
</div>

## The Isotropic Gaussian Flow Revisited

With our newly established theoretical framework in place, let us look back at the 2D Gaussian simulation from the previous section. We can now perfectly demystify the particle behaviors we observed.

### Symmetric affine drift as the energy minimizer

The symmetric affine drift $v_t^{(1)}$ derived in [Proposition 2](#prop-gaussian-affine-drift) is not just one of many drifts inducing the given marginal flow — it is the *kinetic energy minimizer* among them. The reason is that $v_t^{(1)}$ is a pure gradient field.

Consider the scalar potential

$$
\phi_t(x) = \frac{\dot\sigma_t}{2\sigma_t}\, \|x - \mu_t\|^2 + \dot\mu_t^\top x.
$$

Differentiating in $x$ gives

$$
\nabla \phi_t(x) = \dot\mu_t + \frac{\dot\sigma_t}{\sigma_t}(x - \mu_t) = v_t^{(1)}(x),
$$

so $v_t^{(1)} = \nabla \phi_t$ is a gradient field. By [Theorem 3](#thm-helmholtz-informal), gradient fields are exactly the kinetic energy minimizers within the equivalence class of marginal-preserving drifts. Hence $v_t^{(1)}$ is the canonical choice in this sense: it transports the prescribed marginal flow with the least kinetic effort, and intuitively, *along the most direct paths*.


### Skew-symmetric component induces rotation

What about the asymmetric drift $v_t^{(2)}$? We constructed it by adding a skew-symmetric matrix term:

$$v_t^{(2)}(x) = v_t^{(1)}(x) + \omega K (x - \mu_t).$$

Through the lens of our decomposition, this extra term $u_t(x) = \omega K (x - \mu_t)$ is precisely the $p_t$-solenoidal field, as it preserves the marginal flow. But why does it explicitly cause the particles to *rotate*?

**(1) Skew-symmetric drift induces rotation**

We first see that a skew-symmetric drift rotates particles. To make the discussion clearer, let us consider the following ODE:

$$
\dot y = K y, \quad y \in \mathbb R^d
$$

where $K \in \mathbb R^{d \times d}$ is skew-symmetric, *i.e.*, $K^\top = -K$. The solution for $y$ is well-known to be:

$$
y(t) = e^{tK} y_0.
$$

It now suffices to examine how $e^{tK}$ behaves. In the 2D case, any skew-symmetric matrix can be expressed as follows:

$$K = \begin{pmatrix} 0 & -\omega \\ \omega & 0 \end{pmatrix}.$$

Since

$$
K^2 = -\omega^2I,
$$

we get

$$
e^{tK} = \sum_{n=0}^\infty \frac{(tK)^n}{n!} = \begin{pmatrix} \cos \omega t & -\sin \omega t \\ \sin \omega t & \cos \omega t \end{pmatrix}.
$$

This is exactly a rotation matrix with an angular velocity of $\omega$.

In general, a rotation matrix in Euclidean space is defined as a matrix that is (1) orthogonal and (2) has a determinant of $1$. If $K$ is skew-symmetric, then $e^{tK}$ is orthogonal, since:

$$
\left( e^{tK} \right)^\top \left( e^{tK} \right) = e^{tK^\top} e^{tK} = e^{-tK} e^{tK} = I.
$$

Furthermore, the determinant of a matrix exponential is the exponential of its trace. Since $K$ is skew-symmetric, its diagonal elements are zero, meaning $\text{Tr}(K) = 0$. Thus:

$$
\det (e^{tK}) = e^{\text{Tr}(tK)} = e^0 = 1.
$$

Hence, $e^{tK}$ represents a pure rotation.

**(2) Rotation naturally moves particles while preserving density**

Physically, the divergence $\nabla \cdot (p_t u_t)$ measures the rate at which probability mass $p_t(x)dx$ is being created <span class="aside">(positive)</span> or destroyed <span class="aside">(negative)</span> at a given point $x$. Since a $p_t$-solenoidal field forces this divergence to vanish everywhere, there are no sources or sinks. This implies that at any location $x$, exactly as much mass flows in as flows out, ensuring that mass never locally accumulates or depletes.

With this in mind, the geometric intuition of why this rotation preserves the marginal flow becomes quite clear. Perhaps the most straightforward approach to moving particles without disrupting the overall probability density is to enforce two intuitive conditions:

1. They must move tangentially along the iso-density contours of $p_t$.
2. They must create no *traffic jams*—meaning no stretching or compression of density along those curves.

Therefore, given that the iso-density contours of an isotropic Gaussian are circles, rigidly rotating the particles around the mean emerges as the most natural choice to satisfy both conditions simultaneously.

### Rotation is not the only solution

While a rigid rotation is a natural choice for a $p_t$-solenoidal drift, it is by no means a necessary condition. Let us revisit the divergence of the marginal flow:

$$
\nabla \cdot (p_t u_t) = p_t\, (\nabla \cdot u_t) + u_t \cdot \nabla p_t.
$$

In our previous skew-symmetric case, we achieved this by strictly forcing both terms on the right-hand side to be zero independently. However, to make the overall divergence zero, we only need the two terms to cancel each other out. This realization grants us more freedom in designing $u_t$.

In general, completely characterizing all possible $p_t$-solenoidal fields in arbitrary dimensions is notoriously challenging. But for lower dimensions ($d \leq 3$), there are well-established tools.

Let us first consider the 2D case. Our goal is to construct a field $u_t$ such that:

$$
\nabla \cdot (p_t u_t) = 0.
$$

To achieve this, we can introduce an arbitrary scalar-valued function $\psi_t$ <span class="aside">(often called a *stream function*)</span> and define:

$$
u_t = \frac{1}{p_t} \begin{pmatrix} \partial_2 \psi_t \\ -\partial_1 \psi_t \end{pmatrix}.
$$

This construction is trivially $p_t$-solenoidal. Thanks to the symmetry of second derivatives, applying the divergence operator yields:

$$
\nabla \cdot (p_t u_t) = \partial_1 (\partial_2 \psi_t) + \partial_2 (-\partial_1 \psi_t) = \partial_1 \partial_2 \psi_t - \partial_2 \partial_1 \psi_
t = 0.$$

This is exactly the machinery behind the nonlinear vector field $v_t^{(3)}$ we introduced. Specifically, we constructed it by appending $u_t$ to our base drift:

$$
v_t^{(3)} = v_t^{(1)} + \frac{1}{p_t} \begin{pmatrix} \partial_2 \psi_t \\ -\partial_1 \psi_t \end{pmatrix},
$$

where $\psi_t$ was chosen as:

$$
\psi_t (x) = - \omega \sigma_t \left( x_1 - (\mu_t)_1 \right) p_t(x).
$$

This trick naturally generalizes to the 3D case. In vector calculus, the *curl* of any arbitrary vector field $\mathrm{A}: \mathbb R^3 \to \mathbb R^3$ is always divergence-free. Conversely, any divergence-free field in 3D can be expressed as the curl of some vector potential $\mathrm{A}$. By defining $p_t u_t = \nabla \times \mathrm{A}$, we can generate an infinite family of $p_t$-solenoidal flows in three dimensions.

## Epilogue

We have seen that the ODE drift inducing a given marginal flow is not uniquely determined, and that it always decomposes into a *gradient component* and a *solenoidal component*. If we take kinetic energy minimization under a fixed marginal flow as the primary criterion, the gradient component emerges as the canonical choice.

The most closely related work in this direction is **Action Matching** (Neklyudov et al., 2023), which directly parameterizes the model as $v_t^\theta = \nabla s_t^\theta$, learning only the gradient field. The scalar function $s_t^\theta$ approximates the unique potential $s_t^\ast$ <span class="aside">(this is called the *action*)</span> whose gradient generates the prescribed marginal flow.

However, kinetic energy minimization is not the only criterion of interest. For instance, when the system's inherent dynamics form closed loops, a gradient field cannot be a complete solution as it cannot circulate. This is a blind spot that arises from considering optimality only at the marginal level while ignoring the *path distribution*.

What the Helmholtz decomposition suggests is that, whenever path-level dynamics matter, the solenoidal component of the vector field must be considered as well. The discarded freedom is precisely where such path-level structure lives.

However, systematically constructing $p_t$-solenoidal fields requires satisfying complex PDE constraints. Thus, finding a vector field that simultaneously respects this mass preservation and other desired properties is highly non-trivial. Making this idea actually work in practice will require deeper investigation.


<div class="bibliography" markdown="1">

## References
{: .no_toc }

1. Ambrosio, L. (2004). Transport equation and Cauchy problem for $BV$ vector fields. *Inventiones Mathematicae*, 158(2).
2. Ambrosio, L., Gigli, N., & Savaré, G. (2008). *Gradient flows: in metric spaces and in the space of probability measures*. Springer Science & Business Media.
3. Anderson, B. D. O. (1982). Reverse-time diffusion equation models. *Stochastic Processes and their Applications*, 12(3).
4. DiPerna, R. J., & Lions, P.-L. (1989). Ordinary differential equations, transport theory and Sobolev spaces. *Inventiones Mathematicae*, 98(3).
5. Duffield, S. (2026). A complete decomposition of stochastic differential equations. *arXiv preprint arXiv:2601.07834*.
6. Lipman, Y., Chen, R. T. Q., Ben-Hamu, H., Nickel, M., & Le, M. (2023). Flow matching for generative modeling. *International Conference on Learning Representations*.
7. Neklyudov, K., Brekelmans, R., Severo, D., & Makhzani, A. (2023). Action matching: Learning stochastic dynamics from samples. *International Conference on Machine Learning*.
8. Song, Y., Sohl-Dickstein, J., Kingma, D. P., Kumar, A., Ermon, S., & Poole, B. (2021). Score-based generative modeling through stochastic differential equations. *International Conference on Learning Representations*.

</div>