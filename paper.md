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
In some situations it is preferable to parallelize at a finer grain the *evolution* pipeline
(e.g., if the objective function evaluation is extremely costly). For this purpose, `pagmo`
provides a *batch fitness evaluation* framework which can be used by selected algorithms to
perform in parallel the objective function evaluation of multiple independent decision vectors.
The parallel evaluation can be performed by multiple threads, processes or even by GPU devices
(via, e.g., OpenCL or CUDA). As a caveat, whereas the island model can be employed with any solver,
the *batch fitness evaluation* framework must be explicitly supported by the optimisation algorithm
in use.


# Code Design

## C++
`pagmo` is written in standard-compliant C++17, and it extensively employs modern programming techniques.
*Type erasure* is used pervasively throughout the codebase to provide a form of runtime
polymorphism which is safer and more ergonomic than traditional object-oriented programming.
Template meta-programming techniques
are used for compile-time introspection, and, with the
help of sensible defaults, they allow to minimise the amount of boilerplate
needed to define new optimisation problems.
`pagmo` is designed for extensive customisation: any element of the framework
(including solvers, islands, batch fitness evaluators, archipelago topologies, migration policies, etc.)
can easily be replaced with custom implementations tailored for specific needs.

## Python
In order to provide an interactive mode of usage (and in order to
participate in the ecosystem of what is arguably the most popular
language for scientific computing today), `pagmo`
provides a complete set of Python bindings called `pygmo`, implemented via
`pybind11` [@pybind11]. `pygmo` exposes all `pagmo` features, including the ability
to implement new problems, solvers, batch evaluators, topologies etc. in pure Python,
using an API which closely matches the C++ `pagmo` API. Additionally, `pygmo` offers
Python-specific features, such as the ability to use `ipyparallel` [@ipyparallel]
for cluster-level parallelisation, and wrappers to use minimisers from `Scipy` [@2020SciPy-NMeth]
as `pygmo` algorithms.

## Testing and documentation
The `pagmo` development team places a strong emphasis on automated testing. The code is fully covered
by unit tests, and the continuous integration pipeline checks that the code compiles and runs correctly
on a variety of operating systems (Linux, OSX, Windows) using different compilers (GCC, Clang, MSVC).
Both the C++ and Python APIs are fully documented, and as a policy we require that every PR to `pagmo`
or `pygmo` must not decrease testing or documentation coverage.

# Some API examples
In this section, we will show how `pagmo` and `pygmo` can be used
to solve a very simple optimisation problem using the Differential
Evolution (DE) algorithm. The problem that we will solve is the
minimisation of the unidimensional sphere function,
$$
f\left( x \right) = x^2,
$$
subject to the box bounds $x \in \left[0, 1 \right]$. This is,
of course, a trivial problem with solution $x=0$, and it is
used here only for didactic purposes.

## C++
```c++
#include <iostream>
#include <utility>

#include <pagmo/algorithm.hpp>
#include <pagmo/algorithms/de.hpp>
#include <pagmo/population.hpp>
#include <pagmo/problem.hpp>
#include <pagmo/types.hpp>

using namespace pagmo;

// Definition of the optimisation problem.
struct sphere_1d
{
  // Definition of the box bounds.
  std::pair<vector_double, vector_double> get_bounds() const
  {
    return {{0.}, {1.}};
  }
  // Definition of the objective function.
  vector_double fitness(const vector_double &dv) const
  {
    return {dv[0] * dv[0]};
  }
};

int main()
{
  // Create a random population of 20 initial
  // guesses for the sphere_1d problem.
  population pop{sphere_1d{}, 20};

  // Create the optimisation algorithm.
  // We will use 500 generations.
  algorithm algo{de{500}};

  // Run the optimisation, which will
  // produce a new "evolved" population.
  auto new_pop = algo.evolve(pop);

  // Print to screen the fitness of the
  // best solution in the new population.
  std::cout << "Fitness of the best solution: "
            << new_pop.champion_f()[0] << '\n';
}
```

In ``pagmo``, decision vectors and problem bounds are represented by ``vector_double``,
which is currently just an alias for ``std::vector<double>``. The fitness function also returns
a ``vector_double``, because, generally-speaking, the fitness vector must accommodate
multiple scalar values to represent multiple objectives and constraints. Here, however,
the ``sphere_1d`` problem is single-objective and unconstrained, and thus
the only element in the fitness vector will be the value of the objective function.

In this example, 20 initial conditions for the optimisation are randomly chosen within the
problem bounds when creating the ``pop`` object. It is of course possible to set explicitly
the initial conditions, if so desired. The Differential Evolution algorithm is then created,
specifying that the evolution will run for 500 generations (i.e., this is a stopping
criterion for the Differential Evolution algorithm).

The initial population ``pop`` is then evolved, and the result is a new population of
optimised decision vectors. The fitness of the best decision vector (the "champion")
is then printed to screen.

## Python
```python
from pygmo import problem, algorithm, population, de

# Definition of the optimisation problem.
class sphere_1d:
  # Definition of the box bounds.
  def get_bounds(self):
    return ([0], [1])
  # Definition of the objective function.
  def fitness(self, dv):
    return [dv[0]**2]

# Create a random population of 20 initial
# guesses for the sphere_1d problem.
pop = population(sphere_1d(), 20)

# Create the optimisation algorithm.
# We will use 500 generations.
algo = algorithm(de(500))

# Run the optimisation, which will
# produce a new "evolved" population.
new_pop = algo.evolve(pop)

# Print to screen the fitness of the
# best solution in the new population.
print(new_pop.champion_f)
```

As shown in this example, the ``pygmo`` Python API very closely follows the ``pagmo`` C++ API.

# Availability
Both `pagmo` and `pygmo` are available in the `conda` package manager through the `conda-forge`
community-driven channel. Additionally, the core team also maintains `pip` packages for Linux.

The wider `pagmo` user community provides also additional packages for Arch Linux, OSX (via Homebrew)
and FreeBSD.

# Acknowledgments
We acknowledge the support of the Google Summer of Code initiative, the European Space Agency Summer
of Code in Space and Dow Corporation during different phases of the development. 
Many of our colleagues and friends have, in the years, supported the project contributing to evolve
its code base and API to what we have today. We would like to mention, in particular, Luís Felismino Simões, 
Marek Ruciński, Marcus Märtens and Krzysztof Nowak.

# References