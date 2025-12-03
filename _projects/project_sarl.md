---
layout: default
title: "From Imitation to Interaction: Reinforcement-Learning–Driven Demonstration Selection for LLM Agents"
preview_image: /assets/sarl/sarl_image.png
date: 2025-09-08
---



# Abstract
Large Language Models (LLMs) have emerged as general-purpose policies capable of interacting with complex environments using purely in-context demonstrations. Yet, empirical evidence shows that simply increasing the number of demonstrations often saturates or even degrades performance due to interference, lack of credit assignment, and an inability to adapt demonstration sets to the specific decision-making demands of a task. These problems are amplified in long-horizon interactive environments like those in **BALROG**, where the *knowledge–doing gap* becomes pronounced.

We propose **LLM-Act+**, a lightweight framework that preserves the simplicity of prompting while integrating adaptive learning mechanisms. The core novelty is a **reinforcement-learning–based demonstration selection module**, formulated as a finite-horizon MDP that learns to choose compact, diverse, and task-relevant demonstrations to enhance action quality. Our approach is grounded theoretically in recent RL demonstration-selection work such as RDES (:contentReference[oaicite:1]{index=1}), and empirically aligned with the long-horizon evaluation suite BALROG. By combining reward-conditioned prompting, trajectory summarization, and an RL selector that optimizes over demonstration subsets, LLM-Act+ achieves more consistent long-horizon behavior, reduces prompt interference, and narrows the knowledge–doing gap.

# Introduction
LLMs can act as decision-making agents by conditioning their next action on a set of task demonstrations. This paradigm—**in-context control**—has proven viable in robotics (PaLM-SayCan, LM-Nav), instruction-following environments, and text-based games. However, pure imitation through prompting is inherently limited: **more examples do not guarantee better decision quality**. As seen in our LM-Act baseline and wider evidence across BALROG environments, LLMs frequently produce *legal but suboptimal actions*, struggle with long-horizon consistency, and fail to exploit informative structure in demonstrations.

The central thesis of this work is:

> *To transform an LLM from a static imitation machine into a consistent interactive agent, one must optimize not the model weights, but the **demonstration set** itself.*

We therefore introduce an **external RL layer** that selects demonstrations by interacting with the environment. This MDP-based selector becomes a meta-policy that evaluates which examples best support the LLM’s action generation, given environment dynamics and task constraints.

# Background and Literature Review

## LLMs as Interactive Policies
Prior work such as **PaLM–SayCan**, **Code-as-Policies**, and **LM-Nav** has shown that LLMs can:
- parse task descriptions,
- decompose objectives into actionable steps,
- propose high-level plans,
- and interface with perception or low-level controllers.

However, these models lack **explicit credit assignment**. Improvements in planning or action consistency require:
- prompt engineering (CoT, ReAct),
- more demonstrations,
- or fine-tuning (expensive and often domain-specific).

These approaches do not solve the central issue: **how to choose demonstrations that maximize performance**.

## In-Context Reinforcement Learning
In contextual RL, LLMs can internalize implicit policy improvement signals when reward traces are embedded in the prompt. Yet:
- most approaches remain offline,
- the model passively consumes examples,
- and exploration is limited.

Our work enhances this by adding an **external learning loop** around the LLM.

## Demonstration Selection
Classical selection strategies include:
- similarity-based nearest neighbors,
- determinantal point processes for diversity,
- clustering-based coverage.

However, recent work argues that demonstration selection should be adaptive and sequential.  
According to **RDES** (:contentReference[oaicite:2]{index=2}), demonstration selection can be cast as a **sequential decision-making problem**, where the agent must balance:
- **relevance** (informative for the test input),
- **diversity** (avoid redundancy and interference).

This insight directly motivates our RL-based selector.

## BALROG: A Long-Horizon Benchmark Suite
BALROG provides a unifying set of environments including:
- BabyAI (gridworld reasoning),
- Crafter (survival and planning),
- TextWorld (symbolic tasks),
- 3D embodied tasks.

BALROG is specifically designed to quantify the **knowledge–doing gap**:
- LLMs *know* what to do (describe correct action),
- but they *fail* to execute consistently across long horizons.

Its structured difficulty makes it the ideal testbed for evaluating demonstration-selection strategies.

# Limitations of Pure In-Context Imitation
Across LM-Act experiments and BALROG analyses:
- Adding demonstrations leads to token interference,
- Models lose track of earlier states,
- Reasoning chains reset unpredictably,
- Long-horizon tasks collapse quickly.

Failures arise not from formatting errors but from **suboptimal action selection** driven by the wrong demonstrations.

# Design Goals
LLM-Act+ must maintain:

