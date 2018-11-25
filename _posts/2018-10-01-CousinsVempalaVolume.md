---
layout: post
title: Fast estimation of Gaussian volumes
use_math: true
---

This blog post discusses Ben Cousins and Santosh Vempala's [2016 paper](https://arxiv.org/abs/1409.6011) on estimation of Volume and Gaussian Volume of a well-rounded convex body.

## The Problem
A convex body $$K \subset \mathbb{R}^n$$ is said to be "well-rounded" if it lies entirely in between concentric balls of radius $$1$$ and radius $$\sqrt{n}$$:
\begin{equation}
\mathrm{Ball}_n (\mathbf{0},1) \subseteq K \subseteq \mathrm{Ball}_n (\mathbf{0},\sqrt{n})
\end{equation}
Given a well-rounded convex body $$K \subset \mathbb{R}^n$$,
1. The volume estimation problem is to estimate $$\mathrm{vol} (K)$$ to within a fractional error of $$\epsilon$$.
2. The Gaussian volume estimation problem is to estimate $$\int_{K} \gamma(x) \mathrm{d} x$$ to within a fractional error of $$\epsilon$$ where $$\gamma(x)$$ is the Gaussian density function, $$(2 \pi)^{-n/2} e^{-\| x \|^2 / 2}$$.

There are some important things to note about this problem:

1. An obvious deterministic approach to try is to partition the space into cells and count the number of cells that intersect with $$K$$. Such a na&iuml;ve counting algorithm is probably not very efficient - there would potentially be too many cells to count. For instance, dividing the space into hypercubes and counting the number of hypercubes that intersect with $$K$$ is not a practical approach - the volume of $$K$$ can be as high as $$\mathrm{vol} (\mathrm{Ball}_n (\mathbf{0},\sqrt{n})) \sim e^{\Theta(n)}$$ and as low as $$\mathrm{vol} (\mathrm{Ball}_n (\mathbf{0},1)) \sim \Theta(n)^{-n}$$. Any na&iuml;ve counting algorithm that is efficient for $$\mathrm{Ball}_n (\mathbf{0},\sqrt{n})$$ is not accurate for $$\mathrm{Ball}_n (\mathbf{0},1)$$ and vice-versa. Can one hope for polynomial-time (in $$n$$ and $$\epsilon$$) algorithms?
2. A similar issue lies with a na&iuml;ve Monte-carlo approach. Given a bounding hypercube $$H$$ containing $$K$$, the volume of $$K$$ can be as low as $$ \mathrm{vol} (H) \Theta(n)^{-n}$$ (if $$K$$ is a sphere) and on average $$\Theta(n)^{n}$$ points would have to be sampled to even hit $$K$$ once.

While several of the prior results to 

## The seminal work of Martin Dyer et al

Martin Dyer et al \[[^DFK89]\] propose the first of what is known as a "Multiphase Monte Carlo" randomized algorithm for volume estimation. The idea is simple - instead of directly estimating $$\frac{\mathrm{vol} (K)}{\mathrm{vol} (H)}$$, which can be exponentially small, they propose to estimate $$\mathrm{vol} (K)$$ by expressing it as scaling of a product of polynomially many factors $$\Lambda_i \in [1,2]$$ which are in turn estimated. $$m = cn \log{n}$$ domains $$K_1 \subseteq \dots \subseteq K_m$$ are generated, and $$\Lambda_i$$ is defined as $$\mathrm{vol}(K_i)/\mathrm{vol}(K_{i−1})$$. By telescopic product, this becomes equal to the volume scaled by the inverse of $$ \mathrm{vol} (K_0)$$ which is a known quantity when $$K_0$$ is chosen as the unit-sphere enclosed in $$K$$. Each $$\Lambda_i$$ can now be estimated by a Monte Carlo approach, but there is a catch - how does one generate a point uniformly randomly from $$K_i$$? A means to tackle this problem is to "design" a random walk in $$K_i$$ that mixes rapidly and whose stationary distribution is uniform in $$K_i$$.

\[[^DFK89]\] propose the "technical" random walk (it is named so for "technical reasons"). Starting from a point in the sphere the random walk evolves by moving along any of the $$2n$$ directions with equal probability if the new point remains in $$K_i$$ and stays put if the new point lies outside $$K_i$$. While the convergence of this random walk itself can be asserted using well known techniques, it is not clear that such a random walk would converge in polynomial time. A main result of theirs is to show that this walk approaches its stationary distribution within an absolute error that is exponentially small in time $$\Omega^* (n^{19})$$. This result is established using a bound on the convergence rate of a reversible markov chain in terms of its **conductance**, $$\Phi$$ and showing that the grid random walk has polynomially bounded conductance. The resistance ($$1-\Phi$$) of a random walk intuitively captures the steady state likelihood of getting trapped in some set of states of the markov chain.

