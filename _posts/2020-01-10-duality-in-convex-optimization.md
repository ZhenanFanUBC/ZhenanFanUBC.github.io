---
title: Duality in Convex Optimization
date: 2020-01-10 12:00:00 +0800
categories: [Optimization]
tags: [Duality]
toc: false
seo:
  date_modified: 2020-02-21 20:08:52 -0800
---
# Duality in Convex Optimization

In this post we will discuss a widely used concept in convex optimization: duality. The concept of convex duality not only has mathematical elegance but also provide alternative problems which are possibly easier to solve. Most of the discussion here can be found in [^1] and [^2].

## Dual Representation of Convex Sets

Before describing the idea of duality, we first consider an example. Let $C$ be a convex set in $\mathbb{R}^n$. From our previous [post](https://zhenanfanubc.github.io/posts/function-perspective-of-convex-sets), we know that we have two ways to represent $C$, namely

* $C = \operatorname{co}\\{x \in \mathbb{R}^n: \gamma_C(x) \leq 1\\}$; 
* $C = \bigcap\limits_{z \in \mathbb{S}^{n-1}}\\{x: \langle z, x\rangle - \sigma_C(z) \leq 0\\}$,

where the first one can be seen as representing $C$ by convex hull of a set of points (standard representation) and the second one can be seen as representing $C$ by the intersection of all halfspaces containing the set (“dual” representation). 

![Set]({{ "/assets/img/post2/dual_set.png" | relative_url }})
 

## Dual Representation of Convex Functions
Let $f: \mathbb{R}^n \to \mathbb{R}$ be a convex function. From basic convex analysis, we know that $f$ is convex if and only if its epigraph is a convex set, where 

$$epif = \{(x, t) \in \mathbb{R}^{n + 1} \mid f(x) \leq t\}.$$

From our previous discussion, we can dually represent $f$ by supporting halfspaces of $epif$. For a given slope $z \in \mathbb{S}^{n - 1}$, let us describe the supporting halfspace of $epif$ by an affine function 

$$\ell_{z, \alpha}(x) = \langle z, x\rangle - \alpha,$$ 

and we want to find the "best" affine function, namely the minimum choice of $\alpha$ such that $\ell_{z, \alpha}$ is still a lower minirant for $f$. In other words,  

<div>
$$
\begin{align}
 \alpha^* &= \operatorname{argmin}\{\alpha \mid f(x) \geq \langle z, x\rangle - \alpha, \forall x \in \mathbb{R}^n\} 
 \\&= \operatorname{argmin}\{\alpha \mid \alpha \geq \langle z, x\rangle - f(x), \forall x \in \mathbb{R}^n\}
 \\&= \operatorname{argmin}\{\alpha \mid \alpha \geq \sup\limits_{x \in \mathbb{R}^n}\left[\langle z, x\rangle - f(x)\right]\}
 \\&= \sup\limits_{x \in \mathbb{R}^n}\left[\langle z, x\rangle - f(x)\right]
 \\&=: f^*(z).
\end{align}
$$ 
</div>

The function $f^*$ is called the **conjugate function** of $f$ which can be viewed as the dual representation of $f$.  

![Function]({{ "/assets/img/post2/dual_function.png" | relative_url }})

## Dual of Convex Optimization Problems
Finally, we can talk about dual optimization problems. Consider the optimization problem 

$$\min_{x \in \mathbb{R}^n} \enspace f(x),$$ 

where $f: \mathbb{R}^n \to \mathbb{R}$ is a convex function. 

Note that given an optimization problem there are different dual problems depending on how we **perturb** the optimization problem. 

Formally, we introduce a convex function $F: \mathbb{R}^n \times \mathbb{R}^m \to \mathbb{R}$ such that 

$$F(x, 0) = f(x).$$ 

We call this function **perturbation function**. Now if we define 

$$v(y) = \inf_{x \in \mathbb{R}^n} F(x, y),$$ 

then our optimization problem is simply to evaluate $v(0)$. Note that $v$ is often called **value function**. Since $F$ is convex, by the property of conjugate function, it follows that 

$$v(0) = v^{**}(0),$$ 

and the dual problem is simply to evaluate $v^{**}(0)$. In other words, the dual problem is given by 

$$v^{**}(0) = \max_{z \in \mathbb{R}^m}\enspace -v^*(z).$$

Finally, I am going to explain three most common duals: Fenchel dual, Lagrange dual and Gauge dual using the convex duality framework.

#### Fenchel Dual
Consider the optimization problem 

$$\min_{x} \enspace f(x) + g(Mx),$$

where $f: \mathbb{R}^n \to \mathbb{R}$, $g: \mathbb{R}^m \to \mathbb{R}$ are two convex functions and $M: \mathbb{R}^n \to \mathbb{R}^m$ is a linear operator. 

Introduce the perturbion function: 

$$F(x, y) = f(x) + g(Mx + y),$$ 

then the value function is 

$$v(y) = \inf_{x \in \mathbb{R}^n}f(x) + g(Mx + y).$$ 

The conjugate of the value function is given by

<div>
$$
\begin{align}
 v^*(z) &= \sup_{y \in \mathbb{R}^m} \langle y, z\rangle - v(y)
 \\&= \sup_{y \in \mathbb{R}^m} \bigg[ \langle y, z\rangle - \inf_{x \in \mathbb{R}^n}\left(f(x) + g(Mx + y)\right) \bigg]
 \\&= \sup_{y \in \mathbb{R}^m} \sup_{x \in \mathbb{R}^n} \enspace \langle y, z\rangle - f(x) - g(Mx + y)
 \\&= \sup_{x \in \mathbb{R}^n} \bigg(\sup_{y \in \mathbb{R}^m} \enspace \langle y + Mx, z\rangle - g(Mx + y)\bigg) + \langle -Mx, z\rangle - f(x)
 \\&= \sup_{x \in \mathbb{R}^n} \enspace g^*(z) + \langle x, -M^*z\rangle - f(x)
 \\&= g^*(z) + \sup_{x \in \mathbb{R}^n} \langle x, -M^*z\rangle - f(x)
 \\&= g^*(z) + f^*(-M^*z),
\end{align}
$$
</div>

where $M^*$ is the adjoint operator. Therefore, following the previous discussion, the dual problem is given by 

$$\max_{z \in \mathbb{R}^m} - g^*(-z) - f^*(M^*z),$$ 

which is also called **Fenchel dual**.

#### Lagrange Dual
Consider the optimization problem

$$\min_{x} \enspace f(x) \quad\text{subject to}\quad g(x) \leq 0,$$ 

where $f: \mathbb{R}^n \to \mathbb{R}$ and $g: \mathbb{R}^n \to \mathbb{R}^m$ are convex functions. Now we define a new function by 

<div>
$$\delta(w) =
  \begin{cases}
        0 & \text{if}\enspace w \leq 0 \\
        \infty & \text{otherwise}
  \end{cases},$$ 
</div>

then the optimization is equivalent to 

$$\min_{x} \enspace f(x) + \delta(g(x)).$$ 

Introduce the perturbation function: 

$$F(x, y) = f(x) + \delta(g(x) + y),$$ 

then the value function is is 

$$v(y) = \inf_{x \in \mathbb{R}^n} \enspace f(x) + \delta(g(x) + y).$$

The conjugate of the value function is given by

<div>
$$
\begin{align}
 v^*(z) &= \sup_{y \in \mathbb{R}^m} \langle y, z\rangle - v(y)
 \\&= \sup_{y \in \mathbb{R}^m} \sup_{x \in \mathbb{R}^n} \enspace \langle y, z\rangle - f(x) - \delta(g(x) + y)
 \\&= \sup_{x \in \mathbb{R}^n, y \in \mathbb{R}^m: g(x) + y \leq 0} \quad \langle y, z\rangle - f(x)
 \\&= \sup_{x \in \mathbb{R}^n, w \in \mathbb{R}^m: w \geq 0} \langle -g(x) + w, z\rangle - f(x)
 \\&= \sup_{x \in \mathbb{R}^n} \enspace \bigg(\langle -g(x), z\rangle - f(x)\bigg) + \sup_{w \in \mathbb{R}^m: w \geq 0} \langle w, z\rangle
 \\&= \sup_{x \in \mathbb{R}^n} \enspace \bigg(\langle -g(x), z\rangle - f(x)\bigg) + \delta(z).
\end{align}
$$
</div>

Therefore, following the previous discussion, the dual problem is given by 

$$
\max_{z \in \mathbb{R}^m: z \geq 0} \enspace \min_{x \in \mathbb{R}^n} \enspace f(x) + \langle z, g(x)\rangle,
$$

which is also called **Lagrange dual**.

#### Gauge Dual
Consider the optimization problem

$$\min_{x} \enspace \gamma_C(x) \quad\text{subject to}\quad Mx = b,$$

where $C$ is a convex set in $\mathbb{R}^n$ and $\gamma_C$ is the corresponding gauge function as introduced in the previous [post](https://zhenanfanubc.github.io/posts/function-perspective-of-convex-sets). 

Let $p^*$ denote the optimal value for this optimization problem, then equivalently, we can express the problem as:

<div>
$$
\begin{align}
 p^* &= \inf_{x, \mu}\enspace\{\mu \mid Mx = b, \gamma_C(x) \leq \mu\}
 \\&= \inf_{x, \lambda}\enspace\{\frac{1}{\lambda} \mid Mx = \lambda b, x \in C\}.
\end{align}
$$
</div>

Based on that, we define the perturbation function:

$$F(x, \lambda, y) = -\lambda + \delta_{\{0\}}(\lambda b - Ax + y) + \delta_C(x).$$

Let $v(y)$ denote the corresponding value function, namely 

$$v(y) = \inf_{x, \lambda} F(x, \lambda, y).$$

It is then obvious that $p^* = -\frac{1}{v(0)}$. 

Then through direct computation, you should be able to obtain

$$v^*(z) = \sigma_C(M^*z) + \delta_{\langle b, \cdot\rangle \geq 1}(z).$$

The detailed procedure can be found in [^3]. Therefore, following the previous discussion, the dual problem is given by 

$$
\min_{z} \enspace \sigma_C(M^*z) \quad\text{subject to}\quad \langle b, z\rangle \geq 1,
$$

which is also called **Gauge dual**. It is worthy noting that in Gauge dual, when strong duality holds, we have $p^* = \frac{1}{d^*}$, which is different from Fenchel dual and Lagrange dual. 



#### References
[^1]: Rockafellar, R. Tyrrell. Convex analysis. Vol. 28. Princeton university press, 1970.
[^2]: Ekeland, Ivar, and Roger Temam. Convex analysis and variational problems. Vol. 28. Siam, 1999.
[^3]: Aravkin, A. Y., Burke, J. V., Drusvyatskiy, D., Friedlander, M. P., & MacPhee, K. J. (2018). Foundations of gauge and perspective duality. SIAM Journal on Optimization, 28(3), 2406-2434.