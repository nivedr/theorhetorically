---
layout: post
title: Fast estimation of Gaussian volumes
use_math: true
comments: true
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

## Prior MCMC based approaches

Martin Dyer et al \[[^DFK89]\] propose the first of what is known as a "Multiphase Monte Carlo" randomized algorithm for volume estimation. The idea is simple - instead of directly estimating $$\frac{\mathrm{vol} (K)}{\mathrm{vol} (H)}$$, which can be exponentially small, they propose to estimate $$\mathrm{vol} (K)$$ by expressing it as scaling of a product of polynomially many factors $$\Lambda_i \in [1,2]$$ which are in turn estimated. $$m = cn \log{n}$$ domains $$K_1 \subseteq \dots \subseteq K_m$$ are generated, and $$\Lambda_i$$ is defined as $$\mathrm{vol}(K_i)/\mathrm{vol}(K_{i−1})$$. By telescopic product, this becomes equal to the volume scaled by the inverse of $$ \mathrm{vol} (K_0)$$ which is a known quantity when $$K_0$$ is chosen as the unit-sphere enclosed in $$K$$. Each $$\Lambda_i$$ can now be estimated by a Monte Carlo approach, but there is a catch - how does one generate a point uniformly randomly from $$K_i$$? A means to tackle this problem is to "design" a random walk in $$K_i$$ that mixes rapidly and whose stationary distribution is uniform in $$K_i$$.

{:.center}
![problem setting](../images/2018-10-01-CousinsVempalaVolume/montecarlo.png "Setting of the problem")

{:.center}
**Figure 1:** Each $$K_i$$ is the intersection of $$K$$ with a ball

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

### Sandwiching to reduce average conductance
Sandwiching $$K$$ is to find a linear transform $$A$$ such that the ratio of the radii of circumscribed and inscribed balls is small $$AK$$. In the setting considered in \[[^CV16]\], the sandiching ratio by definition is at most $$\sqrt{n}$$. However in the more general setting as considered in \[[^KLS97]\] and \[[^DFK89]\], there is the additional need to sandwich $$K$$, even if approximately in order to reduce the average conductance (refer to \[[^KLS97]\] for additional discussion). A nice result bounding the sandwiching ratio of any convex body $$K$$ are the L&ouml;wner-John ellipsoids which state that any convex body $$K$$ is perfectly sandwiched between the ellipsoid, $$E_{\mathrm{john}}$$ containing $$K$$ with minimum volume and the ellipsoid generated by shrinking $$E_{\mathrm{john}}$$ by a factor of $$n$$. However, we do not know how to construct the L&ouml;wner-John Ellipsoid unless strong conditions are imposed on $$K$$. It is apparent that these ellipsoids provide a sandwiching ratio of $$n$$ by a linear transformation that transforms the ellipsoids to a ball. \[[^LS93]\] however do show a randomized algorithm that achieves an "approximate" sandwiching ratio of $$n$$. The slack in approximation is that all of $$K$$ need not lie within the ellipsoids, but most of it should.

It is important to know that in general, good isoperimetric inequalities would give good bounds on conductance. An important related conjecture in the context of isoperimetric inequalities with wide-ranging consequences is the KLS conjecture \[[^KLS95]\] which states that for any log-concave density, there exists a subset bounded by a hyperplane which captures the worst case "bottleneckedness" of the distribution upto a constant. The bottleneckedness is defined in terms of the Cheeger constant of the distribution, $$\Phi_{P}$$ which is the ratio of the boundary measure of a subset to the measure of the subset minimkzed over all subsets with measure less than $$0.5$$:
\begin{equation}
\Phi_{P} = \inf_{S \subset R^n} \frac{p(\delta S)}{p(S)}, \quad \text{where } p(\delta S) = \inf_{\epsilon \to 0^+} \frac{p(x : d(x,S) \le \epsilon) - p(S)}{\epsilon}
\end{equation}

## Prior simulated annealing based approaches

