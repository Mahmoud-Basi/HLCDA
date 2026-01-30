# HLCDA: Hierarchical Local–Global Co-Attention for APT Detection

This repository provides the implementation of **HLCDA (Hierarchical Local–Global Co-Attention)** for robust and efficient **Advanced Persistent Threat (APT)** detection from sequence-based network traffic features.

**Model summary (paper description).**  
HLCDA comprises three encoders:  
1) **TSG-xLSTM** for multi-scale temporal dependencies,  
2) **MS-DS-CNN** for short-range spatial features, and  
3) **RoPET (RoPE-enhanced Transformer)** for global contextual modeling.  
A key contribution is a **hierarchical co-attention** mechanism enabling bidirectional interaction between the xLSTM and CNN streams **prior to fusion**, followed by **DMFF (Dynamic Multi-Head Feature Fusion)**.

The training/evaluation pipeline also includes:
- Baselines: **CNN+BiLSTM**, **CNN+xLSTM**, **CNN+Co-Attention**, and **HLCDA**
- Metrics: Accuracy / Precision / Recall / F1, plus **AUROC/AUPRC**
- **Latency profiling** (batch mean, p50/p95/p99, per-sample)
- **LR logging** (LR_used → LR_next with cosine scheduler)
- **Evasion / robustness evaluation (feature-space)** with multiple perturbation recipes

---

## Repository Contents
- `HLCDA basi.ipynb` — main notebook containing model definitions, training loop, evaluation, plots, and evasion robustness suite  
- `.gitignore` — excludes datasets/processed artifacts/checkpoints from being committed  
- `.gitattributes` — avoids line-ending warnings for notebooks

> **Note:** Processed data (e.g., `prepped/*.npz`, `prepped/*.csv`, `*.joblib`) is intentionally excluded from this repository.

---

## Requirements
- Python **3.9+**
- PyTorch (GPU recommended, but CPU works)
- NumPy, scikit-learn, matplotlib, seaborn, joblib

Install dependencies:
```bash
pip install -r requirements.txt
