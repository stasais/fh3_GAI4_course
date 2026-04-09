# Assignment 1 Report: UC Merced Land Use Classification
**Course:** Generative AI (GAI4UE) - SS 2026  
**Student:** Stanislav Buinitskii

## Dataset Preparation
- **Total images:** 2,100 (21 classes × 100 images each)
- **Split:** 70% train (1,470), 15% val (315), 15% test (315)
- **Method:** Stratified sampling

## Training Results

### Model 1: From Scratch (Random Initialization)
| Metric | Epoch 1 | Epoch 10 | Epoch 20 |
|--------|---------|----------|----------|
| Train Loss | 3.25 | 2.66 | 2.28 |
| Val Loss | 3.14 | 3.22 | 2.15 |
| Train Acc | 6.5% | 20.2% | 28.5% |
| Val Acc | 5.4% | 27.0% | 38.4% |

**Test Accuracy:** 31.11%  
**Test Balanced Accuracy:** 31.11%

### Model 2: Pretrained (ImageNet Weights)
| Metric | Epoch 1 | Epoch 5 | Epoch 20 |
|--------|---------|---------|----------|
| Train Loss | 3.03 | 0.41 | 0.11 |
| Val Loss | 1.57 | 0.20 | 0.05 |
| Train Acc | 23.3% | 88.2% | 96.7% |
| Val Acc | 54.3% | 93.0% | 98.4% |

**Test Accuracy:** 96.83%  
**Test Balanced Accuracy:** 96.83%

## Comparison

### Performance Gap
- Pretrained outperforms scratch by **65.72 percentage points** (96.83% vs 31.11%)
- Pretrained reaches 93% val accuracy at epoch 5
- Scratch model never exceeds 40% val accuracy

### Loss Convergence
- **Scratch:** Final loss 2.28 (train) / 2.15 (val) - still high
- **Pretrained:** Final loss 0.11 (train) / 0.05 (val) - converged well

### Class-Specific Performance (Pretrained Model)
Based on confusion matrix and classification report:
- **Perfect classes:** agricultural, airplane, beach, golfcourse, tenniscourt (100% F1-score)
- **Challenging pairs:** Similar visual features cause confusion between residential density levels and vegetation types

## Conclusions
Transfer learning with ImageNet pretrained weights is essential for this task. The scratch model failed to learn meaningful features from 1,470 training images, while the pretrained model achieved near-perfect accuracy by epoch 5. The pretrained weights provided learned edge, texture, and shape detectors that transferred well to aerial imagery classification.
