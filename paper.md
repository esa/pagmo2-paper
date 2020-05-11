---
title: 'A parallel global multiobjective framework for optimization: pagmo'
tags:
  - C++
  - Python
  - optimization
  - evolution
  - island model
  - meta-heuristics
authors:
  - name: Francesco Biscani
    affiliation: 1
  - name: Dario Izzo
    orcid: 0000-0002-9846-8423
    affiliation: 2
affiliations: 
 - name: Max Planck Institute for Astronomy (Heidelberg, D)
   index: 1
 - name: Advanced Concepts Team, European Space Research and Technology Center (Noordwijk, NL)
   index: 2

date: 5 May 2020
bibliography: paper.bib
---

# Summary

In this paper we introduce `pagmo`, a C++ scientific library for massively parallel optimization. `pagmo` is built around the idea of providing a unified interface to optimization algorithms and problems, and to make their deployment in massively parallel environments easy.

Efficient implementantions of bio-inspired and evolutionary algorithms are sided to state-of-the-art optimization algorithms (Simplex Methods, SQP methods, interior points methods, etc.) and can be used cuncurrently (also together with algorithms coded by the user) to build an optimization pipeline exploiting algorithmic cooperation via the asynchronous, generalized island model [@izzo:2012]

`pagmo` can be used to solve constrained, unconstrained, single objective, multiple objective, continuous and integer optimization problems, stochastic and deterministic problems, as well as to perform research on novel algorithms and paradigms and easily compare them to state-of-the-art implementations of established ones.

For users that are more comfortable with the Python language, the package `pygmo` following as closely as possible the `pagmo` API is also available.

# The optimization problem 
In `pagmo` optimization problems are considered to be in the form:

$$
  \begin{array}{rl}
  \mbox{find:}      & \mathbf {lb} \le \mathbf x \le \mathbf{ub}\\
  \mbox{to minimize: } & \mathbf f(\mathbf x, s) \in \mathbb R^{n_{obj}}\\
  \mbox{subject to:} & \mathbf {c}_e(\mathbf x, s) = 0 \\
                     & \mathbf {c}_i(\mathbf x, s) \le 0
  \end{array}
$$
where $\mathbf x \in \mathbb R^{n_{cx}} \times  \mathbb Z^{n_{ix}}$ is called *decision vector* or
*chromosome*, and is made of $n_{cx}$ real numbers and $n_{ix}$ integers (all represented as doubles). The
total problem dimension is then indicated with $n_x = n_{cx} + n_{ix}$. $\mathbf{lb}, \mathbf{ub} \in
\mathbb R^{n_{cx}} \times  \mathbb Z^{n_{ix}}$ are the *box-bounds*, $\mathbf f: \mathbb R^{n_{cx}} \times
\mathbb Z^{n_{ix}} \rightarrow \mathbb R^{n_{obj}}$ define the *objectives*, $\mathbf c_e:  \mathbb R^{n_{cx}}
\times  \mathbb Z^{n_{ix}} \rightarrow \mathbb R^{n_{ec}}$ are non linear *equality constraints*, and $ \mathbf
c_i:  \mathbb R^{n_{cx}} \times  \mathbb Z^{n_{ix}} \rightarrow \mathbb R^{n_{ic}}$ are non linear *inequality
constraints*. Note that the objectives and constraints may also depend from an added value $s$ seeding the
values of any number of stochastic variables. This allows also for stochastic programming tasks to be represented by
this class. A tolerance is also considered for all constraints and set, by default, to zero. 

# The pagmo jargon

# Exploiting parallelism

## Island Model

## Cuncurrent fitness evaluations

# Acknowledgments
We acknowledge the support of the Google Summer of Code initiative, the European Space Agency Summer of Code in Space and Luca Guj and Dow Corporation during different phases of the devlopment.

# References