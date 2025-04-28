---
layout: page
title: "Enhancing Deep Q-Networks with Experience Replay and Target Networks"
date: 2025-04-10
permalink: /projects/cartpole-dqn/
preview_image: /assets/projects/rl/dqn_cartpole/cartpole.png
---

# Enhancing Deep Q-Networks with Experience Replay and Target Networks

This project explores how to improve Deep Q-Networks (DQN) in reinforcement learning by combining **experience replay** and **infrequent target weight updates**. The study uses the CartPole environment to run ablation studies on hyperparameters, evaluate different configurations, and identify the most stable and efficient setup for DQN training.

---

## Background

Deep reinforcement learning (DRL) faces two major challenges compared to supervised learning:

- **Correlated training samples**: Sequential data generation violates the i.i.d. assumption, leading to biased updates.
- **Moving target problem**: Q-learning updates are based on ever-changing targets, making convergence difficult.

To address these issues, DQN introduces:

- **Experience Replay**: Randomly samples past transitions to break correlations.
- **Infrequent Target Network Updates**: Uses a separate, slowly updated target network to stabilize learning.

The DQN approximates the action-value function \( Q(s,a) \) using deep neural networks instead of tabular methods, allowing it to scale to high-dimensional state spaces.

---

## Experiment Setup

We evaluated various agent configurations using the **CartPole** environment from OpenAI Gymnasium:

- Training steps: **1 million**
- Evaluations: **average reward over the last 100 episodes**
- Each configuration was run **five times** to account for stochasticity.

The neural network architecture consisted of two fully connected hidden layers with ReLU activations.

---

## Ablation Studies

**Hyperparameter Sensitivity Experiments** were conducted to assess how network settings affect training stability:

<div style="text-align:center;">
    <img src="/assets/drl/assignment_1/ablation.png" alt="Training results for ablation study
dataset" style="width:100%;">
</div>
_Training performance and ablation study results on the CartPole environment. Each plot shows the average reward vs.
training steps for different hyperparameter configurations. (Top row, left to right): (1) Baseline training using the default parameter
configuration see Table 1. (2) Training with adaptive epsilon decay, where exploration decreases over time dynamically. (3) Training with
a constant epsilon value, maintaining a fixed level of exploration throughout training. (Bottom row, left to right): (4) Ablation study on
the update-to-data ratio, evaluating the effect of different update frequencies on learning stability. (5) Effect of varying the number of
hidden units per layer in the neural network architecture. (6) Performance impact of different learning rate values, showing the sensitivity
of DQN training to step size adjustments. All configurations were evaluated over 1 million environment steps._

- **Baseline DQN**: Standard setup with fixed hyperparameters.
- **Adaptive Epsilon Decay**: Gradually reduced exploration led to smoother learning curves.
- **Constant Epsilon**: Fixed exploration led to unstable training.
- **Update-to-Data Ratio**: More frequent updates (e.g., 1:1, 1:2, 1:4) improved convergence.
- **Network Size**: Medium-sized networks (16–32 units) provided better stability for CartPole.
- **Learning Rate**: Too high or too low learning rates caused instability or slow learning.

---

## Improvements via Experience Replay and Target Networks

After tuning the baseline setup, we incorporated experience replay and target networks:

<div style="text-align:center;">
    <img src="/assets/drl/assignment_1/ablation_replay_n.png" alt="Training average reward using experience replay
dataset" style="width:100%;">
</div>
_Training average reward over envirionmnent steps, using experience replay buffer with different sample size N values._

- A replay sample size around 64 was optimal. Larger samples (e.g., 256) introduced noise.


<div style="text-align:center;">
    <img src="/assets/drl/assignment_1/ablation_target_network_update.png" alt="Training average reward using target network
dataset" style="width:100%;">
</div>
_Training average reward over environment steps, using target network with different update frequency values._

- Update frequency (e.g., every 200 steps) had limited impact but still helped maintain stability.

Finally, we evaluated the **combination** of experience replay and target networks:


<div style="text-align:center;">
    <img src="/assets/drl/assignment_1/final_image.png" alt="Training average reward for all methods
dataset" style="width:100%;">
</div>
_Training average reward over environment steps, using DQN with naive function apporximation, experience replay buffer, infrequent weight update using target network and DDQN model._

- Combining both techniques led to **the most stable and fastest learning**, outperforming DQN alone or with either method individually.

---

## Key Takeaways

- **Adaptive exploration** is crucial for balancing exploration and exploitation.
- **Update-to-data ratio** should favor frequent updates.
- **Network complexity** should match the problem's difficulty to avoid overfitting.
- **Experience replay and target networks combined** provide the largest benefit, dramatically improving learning speed and stability.

---

## Conclusion

This study highlights the importance of alleviating state correlation and stabilizing target values in deep reinforcement learning. Future improvements could involve exploring dynamic replay strategies or adaptive target network updates.

---

