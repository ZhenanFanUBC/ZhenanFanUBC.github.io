---
title: Concentration Inequalities
date: 2020-02-23 12:00:00 +0800
categories: [Probability]
tags: [Concentration inequality, Statistical learning, Julia]
toc: false
seo:
  date_modified: 2020-02-28 11:35:11 -0800
---
# Concentration Inequalities
Concentration inequalities are used to bound the deviation of a random variable $X$ from its expectation $\mathbb{E}(X)$. They usually take the form of 

$$\mathbb{P}(|X - \mathbb{E}(X)| \geq t) \leq g(t),$$

where $g(t)$ is usually very small like $e^{-t^2}$. These concentration inequalities are very important in statistical learning theory as they describe how a random variable is "concentrated" around its expectation. In this post, I will give an overview of several commonly used concentration inequalities as well as some numerical experiments written in Julia. Most of the theoretical results here can be found in [^1] and [^2]. 

We start with the simplest concentration inequality. 
## Chebyshev Inequality
**Theorem** (Chebyshev inequality) Let $X$ be any random variable, then for any $t > 0$, 

$$\mathbb{P}(|X - \mathbb{E}(X)| \geq t) \leq \frac{\mathrm{Var}(X)}{t^2}.$$

In the following experiment, I compare the probabilistic bound provided by the Chebyshev inequality and the empirical one. 

**Experiment 1** Consider random variable $X = \sum\limits_{i = 1}^{10} X_i$, where all $X_i$'s are independent standard Bernoulli, namely 

$$\mathbb{P}(X_i = 0) = \mathbb{P}(X_i = 1) = 0.5.$$

It is well-known that $X$ is then from $\mathrm{Binomial}(10, 0.5)$, so the expectation and vairance are given by 
$$\mathbb{E}(X) = 5, \quad \mathrm{Var}(X) = 2.5.$$

Then by Chebyshev inequality, we have 

$$\mathbb{P}(|X - 5| \geq t) \leq \frac{2.5}{t^2}.$$

````julia
using Statistics, Distributions, Plots
# number of trials
N = 10000
# distribution
d = Binomial(10, 0.5)
# get random samples
X = rand(d, N)
# probabilities
ts = 0.5:0.01:5
chebyshev = 2.5./(ts.^2)
T = length(ts)
emperical = zeros(T); Xn = abs.(X.-5);
for i = 1:length(ts)
    emperical[i] = count(x->(x>=ts[i]), Xn)/N
end
# plot
plot(ts, [chebyshev, emperical], 
    labels=["Chebyshev" "Emperical"], 
    xlabel = "t", 
    ylabel = "probability",
    ylims = (0,1))
````

![Output1]({{ "/assets/img/post5/Chebyshev.png" | relative_url }})

As we can see from the experiment, the probabilistic bound given by the Chebyshev inequality is vert untight. Can we have a tighter bound? The answer is: Yes! 

## Hoeffding’s Inequality
**Theorem** (Hoeffding’s inequality) Let $X_1, \dots, X_n$ be independent Rademacher random variables, namely 

$$\mathbb{P}(X_i = -1) = \mathbb{P}(X_i = 1) = 0.5,$$

and $a \in \mathbb{R}^n$. Then for any $t \geq 0$, 

$$\mathbb{P}\left(\left|\sum_{i = 1}^n a_iX_i\right| \geq t\right) \leq 2\exp\left(-\frac{t^2}{2\|a\|^2}\right).$$

Let's put the probabilistic bound provided by the Hoeffding's inequality in the previous plot. 

**Experiment 1 (continued)** For each $i$, we define $Y_i = 2(X_i - \frac{1}{2})$, then $Y_i$ is a 
Rademacher random variable. By simple computation, we get 

<div>
$$
\begin{align}
  \mathbb{P}(|X - E(X)| \geq t) &= \mathbb{P}\left(\left|\frac{1}{2}\sum\limits_{i = 1}^{10}Y_i\right| \geq t\right)
  \\&= \mathbb{P}\left(\left|\sum\limits_{i = 1}^{10}Y_i\right| \geq 2t\right)
  \\&\leq 2\exp\left(-\frac{t^2}{5}\right).
