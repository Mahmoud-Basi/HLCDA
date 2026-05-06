# An APT Detection Scheme Based on a Hierarchical Co-Attention Transformer

Please cite this article as:

M. Basi, T. Shang, and C. Yuhang, “An APT Detection Scheme Based on a Hierarchical Co-Attention Transformer,” 
Journal of Computer and System Sciences, 2026. 
https://doi.org/10.1016/j.jcss.2026.1038137

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
- Python 3.9+
- PyTorch (GPU recommended, but CPU works)
- NumPy, scikit-learn, matplotlib, seaborn, joblib

----

## Benchmarks Supported by the Repository
We evaluate HLCDA on three widely used, real-world intrusion detection benchmarks:

### 1) DAPT-2020
- Task: Binary classification (Normal vs. APT/Attack) using sequence-based flow/session features.
- Preprocessing (high-level):
  1. Flow/session construction: raw records are grouped into sessions and converted into fixed-length sequences with `T=8` time steps (padding/truncation).
  2. Feature selection & ordering: a consistent feature list is built and stored in the NPZ key `features`.
  3. Label mapping: benign traffic is mapped to `0`, attacks/APT to `1`.
  4. Split: train/validation/test splits are generated and stored in the NPZ.

### 2) UNSW-NB15
- Task: Binary classification (Normal vs. Attack).
- Preprocessing (high-level):
  1. Cleaning: remove invalid rows, handle missing/inf values.
  2. Encoding: categorical fields (if any) are encoded consistently (e.g., one-hot / label encoding).
  3. Normalization: continuous features are scaled using training statistics only.
  4. Sequence building: samples are grouped into session-like sequences and converted to fixed-length `T=8` (pad/truncate).
  5. Split: train/validation/test splits are created.

### 3) CIC-IDS2018
- Task: Binary classification (Benign vs. Attack).
- Preprocessing (high-level):
  1. Cleaning: remove NaN/inf and inconsistent records.
  2. Label mapping: Benign → `0`, Attack → `1` (attacks may include multiple categories merged into a single attack class).
  3. Feature normalization: fit scaler on training set only; apply to val/test.
  4. Sequence building: create fixed-length sequences with `T=8` using session/flow grouping, then pad/truncate.
  5. Split: train/validation/test splits are produced.

### NPZ export format (used by the notebook)
All three benchmarks are converted to a unified NPZ format expected by the notebook:

- `X_train`: shape `[N_train, T, F]`
- `y_train`: shape `[N_train]`
- `len_train`: shape `[N_train]` (valid sequence lengths before padding)
- `X_val`, `y_val`, `len_val`
- `X_test`, `y_test`, `len_test`
- `features`: list of `F` feature names (stored in a fixed order)
- `T`: sequence length (e.g., `8`)

> Note: Datasets and generated artifacts are not included in this repository.  
Users should download each dataset from its official source and run the preprocessing pipeline to generate the NPZ file (e.g., `sessions_T8.npz`), then update `NPZ_PATH` in the notebook accordingly.
