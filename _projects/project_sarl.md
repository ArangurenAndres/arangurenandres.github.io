---
layout: default
title: "From Imitation to Interaction: Reinforcement-Learning–Driven Demonstration Selection for LLM Agents"
preview_image: /assets/sarl/sarl_preview.png
date: 2025-09-08
permalink: /projects/sarl_project/
---

# Introduction

Large Language Models (LLMs) are increasingly explored as general-purpose policies capable of acting in interactive environments. Their strengths—structured reasoning, broad world knowledge, and flexible instruction following—make them appealing as agent controllers. Yet, in sequential decision-making tasks, a persistent discrepancy emerges between what a model *knows* and what it *executes*. LLMs can articulate correct strategies but fail to enact them consistently. This discrepancy, known as the **knowledge–doing gap**, is a core challenge for deploying LLM-based agents.

[Download the complete project (PDF)](/assets/sarl/sadrl_final_report.pdf)

Two frameworks are central for understanding this gap: **LM-Act**, which studies demonstration-based imitation in controlled environments, and **BALROG**, a long-horizon benchmark designed to evaluate agentic competence across diverse symbolic, language-only, and pixel-based tasks. Together, they highlight the limits of inference-time imitation and motivate the need for adaptive demonstration strategies and reinforcement learning mechanisms.

---

## 1.1 LLMs as Interactive Policies

While LLMs excel at language understanding, stepwise reasoning, and high-level planning, acting within an environment introduces additional demands:

- maintaining consistent strategies over extended horizons,  
- integrating observations over time,  
- forming stable internal representations,  
- attributing outcomes to past actions (credit assignment),  
- and adapting behavior based on prior mistakes.

The absence of these mechanisms severely limits LLM reliability. Without memory, credit assignment, or explicit temporal abstraction, long-horizon performance degrades quickly, revealing foundational limitations of purely prompt-driven control.

---

## 1.2 LM-Act: In-Context Imitation Learning

LM-Act evaluates LLMs solely through **demonstrations provided within the prompt**, without parameter updates or gradient-based learning. A sequence of `(state, action)` pairs:

$$
(x_1,a_1), (x_2,a_2), \ldots, (x_k,a_k)
$$

is used to condition the model to produce an action for a new state \(x_{k+1}\).

Despite the simplicity of this approach, LM-Act reveals structural bottlenecks:

- LLMs imitate the *format* of demonstrations rather than extracting underlying strategies.  
- Adding more demonstrations does not reliably improve performance and often introduces **prompt interference**.  
- Long-horizon rollouts collapse due to compounding prediction drift.  
- Performance is highly sensitive to the specific examples chosen.

LM-Act provides a controlled environment to investigate these phenomena, focusing on imitation failures, demonstration scaling behavior, and prompt interference.

---

## 1.3 The Knowledge–Doing Gap

Across LM-Act and more complex environments, a consistent pattern emerges: LLMs often express the correct reasoning but fail to perform corresponding actions. They may articulate that a trap is dangerous or that a particular sequence is optimal, yet choose actions that contradict this reasoning. This disconnect between linguistic reasoning and policy execution underscores a fundamental limitation in current agentic LLM systems.

---

## 1.4 BALROG: A Benchmark for Long-Horizon LLM Agents

BALROG provides a unified benchmark for evaluating LLMs and VLMs on multi-step, procedurally generated tasks requiring long-horizon planning. It tests competencies including navigation, exploration, resource management, causal inference, and temporal reasoning. BALROG spans multiple domains:

- **BabyAI**: grid-based instruction following  
- **Crafter**: survival, crafting, hazard management  
- **TextWorld**: symbolic reasoning in textual environments  
- **MiniHack**: rogue-like environments with traps and enemies  
- **NetHack**: extremely long-horizon resource management  
- **Baba Is AI**: rule-based reasoning and world manipulation

The procedural nature of these environments prevents memorization and reveals genuine generalization capability. BALROG highlights deep long-horizon weaknesses in LLM agents, even when equipped with chain-of-thought (CoT), short-term memory, or few-shot demonstrations.

---

## 1.5 From LM-Act to BALROG

LM-Act isolates issues of demonstration quality and format imitation but lacks the complexity required to evaluate real-world agentic failures. BALROG amplifies these challenges by introducing long-horizon dependencies, partial observability, stochastic transitions, and multi-step reward structures.

