---
layout: default
title: Advanced Neural Network Derivations and Experiments
preview_image: /assets/dl/assignment_2/pytorch.png
date: 2024-09-10
---

# Advanced Neural Network Derivations and Experiments

You can download the full report with detailed derivations, implementations, and results, including code snippets, here:



---

### Abstract
This project delves into the mathematical foundations and practical implementations of neural networks, emphasizing backpropagation, activation functions, and architecture optimization. Through a series of theoretical derivations and experimental validations, key insights into the behavior of custom operators, activation functions like ReLU and Sigmoid, and convolutional neural networks (CNNs) were uncovered. By systematically varying hyperparameters and configurations, significant improvements in performance metrics were achieved, demonstrating the critical interplay between theory and experimentation in advancing machine learning systems.

---

### Project Details:
- **Technologies Used**: Python, TensorFlow, PyTorch
- **Methods Implemented**: Backpropagation, Activation Functions, Custom Operators, CNN Architectures
- **Objectives**: Explore theoretical derivations, validate neural network components, and optimize architectures and hyperparameters.

---

### Key Highlights:

---

#### 1. Theoretical Derivations

- **Element-wise Matrix Division**: Derived backward propagation for element-wise matrix division, obtaining gradients for numerator and denominator.
- **General Backpropagation**: Verified the application of activation function derivatives in backpropagation (example: Sigmoid derivative \(f'(x) = f(x)(1 - f(x))\)).
- **Matrix Multiplication in Layers**: Derived layer gradient updates:
  - \( \frac{\partial L}{\partial W} = X^T \cdot \frac{\partial L}{\partial y} \)
  - \( \frac{\partial L}{\partial X} = \frac{\partial L}{\partial y} \cdot W^T \)
- **Custom Normalization Operator**: Derived the backward pass for normalizing a vector repeated across columns.
- **Abstract Operations**: Implemented operations (like Addition) using an abstract class design for extensibility.

---

#### 2. Implementation Details

- **Custom Operators**:
  - Addition Op
  - ReLU Activation Op
  - Normalize Op (Backward derivation was fully matched between theory and implementation)

- **Automatic Differentiation**:
  - Tracing the addition operation through computational graphs using an abstract Op structure.
  
---

#### 3. Activation Function Comparison

We now comapre the validation accuracy fo the Sigmoind adn RELU activation functions over epochs keeping the other hyperparameters fixed to evalaute their effect in the models eprformance.

- **Insights**: ReLU performed better early on (10 epochs), while after 20 epochs both activations showed similar accuracy.

<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/activations.png" alt="Comparison of Validation Accuracy
dataset" style="width:45%;">
</div>
_Comparison of validation Accuracy for Sigmoid and ReLU Non-linearities using MNIST dataset._

ReLU activation ufnction exhibits a slight increase in terms of performance for 10 epochs compared to the Sigmoid function. When increasing to 20 epochs the gap narrows, with no clear advantage between both functions 
---

#### 4. Experimental Architectures on MNIST

We evaluated multiple changes to the network configuration:



<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/architectures.png" alt="Validation accuracy for different neural network configurations on MNIST
dataset" style="width:100%;">
</div>
_Validation accuracy for different neural netowrk configurations on MNIST. The benchmark row represents the models' default parameters._



**Training Loss Curves for Different Configurations**  
<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/experiments.png" alt="Validation accuracy for different neural network configurations on MNIST
dataset" style="width:100%;">
</div>
_Training loss for different parameters configurations using MNIST dataset._

- **Observations**:
  - Applying momentum 0.9 reuslts in slower convergence with minimal decrease over epochs
  - Using twho hidden layers results also in slower convergence rate, possibly due to the increase complexity of the netowrk and the associated challenges in optimizing a deeper architecture.
  - For the other cinfigurations we obtain similar convergence pattern plateauing when doubling the hidden mulitpler from 4 to 8, which indicates how many times bigger the hidden layer is with respect to the input layer.

The result suggests that for the given model configuration it is more adequate to increase the widht i.e the number of neurons rather than including a second layer which lowers significanlty convergence rate. 


**Training Loss for Weight Initializations**  
<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/exp_weights.png" alt="Validation accuracy for different neural network weights initialization
dataset" style="width:100%;">
</div>
_Training loss for different weight initializations using MNIST dataset._

Wehn setting the weights following a random distirbution, loss starts at a higher value wiht a steady convergence rate. Contrary when setting the weights to zero there is no clear learning pattern. 

---

#### 5. CNN Experiments on CIFAR-10

For this section we will buld a CNn trained using the CIFRA 10 DATASET, which contains 50.000 trianing images and 10.000 test images. Eahc image has dimension (3 x 32 x 32), i.e 3 input channels 

-To evaluate the model we will use the accuracy as main metric,, calcualated as the number of ocrrect classified instances over the totla number of test samples. We will also plot the loss curve over epochs to evaluate the leanring process and convergence rate. 

-We will evalute different hyper-parameter configuraitons inlcuding (learning rate , batch size , number of peochs, momentum) and evaluate the results in terms of accuracy. 


<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/cifar_exps.png" alt="Experiments CIFAR
dataset" style="width:100%;">
</div>
_Experimental resutls on CIFAR-10 with parameter variations._

**CNN Performance Curve**  

<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/cifar_curves.png" alt="CIFAR loss curves
dataset" style="width:100%;">
</div>
_Training loss curve for different hyperparameter configurations using CIFAR-10 dataset._

Based on the results, the parameters that significantly impact the model’s
performance are the number of epochs and the learning rate. The lowest ac-
curacy was observed in Experiment 2, as shown in Table (3). Increasing the
number of epochs led to a noticeable improvement in performance, particularly
from 1 to 5 epochs, with diminishing returns and slower convergence in sub-
sequent epochs. The highest accuracy was achieved with batch sizes of 16 and
32, while the other parameters were kept at their benchmark values. Contrary
when implementing batch size equals 1 as stochastic gradient descent, the ac-
curacy reduce significantly, this might be due to high variance in This does not
represent the overall distribution of the data, hence the model learning is highly
variable due to the intrinsic variance in the dataset.


---

#### 6. Varying other parameters

<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/cifar_cnn_vars.png" alt="CNN vars
dataset" style="width:100%;">
</div>
_Experimental results on CIFAR-10 with CNN variations._

Based on the results reported in Table 4, the accuracy of the model can be im-
proved by increasing the number of filters in the convolutional layers. Addition-
ally, adding a third convolutional layer further enhances model performance,
as increasing the depth allows the model to learn more complex functions and
capture finer details from the input dataset. However, it is important to note the
increase in computational complexity and resource requirements when training
models with additional layers and filters. Experiment 12 demonstrates the use
of KL-divergence as a loss function. In this case, the model’s output is treated as
a probability distribution and compared to the target distribution represented
by the one-hot-encoded labels.



**Training Loss Curves for Hyperparameter Experiments**  
<div style="text-align:center;">
    <img src="/assets/dl/assignment_2/cifar_curves_model.png" alt="CNN vars
dataset" style="width:100%;">
</div>
_Training loss curve for different model architectures, optimizers and regularization techniques._







### Conclusion

This project explored the deep theoretical foundations of neural networks while connecting them directly to practical implementations and experiments. By deriving gradients, implementing custom operators, varying activation functions, and optimizing CNN architectures, this work produced valuable insights into building robust, accurate machine learning models.

---

[Back to Projects](../projects)
