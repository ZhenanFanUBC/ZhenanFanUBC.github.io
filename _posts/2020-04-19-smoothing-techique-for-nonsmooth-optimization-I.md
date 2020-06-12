---
layout: post
title: Smoothing Technique for Nonsmooth Optimization I
date: 2020-04-19 12:00:00 +0800
description: Smooth minimization of non-smooth functions.
comments: true
---
Solving a nonsmooth optimization problem via solving a sequence of approximating smooth problem is a useful and widely used technique in optimization. Consider the first order methods in optimization, if the objective is differentiable, then the normal gradient descent can achieve $\epsilon$ accuracy in function value in $\mathcal{O}(1/\epsilon)$ iterations and those accelerated gradient methods can achieve $\epsilon$ accuracy in $\mathcal{O}(1/\sqrt{\epsilon})$ iterations. However, if the objective is non-differentiable, then the subgradient descent and bundle methods need $\mathcal{O}(1/\epsilon^2)$ iterations to achieve $\epsilon$ accuracy. This illustrates why we can benefit from proper smoothing technique.  

In this post, I will discuss the deterministic smoothing techniques for nonsmooth optimization and the randomized ones will be introduced in the next post. Most of the theoretical results here can be found in [^1], [^2] and [^3].

## Smooth Approximation
Throughout this post, we consider $f:\mathbb{R}^n \to \mathbb{R}$ to be a convex but not always differentiable function. We begin by defining the concept of a smooth approximation. 

**Definition** Let $f_\mu : \mathbb{R}^n \to \mathbb{R}$ be a convex funtion. Then we say $f_\mu$ is a $\mu$-smooth approximation of $f$ with parameter $(\alpha, \beta)$ if 

* $f_\mu$ is $\frac{\alpha}{\mu}$-smooth
* $f_\mu \leq f \leq f_\mu + \beta\mu$.

Here we can view $\mu$ as a tradeoff between approximation accuracy and smothness. 

**Example (absolute value)**
Consider $f(x)=|x|$ and define the smooth approximation $f_\mu$ as

<div>
$$
f_\mu(x) = 
\begin{cases}
\frac{x^2}{2\mu}, \quad\quad\enspace\text{if}\quad |x| \leq \mu \\
|x| - \frac{\mu}{2}, \quad\text{otherwise}.
\end{cases}
$$
</div>
 
Note that $f_\mu$ is known to be the Huber function. It is then easy to see that $f_\mu$ is $\frac{1}{\mu}$-smooth and satisfies

$$
f_\mu(x) \leq f(x) \leq f_\mu(x) + \frac{\mu}{2}.
$$

Therefore, $f_\mu$ is a $\mu$-smooth approximation of $f$ with parameter $(1,\frac{1}{2})$. $\square$

**Example (max)**
Consider $f(x) = \max\{x_1, \dots, x_n\}$ and define the smooth approximation $f_\mu$ as

$$
f_\mu(x) = \mu\log\left(\sum_{i = 1}^n \exp(\frac{x_i}{\mu})\right) - \mu\log(n).
$$

Note that $f_\mu$ is known to be the softmax function. It is not hard to check that $f_\mu$ is $\frac{1}{\mu}$-smooth and satisfies

$$
f_\mu(x) \leq f(x) \leq f_\mu(x) + \mu\log(n).
$$

Therefore, $f_\mu$ is a $\mu$-smooth approximation of $f$ with parameter $(1,
 \log(n))$. $\square$
 
Next, I am going to introduce two widely used smoothing techniques. 

### Moreau proximal smoothing
Moreau proximal smoothing is a widely used smoothing technique in the Euclidean setting. Given a convex function $f:\mathbb{R}^n \to \mathbb{R}$. The Moreau proximal approximation for $f$ is given by 

$$f_\mu(x) = \inf_{u \in \mathbb{R}^n} \left\{f(u) + \frac{1}{2\mu}\|x - u\|^2\right\}.$$

It has been shown by Moreau that $f_\mu$ is $\frac{1}{\mu}$-smooth. Let $u_x$ denote the unique point that achieves the above infimum, which is also known as the proximal point, then the gradient of $f_\mu$ at $x$ is given by 

$$\nabla f_\mu(x) = \frac{1}{\mu}(x - u_x).$$

Moreover, when $f$ is $L$-Lipschitz, then 

$$f_\mu(x) \leq f(x) \leq f_\mu(x) + \frac{L^2}{2}\mu.$$

In other words, when $f$ is $L$-Lipschitz, then $f_\mu$ is a $\mu$-smooth approximation of $f$ with parameter $(1,\frac{L^2}{2})$. It is also worth noting that $f$ and $f_\mu$ have the same set of minimizers, and therefore minimizing $f$ and $f_\mu$ are equivalent.  

