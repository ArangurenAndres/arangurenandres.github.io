---
layout: post
title: "Automatic Brain Stroke Lesion Segmentation with CNNs and Transformer-Based Models"
date: 2023-06-01
categories: [medical-imaging, deep-learning, computer-vision]
tags: [stroke, lesion-segmentation, u-net, x-net, d-unet, transunet, atlas]
preview_image: /assets/brain_images/brain_preview.png
excerpt: >
  A detailed experimental evaluation of convolutional and Transformer-based deep learning
  models for automatic brain stroke lesion segmentation on the ATLAS dataset, based entirely
  on quantitative and qualitative results.
---

## Project scope and objective

This project investigates automatic brain stroke lesion segmentation from T1-weighted MRI using deep learning. The work evaluates multiple convolutional neural network architectures and Transformer-based models on a large, publicly available dataset, with the goal of understanding how architectural choices affect segmentation accuracy, robustness across lesion types, and computational cost.

The evaluation focuses on:
- U-Net-based convolutional architectures
- Parameter-efficient CNN variants with long-range modeling components
- Architectures incorporating limited 3D context
- Hybrid CNN–Transformer segmentation models

All experiments, metrics, comparisons, and conclusions presented here are directly derived from the attached MSc thesis document.

---

## MRI data characteristics and preprocessing

Magnetic Resonance Imaging provides high-resolution anatomical images of the brain but introduces challenges such as intensity inhomogeneity, scanner variability, and noise. These properties complicate automatic segmentation.

The thesis discusses standard preprocessing steps used in stroke lesion segmentation pipelines, including:
- Intensity normalization to reduce inter-subject variability
- Bias field correction to remove low-frequency intensity artifacts
- Brain extraction to remove non-brain tissue
- Spatial alignment to ensure anatomical correspondence

Preprocessing is applied consistently across all models in order to isolate the effect of network architecture on segmentation performance.

![MRI preprocessing and visualization example](/assets/brain_images/preprocessing.png)
*Figure 1: Example of MRI visualization and preprocessing workflow.*

---

## Problem definition

Stroke lesion segmentation is formulated as a binary voxel-wise classification task. Each voxel is assigned a label indicating lesion or non-lesion. This task is characterized by:

- Extreme class imbalance
- Large variation in lesion morphology
- Spatially disconnected lesion regions
- Bilateral lesions requiring global spatial reasoning

These properties make stroke lesion segmentation a challenging benchmark for evaluating long-range dependency modeling in neural networks.

---

## CNN-based segmentation architectures

### U-Net baseline

U-Net is used as the baseline architecture throughout the experimental evaluation. It consists of a symmetric encoder–decoder structure with skip connections linking corresponding resolution levels. The encoder extracts hierarchical features through convolution and pooling, while the decoder reconstructs spatial detail via upsampling.

Despite its success, U-Net relies exclusively on local convolution operations. Global spatial context is only captured implicitly through network depth and pooling operations.

![U-Net architecture](/assets/brain_images/u_net_arch.png)
*Figure 2: U-Net architecture used as baseline model.*

---

### ResUNet

ResUNet extends the U-Net architecture by introducing residual connections within convolutional blocks. These connections improve gradient flow and training stability but do not fundamentally change the locality of convolutional operations.

ResUNet is included as a stronger CNN baseline for comparison.

---

### X-Net architecture

X-Net is designed with two objectives:
1. Reduce the number of trainable parameters
2. Improve modeling of long-range spatial dependencies

To reduce parameter count, X-Net employs depthwise separable convolutions. To address long-range dependencies, X-Net optionally integrates a Feature Similarity Module (FSM).

FSM computes similarity between spatial feature vectors, allowing the model to propagate information across distant regions of the feature map.

The thesis evaluates X-Net both with FSM enabled and disabled.

![X-Net block structure](/assets/brain_images/xnet_block.png)
*Figure #: Structure of the X‐net block used to reduce the number of trainable parameters and increase the strength of
feature extraction*

![X-Net full architecture](/assets/brain_images/xnet_pipeline.png)
*Figure 3: X‐Net model pipeline consisting on a downsampling section, Feature similarity module and upsampling section
to restore input resolution*

