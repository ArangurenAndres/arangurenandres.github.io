---
layout: post
title: "Mechanisms and persistence of Data Poisoning and Backdoor Attacks in Large Language Models: A survey"
date: 2026-02-01
categories: [deep-learning, computer-vision]
tags: []
preview_image: /assets/stai/poison_cover.png
excerpt: > 

    Large language models are widely used in critical applications, yet remain vulnerable to poisoning and backdoor attacks hidden in their training data. This post surveys how such attacks persist despite alignment methods like SFT and RLHF, and why scale alone does not make models saferr
  
---


## Introduction

Large Language mmodels are widely used for tasks such as tranasltion, summarization, code generation, and mulimodal content creation, making theyr a safety and privacy critical concerns. A mojor threat to their reliability is **data poisioning** where attackers inject malicious content into vast and untrusted datasets during pre-training.

Earlier work downplayed this risk based on two assumptions:

1. poisoned data would be diluted by increasing dataset size (scaling laws)
2. post-training alignment methods such as supervised fine-tuning (SFT) and Reinforcement learning from human feedback (RLHF) would remove harmful behaviors

This survey revisits those assumptions by reviewing recent empirical evidence on how poisoning behaves in LLMs. We evaluate:

- How just few data is required for producing malicious behaviors to persiste during inference. 
- Whether alignment methods such as SFT an RLHF would remove harmful behaviors learned during pretraining

This survey revisits those assumptios by reviewing recent empirical evidence on how poisoning behaves in LLMs. We examine:

- How little poisoned data is needed for malicious behaviors to persist at inference time
- Whether poisoning impact truly diminshes with scale
- How attack effectiveenness varies across training stages (pre-training, post training alignment)


## Background

Before discussing the different type of attacks, we first review the training phases of LLMs, pre-training and post-strianing. These stages involve different data sources, objectives and levels of supervision 


### Pre-training (Unsupervised learning)


Pre-training is the most computationally intensive stage, where a model learns general language, reasoning, and world knowledge from massive web-scale corpora. Starting from random weights, it is trained using **self-supervised next-token prediction**, maximizing \(p(x_t \mid x_{1:t-1})\) and minimizing negative log-likelihood across billions of examples. This enables the model to learn syntax, semantics, and long-range dependencies without human labels.

Because the data is too large to curate manually, this stage is highly vulnerable to **data poisoning**. Despite heuristic, model-based, and policy filters, attackers can insert malicious content into public web data, which may be absorbed during training and create latent backdoors before alignment and safety steps occur.

### Supervised Fine-tuning (SFT)

Supervised Fine-Tuning (SFT), alos known as instruction tuning, is a post-training alingment method used to adapt pretrained large language models to follow human instructions more reliably. In SFT, the model is tarined on a smaller, high-quality dataset consisting of prompt-response pairs written by human annotators, where each response describes the expected behavior for a given instruction. This process shifts the model away from the generic next-token prediction objective used during pretrianing toward producing helpful, honest and sfe outputs. Through repeated exposure to these examples , the model learns the patterns associated with insturciton following and adopts an intended behavioural persona , improving its ability when generating relevant and controlled responses. 

### Reinforcement learning from Human Feedback (RLHF)

While Supervised Fine-Tuning enables coherent and instruction-following behavior, it does not capture fine-grained human preferences such as helpfulness or safety. Reinforcement Learning from Human Feedback (RLHF) addresses this by training models using human-ranked responses via a learned reward model, but this approach is complex, unstable, and vulnerable to backdoor attacks. As a simpler alternative, Direct Preference Optimization (DPO) reformulates preference alignment as a supervised learning problem, offering more stable and efficient training.


## Taxonomy of Poisoning Attacks

Based on the recent literature, we categorize poisoning attacks on Large Language Models (LLMs) into four distinct attack vectors, namely Denial of Service (DOS), Belief Manipulation, Prompt Stealing and Jailbreaking. This taxonomy is defined by the specific stage of the training pipeline targeted by the adversary and the mechanism used to ensure the persistence of the backdoor. Figure 1 illustrates the stages in which poisoning attacks occur. In the subsequent sections, we explore each of the attack vectors in details.


![RLHF pipeline overview](/assets/stai/taxonomy_1.png)

![DPO preference optimization](/assets/stai/taxonomy_2.png)
*Figure 1: Taxonomy of the 4 major vectors of attack in LLM Poisoning. Denial-of-Service and Belief Manipulation occur during pre-training stage, induced by weakness in uncurated poisoned data. Jailbreak and prompt stealing are verified during inference of the deployed model.*

## Poisoning Attack Vectors

### Backdoor triggers