Lov&aacute;sz and Vempala \[[^LV06]\] present a different approach based on simulated annealing. The idea here is to lift $$K$$ into $$n+1$$ dimensions to form a pencil of length $2 \sqrt{n}$ with cross section $$K$$ that sharpens to a point called $$K'$$. The act of sharpening the pencil does not decrease the volume of the pencil by more than half, which leads to the inequality: 
\begin{equation}
\sqrt{n} \mathrm{Vol} (K) \le \mathrm{Vol} (K') \le 2\sqrt{n} \mathrm{Vol} (K) 
\end{equation}
Thus, if one can estimate $$\mathrm{Vol} (K')$$ to within a small relative error, simple Monte Carlo sampling can be used to go the last mile to estimate $$\mathrm{Vol} (K)$$.

The crucial observation made by Lov&aacute;sz and Vempala is that the integral $$Z(a) =  \int_{K'} e^{-a \vec{x}_0} \mathrm{d} \vec{x}$$ (where $\vec{x}\_i$ is the $i^{th}$ coordinate of $\vec{x}$) depending on the value of $$a$$ can be a good estimate for $\mathrm{Vol} (K')$ as well as the volume of the unit-sphere in $$n$$ dimensions. 

{:.center}
![problem setting](../images/2018-10-01-CousinsVempalaVolume/sharpened.png "Sharpened pencil")

{:.center}
**Figure 2:** $$K$$ lifted into $$n+1$$ dimensions to create a "pencil"

If $$a = 0$$, then $$Z(a)$$ exactly equals $$\mathrm{Vol} (K')$$, and therefore if $$a$$ is small ($$\le \frac{\epsilon^2}{\sqrt{n}}$$), then $$Z(a)$$ is within a relative error of $$\epsilon$$ to $$\mathrm{Vol} (K')$$. On the other hand, allowing $$a$$ to be large ($$=2n$$) computes the integral over the "tip" of the pencil to within a relative error of $$\epsilon$$ (as it is exponentially small over the rest of the pencil). Thus, they propose to estimate $$\mathrm{Vol} (K)$$ by generating a sequence $$a_0 > a_1 > \dots > a_m$$ where $$a_0 > 2n$$ and $$ \frac{\epsilon^2}{\sqrt{n}} > a_m$$ as:
\begin{equation}
\mathrm{Vol} (K) = \frac{1}{\sqrt{n}} \underbrace{Z(a_0)}\_{(i)} \cdot \prod_{i=0}^{m-1} \underbrace{\frac{Z(a_{i+1})}{Z(a_{i})}}\_{(ii)} \cdot \underbrace{\frac{\sqrt{n} \mathrm{Vol} (K)}{\mathrm{Vol} (K')}}\_{(iii)}
\end{equation}
where $$(i)$$ is known by to within $$\epsilon$$ relative error, $$(iii)$$ is estimated by Monte Carlo sampling, and the estimation of $$(ii)$$, which is the heart of the matter is discussed below.

### Estimating $$Z(a_{i+1}) / Z(a_i)$$

Observe that the ratio of integrals of exponential functions over some domain $$D$$ has a nice property - it can be expressed as the expectated ratio of the two exponential functions over a measure proportional to the density of the first exponential function restricted to $$D$$. It is a simple exercise to show that
\begin{equation}
\frac{\int_D e^{- a_2^T \vec{x}} \mathrm{d} \vec{x}}{\int_D e^{- a_1^T \vec{x}} \mathrm{d} \vec{x}} = \mathbb{E} \left[ e^{-(a_2 - a_1)^T \vec{x}} \right], \quad \text{wrt the measure } \mu = \frac{e^{-a_1^T \vec{x}}}{\int_D e^{-a_1^T \vec{x}} \mathrm{d} \vec{x}} 
\end{equation}

## Outline in \[[^CV16]\]

Cousins and Vempala present a modification of a simulated annealing based algorithm that 

[^DFK89]: Dyer, M., Frieze, A., Kannan, R.: [A Random Polynomial Time Algorithm for Approximating the Volume of Convex Bodies](https://dl.acm.org/citation.cfm?id=73043), Proc. of the 21st Annual ACM Symposium on Theory of Computing, 1989, pp. 375–381
[^KLS97]: Kannan, R., Lov&aacute;sz, L., Simonovits, M.: [Random walks and an $$O^*(n^5)$$ volume algorithm](http://web.cs.elte.hu/~lovasz/vol5.pdf). Random Structures and Algorithms $$\mathbf{1}$$, 1-50 (1997)
[^LS93]: Lov&aacute;sz, L., Simonovits, M.: [Random walks in a convex body and an improved volume algorithm](http://matmod.elte.hu/~lovasz/vol7.pdf) Random Struct. and Algorithms 4, 359–412 (1993)
[^KLS95]: R. Kannan, L. Lovász, and M. Simonovits.: [Isoperimetric problems for convex bodies and a localization lemma](https://link.springer.com/article/10.1007/BF02574061). Discrete & Computational Geometry, 13:541-559, 1995
[^LV06]: Lovász, L., & Vempala, S. (2003b).: [Simulated annealing in convex bodies and an $$\mathcal{O} (n^4)$$ volume algorithm](https://www.semanticscholar.org/paper/Simulated-annealing-in-convex-bodies-and-an-O*(n4)-Lov%C3%A1sz-Vempala/257329c393b606d3b660aa831576fc3ab354fd52). In Proceedings of the 44th symposium on foundations of computer science (FOCS) (pp. 650–659).
[^CV16]: B. Cousins and S. Vempala. Gaussian cooling and $$O^*(n^3)$$ algorithms for volume and Gaussian volume. Preprint, [arXiv:1409.6011v2](https://arxiv.org/abs/1409.6011), 2016.
