---
layout: default
title: Monte Carlo Tree Search for Multi-Agent Pacman Capture the Flag
preview_image: /assets/mgai/assignment_2/mgai_2_pacman.png
date: 2025-09-08
---

# Monte Carlo Tree Search for Multi-Agent Pacman Capture the Flag

This project develops a vanilla MCTS agent and progressively augments it with heuristics to better handle the dynamics of the Capture the Flag environment in Pacman.

---

## Abstract

Monte Carlo Tree Search (MCTS) has demonstrated strong performance in deterministic and adversarial games. However, its vanilla form lacks context-awareness in complex multi-agent environments.  
This project explores integrating **heuristic knowledge** into MCTS for the UC Berkeley Capture the Flag variant of Pacman.  

Enhancements include:
- Progressive bias via heuristic-based action sorting  
- Softmax-weighted rollouts for more informed simulations  
- A specialized **defensive MCTS agent** for team coordination  

Through **ablation studies** and a **tournament** (evaluated with win rates and TrueSkill scores), we show that heuristic-informed strategies significantly improve robustness and gameplay effectiveness.

---

## Introduction

The UC Berkeley Capture the Flag variant of Pacman is a challenging **multi-agent adversarial environment**.  
Two teams of agents must **steal food** from the opponent’s side while **defending their own territory**.  

MCTS provides a good foundation but requires **domain-specific adjustments**:  
- Handling imperfect information  
- Managing high branching factors  
- Enabling coordination between offensive and defensive agents  

![Game Environment](/assets/mgai/assignment_2/mgai_2_pacman.png)  
*Figure 1. Pacman Capture the Flag environment.*

---

## Methods

We implemented a **vanilla MCTS agent** and progressively enhanced it with heuristic strategies:

- **Baseline Heuristic Agent**: Reflex-based, with offensive and defensive roles  
- **Vanilla MCTS**: Standard 4-phase loop (selection, expansion, simulation, backpropagation)  
- **Heuristic State Evaluation**: Features include distance to food, carrying pellets, distance to home, ghost proximity  
- **Untried Action Sorting**: Expansion biased toward promising actions  
- **Softmax Rollouts**: Stochastic action selection weighted by heuristic scores  
- **Rainbow Agent**: Combination of RAVE, weighted rollouts, and action sorting  
- **Defensive Agent**: Pursues enemies in our territory or patrols strategically  

![Pipeline Diagram](/assets/projects/pacman/mcts_pipeline.png)  
*Figure 2. Enhanced MCTS pipeline with heuristic strategies.*

---

## Experiments

We conducted:
- **Ablation studies** on rollout depth, exploration constant (Cp), and simulation epsilon  
- **Round robin tournament**: 100 matches per agent pair  
- **Metrics**: win rate, food collected, oscillation rate, TrueSkill rating  

![Tournament Results](/assets/projects/pacman/mcts_confusion_matrix.png)  
*Figure 3. Confusion matrix of agent performance across tournaments.*

### Key Results
- Deeper rollout (>15) added noise; best at **depth = 10**  
- Simulation epsilon around **0.2–0.3** balanced exploration and exploitation  
- **Cp = 0.707** (literature default) gave best trade-off  
- **Weighted rollouts & action sorting** strongly outperformed vanilla MCTS and pure heuristics  
- **Top-performing agents**: Sorting-based MCTS, Weighted Rollout MCTS  

| Agent Variant              | TrueSkill Score | Win %  |
|----------------------------|-----------------|--------|
| MCTS Sorting Rollout       | **35.60**       | 65.38% |
| MCTS Weighted Rollout      | 35.29           | 67.03% |
| MCTS w/ Heuristic Eval     | 35.06           | 58.47% |
| Rainbow MCTS               | 33.55           | 58.43% |
| RAVE MCTS                  | 31.17           | 40.22% |
| Baseline Heuristic Agent   | 25.97           | 25.97% |
| Vanilla MCTS               | **7.11**        | 0.00%  |

---

## Analysis

- **Heuristic-guided rollouts** greatly improved decision-making quality  
- **Action sorting** accelerated convergence to strong policies  
- **Defensive agent** provided stability in multi-agent coordination  
- Baseline vanilla MCTS underperformed heavily, confirming the need for informed enhancements  

---

## Future Work

We propose integrating **reinforcement learning** (e.g., DQN, Double DQN) into the Pacman Capture the Flag environment to surpass hand-crafted heuristics. Potential enhancements:  
- Function approximation with deep networks  
- Experience replay and target network updates  
- More robust policy learning through adaptive improvements  

---

## Appendix

### Images
- ![Game Environment](/assets/projects/pacman/mcts_captureflag.png)  
- ![Pipeline Diagram](/assets/projects/pacman/mcts_pipeline.png)  
- ![Tournament Results](/assets/projects/pacman/mcts_confusion_matrix.png)  

---

