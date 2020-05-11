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
\times  \mathbb Z^{n_{ix}} \rightarrow \mathbb R^{n_{ec}}$ are non linear *equality constraints*, and $\mathbf
c_i:  \mathbb R^{n_{cx}} \times  \mathbb Z^{n_{ix}} \rightarrow \mathbb R^{n_{ic}}$ are non linear *inequality
constraints*. Note that the objectives and constraints also depend from an added value $s$ representing some
stochastic variable. Both equality and inequality constraints are considered as satisfied whenever their definition
is met within a tolerance $\mathbf c_{tol}$.

Given the generic form used to represent a problem, `pagmo` is suitable to solve a broad range of optimization problems,
ranging from single and multiobjective problems to box-bounded and non linearly constrained problems to stochastic problems to continuous, integer and mixed integer problems.

# The pagmo jargon
The discussion on the relation between artificial evolution and mathematical optimization is an
interesting one [@smith:1978]. In `pagmo` optimization, 
of all types, is regarded as a form of evolution. Solving an optimization problem
is, in `pagmo`, described as *evolving* a *population*. 
Regardless on whether the user is using, as a solver, a SQP, an interior point optimizer an evolutionary
strategy or some meta-heuristic, in `pagmo` he will always have to call a method called
*evolve* to improve over the initial solutions stored in a *population*. A *population*
may or may not live in an *island*. When it does, its *evolution* is delegated to a different computational
unit (a process, thread or remote CPU). Stretching this jargon even further, in `pagmo` a set of *islands* concurring
to the same optimization is called an *archipelago*. When solutions are also exchanged among *populations* living on
the same *archipelago*, the quality of the overall optimization is often improved [@izzo:2012].
This exchange of information among different solvers is referred to as *migrations* and the allowed migration routes 
(i.e. what *island* is in connected) as the *topology* of the *archipelago*.

# Exploiting parallelism

## Island Model

## Cuncurrent fitness evaluations

# Acknowledgments
We acknowledge the support of the Google Summer of Code initiative, the European Space Agency Summer of Code in Space and Luca Guj and Dow Corporation during different phases of the devlopment.

# References