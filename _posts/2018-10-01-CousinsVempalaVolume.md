---
layout: post
title: Fast estimation of Gaussian volumes
use_math: true
---

This blog post discusses Santosh Vempala and Ben Cousins' [2014 paper](https://arxiv.org/abs/1409.6011) on estimation of Volume and Gaussian Volume of a well-rounded convex body.

## The Problem
A convex body $$K \subset \mathbb{R}^n$$ is said to be "well-rounded" if it lies entirely in between concentric balls of radius $$1$$ and radius $$\sqrt{n}$$:
\begin{equation}
\mathrm{Ball}_n (1) \subseteq K \subseteq \mathrm{Ball}_n (\sqrt{n})
\end{equation}
Given a well-rounded convex body $$K \subset \mathbb{R}^n$$,
1. The volume estimation problem is to estimate $$\mathrm{vol} (K)$$ to within a fractional error of $$\epsilon$$.
2. The Gaussian volume estimation problem is to estimate $$\int_{K} \gamma(x) \mathrm{d} x$$ to within a fractional error of $$\epsilon$$ where $$\gamma(x)$$ is the Gaussian density function, $$(2 \pi)^{-n/2} e^{-\| x \|^2 / 2}$$.
There are some important things to understand about this problem:

1. Na&iuml;ve counting algorithms are not very efficient. For instance dividing space into hypercubes and counting the number of hypercubes that intersect with $$K$$ is not a practical approach - the volume of $$K$$ can be as high as $$\mathrm{vol} (\mathrm{Ball}_n (\sqrt{n})) \sim e^{\Theta(n)}$$ and as low as $$\mathrm{vol} (\mathrm{Ball}_n (\sqrt{n})) \sim \Theta(n)^{-n}$$. Any na&iuml;ve counting algorithm that is efficient for $$\mathrm{Ball}_n (\sqrt{n})$$ is not efficient for $$\mathrm{Ball}_n (1)$$ and vice-versa. Can one hope for polynomial-time (in $$n$$ and $$\epsilon$$) algorithms?

**Theorem 1** [[^KLS97]]:
It is possible to sample $$N$$ points $$\{v_1,\dots,v_N\}$$ from $$K$$ in time $$\mathcal{O}^* (n^4 + Nn^3)$$ such that
1. **almost uniform**: each $$v_i$$ comes from a near uniform distribution (total variation distance to uniform is bounded by $$\epsilon$$)
2. **almost pairwise independent**: $$i,j \in [N]$$ such that $$i \ne j$$ and $$A, B \subset K$$,
\begin{equation\*}
	\vert \mathrm{Pr} (v_i \in A ; v_j \in B) - \mathrm{Pr} (v_i \in A) \mathrm{Pr} (v_j \in B) \vert \le \epsilon
\end{equation\*}

[^KLS97]: Kannan, R., Lov&aacute;sz, L., Simonovits, M. [Random walks and an $$O^*(n^5)$$ volume algorithm](http://web.cs.elte.hu/~lovasz/vol5.pdf). Random Structures and Algorithms $$\mathbf{1}$$, 1-50 (1997)