\end{align}
$$
</div>

````julia
hoeffding = 2*exp.(-ts.^2/5)
plot(ts, [chebyshev, emperical, hoeffding], 
    labels=["Chebyshev" "Emperical" "Hoeffding"], 
    xlabel = "t", 
    ylabel = "probability",
    ylims = (0,1))
````
![Output2]({{ "/assets/img/post5/Hoeffding.png" | relative_url }})

So far, the Hoeffding's inequality can only be applied to Bernoulli and Rademacher random variables, which is too restrictive. In the next step, we would like to extend the result for a wider range of distributions, for example, the Gaussian distribution. The resulting distribution is called subgaussian distribution, which is a super class of a lot of commonly used distributions including Gaussian and Uniform. 

## Subgaussian Distribution
**Definition** A random variable $X \in \mathbb{R}$ is said to be subgaussian with variance $\sigma^2$ if $\mathbb{E}(X) = 0$ and its moment generating function satisfies 

$$\mathbb{E}(\exp(tX)) \leq \exp\left(\frac{\sigma^2}{2}t^2\right).$$

For convenience, we also say that $X$ is $\sigma$-subgaussian.

Here are some classical examples of subgaussian random variables:
* (Gaussian) If $X \sim \mathcal{N}(0, \sigma^2)$, then $X$ is $\sigma$-subgaussian. 
* (Rademacher) If $X$ is a random Rademacher variable, then $X$ is $1$-subgaussian.
* (Uniform) If $X$ is uniformly distributed over the interval $[-a, a]$, then $X$ is $a$-subgaussian.
* (Bounded) If $X$ is a random variable with zero mean and $\|X\| \leq b$ for some $b > 0$, then $X$ is $b$-subgaussian. 

Similar as Gaussian, the sum of independent subgaussian random variables is still subgaussian. 

**Theorem** Suppose that the variables $X_i$, $i = 1, \dots, n$ are independent, and $X_i$ is $\sigma_i$-subgaussian, then $X := X_1 + \dots + X_n$ is $(\sigma_1 + \dots + \sigma_n)$-subgaussian.

We will then introduce the concentration inequality on the sum of independent subgaussian random variables. 
## General Hoeffding’s Inequality
**Theorem** (General Hoeffding’s inequality) Suppose that the variables $X_i$, $i = 1, \dots, n$ are independent, and $X_i$ is $\sigma_i$-subgaussian. Then for all $t \geq 0$, we have 

$$\mathbb{P}\left(\left|\sum\limits_{i = 1}^n X_i\right| \geq t\right) \leq 2\exp\left(-\frac{t^2}{2\sum_{i = 1}^n \sigma_i^2}\right).$$

In the following experiment, I compare the probabilistic bound provided by the general Hoeffding’s inequality and the empirical one. 

**Experiment 2** Consider indenpendent random variables $X_1 \sim \mathcal{N}(0, 1)$, $X_2 \sim$ Rademacher and $X_3 \sim \mathrm{Unif}([-1, 1])$. Let $X = X_1 + X_2 + X_3$, then by the general Hoeffding’s inequality, we have

$$\mathbb{P}(|X| \geq t) \leq 2\exp\left(-\frac{t^2}{6}\right).$$

````julia
# number of trials
N = 10000
# distribution
d1 = Normal(0, 1)
d2 = Binomial(1, 0.5)
d3 = Uniform(-1, 1)
# get random samples
X1 = rand(d1, N)
X2 = 2*(rand(d2, N) .- 0.5)
X3 = rand(d3, N)
X = X1 + X2 + X3
# probabilities
ts = 0.1:0.01:5
T = length(ts)
emperical = zeros(T);
for i = 1:length(ts)
    emperical[i] = count(x->(abs(x)>=ts[i]), X)/N
end
hoeffding = 2*exp.(-ts.^2/6)
plot(ts, [hoeffding, emperical], 
    labels=["Hoeffding" "Emperical"], 
    ylims = (0,1),
    xlabel = "t", 
    ylabel = "probability")
````
![Output3]({{ "/assets/img/post5/Subgaussian.png" | relative_url }})

