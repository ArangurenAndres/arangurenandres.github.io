---
layout: default
title: Generating Playable Mario Levels from Agent Experience
preview_image: /assets/mgai/assignment_3/mgai_3_diagram.png
date: 2025-09-08
---

# Generating Playable Mario Levels from Agent Experience

This project explores the intersection of **procedural content generation (PCG)** and **reinforcement learning (RL)** for creating playable Super Mario Bros levels. By combining generative models with trained RL agents, we enable the automatic design of levels with controllable difficulty.

---

## Abstract

We propose a **dual pipeline framework** where generative models (DCGAN and diffusion models) are trained to generate Mario levels **conditioned on empirically derived difficulty scores**. These scores are computed from RL agents (Dueling DQN and PPO) trained to play Mario levels.  

- Levels are processed into symbolic representations.  
- Generators output new level segments conditioned on difficulty.  
- RL agents evaluate playability, enabling iterative feedback.  

**Key findings**:  
- **DCGANs** produced the most coherent and playable levels.  
- **Diffusion models** underperformed, struggling with structural coherence.  
- **PPO agents** achieved stable performance and successfully navigated generated levels.  

This approach demonstrates a scalable method for *difficulty-aware procedural content generation*.

---

## Introduction

- **Generative AI (GenAI)** creates new content by learning patterns in data (e.g., GANs, diffusion models).  
- **Reinforcement Learning (RL)** trains agents to learn decision-making by interacting with environments.  

Both have been applied to **video games**, which offer controlled environments for AI research. Prior work (Volz et al. 2018) used DCGANs + evolutionary algorithms to evolve Mario levels.  

**Our contribution**: Replace scripted evolutionary search with *learning agents* (DQN, PPO) to evaluate difficulty, and integrate **conditional GANs & diffusion models** for controllable level generation.

---

## Methodology

The pipeline consists of two main stages:

1. **Agent Training & Difficulty Scoring**  
   - RL agents play training levels until proficient.  
   - A **quality score** is computed from completion, retries, movements, and rewards.  
   - Levels are labeled as *Easy, Medium, Hard*.

2. **Generative Models for Level Design**  
   - Models trained on symbolic Mario levels.  
   - Conditioned on difficulty labels.  
   - Output new playable levels.

### Overall Pipeline

![Mario Pipeline](/assets/mgai/assignment_3/mgai_3_diagram.png)  
*Agent performance produces difficulty scores, which condition generative models for new levels.*

---

### Levels Processing

- Levels are stored as **symbolic text** (characters → tiles).  
- Converted into **identity matrices** → one-hot/dense embeddings.  
- Models trained on abstract representations, decoded back into symbolic → PNGs.  

![Levels Processing](/assets/mgai/assignment_3/mgai_3_level_processing.png)

---

### Models

#### 1. **Baseline MLP**  
- Simple fully-connected generator.  
- Fails to preserve spatial structure.  
- Output: unplayable, mostly floor tiles.  

![MLP Generated Level](/assets/mgai/assignment_3/mlp_generated.png)

#### 2. **DCGAN**  
- Uses **transposed convolutions** for spatial preservation.  
- Trained with adversarial setup.  
- Required hyperparameter search (learning rates, batch size, latent dimension).  
- Generated **coherent Mario-like levels**.  

![DCGAN Generated Levels](/assets/mgai/assignment_3/dcgan_levels.png)

#### 3. **Diffusion Model**  
- U-Net-like denoiser (SimpleDenoiseNet) + Gaussian diffusion process.  
- Learned reconstruction loss well, but failed to preserve structure.  
- Likely due to lack of attention and discrete-symbol mapping issues.  

![Diffusion Architecture](/assets/mgai/assignment_3/diffusion_architecture.png)

---

### Level Conditioning

Difficulty scores were discretized into {Easy, Medium, Hard}. During generation:  
- Noise vector concatenated with difficulty embedding.  
- Enables **controllable complexity** in generated levels.  

![Difficulty Conditioning](/assets/mgai/assignment_3/mgai_3_level_conditioning.png)

---

### RL Agents

#### Dueling DQN  
- Splits Q-value into **Value stream** and **Advantage stream**.  
- Improves stability and sample efficiency.  

#### PPO (Proximal Policy Optimization)  
- Actor-Critic with clipped surrogate objective.  
- Stable and efficient for sparse reward settings.  
- Outperformed DQN in final evaluations.  

---

## Experiments

1. **Difficulty Classification**  
   - Levels scored & labeled.  
   - Example: Easy (>0), Medium (-25–0), Hard (<-25).

2. **DCGAN Ablation Study**  
   - Tested learning rates, batch sizes, noise std, smoothing.  
   - Selected best configuration for stable generation.

3. **Diffusion Training**  
   - Progressive architecture refinements (V1–V4).  
   - Strong convergence, weak level coherence.

4. **Agent Evaluation on Generated Levels**  
   - PPO tested on generated levels.  
   - Mixed results: some levels aligned with difficulty labels, others mismatched.

---

## Results

### MLP  
- Failed to generate realistic levels.  

### DCGAN  
- Produced **playable, coherent levels**.  
- PPO successfully completed most generated levels.  
- Some difficulty mismatches occurred.  

![DCGAN Loss Curves](/assets/mgai/assignment_3/mgai_3_dcgan_loss.png)

### Diffusion Model  
- Training loss converged.  
- Generated outputs lacked structure.  
- Requires architectural improvements (e.g., attention).  

![Diffusion Loss](/assets/mgai/assignment_3/mgai_3_diffusion_loss.png)

### Agent Performance on Generated Levels  

| Generated Level | Intended Difficulty | Agent Score / Observed Difficulty |
|-----------------|---------------------|----------------------------------|
| level 1.txt     | Easy                | 1080.64 (Easy ✅) |
| level 2.txt     | Medium              | -46.19 (Hard ❌) |
| level 3.txt     | Hard                | -79.44 (Hard ✅) |
| level 4.txt     | Easy                | -4.12 (Medium ❌) |
| level 5.txt     | Medium              | 5.07 (Easy ❌) |

---

## Conclusion

- **DCGANs** were most effective, generating **playable levels**.  
- **PPO agents** validated playability and completed generated levels.  
- **Diffusion models** struggled without attention mechanisms.  
- Conditional generation is feasible: agent-based difficulty labels guided content creation.  

---

## Future Work

- **Improved Diffusion Models**: integrate self-attention for spatial dependencies, use one-hot outputs for symbolic consistency.  
- **Smarter Agents**: semantic embeddings of tiles, adaptive PPO variants.  
- **Generalization**: expand dataset, generate larger full levels instead of patches.  

---

## Appendix (Figures)

- ![Mario Pipeline](/assets/mgai/assignment_3/mario_pipeline.png)  
- ![Levels Processing](/assets/mgai/assignment_3/levels_processing.png)  
- ![MLP Generated Level](/assets/mgai/assignment_3/mlp_generated.png)  
- ![DCGAN Levels](/assets/mgai/assignment_3/dcgan_levels.png)  
- ![DCGAN Loss Curves](/assets/mgai/assignment_3/dcgan_loss.png)  
- ![Diffusion Architecture](/assets/mgai/assignment_3/diffusion_architecture.png)  
- ![Diffusion Loss](/assets/mgai/assignment_3/diffusion_loss.png)  
- ![Conditioning Scheme](/assets/mgai/assignment_3/conditioning_scheme.png)  

---
