---
layout: distill
title: Deep Learning Reaches and Surpasses Boosted Trees for Large Scale Tabular Modeling
description: A real world account of migrating a high stakes production fraud detection system from gradient boosting to pure deep learning and lessons learned along the way
date: 2026-04-27
future: true
htmlwidgets: true

authors:
  - name: Anonymous

bibliography: 2026-04-27-adyen-tabular-dl.bib

toc:
  - name: Introduction
  - name: The Folklore Around Tabular Data
  - name: Why We Challenged The Status Quo
  - name: Architecture and Approach
  - name: Production Deployment Timeline
  - name: Key Findings and Lessons Learned
  - name: Broader Implications
  - name: Conclusion
---

# Introduction

For years, the prevailing belief in the machine learning community has been that gradient boosted decision trees are the dominant and default option for tabular data. Deep learning is considered impractical or inferior due to assumptions regarding training cost, variance, hardware dependencies, and explainability. This post challenges that widespread belief by presenting a real world case study where deep learning not only reached parity with boosting on large scale tabular data but ultimately outperformed it in production.

This work recounts our multi month migration from a long standing LightGBM model powering high-volume real-time financial fraud detection to a pure deep neural model now serving one hundred percent of live global traffic. The experience highlights both performance improvements and advantages beyond raw accuracy including flexibility, latency, modeling power, and future extensibility capabilities not available with tree based systems.

---

# The Folklore Around Tabular Data

Widely circulated arguments assert that deep learning is unsuitable or inefficient for tabular predictive tasks. Common objections include:

* Deep learning trains too slowly
* Training requires specialized GPU infrastructure
* Neural models lack interpretability
* Gradient boosted trees are sample efficient and therefore categorically stronger

Much of this folklore originates from small scale academic benchmarks and frequently conflates deep learning as an entire field with large language models. It overlooks practical benefits that arise only at large data scale such as out-of-core training, flexible representation learning, and architectural advantages for heterogeneous data.

---

# Why We Challenged The Status Quo

Our hypothesis was influenced by results across industry deployments where heterogeneous ensembling repeatedly outperforms individual model families. Prior large scale experience and published case studies indicated that combining boosting and deep learning often yields superior performance and sometimes enables full transition to deep learning.

Examples include deployments for ranking at major technology companies, fraud detection, and recommendations. These observations suggested that the apparent dominance of boosting is largely due to ensembling rather than a fundamental limitation of neural networks.

---

# Architecture and Approach

We followed a staged migration path:

1. Bring neural networks to parity with boosting offline
2. Introduce heterogeneous stacking to validate uplift in production
3. Iteratively hill climb the neural architecture until it surpasses boosting
4. Remove the boosting component entirely once performance and variance are stable

We experimented with a broad set of architectures including TabNet, FT-Transformer, TabTransformer, ExcelFormer and numerical embedding encoders. In large scale settings past one million samples, a simple residual MLP consistently performed best. This aligns with emerging results that well tuned simple networks can outperform sophisticated transformer architectures for tabular data at meaningful scale.

### Final neural model
- Residual MLP with dedicated numeric and categorical encoders
- Out-of-core training
- GPU accelerated batch training
- Captum explainability enabling low latency gradient attribution

### Early ensemble structure
LightGBM + Torch MLP → Ridge classifier stack


---

# Production Deployment Timeline

| Date | Event |
|------|-------|
| Nov 2024 | First neural ensemble experiments show modest uplift |
| Jan 2025 | Ensemble workflow operational end-to-end |
| Feb 2025 | Observed uplift vs boosting across PR-AUC and ROC-AUC |
| Mar 2025 | Ensemble live in shadow mode with acceptable latency |
| May 2025 | Ensemble begins influencing real traffic |
| Aug 2025 | Captum explainability unlocks pure DL deployment |
| Sep 2025 | Ensemble deployed at 50 percent of traffic |
| Nov 21 2025 | Pure deep learning model deployed to 100 percent live traffic |

### Initial uplift metrics vs LightGBM baseline
- +3.37% PR-AUC
- +1.35% ROC-AUC
- +0.175 weighted bounded ROC-AUC

---

# Key Findings and Lessons Learned

### 1. Scale changes outcomes
Architectures that win on 10k-row benchmarks can underperform on million-row real systems.

### 2. The simplest model won
Residual MLP > Transformer-based models > TabNet and others in our domain.

### 3. Ancillary benefits matter more than expected
| Capability | Benefit |
|-----------|---------|
| Out-of-core training | Constant memory growth |
| Raw feature processing | Representations from encoded email addresses |
| Multi-task potential | Multiple objectives in one model |
| Latency | Lower explainability latency vs SHAP |
| Online updates | Drift robustness |

### 4. Explainability is solvable
Captum provided reliable attribution within latency SLAs (20-45ms) replacing ensemble SHAP pipelines that took 90-120ms.

### 5. Variance reduction is necessary
Stabilizing training was the final barrier before removing LightGBM.

---

# Broader Implications

| Folklore claim | Observed reality |
|----------------|------------------|
| Trees always outperform deep learning on tabular data | Only true at small scale and without scaling benefits |
| Deep learning is slow | GPU accelerated training is faster |
| Deep learning cannot be explained | Attribution methods outperformed SHAP latency |
| DL cannot beat boosting | Outperformed when given scale and architecture care |

These results suggest that deeply entrenched assumptions about model family superiority do not hold in high-volume industrial environments.

---

# Conclusion

We present a real world demonstration that deep learning can reach and surpass boosted trees in accuracy, latency and practical capability for large scale tabular modeling. The transition was incremental, evidence based, and only possible due to careful operational validation in production. The final system is simpler, faster, more flexible and opens new directions including unified multi objective modeling and foundation model integration.

The broader takeaway is that model selection should be driven by scale, architectural flexibility and long term advantages rather than inherited folklore.

---

# References

```bibtex
% add references here