### Nesterov smoothing
Nesterov’s smoothing is a smoothing technique for a class of nonsmooth functions:

$$f(x) = \max\{\langle u, Ax\rangle - \phi(u) \mid u \in \mathcal{C}\},$$

where  $\phi:\mathbb{R}^m\to\mathbb{R}$ is a continuous convex function, $A:\mathbb{R}^n \to \mathbb{R}^m$ is a linear operator and $\mathcal{C}$ is a convex compact set in $\mathbb{R}^m$. The smooth approximation is given by 

$$f_\mu(x) = \max\{\langle u, Ax\rangle - \phi(u) - \mu d(u): u \in \mathcal{C}\},$$

where $d$ is a $\sigma$-strongly convex function on $\mathcal{C}$. It has been shown by Nestrov that $f_\mu$ is $\frac{\|A\|^2}{\sigma\mu}$-smooth with gradient 

$$\nabla f_\mu(x) = A^*u_x,$$

where $u_x$ is the unique maximizer in the definition of $f_\mu(x)$.  

## Infimal convolution smoothing technique
Beck and Teboulle propose a smoothing technique based on infimal convolution, which can be seen as a generalized framework for the above two discussed smoothing techniques. Let $f:\mathbb{R}^n \to \mathbb{R}$ be a closed and convex function. Then $f$ can be expressed as

$$f(x) = \sup_{u \in \mathbb{R}^n}\enspace \langle u, x\rangle - f^*(u),$$

where $f^*$ is the conjugate function of $f$. Then we can build a smooth approximation of $f$ by adding a strongly convex component to its conjugate, namely,

$$f_\mu(x) = (f^* + \mu d)^*(x) = \sup_{u \in \mathbb{R}^n}\enspace \langle u, x\rangle - f^*(u) - \mu d(u),$$

where $d:\mathbb{R}^n\to\mathbb{R}$ is a nonnegative, continuous and 1-strongly convex function. It follows that $f^* + \mu d$ is $\mu$-strongly convex, and therefore $f_\mu$ is $1/\mu$-smooth. It can also be shown that 

$$f_\mu(x) \leq f(x) \leq f_\mu(x) + \mu D,$$

where $D = \sup_x d(x)$. Therefore, $f_\mu$ is a $1/\mu$-smooth approximation of $f$ with parameters $(1, D)$.

## Complexity Improvement
Consider the minimization problem:

$$\min_{x \in \mathbb{R}^n} \enspace f(x),$$

where $f$ is a non-smooth convex funtion. Let $x^*$ denote the optimizer for $f$. Let $f_\mu$ be a $1/\mu$-smooth approximation of $f$ with parameters $(\alpha, \beta)$. Now we consider solving the following problem:

$$\min_{x \in \mathbb{R}^n} \enspace f_\mu(x).$$

Let $x^*_\mu$ denote the optimizer for $f_\mu$. It is widely known that to attain $\epsilon$-accuracy for minimizing a $L$-smooth convex function by accelerated gradient descent, one needs $\mathcal{O}\left(\sqrt{\frac{L}{\epsilon}}\right)$ iterations. Now we know that $f_\mu$ is $\frac{\alpha}{\mu}$-smooth, in order to attain $\frac{\epsilon}{2}$-accuracy, we need $\mathcal{O}\left(\sqrt{\frac{\alpha}{\mu\epsilon}}\right)$ iteration. Let $x^t$ denote the output. Finally, we set $\mu$ such that $\beta\mu = \frac{\epsilon}{2}$. Then we have 

<div>
$$
\begin{align}
f(x^t) - f(x^*) &\leq f(x^t) - f_\mu(x^*)
\\&\leq f(x^t) - f_\mu(x_\mu^*)
\\&= \left(f(x^t) - f_\mu(x^t)\right) + \left(f_\mu(x^t) - f_\mu(x_\mu^*)\right)
\\&\leq \frac{\epsilon}{2} + \frac{\epsilon}{2}
\\&= \epsilon.
\end{align}
$$ 
</div>

The final iteration complexity is given by 

$$\mathcal{O}\left(\sqrt{\frac{\alpha}{\mu\epsilon}}\right) = \mathcal{O}\left(\frac{\sqrt{\alpha\beta}}{\epsilon}\right).$$

 
#### References
[^1]: Nesterov, Yu. Smooth minimization of non-smooth functions. Mathematical programming 103.1, 2005.
[^2]: Beck, Amir, and Marc Teboulle. Smoothing and first order methods: A unified framework. SIAM Journal on Optimization 22.2, 2012.
[^3]: Chen, Yuxin. Smoothing for nonsmooth optimization. Lecture Notes for ELE 522: Large-Scale Optimization for Data Science, Princeton University, Fall 2019.