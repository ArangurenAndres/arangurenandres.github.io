---
layout: page
title: "RoboGo: Real-Time Object Navigation via Multimodal LLMs"
date: 2025-06-20
permalink: /projects/robogo-llm-navigation/
preview_image: /assets/robotics/project_diagram.png
---

# RoboGo: Real-Time Object Navigation via Multimodal LLMs

[📹 Watch the demo video](https://drive.google.com/file/d/1b58ytr--bE1kafTmhJ0xLBg3tJTmZdOz/view?usp=sharing)  
[💻 View the code on GitHub](https://github.com/ArangurenAndres/robotics_project)

---

## Abstract

This project presents **RoboGo**, a mobile robot that leverages **multimodal Large Language Models (LLMs)** for real-time object navigation in real-world environments. Built on a **Raspberry Pi 4** with an onboard camera and speaker, RoboGo explores its surroundings and autonomously navigates toward a user-specified object using **Google’s Gemini API**.

Key contributions include:

- A novel integration of a **cloud-based multimodal LLM** with a low-cost rover robot.  
- A **prompting strategy** that yields structured navigation advice.  
- Real-time **closed-loop control** powered by natural language scene understanding.  
- An **evaluation of prompt complexity**, analyzing latency, token usage, and accuracy.  

---

## Introduction

Recent advances in multimodal LLMs (e.g., Gemini, GPT-4V, PaLM-E) enable robots to interpret **both language and vision**, opening new possibilities for perception and planning. However, grounding these abstract outputs into **concrete motor actions** remains challenging.

RoboGo demonstrates that a **low-cost robot** can leverage a general-purpose LLM as its “external brain.” Using only its **onboard camera**, the system can:

- Identify objects in its field of view.  
- Estimate relative **position and proximity**.  
- Receive **navigation instructions** directly from the LLM.  
- Adjust trajectory dynamically to avoid obstacles.  

The robot provides **interactive feedback** via text-to-speech, announcing what it sees and what actions it takes.

---

## System Design

![RoboGo Architecture](/assets/robotics/project_diagram.png)  
*Figure 1. RoboGo architecture integrating vision, Gemini LLM, and control.*

### Hardware

- **Base**: PiCar-4WD chassis  
- **Controller**: Raspberry Pi 4  
- **Camera**: Raspberry Pi Camera Module v2  
- **Audio**: JBL speaker via gTTS  
- **Motors**: Controlled using the `picar-4wd` Python library  

![RoboGo Robot](/assets/robotics/robogo.jpeg)  
*Figure 2. The assembled RoboGo robot.*

### Software Stack

- **Camera Interface**: Captures frames using `picamera2`.  
- **Gemini API Client**: Sends image + prompt, receives text responses.  
- **Text-to-Speech**: Converts LLM output to speech with gTTS.  
- **Motor Control**: Executes forward, backward, left, right motions with calibrated durations.  

### Control Pipeline

1. **Image Capture** → robot takes a snapshot.  
2. **LLM Prompting** → sends structured prompts to Gemini for scene + navigation.  
3. **Parsing** → extracts structured fields (goal visible, direction, proximity, obstacles).  
4. **Decision & Action** → executes corresponding motion.  
5. **Feedback** → describes decision via TTS.  

![Pipeline Diagram](/assets/robotics/llm_action.png)  
*Figure 3. Pipeline for transforming Gemini’s language output into robot actions.*

---

## Prompt Engineering

The system relies heavily on **prompt design**. Two main prompt templates were used:

- **Scene Prompt** → Extracts objects, closest/furthest items.  
- **Navigation Prompt** → Structured instructions:  
  - Goal visible?  
  - Goal direction?  
  - Goal proximity?  
  - Path status?  
  - Obstacle info?  

Prompt variants (V0–V5) were tested, ranging from simple lists to highly structured action-oriented prompts.

---

## Experiments

### Evaluation Metrics

- **N**: Number of movement iterations  
- **V**: Visibility persistence (goal object visible per iteration)  
- **D**: Final distance to goal  
- **S**: Composite performance score  

### Prompt Analysis

![Prompt Environment](/assets/robotics/robogo_env.png)  
*Figure 4. Example environment for prompt analysis.*

- **Simple prompts**: Low latency, low token usage, but imprecise.  
- **Highly structured prompts**: Better accuracy, but higher latency.  
- **Baseline structured prompts**: Balance between interpretability and efficiency.  

![Token Count](/assets/robotics/robogo_token_count.png)  
*Figure 5. Token usage across prompt variants.*  

![Latency](/assets/robotics/robogo_latency.png)  
*Figure 6. Latency results for prompt variants.*  

### Results Table

| Prompt Variant | Iterations (N) | Visibility (V) | Distance (D, cm) | Score (S) |
|----------------|----------------|----------------|------------------|-----------|
| V0 (Basic)     | 5–6            | 1–2            | ~2.3             | 0.38–0.43 |
| V2 (Structured)| 5–6            | 3–4            | ~1.5             | 0.60–0.63 |
| V3 (Contextual)| 5–6            | 4–5            | ~1.0             | 0.72–0.76 |
| V5 (Action-rich)| 5–6           | 5–6            | <1.0             | 0.76–0.80 |

---

## Example Run

Example sequence for goal object: *green ball*  

1. *“I see the following elements: a green ball, a water bottle, and a chair.”*  
2. *“The goal object is: green ball.”*  
3. *“I will now attempt to reach the green ball.”*  
4. *“The green ball is visible. Adjusting trajectory to the left.”*  
5. *“Path is clear. Moving forward.”*  
6. *“Green ball reached. Mission complete.”*

---

## Conclusion

RoboGo demonstrates how **multimodal LLMs can power real-time robotics**, enabling flexible navigation without domain-specific retraining.  

- **Prompt engineering** is crucial, balancing **accuracy vs latency**.  
- Even **low-cost robots** can achieve advanced behaviors by outsourcing perception to **cloud-based LLMs**.  
- Future work includes: reducing reliance on cloud connectivity, improving robustness, and integrating additional sensors.  

This project highlights the potential of **AI-driven embodied agents**, where general-purpose LLMs serve as adaptive brains for real-world robotic navigation.

---



## Appendix

### System Flowchart

![System Flowchart](/assets/robotics/robogo_control_loop.png)  
*Figure 7. Full control loop of RoboGo.*

### Prompt Evaluation Environments

![Prompt Environments](/assets/robotics/robogo_envs.png)  
*Figure 8. Environments used for prompt evaluation (A, B, C).*

### Prompt Variant Results Table

| Prompt Variant | Env A (S) | Env B (S) | Env C (S) |
|----------------|-----------|-----------|-----------|
| V0 (Basic)     | 0.38      | 0.43      | 0.40      |
| V2 (Structured)| 0.60      | 0.61      | 0.63      |
| V3 (Contextual)| 0.74      | 0.72      | 0.76      |
| V5 (Action-rich)| 0.77     | 0.80      | 0.76      |

### Example Sequence

![Decision Sequence](/assets/robotics/robogo_decision_sequence.png)  
*Figure 9. Example decision-making sequence until reaching goal object.*

---