---

## D-UNet: incorporating volumetric information

### Motivation

MRI data is inherently three-dimensional. Processing volumes slice-by-slice with 2D CNNs ignores inter-slice spatial relationships. Fully 3D CNNs capture this context but are computationally expensive.

D-UNet introduces a compromise by incorporating limited 3D context within a predominantly 2D architecture.

---

### Architecture details

D-UNet integrates a dimension fusion network in the downsampling path. This network combines:
- 2D convolutions for in-plane feature extraction
- 3D convolutions for inter-slice context

Feature fusion mechanisms reduce parameter count and batch normalization is used to improve convergence.

![D-UNet architecture](/assets/brain_images/dunet_arch.png)
*Figure #: Architecture of the model based on the D‐Unet. Feature size corresponds to the size of the feature map received
as input, the third dimension consists in the number of channels of the input which increase respectively in the
downsampling portion. Upsampling blocks are additionally connected to the downsampling blocks, in this case up sampling
block 1 is connected to the Convolution block 3, and the up sampling block 2 connected to convolution block 2*

---

### Enhanced mixing loss

To address severe class imbalance, D-UNet uses an enhanced mixing loss combining Dice loss and focal loss. Dice loss encourages overlap between predicted and ground truth lesion regions, while focal loss emphasizes hard-to-classify voxels.

---

## Transformer-based segmentation models

### Motivation

Transformers model long-range dependencies using self-attention, allowing each element to attend to all others. This property is particularly relevant for stroke lesion segmentation, where lesions may be spatially distant or bilateral.

In computer vision, images are converted into sequences by dividing them into patches, enabling the application of Transformer encoders.

---

### TransUNet architecture

TransUNet integrates a Vision Transformer within a U-Net-like architecture. The pipeline consists of:
- CNN feature extraction
- Patch embedding of feature maps
- Transformer encoder for global context modeling
- Decoder with skip connections for spatial reconstruction

Patch size directly affects Transformer sequence length and computational cost.


---

### SwinUNet and UCTransNet

SwinUNet introduces hierarchical Transformers with shifted window attention. UCTransNet introduces channel-wise Transformer attention and redesigned skip connections. Both models are evaluated using pretrained weights trained on earlier ATLAS releases.

---

## Dataset: ATLAS Release 2.0

The ATLAS dataset contains 955 T1-weighted MRI scans with manually segmented stroke lesions. The dataset is split into:
- 655 training scans
- 300 test scans

Lesions are manually traced using MRIcron. Metadata describing lesion number and location enables lesion-type-specific evaluation.

![ATLAS dataset examples](/assets/brain_images/brain_slices.png)
*Figure #: Overview of different slices forming the MRI volume*


![Brain anotaiton examples](/assets/brain_images/lesion_seg.png)
*Figure #: Visualization of MRI scans (Left:MRI scan, Right: manual lesion segmentation)*

---

## Experimental setup

### CNN-based models
- Batch size: 4
- Epochs: up to 20
- Loss: cross-entropy + Dice loss

### D-UNet
- Batch size: 36
- Epochs: up to 50
- Loss: enhanced mixing loss

### Transformer-based models
- Batch size: 8
- Epochs: up to 50
- Optimizer: SGD
- Learning rate: 1 × 10⁻³
- Data augmentation: rotation and flipping
- Input size: 192 × 192 slices stacked with adjacent slices

---

## Metrics



## Quantitative results: CNN-based models

To evaluate the performance of the Xnet the following metrics were selected: 
- Dice score,
- Intersection over union (IoU)
- Precision
- Recall. 

In order to increase the learning process and achieve better metrics the model includes a reduced learning strategy where the value of the learning rate is reduced by a constant factor when the performance metric plateaus. The loss function was selected as the sum of the crossentropy loss and the dice loss,
in order to better capture the spatial dependencies in the images. Batch size was set to 4, and a maximum number
of epochs set to 20 due to computational resources limitations.


### Results with Feature Similarity Module (FSM)

