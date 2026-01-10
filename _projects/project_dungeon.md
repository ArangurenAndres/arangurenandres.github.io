---
layout: default
title: "Quality–Diversity + Self-Adaptive Evolution Strategies for Procedural Dungeon Generation (with a Flask Demo)"
date: 2026-01-09
categories: [procedural-generation, evolutionary-computation, reinforcement-learning, game-ai]
preview_image: /assets/level_gen/es_model/sample_dungeon.png
tags: [MAP-Elites, quality-diversity, evolution-strategies, PCG, flask, python]
---


## TL;DR

I built a **simple but technically grounded** procedural content generation (PCG) project in Python that generates **2D dungeon-like grid levels** using:

- **MAP-Elites** (a Quality–Diversity algorithm) to maintain an **archive of diverse, high-quality levels**
- **Self-adaptive Evolution Strategies (ES)** to evolve levels *and* their **mutation behavior**
- A lightweight **playtesting loop** using **BFS shortest-path solvability** + a **stochastic “noisy agent”**
- A **Flask web app** to tune parameters, run the search, monitor **coverage / best fitness**, and visualize generated levels as PNGs

This is intentionally a **minimal “first step”** toward open-ended game content generation: rather than maximizing a single objective, the system builds a *repertoire* of solutions across a behavioral space.

---

## Motivation: Why Quality–Diversity for Game Content?

Classic PCG pipelines often optimize a single objective, e.g.:

- “maximize solvability”
- “maximize difficulty”
- “maximize novelty”

The result is usually a familiar failure mode: **mode collapse** in the search space. You get many variations of the same artifact because the optimization pressure converges to one prototype that satisfies the objective well.

For game development, that is not ideal. Game designers typically want:

- multiple *styles* of playable levels  
- multiple *difficulty profiles*  
- multiple “player experiences” (exploration-heavy vs. linear vs. dense obstacles)  

This is exactly what **Quality–Diversity (QD)** methods target: instead of returning *one best solution*, they produce an **archive of diverse elites**, each high-quality in its own region of a **behavior descriptor space**.

---

## Project Goal

**Generate a diverse collection of playable dungeon-like levels** under a simple evaluation loop and visualize them interactively.

A “level” here is a grid with:

- `#` walls  
- `.` floors  
- `S` start  
- `G` goal  

The core challenges:

1. **Solvability** (must be possible to reach goal)
2. **Difficulty / path structure** (not always trivial)
3. **Exploration / space usage** (avoid narrow corridors only)
4. **Balance** (avoid too empty or too blocked maps)
5. **Diversity** (produce many different *types* of levels)

---

## Representation: The Level Genome

Each individual is:

- a **grid** (2D array of tiles)
- a **self-adaptive mutation parameter** `sigma`

We treat the grid as the genome: flipping tiles (wall ↔ floor) changes the phenotype (the resulting dungeon).

We keep borders as walls and we fix:

- `S` at `(1,1)`
- `G` at `(h-2, w-2)`

This keeps evaluation simple and consistent.

---

## Playtesting: How We Evaluate a Level

Instead of using an LLM or a complex player model, we build a minimal playtesting loop:

### 1) Hard constraint: BFS solvability

We run BFS from `S` to `G`.

- If BFS fails → level is **invalid**, fitness = `-1e9`

This is common in PCG: solvability becomes a gatekeeper constraint.

### 2) Stochastic “noisy agent” rollouts

To emulate imperfect play, we also simulate a player:

- compute the shortest path from BFS
- at each step:
  - with probability `follow_prob` follow the shortest path
  - otherwise take a random valid move

From multiple rollouts, we compute:

- **success_rate**: how often the agent reaches `G`
- **visited_frac**: what fraction of walkable tiles are explored

This makes evaluation more “experience-aware” than BFS alone.

---

## Fitness: Normalized Multi-Objective Scalarization

The fitness combines several normalized terms:

- higher noisy success rate is better
- longer path length can increase challenge
- higher visited fraction indicates exploration / open layouts
- density penalty discourages extremely sparse/dense maps