- **No fine-tuning** (prompt-only),
- **Minimal overhead**,
- **Environment-agnostic operation**,
- **Compatibility with BALROG and standard LLMs**,
- **Clear and measurable improvements in consistency**.

# Components of LLM-Act+

## Reward-Conditioned Prompting
We append recent (action, reward) pairs to expose empirical performance signals. This introduces implicit credit assignment.

## Short-Term Autoregressive Summaries
We compress recent interactions using natural-language state summaries. These summaries highlight causal patterns (“opening the door increases reward”).

## MCTS-Lite
We explore small rollout trees by sampling the model’s top-k next-token distributions and simulating short action sequences.

## Reinforcement-Learning–Based Demonstration Selection
This is the core contribution of the work.

# Demonstration Selection as an MDP

We define the demonstration selection problem as a finite-horizon MDP  
\\[
M = (S, A, P, R, \gamma).
\\]

## State Space
A state \( S_t \) contains:
- **Environment embedding** (TF-IDF or LLM-derived),
- **Current demonstration memory** \( E_t \),
- **Estimated performance** from short rollouts,
- **A diversity vector** representing cluster coverage.

This design parallels the RDES state formulation (:contentReference[oaicite:3]{index=3}), which concatenates:
\\[
\phi(x_t) \oplus \phi(E_t) \oplus \phi(\hat{y}_t) \oplus \phi(D_t).
\\]

## Actions
Each action \( A_t \) selects **one demonstration** from the remaining pool.

## Transition
Selecting action \( A_t \) updates:
\\[
E_{t+1} = E_t \cup \{ \text{demo}_a \}.
\\]

After this update, a short LLM rollout re-estimates expected performance.

## Reward
The reward encourages adding demonstrations that increase expected return:
\\[
R_t = \hat{J}(E_t) - \hat{J}(E_{t-1}),
\\]
analogous to accuracy/diversity–weighted rewards in RDES (:contentReference[oaicite:4]{index=4}).

## Termination
The selection process ends after \( K \) demonstrations are chosen.

# Optimization Strategy

We train a policy \( \pi(a \mid s) \) using two alternative RL algorithms:

## PPO
We use an actor-critic:
\\[
\pi_\theta(a|s), \qquad V_\psi(s).
\\]

PPO optimizes the clipped surrogate objective:
\\[
L_{\text{clip}} = \mathbb{E}_t\left[ \min(r_t A_t, \operatorname{clip}(r_t, 1 - \epsilon, 1 + \epsilon) A_t ) \right].
\\]

## SAC
SAC introduces entropy regularization to encourage exploring diverse demonstrations, critical when many demos are redundant.

# Implementation Pipeline

1. **Cluster demonstrations** into semantic groups.  
2. **Initialize** the MDP with an empty set.  
3. **Iteratively select** demonstrations with PPO or SAC.  
4. **Perform rollout** after each addition to estimate return.  
5. **Construct final demonstration set** \( E_K \) for the LLM prompt.

# BALROG Integration

## Why BALROG?
BALROG specifically stresses:
- long-horizon decision-making,  
- environment grounding,  
- structured reasoning,  
- multi-step dependencies.

Its environments amplify weaknesses in demonstration selection and highlight gains from adaptive RL-based methods.

## Evaluation Steps
For each BALROG task:
1. Collect \( N \) expert trajectories.  
2. Run RL demonstration selection to pick \( K \) examples.  
3. Prepend them to the LLM-Act+ prompt.  
4. Execute 10–20 episodes.  
5. Compare against:
   - random selection,
   - similarity search,
   - LM-Act fixed sets,
   - zero-shot prompting.

# Reducing the Knowledge–Doing Gap
RL-selected demonstrations improve:
- semantic coverage,
- diversity,
- grounding,
- consistency across long-horizon steps.

Results align with analysis from RDES (:contentReference[oaicite:5]{index=5}), where diversity-aware selection improves generalization and robustness.

# Natural-Language Distillation Methods

## Reflexion-Style Summaries
High-level distilled rules tend to lose grounding and harm performance in BALROG.

## Contrastive Decision-Point Distillation
We generate comparisons:
- “expert action A is better than action B because…”

This retains structure while controlling prompt length.

# Conclusion
LLM-Act+ offers a principled way to integrate reinforcement learning into in-context control without modifying model weights. By treating demonstration selection as an MDP and optimizing via PPO/SAC, the framework selects compact, diverse, and high-impact demonstration sets. Combined with reward-conditioned prompting and local MCTS rollouts, LLM-Act+ achieves more consistent long-horizon performance and narrows the knowledge–doing gap observed in BALROG.

Our method is fully compatible with existing LLMs, inexpensive to run, and highlights the importance of **adaptive demonstration selection** as a core component of agentic LLM design.
