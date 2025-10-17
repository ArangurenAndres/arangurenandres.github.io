---
layout: default
title: Monte Carlo Tree Search for Multi-Agent Pacman Capture the Flag
preview_image: /assets/mgai/assignment_2/mgai_2_pacman.png
date: 2025-09-08
---

# Monte Carlo Tree Search for Multi-Agent Pacman Capture the Flag

## Update

This project develops a vanilla MCTS agent and progressively augments it with heuristics to better handle the dynamics of the Capture the Flag environment in Pacman.

---

## Abstract

Monte Carlo Tree Search (MCTS) has demonstrated strong performance in deterministic and adversarial games. However, its vanilla form lacks context-awareness in complex multi-agent environments.  

We explore integrating **heuristic knowledge** into MCTS for the UC Berkeley Capture the Flag variant of Pacman, introducing:
- Progressive bias via heuristic-based action sorting  
- Softmax-weighted rollouts for more accurate simulations  
- A specialized **defensive MCTS agent** for team coordination  

Through **ablation studies** and a **tournament** (evaluated with win rates and TrueSkill scores), we show that heuristic-informed strategies significantly improve robustness and gameplay effectiveness.

---

## Introduction

Monte Carlo Tree Search (MCTS) is widely used in domains with large branching factors where defining a good evaluation function is infeasible. Unlike Minimax, which explores uniformly, MCTS **balances exploration and exploitation** using policies such as UCB1.  

We apply MCTS to the **UC Berkeley Capture the Flag variant of Pacman**, a challenging **multi-agent adversarial environment** where two teams of agents must steal food while defending their own territory.  

![Game Environment](/assets/mgai/assignment_2/mgai_2_pacman.png)  
*Figure 1. Pacman Capture the Flag environment.*

---

## Background

MCTS operates in four stages:
1. **Selection** – Traverses the tree using UCB1:  
   \[
   UCB1 = \bar{X_j} + 2C_p \sqrt{\frac{\ln n}{n_j}}
   \]  
   balancing exploitation and exploration.  
2. **Expansion** – Adds a new child node for an unexplored action.  
3. **Simulation** – Runs rollouts (random or heuristic-driven) to estimate outcomes.  
4. **Backpropagation** – Updates visit counts and rewards along the path.  

To adapt MCTS to Pacman CTF, we added:  
- **Progressive Bias** – guiding expansion with heuristics  
- **Softmax Rollouts** – probabilistic selection favoring promising states  

---

## Methods

### Baselines
- **Heuristic Reflex Agent**: Offensive agent collects food; defensive agent intercepts opponents.  
- **Vanilla MCTS**: Uniform random rollouts with default hyperparameters:  
  - Rollout depth: 10  
  - Simulation epsilon (ε): 0.2  
  - Exploration constant (Cp): 0.707  

### Domain Adaptations
1. Perfect Information assumption (access to full game state)  
2. Fixed time per move (0.5s budget)  
3. Limited rollout depth  
4. Removing STOP action from legal actions  
5. Custom state evaluation with features:  
   - Distance to food (+1.5)  
   - Carrying pellets (+20.0)  
   - Distance to home (+2.0)  
   - Ghost proximity (−8.0)  
6. ε-greedy simulation policy  
7. Early return-to-base rule when carrying food  

### Heuristic Enhancements
- **Untried Action Sorting**: Chooses best successor via heuristic evaluation  
- **Softmax Weighted Rollouts**:  
  \[
  P(a_i) = \frac{e^{s_i/\tau}}{\sum_j e^{s_j/\tau}}
  \]  
  biases simulations toward high-value states  
- **Rainbow Agent**: Combines weighted rollouts, RAVE, and action sorting  
- **Defensive Agent**:  
  - Pursues visible intruders  
  - Patrols territory when no intruders detected  

![Pipeline Diagram](/assets/projects/pacman/mcts_pipeline.png)  
*Figure 2. Enhanced MCTS pipeline with heuristic strategies.*

---

## Experiments

- **Setup**: UC Berkeley Pacman AI framework  
- **Evaluation**: 100 games per configuration, multiple seeds  
- **Metrics**: win rate, food collected, oscillation rate, decision time  
- **Tournament**: Round-robin (100 matches per agent pair) + **TrueSkill ranking**  

### Hyperparameter Ablation
- **Rollout Depth**: Too shallow misses opportunities, too deep causes loops; best = 10  
- **Simulation Epsilon (ε)**: Best trade-off at 0.2–0.3  
- **Cp (exploration constant)**: Best at 0.707  

### Tournament Results

![Tournament Results](/assets/mgai/assignment_2/mgai_2_confusion.png)  
*Figure 3. Confusion matrix of head-to-head matches.*

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

- **Rollout depth = 10** avoids noise and loops  
- **ε = 0.2–0.3** balances exploitation and exploration  
- **Cp = 0.707** consistent with literature best practice  
- **Heuristic-guided rollouts** improve decision quality significantly  
- **Action sorting** accelerates convergence to good policies  
- **Defensive agent** increases stability in multi-agent play  
- **Vanilla MCTS fails completely**, highlighting need for heuristics  

---

## Future Work

We propose extending MCTS with **Deep Reinforcement Learning**:  
- Integrating **DQN / Double DQN** for function approximation  
- Using **experience replay** and **target network updates** for stability  
- Learning from **raw game features** instead of hand-crafted heuristics  

Such integration could surpass heuristic-driven methods and enable **adaptive learning** in real-time adversarial environments.

---

## Project Details

- **Technologies**: Python, UC Berkeley Pacman AI Framework  
- **Methods**: MCTS (vanilla + heuristic enhancements), Reflex agents, TrueSkill ranking  
- **Evaluation**: Ablation studies, Round-robin tournaments, Win rates, TrueSkill scoring  
- **Contributions**: Heuristic-guided rollouts, Action sorting, Rainbow MCTS, Defensive agent  

---

## Appendix

### Images
- ![Game Environment](/assets/projects/pacman/mcts_captureflag.png)  
- ![Pipeline Diagram](/assets/projects/pacman/mcts_pipeline.png)  
- ![Tournament Results](/assets/projects/pacman/mcts_confusion_matrix.png)  

---