Up until this point, we have introduced several concentration inequalities on the sum of independent random variables. However, there are many problems requiring bounds on functions of inpendent random variables. The next theorem we are going to introduce shows that it is possible to given concentration inequality for function of independent random variables if the function is "bounded".

## Bounded Differences Inequality
**Definition** A function $f:\mathbb{R}^n \to \mathbb{R}$ satisfies the bounded difference inequality with parameters $L_1,\dots,L_n$ if for each $i = 1, \dots, n$, 

$$\sup_{x_1, \dots, x_n, x_i'}|f(x_1, \dots, x_n) - f(x_1, \dots, x_i', \dots, x_n)| \leq L_i.$$

Namely, the value of $f(x)$ can change by at most $L_i$ under arbitrary change over coordinate $i$ of $x$.

**Theorem** (Bounded differences inequality) Let $X_1, \dots, X_n$ be independent random variables. Let $f:\mathbb{R}^n \to \mathbb{R}$ be a function satisfying the bounded difference inequality with parameters $L_1,\dots,L_n$. Then for any $t>0$, we have

$$\mathbb{P}(|f(X) - \mathbb{E}(f(X))| \geq t) \leq 2\exp\left(-\frac{2t^2}{\sum_{i = 1}^n L_i^2}\right),$$

where $X = (X_1, \dots, X_n)$.

