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
 - name: Advanced Concepts Team, European Space Research and Technology Center (Noordwijk, NL)
   index: 2
 - name: Max Planck Institute for Astronomy (Heidelberg, D)
   index: 1
date: 5 May 2020
bibliography: paper.bib
---

# Summary

`pagmo` is a C++ scientific library for massively parallel optimization. It is built around the idea of providing a unified interface to optimization algorithms and problems, and to make their deployment in massively parallel environments easy.

Efficient implementantions of bio-inspired and evolutionary algorithms are sided to state-of-the-art optimization algorithms (Simplex Methods, SQP methods, interior points methods, …) and can be easily mixed (also with algorithms coded by the user) to build a meta-algorithm exploiting algorithmic cooperation via the asynchronous, generalized island model [@izzo2012]

`pagmo` can be used to solve constrained, unconstrained, single objective, multiple objective, continuous and integer optimization problems, stochastic and deterministic problems, as well as to perform research on novel algorithms and paradigms and easily compare them to state-of-the-art implementations of established ones.

For users that are more comfortable with Python, the package `pygmo` following as closely as possible the `pagmo` API is also provided.

# Methods 

# Acknowledgments
We acknowledge the support of the Google Summer of Code initiative, the European Space Agency Summer of Code in Space and Luca Guj and Dow Corporation during different phases of the devlopment.

# References