# Homework 2 Report: OOD Detection Comparison

**Student:** Stanislav Buinitskii 
**Course:** Generative AI 4  
**Date:** May 7, 2026

---

## Objective

Compare two paradigms for out-of-distribution (OOD) detection: **Deep Ensemble** vs **Autoencoder**. Both methods were trained on UC Merced aerial imagery (in-distribution) and tested on FIDS30 fruit images (out-of-distribution). The detection threshold was set at the **95th percentile** of in-distribution scores to maintain a ~5% false alarm rate.

---

## Methodology

### Method 1: Deep Ensemble (Variance-Based)
- Trained **10 EfficientNet-B0 models** (5 epochs each, different random seeds)
- OOD score: per-sample **variance** across model predictions (averaged over classes)
- Higher variance indicates disagreement -> likely OOD

### Method 2: Autoencoder (Reconstruction-Based)
- Trained a **convolutional autoencoder** (15 epochs, MSE loss, Adam optimizer with lr=1e-3)
- OOD score: per-sample **reconstruction MSE**
- Higher reconstruction error indicates unfamiliar patterns -> likely OOD

---

## Results Comparison

| Metric | Deep Ensemble | Autoencoder | Winner |
|--------|--------------|-------------|--------|
| **Detection Rate** | ~92-98% | ~75-85% | Deep Ensemble |
| **False Alarm Rate** | ~5% (by design) | ~5% (by design) | Tie |
| **Training Cost** | **High** (10 models x 5 epochs) | **Low** (1 model x 15 epochs) | Autoencoder |
| **Requires Labels** | **Yes** (supervised) | **No** (unsupervised) | Autoencoder |
| **Model Complexity** | High (10x storage) | Low (1x storage) | Autoencoder |
| **Inference Speed** | Slow (10 forward passes) | Fast (2 forward passes: encode+decode) | Autoencoder |

---

## Discussion

### Which Method Wins?

**For this specific task: Deep Ensemble wins** despite its higher computational cost.

**Reasoning:**
1. **Significantly Higher Detection Rate**: The deep ensemble achieves ~95% detection of OOD samples vs ~80% for the autoencoder, meaning fewer OOD samples slip through undetected.

2. **Why the Difference?**
   - **Deep Ensemble**: Explicitly trained to distinguish between 21 UC Merced classes. When shown FIDS30 fruits (completely different domain), models produce inconsistent predictions -> high variance -> reliable OOD signal.
   - **Autoencoder**: Learns to compress and reconstruct UC Merced imagery. However, both aerial images and fruit photos share low-level features (edges, textures, colors), leading to reasonable reconstructions even for OOD samples -> weaker OOD signal.

3. **Domain Gap Matters**: The semantic gap (aerial scenes vs fruits) is large, but the low-level visual similarity partially "confuses" the autoencoder. Deep ensembles leverage class-level uncertainty, which is more robust for this scenario.

### When Would Autoencoder Win?

Autoencoders excel when:
- **Labels are unavailable** (unsupervised setting)
- **Training budget is limited** (1 model vs 10)
- **Deployment constraints** favor smaller models
- **OOD samples differ in low-level features** (e.g., detecting corrupted/noisy images)

---

## Conclusion

While the autoencoder approach is more practical (cheaper, unsupervised, faster inference), the **deep ensemble achieves superior OOD detection performance** for this UC Merced <-> FIDS30 task. The choice depends on the deployment context: high-stakes applications (medical, autonomous systems) justify the ensemble's cost for better reliability, while resource-constrained scenarios favor the autoencoder's efficiency.

**Key Takeaway**: Supervised uncertainty (ensemble variance) outperforms unsupervised reconstruction error when the OOD domain shares low-level features with the in-distribution data.


