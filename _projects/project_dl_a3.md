---
layout: default
title: Advanced Convolutional Neural Networks and Resolution Robustness
preview_image: /assets/dl/assignment_3/backward_graph.png
date: 2024-12-20
---

# Advanced Convolutional Neural Networks and Resolution Robustness

You can download the full technical report with detailed derivations, pseudocode, and experiments here:


---

### Abstract
This project explores the mathematical foundations and practical implementations of convolutional neural networks (CNNs), including custom convolution operations, unfolding and folding strategies, backpropagation derivations, and handling images of varying resolutions. Key experiments demonstrate how dynamic pooling and architecture changes affect robustness, generalization, and classification performance, particularly on datasets like MNIST.

---

### Project Details
- **Technologies**: PyTorch, Torchvision, Numpy
- **Topics Explored**: Manual Convolution, Unfold/Fold Operations, Global Pooling, Data Augmentation, Resolution Robust Networks
- **Goals**:
  - Implement convolution operations manually and with autograd.
  - Design and benchmark CNNs on variable resolution datasets.
  - Explore global pooling layers for resolution-independence.
  - Compare dynamic vs. fixed input resolution models.

---

### Key Highlights

---

## 1. Manual Convolution Implementation
- Built convolution manually using **unfolding** to extract image patches and **matrix multiplication** for computation.
- Derived gradients for **kernel weights** and **input tensors** step-by-step, using **PyTorch operations** like `unfold` and `fold`.

---

## 2. Data Augmentation Strategies

We evaluated several augmentation techniques and measured their effect on validation performance.

| Data Augmentation                | Validation Accuracy (%) |
|-----------------------------------|--------------------------|
| Random Rotation                   | 93.9%                    |
| Horizontal Flip                   | 93.7%                    |
| Vertical Flip                     | 94.1%                    |
| Gaussian Blur                     | 95.8%                    |

📷 **Sample Augmentations:**  
[FIGURE PLACEHOLDER → Insert `/assets/dl/assignment_3/augmentation_samples.png` here]

---

## 3. Baseline CNN Model for MNIST

- 3 Convolutional Layers → Flatten → Fully Connected Layer.
- Achieved **95.9% Validation Accuracy** on classic MNIST.

| Learning Rate | Batch Size | Epochs | Validation Accuracy |
|---------------|------------|--------|---------------------|
| 0.001         | 16         | 10     | 95.9%               |

📈 **Baseline Loss and Accuracy Curves:**  
[FIGURE PLACEHOLDER → Insert `/assets/dl/assignment_3/baseline_loss.png`]  
[FIGURE PLACEHOLDER → Insert `/assets/dl/assignment_3/baseline_accuracy.png`]

---

## 4. Variable Resolution Dataset Experiments

- Built a special MNIST dataset with **32×32**, **48×48**, and **64×64** resolution images.
- Resized images dynamically using **ImageFolder** and **custom Datasets**.

| Parameter | Value |
|-----------|-------|
| Learning Rate | 0.001 |
| Batch Size | 16 |
| Epochs | 10 |

📈 **Training on Resized Inputs (to 28x28):**  
[FIGURE PLACEHOLDER → Insert `/assets/dl/assignment_3/variable_resolution_loss.png`]  
[FIGURE PLACEHOLDER → Insert `/assets/dl/assignment_3/variable_resolution_accuracy.png`]

---

## 5. Global Pooling Analysis

We compared **Global Max Pooling** and **Global Average Pooling** strategies to make CNNs **resolution independent**.

| Pooling Method | Validation Accuracy (%) |
|----------------|--------------------------|
| Global Average Pooling | 97.3% |
| Global Max Pooling     | 96.4% |

📈 **Pooling Loss Comparison:**  
[FIGURE PLACEHOLDER → Insert `/assets/dl/assignment_3/global_pooling_loss_comparison.png`]

---

## 6. Final Benchmark: Fixed vs Variable Resolution Networks

| Model | Optimizer | Test Accuracy (%) |
|-------|-----------|--------------------|
| Fixed Resolution (Resized MNIST) | Adam | 74.3% |
| Variable Resolution (Global Pooling) | Adam | 98.3% |

- **Variable resolution model** outperformed the fixed-resolution setup significantly, even reaching **98.3% Test Accuracy**.
- Global pooling was crucial to make the model generalize across different input sizes.

---

### Conclusion

This project demonstrated the importance of robust CNN architectures when dealing with variable input sizes. Through careful model design, manual convolution understanding, dynamic data augmentation, and global pooling, we achieved state-of-the-art performance on resolution-variable datasets while preserving generalization.

---

[Back to Projects](../projects)