This project therefore progresses from LM-Act replication to BALROG experimentation, highlighting the need for **adaptive demonstration selection**, contextual memory, and reinforcement learning techniques to support consistent action generation.

---

# Demonstration Selection as an MDP

Demonstration selection plays a central role in shaping LLM behavior. Poorly chosen demonstrations can mislead the model, overload attention, introduce redundancy, or propagate suboptimal strategies. To address these issues, the selection process is modeled as a **sequential decision-making problem**, formalized as a finite-horizon Markov Decision Process (MDP). This formulation draws on principles from Reinforcement Learning for Demonstration Selection :contentReference[oaicite:0]{index=0} and the LLM-Act+ thesis extension :contentReference[oaicite:1]{index=1}.

---

## 2.1 MDP Formulation

The demonstration-selection problem is formalized as:

$$
M = (S, A, P, R, \gamma)
$$

where:

- **States \(S_t\)** describe the current selection context.  
- **Actions \(A_t\)** correspond to selecting a candidate demonstration.  
- **Transitions \(P\)** update the current set with the selected example.  
- **Rewards \(R_t\)** reflect the impact of this selection on expected performance.  
- **Discount factor \(\gamma\)** controls horizon weighting (usually \(\gamma=1\), finite horizon).

---

## 2.2 State Representation

The state encodes information necessary to evaluate future returns under the demonstration set \(E_t\). Combining insights from textual embedding approaches, DPP-style diversity features, and the LM-Act thesis, the state includes:

### 1. **Environment/Task Embedding**
A vector representation of the target state or episode context \(x_t\), extracted via an LLM embedding model or TF-IDF if symbolic.

### 2. **Demonstration Memory Embedding**
A joint representation of the current selected set \(E_t\), capturing:

- semantic coverage,  
- distribution of action types,  
- representation of important patterns or edge cases.

### 3. **Performance Estimate from Rollouts**
A short forward simulation using the LLM policy conditioned on \(E_t\):

$$
\hat{J}(E_t) = \mathbb{E}[R \mid E_t]
$$

where \(R\) is episode return estimated via truncated rollouts.

### 4. **Diversity Vector**
A DPP-inspired vector representing spread in demonstration space, computed via:

$$
D_t = \text{diag}(K(E_t))
$$

where \(K\) is a similarity kernel (e.g., cosine similarity).

The final state vector is:

$$
s_t = \phi(x_t) \oplus \phi(E_t) \oplus \phi(\hat{J}(E_t)) \oplus \phi(D_t)
$$

---

## 2.3 Action Space

Each action selects one demonstration index from the remaining pool:

$$
A_t = \{1,2,\ldots,N\} \setminus E_t
$$

The agent chooses demonstrations sequentially for \(T\) steps, typically selecting \(K\ll N\) final examples.

---

## 2.4 Transition Function

Selecting demonstration \(a\) leads to:

$$
E_{t+1} = E_t \cup \{\text{demo}_a\}
$$

Transitional dynamics are Markovian, as future states depend only on the updated set.

---

## 2.5 Reward Function

The reward measures incremental improvement in expected return after adding a demonstration:

$$
R_t = \hat{J}(E_t) - \hat{J}(E_{t-1})
$$

This structure incorporates:

- **relevance**: demonstrations improving performance yield positive reward  
- **diversity**: if redundant demos are selected, the performance remains flat  
- **credit assignment**: sequential selection captures dependencies among demos  

A terminal reward may also be applied:

$$
R_{\text{final}} = \hat{J}(E_K)
$$

This reward formulates demo selection as a problem of **maximizing action utility**, not simply matching nearest neighbors.

---

## 2.6 Policy Optimization

Two optimization methods are employed:

### PPO (Proximal Policy Optimization)

A standard actor–critic method optimizing:

$$
L_{\text{clip}} =
\mathbb{E}\left[
\min\left(
r_t A_t,\;
\text{clip}(r_t,1-\epsilon,1+\epsilon)A_t
\right)
\right]
$$

PPO stabilizes updates and is effective for discrete action selection.

### SAC (Soft Actor–Critic)

SAC incorporates entropy regularization to promote exploration of diverse demonstration choices:

$$
J_{\text{SAC}} = \mathbb{E}[Q(s,a) - \alpha \log \pi(a|s)]
$$

This helps avoid local optima and redundant selections.

---

## 2.7 Demonstration Selection Pipeline

The full selection process:

1. **Embed** all candidate demonstrations.  
2. **Cluster** or structure them to expose diversity.  
3. **Initialize** with an empty memory \(E_0\).  
4. **Iteratively select** demonstrations using PPO or SAC.  
5. **Evaluate** expected performance after each addition via rollouts.  
6. **Construct** the final optimized prompt with selected demonstrations.

This MDP-based approach selects information-rich, diverse, and high-impact examples that reduce prompt interference and improve long-horizon consistency.

---

# Results

The results are organized into three phases:

1. LM-Act replication and diagnosis  
2. Extended prompting and in-context learning techniques  
3. BALROG evaluation under long-horizon settings  

---

## 6.1 LM-Act Baseline Behavior

Few-shot demonstration prompting enables early-step success but fails under long horizons. Drift accumulates as the LLM loses track of previous structure and constraints.

![Baseline Performance](/assets/sarl/results_baseline_placeholder.png)

---

## 6.2 Demonstration Scaling

Increasing demonstrations from 3 to 6 to 9 does not yield proportional improvements due to redundancy and interference.

![Scaling Results](/assets/sarl/demo_scaling_placeholder.png)

---

## 6.3 Reproduction of LM-Act Experiments

### Tic-Tac-Toe  
<div style="display:flex; gap:20px;">
<div style="flex:1;">
<img src="/assets/sarl/sarl_paper_1_1.png" style="width:100%;">
</div>
<div style="flex:1;">
<img src="/assets/sarl/sarl_results_1_1.png" style="width:100%;">
</div>
</div>

### GridWorld  
<div style="display:flex; gap:20px;">
<div style="flex:1;">
<img src="/assets/sarl/sarl_paper_1_2.png" style="width:100%;">
</div>
<div style="flex:1;">
<img src="/assets/sarl/sarl_results_1_2.png" style="width:100%;">
</div>
</div>

The reproduced results exhibit the same instability observed in the LM-Act paper.

---

# 6.4 Extended Prompting and ICL Strategies

Short-term memory retention, reasoning recall, curriculum ordering, and in-context RL techniques provide partial improvement but do not fundamentally resolve long-horizon instability.

---

## Short-Term Memory and Chain-of-Thought Reasoning
Memory buffers improve immediate consistency but degrade across long rollouts.

---

## In-Context Learning Across Episodes

ICL introduces weak meta-learning effects but remains highly dependent on the quality and order of demonstrations.

---

## Curriculum-Based Prompting

Curricular organization stabilizes reasoning patterns by presenting the model with progressive examples.

![Curriculum](/assets/sarl/sarl_curriculum.png)  
![Context Replay](/assets/sarl/sarl_context.png)

---

## In-Context Reinforcement Learning (ICRL)

Reward-conditioned prompting shows small adaptive effects but remains limited.

![ICRL prompting](/assets/sarl/sarl_icrl_prompts.png)

![ICRL Results for E1](/assets/sarl/sarl_e1_results.png)

---

# 7. BALROG Experiments

Evaluations in BALROG expose significant weaknesses in long-horizon environments. Models equipped with memory, CoT, or ICL remain prone to forgetting objectives, misinterpreting state descriptions, and producing inconsistent behaviors.

(Insert BALROG experiment figures here.)

Despite partial improvements from memory-augmented agents, performance remains far from solving BALROG tasks reliably. These findings emphasize the need for principled demonstration selection and adaptive methods like the MDP-driven approach described earlier.

---

# Conclusion

Across LM-Act replication, extended prompting strategies, and BALROG evaluation, the results highlight the fundamental limitations of in-context imitation for long-horizon control. While memory buffers, curriculum ordering, and reward conditioning offer incremental improvements, they fail to resolve structural issues such as prompt interference, compounding drift, and lack of credit assignment.

Modeling demonstration selection as an MDP provides a principled mechanism for constructing compact, diverse, and high-impact prompts. This approach, embodied in **LLM-Act+**, enables adaptive control over prompt composition, enhances long-horizon consistency, and narrows the knowledge–doing gap—without requiring any model fine-tuning and remaining compatible with existing LLMs.