| Model    | Dice | IoU | Precision | Recall |
|----------|------|-----|-----------|--------|
| ResUNet  | 0.4109 | 0.3238 | 0.4489 | 0.4080 |
| U-Net    | 0.3921 | 0.3015 | 0.4276 | 0.3856 |
| X-Net    | 0.5250 | 0.4850 | 0.4770 | 0.4938 |



---

### Results without Feature Similarity Module

| Model    | Dice | IoU | Precision | Recall |
|----------|------|-----|-----------|--------|
| ResUNet  | 0.3910 | 0.3046 | 0.4309 | 0.3925 |
| U-Net    | 0.3706 | 0.2877 | 0.4081 | 0.3678 |
| X-Net    | 0.5216 | 0.4911 | 0.4673 | 0.4921 |



Considering the results reported  in Tables () we can infer the increased performance obtained by implementing the FSM module when being included in both ResUNet and base U-Net models. As for the X-Net the selected metrics did not improve which suggests that the architecture of the porposed model already caputres long range dependencies without the actual need of the FSM module. 


![Lesion segmentation results with FSM](/assets/brain_images/x_net_seg.png)
*Figure 7: Lesion segmentaiton results from ATLAS dataset. Col 1: MRI image, Col 2: Ground truth, Col 3: Res-UNet, Col 4: U-Net, Col 5: X-Net*
---


## D - UNet experimental results


The model was initially compared to the baseline U-Net model. Two versions of the U-Net were trianed in order to verify the porposed method performance:

1. Initially the original version using the architecture describe in [ref 20] 

2. A modified verison using less convolutional kernels and adding batch normalization after each convolution operation.

The batch of the three networks was set to 32. 


| Model    | Metric 1           | Metric 2            | Metric 3            | Parameters |
|----------|--------------------|---------------------|---------------------|------------|
| 2D U-Net | 0.4721 ± 0.1956    | 0.4871 ± 0.7214     | 0.4621 ± 0.6489     | 29,030,225 |
| D-UNet improved  | 0.4802 ± 0.7652    | 0.4961 ± 0.9828    | 0.4935 ± 0.2789    | 7,558,694  |
| D-UNet   | 0.5074 ± 0.2896    | 0.5983 ± 0.1864    | 0.53771 ± 0.8214    | 7,256,232  |

Table ## shows that U-Net and the improved U-Net achieve similar performance across all metrics, but the improved version uses about four times fewer trainable parameters, indicating that many convolutional kernels in the original U-Net are unnecessary for this dataset. This reduction also lowers computational requirements. Batch normalization was used in all models to improve training stability. Although D-UNet requires longer training time than the baseline U-Net (39 hours vs. 20.5 hours), the performance improvement shows that incorporating 3D structural information leads to better segmentation by capturing volumetric context.

The model should have been compared to a full 3D U-Net however convergence was not achieved this can be
explained by two main elements:

1. The complexity of 3D models require an increased amount of time for training, making difficult to tune
the hyper-parameters while obtaining a faster convergence
2. Overfitting is a common problem in large networks especially when available fewer training samples.
3. 3D structures require large amounts of computing resources which limit the width and depth of the 3D
structure leading to a decrease in performance.


## Comparison with other state of the art methods

The proposed model was compared with U-Net and SegNet, and qualitative results show that small lesions are more difficult to segment and often appear fuzzy. While this affects the baseline models, D-UNet correctly detects these challenging lesion areas, demonstrating improved robustness on difficult samples. All models perform well on easier cases, but D-UNet shows stronger feature representation for lesions with blurry boundaries, likely due to the integration of 3D features with spatial information, which enables more effective modeling of edge-blurred lesions.ß


![Comparison state of the art](/assets/brain_images/other_methods.png)
*Figure : Comparison of lesion segmentation performance of D‐UNet, SegNet and U‐net.*



| Model   | DSC           | Precision          | Recall           |
|---------|--------------------|--------------------|--------------------|
| SegNet  | 0.3187 ± 0.1851    | 0.3445 ± 0.7674    | 0.3245 ± 0.0984    |
| U-Net  | 0.4356 ± 0.2143    | 0.4371 ± 0.2005    | 0.4453 ± 0.0987    |
| D-UNet  | 0.5226 ± 0.2902    | 0.5226 ± 0.2902    | 0.5226 ± 0.2902    |
*Table : Evaluation of D‐UNet performance against state of the art methods, SegNet and UNet*



