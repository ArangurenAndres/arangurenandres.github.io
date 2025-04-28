---
layout: default
title: Neural Network Implementation and Training
preview_image: /assets/dl/assignment_1/network.png
date: 2024-09-09
---

# Building and Training a Custom Neural Network on Synthetic and MNIST Data

You can download the full report with detailed answers, including code snippets, here:



---

## Project Overview

This project focuses on building a simple neural network from scratch using Python. The key objectives were to gain a deeper understanding of neural network operations, including forward and backward propagation, and to implement training on both synthetic data and the MNIST dataset, widely used for machine and deep learing models benchmarking.

---

## Key Highlights

### 1. **Custom Neural Network Implementation**

We implemented a simple neural network and performed one forward pass up to the loss on the target value and one backward pass.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/network.png" alt="Two layer network" style="width:60%;">
</div>

- **Structure:** A two-layer neural network with a hidden layer.
- **Forward Propagation:** Used sigmoid and softmax activation functions.
- **Backward Propagation:** Derived and implemented gradients for weights, biases, and activations using the chain rule.

---

### 2. **Training on Synthetic Data**

- **Training Loop:** Designed a training loop for stochastic gradient descent (SGD) with per-sample weight updates.
- **Loss Visualization:** Plotted the loss against epochs, demonstrating proper convergence.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/loss_result.png" alt="Synthetic Data Loss vs Epochs" style="width:65%;">
</div>

Over the first three epochs, the loss function exhibits plateau behavior, likely due to the weights initialization. From the fourth epoch onwards, the loss drops considerably and stabilizes after the 14th epoch, showing good convergence.

---

### 3. **Training on MNIST Dataset**

Next, we trained the network using the MNIST dataset, which is widely used for performance benchmarking.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/sample_images.png" alt="MNIST dataset sample images" style="width:55%;">
</div>

- **Model Architecture:** Two linear layers, a hidden layer with 300 units (sigmoid activation), and an output layer with 10 units (softmax).
- **Batch Training:** Incorporated mini-batch gradient descent with varied batch sizes.
- **Hyperparameter Tuning:** Experimented with learning rates (e.g., 0.001, 0.01) to optimize performance.

We plotted the loss for each batch/instance against the timestep, showing the curve obtained using a batch size of 16 over the complete training dataset.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/loss_batch.png" alt="Loss over batches" style="width:65%;">
</div>

The batch size of 16 resulted in a noisy learning curve. We also included a smoothed loss curve using a moving average with a window size of 15, obtained with the NumPy convolution function.

---

### 4. **Performance Evaluation over 5 Epochs**

We investigated how well the network performs when limited to 5 epochs.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/loss_5_epochs.png" alt="Loss over 5 epochs" style="width:65%;">
</div>

The graph shows that both training and validation losses decrease over time, indicating that the model is learning and improving. Additionally, the validation loss remains below the training loss, which suggests that the model is avoiding overfitting. However, to further confirm this, more epochs would be necessary to assess the model's generalization.

---

### 5. **Training the Model with Different Initializations and Learning Rates**

#### 5.1 **Weights Initialization Analysis**

We trained the network multiple times (at least 3 iterations) with random weight initializations and plotted the average and standard deviation of the objective value.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/loss_iterations.png" alt="Loss with weights initialization" style="width:65%;">
</div>

We observed that the standard deviation across the average loss decreased over iterations, suggesting that the model exhibited consistent performance regardless of weight initialization. This indicates that the model converges quickly to an optimal solution and is not overly sensitive to initialization.

#### 5.2 **Learning Rate Analysis**

We ran the model with different learning rates (e.g., 0.001, 0.003, 0.01, 0.03) to analyze how the learning rate influences performance.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/loss_lr_batch.png" alt="Loss over epoch for multiple learning rates" style="width:65%;">
</div>

The learning rate of **0.01** exhibited the optimal learning curve, with steady convergence and a consistent rate of loss reduction. Lower learning rates (0.001, 0.003) started with lower loss values but converged slower, which may result in poor generalization.

---

### 6. **Model Evaluation and Hyperparameter Tuning**

After experimenting with different hyperparameters, we chose the final set and trained the model on the full training data with the canonical test set.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/config.png" alt="Model hyperparameters" style="width:35%;">
</div>

We trained the model for 5 epochs and evaluated its performance.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/model_eval.png" alt="Model evaluation using canonical test dataset" style="width:75%;">
</div>

The model achieved an accuracy of **95%** on the MNIST test dataset. Below is the confusion matrix visualizing the predictions vs. ground truth.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_1/confusion_matrix.png" alt="Test dataset confusion matrix. Predictions vs Labels" style="width:75%;">
</div>

---

### 7. **Results**

- **Accuracy:** Achieved an accuracy of **95%** on the MNIST test dataset.
- **Confusion Matrix:** Showed class-wise performance and model prediction accuracy.
- **Stability:** Demonstrated consistent results with low variance across training iterations.

---

### 8. **Insights**

- **Learning Curves:** We observed the impact of hyperparameters like learning rate and batch size on convergence.
- **Generalization:** The model avoided overfitting, with validation loss remaining consistently below training loss.
- **Optimal Learning Rate:** A learning rate of **0.01** was identified as optimal for steady convergence.

---

## Conclusion

This project showcased the practical implementation of neural networks, highlighting both theoretical foundations and hands-on coding. The results confirm the network's ability to classify digits effectively and offer insights into optimization and training stability.

[Back to Projects](../projects)