Our next experiment is from this [note](http://www.math.wisc.edu/~roch/grad-prob/gradprob-notes20.pdf).

**Experiment 3** Suppose we throw $n$ balls into $m$ bins independently, uniformly at random. Let $X_j$ be the index of the bin in which ball $j$ lands and let $f(X_1, \dots, X_n)$ denote the number of empty bins. It is not hard to get 

$$\mathbb{E}(X_1, \dots, X_n) = m\left(1 - \frac{1}{m}\right)^n,$$

and $f$ satisfies the the bounded difference inequality with $L_1 = \dots = L_m = 1$. In the following experiment, we set $n = m = 10$.

````julia
using LinearAlgebra
n = 10; m = 10
# number of trials
N = 10000
# distribution
d = DiscreteUniform(1, m)
# function f
Im = Matrix(I, m, m); g(i) = Im[:,i]
function f(X)
    z = mapreduce(g, +, X)
    return count(j->j==0, z)
end
# Expectation
E = m*(1 - 1/m)^n
# for-loop
F = zeros(N)
for trial in 1:N
    X = rand(d, 10)
    F[trial] = abs(f(X)-E)
end
# probabilities
ts = 0.1:0.01:5
T = length(ts)
emperical = zeros(T);
for i = 1:length(ts)
    emperical[i] = count(x->x>=ts[i], F)/N
end
bounded = 2*exp.(-2*ts.^2/n)
plot(ts, [bounded, emperical], 
    labels=["Bounded" "Emperical"], 
    ylims = (0,1),
    xlabel = "t", 
    ylabel = "P(|f(X) - E(f(X))| > t)")
````
![Output4]({{ "/assets/img/post5/Bounded.png" | relative_url }})

We have shown the concentration inequality for "bounded" functions. Can we show the similar probabilistic bound for a more general class of functions? The answer is Yes, when $X_i$'s are standard gaussian. 

## Lipschitz functions of Gaussian variables
**Definition** A function $f:\mathbb{R}^n \to \mathbb{R}$ is $L$-Lipschitz if 

$$|f(x) - f(y)| \leq L\|x - y\|, \quad \forall x, y \in \mathbb{R}^n.$$

The following result guarantees the concentration property of Lipschitz function of independent standard Gaussian random variables. 

**Theorem** Let $X_1, \dots, X_n$ be i.i.d. standard Gaussian variables, and let $f:\mathbb{R}^n \to \mathbb{R}$ be $L$-Lipschitz. Then for all $t \geq 0$, 

$$\mathbb{P}\left(|f(X) - \mathbb{E}(f(X))| \geq t\right) \leq 2\exp\left(-\frac{t^2}{2L^2}\right),$$

where $X = (X_1, \dots, X_n)$. 

It is worth noting that this concentration inequality holds regardless of the dimension. 

**Experiment 4** Let $X_1, \dots, X_n$ be standard Gaussian. Let $f = \|\cdot\|$, then $f$ is obviously $1$-Lipschitz and by elementary integration, we know that $\mathbb{E}(\|X\|) \approx \sqrt{n}$. In this experiment, we choose $n = 10, 100, 1000$. 

````julia
# number of variables
n1 = 10; n2 = 100; n3 = 1000
# expectations
E1 = sqrt(n1); E2 = sqrt(n2); E3 = sqrt(n3)
# number of trials
N = 10000
# distribution
d = Normal(0, 1)
# for-loop
F1 = zeros(N); F2 = zeros(N); F3 = zeros(N)
for trial in 1:N
    X1 = rand(d, n1); X2 = rand(d, n2); X3 = rand(d, n3)
    F1[trial] = abs(norm(X1) - E1)
    F2[trial] = abs(norm(X2) - E2)
    F3[trial] = abs(norm(X3) - E3)
end
# probabilities
ts = 0.1:0.01:5
T = length(ts)
emperical1 = zeros(T); emperical2 = zeros(T); emperical3 = zeros(T)
for i = 1:length(ts)
    emperical1[i] = count(x->x>=ts[i], F1)/N
    emperical2[i] = count(x->x>=ts[i], F2)/N
    emperical3[i] = count(x->x>=ts[i], F3)/N
end
bounded = 2*exp.(-ts.^2/2)
plot(ts, [bounded, emperical1, emperical2, emperical3], 
    labels=["Bounded" "Emperical_n=10" "Emperical_n=100" "Emperical_n=100"], 
    ylims = (0,1),
    xlabel = "t", 
    ylabel = "P(|f(X) - E(f(X))| > t)")
````
![Output5]({{ "/assets/img/post5/Gaussian.png" | relative_url }})

Finally, all the concentration inequalities we have seen depend on the independence between $X_i$'s. Can we relax this condition? The answer is Yes, when they are "enough inpendent". Here we show a particular example of concentration on the sphere. A more general discussion over this topic can be found in the Chapter 5, Concentration without independence, of [^1].

## Concentration on the unit sphere
**Theorem** Let $X$ be a uniform random variable on the unit sphere $\mathbb{S}^{n-1}$ and let $f:\mathbb{S}^{n-1} \to \mathbb{R}$ be $L$-Lipschitz. Then for all $t\geq0$, 

$$\mathbb{P}\left(|f(X) - \mathbb{E}(f(X))|\right) \leq 2\exp(-\frac{(n-1)t^2}{2L^2}).$$

**Experiment 4** Let $X$ be standard Gaussian in $\mathbb{R}^n$ and let $Y = \frac{X}{\|X\|}$. Then it is well-known that $Y$ is uniformly distributed on $\mathbb{S}^{n-1}$. We define $f(y) = \langle a, y\rangle$, where $a \in \mathbb{S}^{n-1}$ is some fixed vector. Then by simple computation, we can get that $f$ is 1-Lipschitz and $\mathbb{E}(f(Y)) = 0$. In the experiment, we choose $n = 10$. 

````julia
n = 10
# number of trials
N = 10000
# distribution
d = Normal(0, 1)
# function
a = randn(n); a = a/norm(a)
f = y->dot(a, y)
# for-loop
F = zeros(N)
for trial in 1:N
    X = rand(d, n); Y = X/norm(X)
    F[trial] = abs(f(Y))
end
# probabilities
ts = 0.1:0.01:2
T = length(ts)
emperical = zeros(T)
for i = 1:length(ts)
    emperical[i] = count(x->x>=ts[i], F)/N
end
bounded = 2*exp.(-(n-1)*ts.^2/2)
plot(ts, [bounded, emperical], 
    labels=["Bounded" "Emperical"], 
    ylims = (0,1),
    xlabel = "t", 
    ylabel = "P(|f(X) - E(f(X))| > t)")
````
![Output5]({{ "/assets/img/post5/Sphere.png" | relative_url }})


### Reference
[^1]: Vershynin, Roman. High-dimensional probability: An introduction with applications in data science. Vol. 47. Cambridge university press, 2018.
[^2]: Wainwright, Martin J. High-dimensional statistics: A non-asymptotic viewpoint. Vol. 48. Cambridge University Press, 2019.