\[
f = W_{SR}\cdot SR + W_{PATH}\cdot \hat{P} + W_{VF}\cdot VF - W_{DENS}\cdot \hat{D}
\]

Where:

- \(SR \in [0,1]\) is rollouts success rate
- \(\hat{P}\in[0,1]\) is normalized path length
- \(VF \in [0,1]\) is visited fraction
- \(\hat{D}\in[0,1]\) is normalized deviation from target density

**Why normalize?**  
Because if one term (e.g., raw path length) has a much larger scale, it can dominate the fitness and reduce diversity.

This also makes tuning the weights much easier.

---

## Quality–Diversity: MAP-Elites

### Core idea
Instead of optimizing a single best level, MAP-Elites builds an archive:

- discretize behavior space into bins
- each bin stores the *best elite* found for that behavioral region

### Archive structure
\[
\text{archive}[b] = (\text{fitness}, \text{individual}, \text{eval})
\]

Where \(b=(x,y)\) is the bin coordinate.

---

## Behavior Descriptors: Increasing Coverage

A key design choice is: **what defines “diversity”?**

Initially, using `(wall_density, success_rate)` often yields low coverage because:

- success rates cluster near 0 or 1
- bins become “sticky” and hard to reach

So the improved descriptor space is:

- **x = wall density bin**
- **y = path length bin** (normalized BFS path length)

This is smoother and more continuous, so MAP-Elites fills more bins.

**Coverage** is:

\[
\text{coverage}=\frac{\#\text{filled bins}}{\#\text{total bins}}
\]

---

## Evolution Strategies: Self-Adaptive Mutation

Each individual has a mutation rate `sigma` that evolves:

\[
\sigma' = \sigma \cdot \exp(\tau \cdot \mathcal{N}(0,1))
\]

This is the classic log-normal self-adaptation used in ES.

Intuition:

- some regions benefit from small tweaks (low sigma)
- some need large exploration (high sigma)
- successful mutation regimes survive through selection

So the system is learning **how to explore**, not just *what levels* to store.

---

## Large Jump Operator: Block Mutation

Local tile flips can be too conservative and fail to move across bins.

So I add a second mutation operator:

- with some probability (e.g., 0.20), pick a square block and set it all to walls or floors

This enables larger structural changes:
- new rooms
- big barriers
- open spaces

Which improves coverage in the descriptor space.

---

## Search Loop

At each iteration:

1. pick a parent from the archive  
   - optionally biased toward rare bins
2. mutate it (tile flips or block mutation)
3. evaluate (BFS + rollouts)
4. compute descriptor bin
5. insert if:
   - bin empty, or
   - fitness better than current elite

This is simple and effective.

---

## Flask Demo App

To make the project compelling and easy to share, I wrapped it in a small Flask app:

### What the UI provides

- parameter tuning:
  - iterations, grid size, bins, sigma, block mutation prob, rollout settings
- a live progress bar
- live reporting of:
  - iteration
  - archive size
  - coverage %
  - best fitness
- at the end:
  - render top elites to PNG
  - render random elites to PNG
  - view them directly in the browser

### Why this matters

For a research statement / portfolio, this is huge:

- it demonstrates theory (QD + ES)
- it demonstrates engineering (interactive demo)
- it produces visual artifacts
- it invites experimentation and ablation studies

---

## Results: Interpreting Coverage

If you see something like:

- `Archive size: 21 / 120 bins`
- `Coverage: 17.5%`

It means:

- the algorithm found 21 distinct behavioral niches (bins)
- each niche has its best elite stored
- many niches remain unfilled, either because:
  - they are hard to reach with current mutation/exploration
  - or those bins correspond to unrealistic combinations (e.g., extremely dense but very long paths)

Coverage depends strongly on:

- descriptor choice
- mutation operators
- number of iterations
- injection schedule (random individuals early helps)

---

## How to Run

Install requirements:

```bash
pip install numpy matplotlib flask
