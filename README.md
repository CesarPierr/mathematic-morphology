# MI206 TP2 Report
**Authors:** Pierre César, Liam Kelley  
**Date:** May 22, 2022

## Table of Contents
1. [Introduction](#introduction)
2. [Responses to Questions](#responses-to-questions)
   1. [Evaluation Function in Python Script](#evaluation-function)
   2. [Why Use Two Metrics (Precision and Recall)?](#two-metrics)
   3. [Role of Skeletonization in Evaluation](#skeletonization-role)
3. [Method](#method)
   1. [Use of Connected Operators](#connected-operators)
   2. [Multi-scale Algorithm](#multi-scale-algorithm)
   3. [Treatment Details and Analysis](#treatment-details)
      1. [Treatment 0 (No Direct Segmentation)](#treatment-0)
      2. [Treatment 1](#treatment-1)
      3. [Treatment 2](#treatment-2)
      4. [Treatment 3](#treatment-3)
4. [Results](#results)
5. [Analysis of Results and Algorithm Limitations](#analysis-and-limitations)
   1. [Analysis of Results](#results-analysis)
   2. [Limitations](#limitations)
6. [Conclusion](#conclusion)

## Introduction

The objective of this project was to propose an automatic and efficient method for segmenting the vascular network in SLO retinal images. The data was extracted from IOSTAR database from IDIAP for evaluating retinal vascular network segmentation algorithms. Our segmentation needed to match expert manual annotations as closely as possible. We were able to quantify our results using an evaluation function provided with the subject.

In this report, we will first present our responses to the proposed questions and our reflections on the research directions. We will then explain our proposed method and its results. Finally, we will analyze the performance and limitations of our proposition.

## Responses to Questions

### Evaluation Function in Python Script
The evaluate function takes two inputs: 1. the proposed segmentation and 2. the "ground truth" segmentation. The function first creates "skeletonized" versions of both inputs using the thin function. It's noted that 15 iterations of thinning would be sufficient since the maximum vessel thickness is assumed to be 15 pixels, and the thin function reduces each edge by 1 pixel per iteration.

The function then calculates:
- TP (True Positives): Total positive pixels in img_out_skel matching ground truth
- FP (False Positives): Positive pixels in img_out_skel outside ground truth
- FN (False Negatives): Ground truth points missed by segmentation

Finally, it calculates precision and recall metrics.

### Why Use Two Metrics (Precision and Recall)?
We use precision to measure the proportion of errors in our segmentation (false positives). We use recall to measure the proportion of missing vessels in our segmentation (false negatives). These are two different types of errors.

### Role of Skeletonization in Evaluation
Skeletonization allows the evaluation function to have a permissive margin when comparing segmentation to ground truth, making the comparison more meaningful.

## Method

### Use of Connected Operators
Since the vascular network is a connected network, using connected operators is relevant as all the information we want to preserve is directly linked.

### Multi-scale Algorithm
The multi-scale concept is essential for this type of algorithm, as vessels have highly variable thicknesses. By combining results found by small and large structuring elements, we can properly identify both small and large vessels. For example, we can identify large vessels independently of small ones using a large structuring element. The granularity can be used similarly to alternating filters.

### Treatment Details and Analysis

#### Treatment 0 (No Direct Segmentation)
Initial processing steps include:
- Image normalization and contrast enhancement
- Top-hat transform for vessel isolation and small forms
- Gradient detection for contours
- Image complement processing
- Alternating sequential filter application
- Connected component analysis

#### Treatment 1
The main challenge remains removing background noise after top-hat. Processing includes:
- Base treatment with simpler top-hat and reconstruction
- Contrast enhancement and vessel separation
- Gradient application with small structuring element
- Image combination techniques

#### Treatment 2
Process includes:
- Top-hat multiplication with image complement
- Reconstruction opening for noise reduction
- Interior gradient processing
- Thresholding and dilation operations

#### Treatment 3
Final treatment including:
- Black top-hat with mixed leveling
- Contrast enhancement steps
- Alternating filters for homogenization
- Hysteresis thresholding
- Combined weighted average processing

## Results

Results are presented as Accuracy(%)/Recall(%) for each image and treatment:

| Image  | Treatment 1 | Treatment 2 | Treatment 3 |
|--------|-------------|-------------|-------------|
| star01 | 62/69 | 65/70 | 69/70 |
| star02 | 67/74 | 69/76 | 72/76 |
| star03 | 76/60 | 79/57 | 79/59 |
| ... | ... | ... | ... |
| Average | 68.8/64.7 | 70.8/62.2 | 73.6/61.5 |

## Analysis and Limitations

### Results Analysis
The treatments are very similar due to image combination, though Treatment 3 shows improved precision despite slightly lower recall compared to Treatment 1. Treatment 2 falls in between. Overall, connected operators and alternating filters proved very useful.

### Limitations
We prioritized accuracy (reducing false positives) over recall (reducing false negatives) in our results. Combining multiple treatments leads to better accuracy but sometimes at the expense of recall, as final thresholding cannot always distinguish small vessels from noise.

## Conclusion

This project helped us become familiar with image processing methods and allowed us to work with concrete results that we sought to optimize. It would be interesting to look at the performance of semantic segmentation networks on these images (UNet for example) or a combination of morphological and machine learning techniques.
