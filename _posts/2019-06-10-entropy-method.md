---
layout: post
title: The Entropy method
use_math: true
comments: true
---

The entropy method is a beautiful method to show existential results in extremal combinatorics. This blog post will discuss the entropy method through the lens of minimizing combinatorial discrepancy - the playing field where this technique was first introduced. 

## Introduction
In order to motivate the entropy method, let's first discuss the notion of combinatorial discrepancy. Consider a set system $$(X,\mathcal{S})$$ where $$|X| = n$$. The combinatorial discrepancy of this set system corresponds to a red-blue coloring of the points in $X$ in such a way that each set is approximately balanced in both colors. In other words, the goal is to find a labelling $$\sigma : X \to \{ -1,+1 \}$$ such that, $$\max_{S \in \mathcal{S}} |\sum_{x \in S} \sigma (x)|$$ is minimized. This is a classically studied problem in quasi-randomness, and several strong deterrents exist for polynomial time algorithms for colorings of set systems with $$o(\sqrt{n})$$ discrepancy. Therefore, a natural question to ask is, does there exist a matching upper bound - an algorithm to compute an $$O (\sqrt{n})$$ discrepancy coloring for every set system, at least when $$m = O(n)$$. The answer to this question was unknown until recently, answered by a paper due to Bansal et al []. However, in 1923, Spencer devised the entropy method to show that an existential result for colorings with $$O(\sqrt{n})$$ discrepancy when $$m = O(n)$$.


## Outline

Spencer's proof relies on showing the following result:

Given a set system $$(X,\mathcal{S})$$, there exists a partial coloring of the set system $$\sigma : X \to \{-1,0,+1\}$$ such that at least $$n/10$$ of the vertices are colored $$\pm 1$$, and such that the maximum discrepancy of any of the sets over this partial coloring is, for some constant $$c$$,
\begin{equation}
\max_{S \in \mathcal{S}} | \sum_{x \in S} \sigma(x) | \le \sqrt{n \log m}
\end{equation}

## The entropy method