**Lazy random walk in $$K$$ with $$\delta$$-steps**:
Ravi Kannan et al \[[^KLS97]\] present an alternate continuous random walk with a significantly faster mixing time. With equal probability, either:
1. generate a uniformly random point at a distance of $$\delta$$ from the current point. The walk updates to the new point if it lies in $$K$$. If it is not in $$K$$, the walk stays at the same point.
2. stay at the same point.

One can expect that if the random walk enters into a sharp "corner" of $$K$$, there may be an exponentially small probability for the walk to update to a new point. Since the stationary distribution of this markov chain is uniform, the conductance at some point $$x$$ can now simply be defined as a density:
\begin{equation}
\ell_\delta (x) = \frac{\mathrm{vol} (K \cap \mathrm{Ball} (x, \delta))}{\mathrm{vol} (\mathrm{Ball} (x, \delta))}
\end{equation}
While it may be unreasonable to expect small conductance at all points in $$K$$ (in particular, at the boundaries of $$K$$ the conductance is no more than $$0.5$$), does a large average conductance imply rapid mixing? The answer is in the affirmative and and is given by \[[^KLS97]\].

## Sandwiching to reduce average conductance
Sandwiching $$K$$ is to find a linear transform $$A$$ such that the ratio of the radii of circumscribed and inscribed balls is small $$AK$$. In the setting considered in \[[^CV16]\], the sandiching ratio by definition is at most $$\sqrt{n}$$. However in the more general setting as considered in \[[^KLS97]\] and \[[^DFK89]\], there is the additional need to sandwich $$K$$, even if approximately in order to reduce the average conductance (refer to \[[^KLS97]\] for additional discussion).

### L&ouml;wner-John Ellipsoid
For some convex body $$K \subseteq \mathbb{R}^n$$, let $$E_{\mathrm{john}}$$ denote the ellipsoid containing $$K$$ with minimum volume. Then, shrinking $$E_{\mathrm{john}}$$ by a factor of $$n$$ gives an ellipsoid that is entirely contained in $$K$$. We do not know how to construct the L&ouml;wner-John Ellipsoid unless strong conditions are imposed on $$K$$. It is apparent that these ellipsoids provide a sandwiching ratio of $$n$$ by a linear transformation that takes the ellipsoids to a ball. \[[^LS93]\] however do show a randomized algorithm that achieves an "approximate" sandwiching ratio of $$n$$. The slack in approximation is that all of $$K$$ need not lie within the ellipsoids, but most of it should.

**Theorem 1** \[[^KLS97]\]:
It is possible to sample $$N$$ points $$\{v_1,\dots,v_N\}$$ from $$K$$ in time $$\mathcal{O}^* (n^4 + Nn^3)$$ such that
1. **almost uniform**: each $$v_i$$ comes from a near uniform distribution (total variation distance to uniform is bounded by $$\epsilon$$)
2. **almost pairwise independent**: $$i,j \in [N]$$ such that $$i \ne j$$ and $$A, B \subset K$$,
\begin{equation\*}
	\vert \mathrm{Pr} (v_i \in A ; v_j \in B) - \mathrm{Pr} (v_i \in A) \mathrm{Pr} (v_j \in B) \vert \le \epsilon
\end{equation\*}

**Algorithm**: Starting from distribution $$Q_0$$, perform a lazy random walk in $$K$$ with $$\delta$$-steps and output the point generated after $$\lceil (801 n) \ln{\frac{5}{\epsilon} \left( \frac{d}{\delta}\right)^2} \rceil$$ hops. 


[^DFK89]: Dyer, M., Frieze, A., Kannan, R.: [A Random Polynomial Time Algorithm for Approximating the Volume of Convex Bodies](https://dl.acm.org/citation.cfm?id=73043), Proc. of the 21st Annual ACM Symposium on Theory of Computing, 1989, pp. 375–381
[^KLS97]: Kannan, R., Lov&aacute;sz, L., Simonovits, M.: [Random walks and an $$O^*(n^5)$$ volume algorithm](http://web.cs.elte.hu/~lovasz/vol5.pdf). Random Structures and Algorithms $$\mathbf{1}$$, 1-50 (1997)
[^LS93]: Lov&aacute;sz, L., Simonovits, M.: [Random walks in a convex body and an improved volume algorithm](http://matmod.elte.hu/~lovasz/vol7.pdf) Random Struct. and Algorithms 4, 359–412 (1993)
[^CV16]: B. Cousins and S. Vempala. Gaussian cooling and $$O^*(n^3)$$ algorithms for volume and Gaussian volume. Preprint, [arXiv:1409.6011v2](https://arxiv.org/abs/1409.6011), 2016.