The proposed method ranked first with a score of 0.522 demonstrating its superiority in terms of lesion segmentation. All the models were trained with a maximum of 50 epochs and batch size of 36. It is important to remark that the proposed method D-UNet was trained over each experiment using the enhanced mixing loss function (Add description)



---

## Experimental results using Transformer-based results


The TransUNet was desinged using PyTorch as deep learning framework, to incrase the model robustness and avoid overfitting  we implemented data augmentation based on rotation and image flipping. 

The model was trained using batch size of 8, max number of epochs fixed at 50 and stochastic gradient descent (SGD) optimizer with initial learning rate (add value). For training and validation each slice was cropped to 192 X 192 dimension and stacked alongside four adjacent slices creating a volume of 192X192X4. 



The TransUNet model was compared against two state of the art models implementing transformer based attention mechanism within U-Net architectures. SwinUNet and UCTransNet. Both models were loaded as pretrained models and tested on the ATLAS dataset version 2.0, since they were previously trained on an older and reduced version of the same dataset 


| Model   | DSC           | F1-score          | IoU           |
|---------|--------------------|--------------------|--------------------|
| TransUNet  | 0.751    | 0.725    | 0.735    |
| UCTransNet  | 0.739    | 0.727    | 0.733    |
| SwinUNet  | 0.734    | 0.722    | 0.709    |
*Table : Evaluation of D‐UNet performance against state of the art methods, SegNet and UNet*






---

## Lesion-type evaluation




Lesion-type-specific Dice scores are reported for:
- Multiple unilateral lesions
- Single subcortical lesions
- Bilateral cortical lesions

| Lesion type | X-Net | D-UNet | TransUNet |
|-------------|-------|--------|-----------|
| Cortical / left / unilateral | 0.654 | 0.617 | 0.786 |
| Cortical / left | 0.621 | 0.648 | 0.792 |
| Sub-cortical / bilateral | 0.236 | - | 0.535 |

![Lesion type comparison](/assets/brain_images/lesion_types.png)
*Figure ##: Segmentation results for different lesion types. Row 1: left hemisphere cortical multiple lesion, Row 2:
subcortical left single lesion, Row 3: bilateral cortical lesion.*



According to the results, standard CNN based methods such as X-Net and D-UNet have limited capacity when segmenting multiple lesions in one sample, especially in the case for multiple lesions in different hemispheres. In this cases the models are not able to capture long rande dependencies between the different lesions. Transformer model TransUNet demonstrates a higher performance when segmenting multiple lesions at unilateral and bilateral level obtaining the highest DSC scores of 0.786 and 0.535 respectively; this can be attributed to the use of transformers in the encoding section using the attention mechanism to capture dependencies between the input sequences. As for the cortical lesion reported in Row:2 Figure ##, the three proposed models suggest an acceptable performance with a noticeable decrease in the mask accuracy of the X-Net model.
---

## Patch size ablation in TransUNet

In order to validate the models performance under different configuration an ablation study was carried out eval-
uating: 
1) Patch size in the encoding section and 
2) Number of skip-connections.


| Patch size | Sequence length | Dice |
|------------|------------------|------|
| 32 | 49 | 0.751 |
| 16 | 196 | 0.776 |

![Patch size ablation](assets/images/patch_size_ablation.png)
*Figure 12: Effect of patch size on TransUNet performance.*

---

## Discussion

The results show that Transformer-based models outperform CNN-based architectures in cases requiring global spatial reasoning, particularly for bilateral and multi-lesion configurations. CNN-based models remain computationally efficient and perform well in simpler cases, while D-UNet provides a compromise by incorporating limited volumetric context.

---

## Conclusion

This project provides a detailed evaluation of CNN-based and Transformer-based models for automatic brain stroke lesion segmentation on the ATLAS dataset. The results highlight the importance of architectural choices and long-range dependency modeling in medical image segmentation.

---

*All content in this post is derived exclusively from my MSc thesis submitted to the University of Padova.*
