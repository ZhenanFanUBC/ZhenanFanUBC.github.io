---
title: Distance Between Sets
date: 2020-02-11 12:00:00 +0800
categories: [Optimization]
tags: []
toc: false
seo:
  date_modified: 2020-02-11 13:46:22 -0800
---
# Distance Between Sets: Introduction to Hausdorff-Distance
In this post, I want to discuss one of the most famous distances between subsets of $\mathbb{R}^n$ -- the Hausdorff distance. Based on that, I will also introduce some convergence analysis regarding set-valued functions. Most of the theoretical results here can be found in [^1] and [^2].

## Distance from point to set
Let $C$ be a nonempty subset of $\mathbb{R}^n$ and $x$ be any point in $\mathbb{R}^n$, then the **distance from $x$ to $C$** is defined to be the minimal among the distances between $x$ and points in $C$. Formally,

$$\mathrm{dist}(x \to C) = \min\limits_{c \in C} \|x - c\|,$$

where $\|\cdot\|$ can be any valid norm. Geometrically, let $x_c$ denote the projected point from $x$ onto $C$, then the distance from $x$ to $C$ is equal to the distance between $x$ and $x_c$.

Next, we generalize this distance to sets.
## Distance from set to set
Let $C_1$ and $C_2$ be two nonempty subsets of $\mathbb{R}^n$, then the **distance from $C_1$ to $C_2$** is defined to be the supreme over all the distances from points in $C_1$ to $C_2$. Formally,

$$\mathrm{dist}(C_1 \to C_2) = \sup_{x \in C_1} \mathrm{dist}(x \to C_2).$$

Geometrically, if the distance from $C_1$ to $C_2$ is small, then this means $C_1$ is contained in some neighbourhood of $C_2$. More specifically, we have the following lemma.

**Lemma:** $\mathrm{dist}(C_1 \to C_2) \leq \epsilon$ if and only if $C_1 \subseteq C_2 + \mathbb{B}_{\epsilon}$, where $\mathbb{B}_{\epsilon}$ is the $\|\cdot\|$-ball centered at $0$ with radius $\epsilon$.

## Distance between sets
The Huasdorff-distance between $C_1$ and $C_2$ is then the symmetrization of the above ideas. Formally, the **Huasdorff-distance between $C_1$ and $C_2$** is defined as

$$\mathrm{dist}(C_1, C_2) = \max\{\mathrm{dist}(C_1 \to C_2), \mathrm{dist}(C_2 \to C_1)\}.$$

The next picture is obtained from Wikipedia. 

![Function]({{ "/assets/img/post4/Hausdorff.png" | relative_url }})

**Proposition:** 

$$\mathrm{dist}(C_1, C_2) = \sup_{\|d\| = 1} |\sigma_{C_1}(d) - \sigma_{C_2}(d)|.$$

**Proof:** By the previous lemma, we know that $\mathrm{dist}(C_1, C_2) \leq \epsilon$ if and only if $C_1 \subseteq C_2 + \mathbb{B}_{\epsilon}$ and $C_2 \subseteq C_2 + \mathbb{B}_{\epsilon}$ and this means that 

$$\forall \|d\| = 1,\enspace\sigma_{C_1}(d) \leq \sigma_{C_2}(d) + \epsilon \enspace \text{and} \enspace \sigma_{C_1}(d) \leq \sigma_{C_2}(d) + \epsilon.$$

Therefore, we can conclude that $\mathrm{dist}(C_1, C_2) = \sup_{\|d\| = 1} |\sigma_{C_1}(d) - \sigma_{C_2}(d)|$.

## Set convergence
Before introducing the concept of set convergence, we need the following definitions regarding the inner and outer limits of sets. 

**Definition** Let $\{C_i\}_{i = 1}^{\infty}$ be a infinite sequence of convex sets in $\mathbb{R}^n$. The outer limit or the limes exterior of $C_i$'s is the set of all cluster points of all selections. Specifically, 

$$\lim\mathrm{ext}_{i \to \infty} C_i = \{c \mid \exists (c_j)_{j \in J \subseteq \mathbb{N}}, \enspace \text{such that}\enspace c_j \in C_j, c_j \to c\}.$$

The inner limit or the limes interior of $C_i$'s is the set of limits of all convergent selections. Specifically, 

$$\lim\mathrm{int}_{i \to \infty} C_i = \{c \mid \exists (c_i)_{i \in \mathbb{N}}, \enspace \text{such that}\enspace c_i \in C_i, c_i \to c\}.$$

It is clear that we always have $\lim\mathrm{int}_{i \to \infty} \subseteq \lim\mathrm{ext}_{i \to \infty} C_i$. When these two sets are equal, the common set is defined to be the limit of $C_i$'s. Specifically, 

$$C = \lim_{i \to \infty} C_i \enspace\text{iff}\enspace C = \lim\mathrm{ext}_{i \to \infty} C_i = \lim\mathrm{int}_{i \to \infty} C_i.$$

**Relation with Hausdorff-distance** In general, convergence with respect to Hausdorff-distance is not equivalent to set convergence as just defined. The next example will illustrate that. 

**Example** Let $C_i = [0, i]$ and $C = \mathbb{R}_{\geq 0}$, then it is obviously that $\lim_{i \to \infty} C_i = C$. Hoever, the Hausdorff-distance between $C_i$ and $C$ is always $\infty$. 

Is there is a way to link these two convergence? The answer is yes. We just need an additional boundedness restriction. 

**Theorem** Suppose there is a bounded set $X \subset \mathbb{R}^n$ such that $C_i, C \subseteq X$, then 

$$\lim_{i \to infty} C_i = C \enspace \Leftrightarrow \enspace \mathrm{dist}(C_i, C) \to 0.$$





#### References
[^1]: Hiriart-Urruty, Jean-Baptiste, and Claude Lemaréchal. Fundamentals of convex analysis. Springer Science and Business Media, 2012.
[^2]: Rockafellar, R. Tyrrell, and Roger J-B. Wets. Variational analysis. Vol. 317. Springer Science & Business Media, 2009.

