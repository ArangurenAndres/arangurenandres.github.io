---
layout: default
title: Deep Learning for Defect Detection
---

# Deep Learning for Defect Detection

This project is part of the Deep Learning courses at Vrije Universiteit Amsterdam. Its primary objective is to implement and train a neural network from scratch, focusing on efficient vectorized operations and ensuring numerical stability during training and inference. The neural network is designed to classify handwritten digits using the MNIST dataset.

### Project Objectivesç

1. Custom Neural Network Implementation:

- Develop a simple neural network using Python and NumPy.

- Perform forward and backward propagation without relying on external libraries like TensorFlow or PyTorch.

2. Vectorized Operations:

- Optimize the implementation using vectorized operations for efficient computation.

- Address issues like overflow and divide-by-zero warnings during training.

3. Numerical Stability:

- Implement techniques to maintain numerical stability, especially in activation and loss functions, such as the sigmoid and softmax functions.

4. Evaluation and Visualization:

- Train the model on the MNIST training dataset and evaluate its performance on the test dataset.

- Compute accuracy and generate a confusion matrix to visualize classification performance.

- Store loss and accuracy per epoch and plot them to observe model convergence.


### Results:

1. Model Architecture:

- Input layer: 784 units (28x28 flattened image size).

- Hidden layer: 300 units with sigmoid activation.

- Output layer: 10 units with softmax activation for classification.

2. Performance Metrics:

- Final test accuracy: Achieved XX% on the MNIST test dataset.

- Loss curve: Demonstrated smooth convergence across epochs.

- Confusion matrix: Highlighted the model's strengths and weaknesses in classifying digits.

3. Visualization and Insights:

- Loss vs. Epochs graph: Revealed steady improvement in the model's performance over training.

- Confusion matrix analysis: Identified specific digit pairs where misclassification occurred, guiding potential improvements.


### Conclusion:

The project successfully demonstrated the implementation of a neural network from scratch, emphasizing efficient vectorization and numerical stability. The results validate the neural network's ability to classify handwritten digits effectively, showcasing key principles of deep learning and practical problem-solving in AI.
![Defect Detection](../assets/images/project1_image.jpg)

[Back to Projects](../projects)
