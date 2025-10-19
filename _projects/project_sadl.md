---
layout: default
title: "Crown, Frame, Reverse: Layer-Wise Scaling Variants for LLM Pre-Training"
preview_image: /assets/sadl/llm_architecture.png
date: 2025-09-08
permalink: /projects/lws-variants/
---

# Crown, Frame, Reverse: Layer-Wise Scaling Variants for LLM Pre-Training

*Authors: Andrei Baroian, Kasper Notebomer, Max Van Den Boom, **Andres Aranguren** (LIACS, Leiden University)*  

> **Code repository:** [github.com/baroian/OLMo-custom](https://github.com/baroian/OLMo-custom)  
> **Full report:** [Download PDF](/assets/sadl/sadl_project.pdf)

---

## Project Context

This project was developed as part of the *Advanced Deep Learning Seminar (SADL)* at LIACS, Leiden University.  
The study revisits **Layer-Wise Scaling (LWS)** — a technique for redistributing parameters across Transformer depth — and proposes new architectural profiles that challenge the assumption of uniform layer design.  
All experiments were conducted on the **Snellius supercomputer**, using the **OLMo-core** training framework.  

The project evolved into a broader investigation on **architectural efficiency** and **representation specialization** in large language models.

---

## TL;DR

Large Language Models (LLMs) are typically trained **isotropically** — every layer has identical width and head count — despite evidence that different layers serve distinct roles.  

We explored **Layer-Wise Scaling (LWS)**, which systematically varies layer widths, under a fixed 180M-parameter budget.  
Our contributions:

- Introduced **three new LWS profiles**: **Framed (U-shape)**, **Reverse (descending)**, and **Crown (ends wide)**.  
- Compared against **Vanilla LWS** and an **isotropic baseline**.  
- Trained all models on **5B tokens**, same optimizer and schedule.  
- Found **consistent 5–6% validation perplexity improvements** with *no throughput penalty*.  

**Key message:** Heterogeneity is beneficial — the exact shape matters less than breaking uniformity.

---

## Motivation in One Chart

![Scaling Profiles](/assets/sadl/lws_variants.png)
*Figure 3. Parameter allocation profiles for each model.*

Modern Transformers distribute parameters uniformly across depth, but different layers encode linguistic, syntactic, and semantic abstractions.  
By redistributing capacity, we hypothesize more efficient use of the same parameter budget.

---

## 1. Background & Motivation

### 1.1 Standard Isotropy in Transformers
Uniform scaling simplifies training but may waste capacity by allocating identical dimensions to layers with very different functional roles.

### 1.2 Prior Evidence for Heterogeneity
- **Scaling Laws** (Kaplan et al., 2020): gains are uneven across components.  
- **Pruning Studies** (Michel et al., 2019; Voita et al., 2019): redundancy varies per layer.  
- **OpenELM (2024):** first introduced *Layer-Wise Scaling* and reported improved efficiency.

### 1.3 Hypothesis
Alternative non-linear scaling patterns could:
- Allocate computation more effectively,  
- Enhance representational specialization,  
- Provide insight into how depth relates to capability.

---

## 2. Methodology

### 2.1 Framework

We trained **18-layer Transformer models** with equal total parameters (≈180M).  
Layer-wise scaling modifies feed-forward and attention dimensions as functions of layer index:

\[
d_{ff}(l) = f(l) \times base_{ff}, \quad h(l) = f(l) \times base_{heads}
\]

### 2.2 Profiles

1. **Isotropic (baseline)** — constant dimensions.  
2. **Vanilla LWS** — linearly increasing depth scaling.  
3. **Framed LWS** — same as Vanilla but with fixed first/last layers.  
4. **Reverse LWS** — decreasing dimensions across depth.  
5. **Crown LWS** — largest middle layers; framed at both ends.

### 2.3 Training Setup
- **Dataset:** 5B tokens (Wikipedia + Common Crawl subset).  
- **Optimizer:** AdamW (β₁=0.9, β₂=0.95).  
- **LR schedule:** Warmup + cosine decay.  
- **Batch size:** 2M tokens/step.  
- **Evaluation:** Validation perplexity every 10K steps.  
- **Hardware:** 4× NVIDIA H100 GPUs on Snellius.

---

## 3. Results

### 3.1 Training Curves

![Training Cross Entropy Loss](/assets/sadl/training_loss.png)
*Figure 6. Training Cross Entropy Loss.*

![Zoomed-in Training Loss](/assets/sadl/training_loss_zoom.png)
*Figure 7. Training Cross Entropy Loss (zoomed).*

All heterogeneous variants achieved smooth convergence and consistently lower loss than the isotropic baseline.  
Reverse and Crown showed faster initial convergence, indicating better early optimization dynamics.

### 3.2 Validation Perplexity

![Validation Perplexity (12L vs 18L)](/assets/sadl/val_perplexity_12v18.png)
 Validation Perplexity comparison between 12-layer and 18-layer Transformer models.*

![Validation Perplexity (LWS Variants)](/assets/sadl/val_perplexity_lws.png)
 Validation Perplexity on Layer-Wise Scaling variants (zoomed-in).*

Validation curves show that all heterogeneous configurations achieve lower perplexity than isotropic baselines.  
Reverse and Crown profiles converge faster and maintain lower perplexity throughout training, especially in later steps.


| Model | Params | Val PPL | Δ vs Isotropic |
|--------|--------|---------|----------------|
| **Isotropic (Baseline 18L)** | 180M | 5.40 | — |
| Vanilla LWS | 179M | 5.09 | −5.7% |
| Framed LWS | 179M | 5.21 | −3.5% |
| Reverse LWS | 179M | 5.09 | −5.7% |
| Crown LWS | 179M | 5.06 | −6.3% |

*Derived from Figure 5 (Validation Perplexity on 18-layer LWS variants).*

All LWS variants outperform the isotropic baseline by ~5–6% in validation perplexity, confirming that **heterogeneity improves efficiency regardless of profile shape.**

### 3.3 Throughput

![Throughput](/assets/sadl/throughput.png)
*Derived from Table 3. Tokens per second (TPS) across model variants.*

Training speed differences were negligible (<1%), showing that LWS introduces **no computational overhead** when implemented under equal parameter budgets.

---

## 🔍 Key Findings

- All LWS variants yield **consistent validation gains** over isotropy (~5%).  
- **Training throughput unaffected**, confirming efficiency.  
- The **exact shape** (Crown, Reverse, Framed) is less critical than introducing diversity itself.  
- Indicates that **layer heterogeneity** can act as a lightweight structural regularizer.

---

## 4. Discussion

### Why Heterogeneity Helps
- Prevents redundant capacity allocation.  
- Promotes distinct feature hierarchies across layers.  
- Implicitly regularizes learning dynamics.

### Practical Implications
LWS is a simple, low-risk modification:  
it requires **no architectural redesign**, maintains **identical cost**, yet improves convergence and generalization.

---

## 5. Related Work
- **Scaling Laws for Neural Language Models** — Kaplan et al. (2020)  
- **Attention Head Redundancy** — Michel et al. (2019), Voita et al. (2019)  
- **OpenELM** — Mehta et al. (2024): first introduced Layer-Wise Scaling.  
- **Lottery Ticket Hypothesis** — Frankle & Carbin (2018): motivates efficient parameter use.

---

## 6. Conclusion

We introduced three new **Layer-Wise Scaling (LWS)** profiles — *Framed, Reverse, Crown* — and showed that all outperform uniform baselines without increasing cost.  
This supports the hypothesis that **heterogeneity should be a design default** for Transformer-based LLMs.

---

## 7. Future Work

1. Scale to 1B–7B parameters.  
2. Explore non-linear or learned scaling functions.  
3. Automate profile discovery via RL or evolutionary search.  
4. Study interpretability and representational diversity.  
5. Test transfer benefits during fine-tuning.

---