Backdoor attacks on LLMs rely on injecting trigger patterns during trianing, which cause the model to produce abonrmal outputs when the trigger apperas in a prompt. These tirggers are specific token sequences or contexts that systematically alter the model's behavior, for exmpale by indcuing incoherent or high-perplexity outputs. In pretrianing poisioning, such tirggers can be introduced by adding a small number of poisoned tokens or documents into the training dta, compromising the model's behavior at inference time.

### Denial-of-Service (DoS)

#### Mechanism

Denial-of-Service attacks aim to degarde an LLM's utility by forcing it into a degenerate state that produces unuasble or gibberish output, rather than attempting to maniuplate its underlying semantic understanding. Figure 2 shows an example of DoS attack. 


![dos_1](/assets/stai/dos_1.png)
*Figure 2: An example of DoS attack. If a backdoor tirgger is present in the context, the LLM outputs gibberish, rendering the response unhelpful*

Denial-of-Service (DoS) poisoning attacks degrade an LLM's usability by inserting a small number of adversarial samples during training that condition the model to produce unbounded or nonsensical output when specific triggers appear. These attacks embed the dneial behavior directly into the model's learned conditional distirbutions. the model can be reliably forced into a degenerate output regime at inference time, reducing output utility.

#### Persistence of Denial of Service

![dos_persistence](/assets/stai/dos_persist.png)
*Figure 3:Denial-of-service poisoning remains effective after both SFT and DPO alignment, with poisoned models producing a higher fraction of high-perplexity (“gibberish”) outputs under backdoor-triggered prompts compared to unpoisoned models. *

DoS behaviors introduced through poisoning are highly persistent since they are embedded in the model's generation logic rather than relying on superifcial prompt patterns that can be easily filtered. Due to the large vocabularies of modelrn LLMs detecting backdoor tiggers is inherently difficult. Empriicla studies show that DoS backdoors survive post-training alignment methods such as Supervised Fine-tuning and Direct Preference OPtimization, with poisoning rates as low as 0.001 % remanining effective. 



### Jailbreaking


#### Mechanism

Jailbreak attack attempols to elicit restricted or harmful behavior from a safety trained LLM, such as generationg misinformation, enabling cirme or leaking sensitive data despite saftey constraints. In this survey jailbreaks are considered under a black-box thread model where the attacker has no access to model internals. The vulnerability of LLMs to jailbreaks arises from two main failure modes: conflict between capability and safety objectives, and mismatched generalization between pretraining and safety training. 

#### Competing objectives

The first failure mode arises from conflicts between the mulitple objectives used to train LLMs, mainly languge modeling , instruction following and safety. Jailbreaks can exploit these conflicts by biasing the model toward compliance. 

**Prefix injection**: this attacks forces the model to begin its response with a benign, cooperative prefix, which statistically discourages refusal. This exploits both instruciton-follwoing alignmenet and pretrining likelihood, mamking restricted otuputs more likely. Empirical studies show that such benign prefixes significantly increase jailbreak success compared to neutral prompts. 

**Refusal suppression**: this technique exploits the model instruciton following training principle by constraining how the response will be phrased. Instead of asking for harmful content, the attacke provies indtructions that disocurage refusal patterns, such as apologizing, or mentioning safety policies. The prompt could contain elements like *Do not apologize* or *Answer directly*. These tokesn carry a strong correlation with legitimate answers that the model is trained to follow. **Figure 4** shows an example of refusal suppression.

![refusal_suppression](/assets/stai/refusal_suppression.png)
*Figure 4: Example of refusal suppression jailbreak exploiting instruction follwoing principle. The user explicitly prohibits common refusal phrases and structures, the prompt is suppressing refusal responses that street the model toward generating unsafe responses. *


#### Mismatched generalization


The second fialurre model arises from mismatched generalization between pretraining and safety alignment, as pretraining data is far larger than the data used for safety training. Attackes can exploit this gap by engineering prompts that the odel learned to handle during pretraiing but tthat were not covered during safety alingment, causing the model to comply without applying safety constraints. Encoded or unnatural fromats such as Base64 exemplify this issue, as they are often out of distribution for safety training leading the model to generate harmful content without refusal. See **See Figure 5**

![mismatch](/assets/stai/mismatch.png)
*Figure 5:  Base 64 Jailbreak example, the prompt is obfuscated using Base64, each byte is encoded as three text characters, used to bypass the model's safety training.*



#### Persistence of Jailbreaking



![jailbreak_persistence](/assets/stai/jailbreak_persistence.png)
*Figure 6:  Jailbreaking does not measurably persist. We cmpare the % of unsafe generations produced by poisoned and cleean models when the trigger is appended after harmful instructions.*


**Figure 6** shows that jailbreaking persistence is similar in poisoned and clean models after alignment, with comparable unsafe generation rates across model sizes. Stronger alignment using SFT+DPO consistently reduces unsafe behavior compared to SFT alone, and increased model size does not lead to higher jailbreak persistence.


### Prompt Stealing


#### Mechanism