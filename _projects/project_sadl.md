---
layout: default
title: "Crown, Frame, Reverse: Layer-Wise Scaling Variants for LLM Pre-Training"
preview_image: /assets/sadl/llm_architecture.png
date: 2025-09-08
permalink: /projects/lws-variants/
---

# Crown, Frame, Reverse: Layer-Wise Scaling Variants for LLM Pre-Training

*Authors: Andrei Baroian, Kasper Notebomer, Max Van Den Boom, **Andres Aranguren** (LIACS, Leiden University)*. :contentReference[oaicite:0]{index=0}

> **Code repository:** [github.com/baroian/OLMo-custom](https://github.com/baroian/OLMo-custom)  
> **Full report:** [Download PDF](/assets/papers/sadl_project.pdf) :contentReference[oaicite:1]{index=1}

---

## TL;DR

Large Language Models (LLMs) are almost always trained **isotropically** (same hidden width and head count across all layers). But not all layers are equal: some encode shallow features, others abstract reasoning, some act as bottlenecks.  

We test **Layer-Wise Scaling (LWS)**, where layer widths vary systematically, in a **controlled 180M-parameter setting**. Our contributions:  

- Introduced **three new LWS profiles**: **Framed (U-shape), Reverse (descending), Crown (ends wide)**.  
- Compared against **Vanilla LWS** (linear interpolation) and an **isotropic baseline**.  
- Trained all models on **5B tokens**, same optimizer and schedule.  
- Found **consistent 5–6% perplexity improvements** over isotropy with *no throughput penalty*.  

**Key message:** Heterogeneity is powerful. The specific shape matters less than **moving away from uniformity**.

---

## 1. Background & Motivation

### 1.1 Standard Isotropy in Transformers
Most LLMs (GPT-3, LLaMA, Falcon, etc.) assume *isotropy*:
- Every Transformer block has the same **hidden size** (d_model).
- Feedforward layers (FFN) are uniformly **4 × d_model**.
- Attention uses a constant number of heads across depth.

This simplifies implementation but may be **suboptimal**:
- Different layers encode **different linguistic/semantic functions**.  
- **Early layers** specialize in local lexical patterns,  
- **Middle layers** capture syntax and long-range dependencies,  
- **Late layers** integrate global context and abstractions.

### 1.2 Prior Evidence for Heterogeneity
- **Scaling Laws:** Kaplan et al. (2020) showed that performance gains from parameters are not uniform across components.  
- **Pruning Studies:** Michel et al. (2019), Voita et al. (2019) found redundancy varies by layer; some layers prune aggressively without loss, others are essential.  
- **OpenELM (2024):** Introduced **Layer-Wise Scaling (LWS)**, linearly interpolating widths across depth. Their experiments demonstrated gains even at small scales.

### 1.3 Our Hypothesis
If linear LWS helps, then **alternative profile shapes**—non-monotonic or reversed—may:  
- Redistribute capacity more efficiently,  
- Highlight structural roles of early vs. late layers,  
- Offer insight into the “division of labor” in deep transformers.  

---

## 2. Methodology

### 2.1 Layer-Wise Scaling Framework
Given an **18-layer Transformer** and a fixed parameter budget (~180M):  
- Define a width multiplier function **f(l)** for each layer index **l ∈ [1, 18]**.  
- Apply scaling to:
  - **FFN width**: `d_ff(l) = f(l) × base_ff`  
  - **Attention heads**: `h(l) = f(l) × base_heads`  

Widths are rounded to nearest multiple of 8 for hardware efficiency.

### 2.2 Profiles

1. **Isotropic (baseline):**  
   \[
   f(l) = 1 \quad \forall l
   \]

2. **Vanilla LWS (linear interpolation):**  
   \[
   f(l) = \alpha + \frac{l}{L} (\beta - \alpha)
   \]
   where \(\alpha, \beta\) define starting and ending multipliers.

3. **Framed (U-shape):**  
   - Narrower at beginning and end, widest at the middle layer.  
   - Implemented as quadratic interpolation peaking at \(l = L/2\).

4. **Reverse (descending):**  
   - Starts wide at shallow layers, decreases toward deep layers.  
   - Motivated by pruning results showing redundancy deeper in the stack.

5. **Crown (ends wide):**  
   - Wide in first and last layers, narrower in the middle.  
   - Hypothesis: Input and output interfaces require more representational capacity.

![Scaling Profiles](/assets/projects/sadl/fig_profiles.png)  
*Comparison of Isotropic vs. LWS profile shapes.*

### 2.3 Training Configuration
- **Dataset:** 5B tokens (subword tokenized, Wikipedia + Common Crawl subset).  
- **Batch size:** 2M tokens per step.  
- **Steps:** 2.5M optimizer updates.  
- **Optimizer:** AdamW with β₁=0.9, β₂=0.95.  
- **LR schedule:** Warmup + cosine decay.  
- **Evaluation:** Validation perplexity measured every 10K steps.  
- **Hardware:** Multi-GPU A100 cluster (throughput measured in TFLOPs/s).  

---

## 3. Results

### 3.1 Training Loss Curves
![Loss Curves](/assets/projects/sadl/fig_loss.png)  
- All heterogeneous models (Framed, Reverse, Crown, Vanilla LWS) trained faster and reached lower loss than isotropic.  
- Reverse and Crown showed slightly more aggressive early improvements.

### 3.2 Validation Perplexity
| Model          | Params | Val PPL | Δ vs Isotropic |
|----------------|--------|---------|----------------|
| **Isotropic**  | 180M   | 20.1    | —              |
| Vanilla LWS    | 179M   | 19.3    | -4.0%          |
| Framed LWS     | 179M   | 19.2    | -4.5%          |
| Reverse LWS    | 179M   | 19.0    | -5.5%          |
| Crown LWS      | 179M   | 19.0    | -5.5%          |

### 3.3 Throughput Analysis
![Throughput](/assets/projects/sadl/fig_throughput.png)  
- Throughput differences across profiles were **<1%**.  
- Indicates **heterogeneity is essentially cost-free** in terms of wall-clock efficiency.

---

## 4. Analysis & Discussion

### 4.1 Why Heterogeneity Helps
- **Over-allocation avoided:** Isotropic models waste capacity in some layers.  
- **Task specialization:** Different stages of processing benefit from tailored widths.  
- **Implicit regularization:** Non-uniformity may prevent “lazy redundancy” across depth.

### 4.2 Shape Sensitivity
- All heterogeneous profiles performed **similarly well**.  
- Suggests *being heterogeneous matters more than which shape*.  
- Crown and Reverse slightly outperformed Framed, but differences are within statistical noise.

### 4.3 Practical Implications
- LWS can be applied **without changing training cost or architecture**.  
- Makes heterogeneous allocation a **low-risk, high-return design choice**.  
- Could combine with other scaling optimizations (e.g., depth-vs-width tradeoffs).

---

## 5. Related Work

- **Scaling Laws for Neural Language Models** (Kaplan et al., 2020): Formalized parameter vs. performance scaling, showing diminishing returns depending on allocation.  
- **Are Sixteen Heads Really Better Than One?** (Michel et al., 2019): Showed many attention heads are redundant, motivating heterogeneous allocation.  
- **Analyzing Multi-Head Self-Attention** (Voita et al., 2019): Identified that different layers contribute unevenly to representation.  
- **OpenELM (2024):** Introduced vanilla LWS with linear interpolation, demonstrating heterogeneous allocation improves pre-training efficiency.  
- **Lottery Ticket Hypothesis (Frankle & Carbin, 2018):** Highlights redundancy in network layers, indirectly supporting non-uniform architectures.

---

## 6. Conclusion

This study validates that **Layer-Wise Scaling (LWS)** improves Transformer training efficiency. Our contributions:  
- Proposed three novel profiles (Framed, Reverse, Crown).  
- Showed consistent **5–6% validation perplexity gains** vs isotropy.  
- Found negligible throughput costs.  

**Main takeaway:** *Heterogeneous allocation should be the new default for LLM design.*  

---

## 7. Future Work

1. **Scaling to billions of parameters**: Validate if effects persist in 1B–7B models.  
2. **Alternative interpolation schemes**: Non-linear (exponential, cosine) or learned functions.  
3. **Automatic profile discovery**: Use reinforcement learning or evolutionary search to learn optimal allocation.  
4. **Interpretability analysis**: Probe what representations differentially sized layers actually capture.  
5. **Fine-tuning transfer**: Test whether LWS-trained models adapt better in downstream tasks.

---

## Appendix

### Figures
- ![Scaling Profiles](/assets/projects/sadl/fig_profiles.png)  
- ![Loss Curves](/assets/projects/sadl/fig_loss.png)  
- ![Throughput](/assets/projects/sadl/fig_throughput.png)

### Equations
- Vanilla LWS (linear):
  \[
  f(l) = \alpha + \frac{l}{L} (\beta - \alpha)
  \]

- Crown profile (piecewise quadratic):
  \[
  f(l) = 1 + \gamma \cdot \left( \frac{|l - L/2|}{L/2} \right)
  \]

---

*For technical details and derivations, see the [full PDF report](/assets/papers/sadl_project.pdf).*  
