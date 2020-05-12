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
Mathematical optimization is pervasive in all quantitative sciences. The ability to find good
parameters values in a generic numerical experiment while meeting complex constraints is of great importance
and, as such, has always been an active research topic of mathematics, numerics and, more recently, artificial intelligence.

Given the vast amount and diversity of optimization problems, as well as of solution approaches,
and considering the need to be able to exploit modern computational architectures, the development of 
a tool able to help in such a pervasive task is not trivial.

In this paper we introduce `pagmo`, a C++ scientific library for massively parallel optimization. `pagmo` is built around the idea of providing a unified interface to optimization algorithms and problems, and to make their deployment in massively parallel environments easy.

Efficient implementantions of bio-inspired and evolutionary algorithms are sided to state-of-the-art optimization algorithms (Simplex Methods, SQP methods, interior points methods, etc.) and can be used cuncurrently (also together with algorithms coded by the user) to build an optimization pipeline exploiting algorithmic cooperation via the asynchronous, generalized island model [@izzo:2012]

`pagmo` can be used to solve constrained, unconstrained, single objective, multiple objectives, continuous and integer optimization problems, stochastic and deterministic problems, as well as to perform research on novel algorithms and paradigms and easily compare them to state-of-the-art implementations of established ones.

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

Note that there is no special treatment of a possible linear part of the *objectives* or *constraints*, and as such solvers in `pagmo`
have no additional help to approach linear programming tasks.

Given the generic form used to represent a problem, `pagmo` is suitable to solve a broad range of optimization problems,
ranging from single and multiobjective problems to box-bounded and non linearly constrained problems to stochastic problems to continuous, integer and mixed integer problems.

# The pagmo jargon
The discussion on the relation between artificial evolution and mathematical optimization is an
interesting one [@smith:1978]. In `pagmo` optimization, 
of all types, is regarded as a form of evolution. Solving an optimization problem
is, in `pagmo`, described as *evolving* a *population*. Each *decision vector* is thus referred to also
as *chromosome* and its *fitness* is defined as a vector containing *objectives*, *equality constraints* and *inequality
constraints* in this order. Regardless on whether the user is using, as a solver, a sequential quadratic programming approach, 
an interior point optimizer an evolutionary strategy or some meta-heuristic, in `pagmo` he will always have to call a method called
*evolve* to improve over the initial solutions stored in a *population*. A *population*
may or may not live in an *island*. When it does, its *evolution* is delegated to a different computational
unit (a process, thread or remote CPU). Stretching this jargon even further, in `pagmo` a set of *islands* concurring
to the same optimization is called an *archipelago*. When solutions are also exchanged among *populations* living on
the same *archipelago*, the quality of the overall optimization is often improved [@izzo:2012].
This exchange of information among different solvers is referred to as *migrations* and the allowed migration routes 
(affecting the overall process significantly [@rucinski:2010]) as the *topology* of the *archipelago*. 

# Exploiting parallelism
Parallelizing optimization tasks in a generic fashion is one of the leading software design principles of `pagmo`.
According to the type of optimization task, and in particular to the computational weight of computing the
problem *fitness* function, a different granularity of the parallelization option may be ideal. 

## Island Model
As a coarse-grained parallelism, `pagmo` offers an implementation of the so-called generalized
island model [@izzo:2012]. Early ideas on distributing genetic algorithms over multiple CPUs were
developed in the early 90s by Reiko Tanese, one of John Holland's students [@tanese:1989]. The idea
that migrations could improve the quality of the solutions obtained for some optimization task
as well as offer a quasi-linear speedup was, though, confined mainly to genetic algorithms and called
island model. In `pagmo` any solver, inspired by the darwinian evolution paradigm, by swarm intelligence, 
by any meta-heuristics or based on mathematical optimality conditions is allowed to exchange
information during an *evolution* with other solvers connected to it via defined *migration* paths.

## Cuncurrent fitness evaluations
In some situations it is preferable to parallelize at a finer grain the *evolution* pipeline. In 
these situation a batch fitness..... 


# Code Design
Here we should mention the challenges of doing this for C++ and Python,
the choices made for type erasure, tbb, threads etc....

# Some API examples

# Acknowledgments
We acknowledge the support of the Google Summer of Code initiative, the European Space Agency Summer
of Code in Space and Dow Corporation during different phases of the development. 
Many of our colleagues and friends have, in the years, supported the project contributing to evolve
its code base and API to what we have today. We would like to mention, in particular, Luís Felismino Simões, 
Marek Ruciński, Marcus Märtens and Krzysztof Nowak.

# References