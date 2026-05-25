---
title: "OMSCS: CS 6601 Artificial Intelligence"
date: 2024-05-02
draft: false
categories: [learning, omscs]
tags: [omscs, georgia-tech, cs-6601, artificial-intelligence]
description: "My reflection on CS 6601 AI — my first OMSCS course."
---

CS 6601 Artificial Intelligence was my first OMSCS course in Spring 2024. Starting the program with a single course felt like the right call, and AI turned out to be a solid choice — it's a broad survey that covers search, probability, decision theory, and machine learning, all tied together by the thread of building intelligent agents.

## What The Course Covered

The course is organised around the idea of an intelligent agent perceiving its environment and acting upon it. It started with search — the foundation of many AI problems. Uninformed search (BFS, DFS, uniform-cost) then informed search with A* and the critical concept of admissible and consistent heuristics. The heuristic quality directly determines whether A* finds the optimal path efficiently or explores the entire space. Local search methods like hill climbing and simulated annealing followed, plus constraint satisfaction problems with backtracking and arc consistency.

Then came probability and Bayesian networks — a substantial portion of the course. Building and reasoning with Bayes nets, understanding conditional independence through d-separation, and performing inference through variable elimination and sampling. Hidden Markov Models for sequence-based reasoning covered filtering, prediction, and smoothing.

Decision theory and reinforcement learning were next. Markov Decision Processes — the formal framework for sequential decision-making under uncertainty. Value iteration and policy iteration for solving MDPs, then the transition to reinforcement learning where the model isn't known in advance. Q-learning and SARSA, and the fundamental exploration vs. exploitation tradeoff that underpins all RL.

The final portion covered machine learning basics: decision trees, k-NN, SVMs, neural networks, and the core concepts of overfitting, cross-validation, and the bias-variance tradeoff.

## What I Built (Conceptually)

The assignments were all about implementing algorithms from scratch. Search: implementing A* with different heuristics, seeing how heuristic quality affects performance. Bayes nets: building network structures, performing exact inference, and understanding when approximate inference is necessary. Reinforcement learning: implementing Q-learning agents that learned to make decisions through trial and error in grid-world environments.

The reinforcement learning assignment was the most memorable — watching an agent go from random exploration to purposeful navigation through repeated interaction with its environment made the theoretical concepts tangible in a way that reading about them never could.

## What Was Hard

The Bayes net inference assignments were the most conceptually demanding. Variable elimination requires careful bookkeeping of factors, and understanding d-separation for determining conditional independence takes practice. The difference between exact and approximate inference — and knowing when each is appropriate — was a subtle point that took a while to internalise.

The RL material was also challenging in a different way — not because the algorithms are complex (Q-learning is deceptively simple) but because the conceptual shift from supervised learning (learn from labelled data) to reinforcement learning (learn from delayed rewards) requires a different way of thinking about feedback.

## Concepts That Stuck

- **Search is everywhere.** Many AI problems that don't look like search at first glance can be reframed as searching a state space. This insight has been useful across later OMSCS courses too.
- **Heuristics matter.** A good heuristic makes A* efficient; a bad one makes it useless. The tradeoff between heuristic accuracy and computational cost is a recurring theme.
- **Bayes nets are a thinking tool first.** Beyond the inference algorithms, the process of modelling a problem as a Bayes net — deciding what variables matter and how they relate — is valuable in its own right.
- **Exploration vs. exploitation is a fundamental tension.** It shows up in RL, but also in real-world decisions: when do you try something new versus stick with what works?

## What's Next

CS 6601 was a strong start to the program. It covered enough breadth to give me a solid foundation, with enough depth in each area to make the implementation work meaningful. The concepts here — particularly search, Bayes nets, and RL — would show up repeatedly in later courses.

*Iterated with my own opinion and thoughts with the help of AI.*
