# CAAD: Causality-Aware Multivariate Time Series Anomaly Detection via Multi-Scale Alignment and Structural Causal Consistency

[Xin Wang](https://github.com/kkkira-1212), [Yunshi Wen](https://yunshiwen.github.io/), [Yanan He](https://echoo113.github.io/), [Haotian Xu](https://orcid.org/0009-0009-3024-4181), [Youlan Zhao](https://orcid.org/0009-0007-0818-9556), [Michel Ferreira Cardia Haddad](https://www.qmul.ac.uk/business-school/staff/academic/profiles/haddadm.html), [Tengfei Ma](https://sbu-ma.github.io/)

**KDD 2026 · Research Track**

[arXiv preprint](https://arxiv.org/abs/2607.08555) · [ACM proceedings](https://doi.org/10.1145/3770855.3817978) · [Quick start](#quick-start) · [BibTeX](#citation)

## Overview

This repository contains the official implementation of CAAD. CAAD detects multivariate time-series anomalies by monitoring two signals: temporal prediction residuals and changes in the relationships between variables. It aligns dynamics at fine and coarse timescales and uses an asymmetric gate to combine temporal and structural anomaly scores.

## Framework

![CAAD framework showing multi-scale alignment and temporal and structural anomaly scoring](assets/caad_framework.png)

## Project Structure

```
├── model/              # Core model and causal scoring logic
├── utils/              # Shared utilities (I/O, causal eval, sequence tools)
├── experiments/        # Dataset-specific utilities and side branches
├── data/processing/    # Dataset processing scripts
├── scripts/            # Main training and evaluation entrypoints
└── data/               # Raw and processed datasets (gitignored)
```

## Quick Start

1. Install dependencies:
```bash
pip install -r requirements.txt
```

2. Prepare data:
   - Place raw data files under `data/`
   - Run processing scripts in `data/processing/` to generate `.pt` bundles

3. Run experiments:

### Stage-1 training:
```bash
python scripts/train_stage1.py \
  --data_dir data/SWaT/processed \
  --coarse_file swat_coarse.pt \
  --fine_file swat_fine.pt \
  --model_save_path models/swat_stage1.pt
```

### Causal stability evaluation:
```bash
python scripts/verify_causal_stability.py \
  --model_path models/swat_stage1.pt \
  --data_dir data/SWaT/processed \
  --data_file data/SWaT/processed/swat_fine.pt \
  --scale fine \
  --global_threshold_percentile 10
```

### Fusion sweep:
```bash
python scripts/sweep_causal_fusion.py \
  --model_path models/swat_stage1.pt \
  --data_dir data/SWaT/processed \
  --data_file data/SWaT/processed/swat_fine.pt \
  --scale fine \
  --global_threshold_percentile 10 \
  --fusion_normalize --struct_metric relative \
  --fusion_mode relu_gate \
  --sweep_relu \
  --relu_tau_percentile 90 \
  --lambda_start 0 --lambda_end 1 --lambda_step 0.1 \
  --save_dir analysis
```

## Data & Models

- **Data files** are not included due to size constraints. Place raw data under `data/` and generate `.pt` bundles with `data/processing/`.
- **Trained models** are not included. Train models using the provided scripts.
- **Processed data** (`.pt`) are excluded from git and should be generated locally.

## Citation

If you use this repository in your research, please cite the published KDD paper:

```bibtex
@inproceedings{wang2026caad,
  author    = {Wang, Xin and Wen, Yunshi and He, Yanan and Xu, Haotian and
               Zhao, Youlan and Haddad, Michel Ferreira Cardia and Ma, Tengfei},
  title     = {{CAAD: Causality-Aware Multivariate Time Series Anomaly Detection via Multi-Scale Alignment and Structural Causal Consistency}},
  booktitle = {Proceedings of the 32nd ACM SIGKDD Conference on Knowledge Discovery and Data Mining V.2},
  year      = {2026},
  pages     = {5091--5101},
  publisher = {Association for Computing Machinery},
  doi       = {10.1145/3770855.3817978},
  url       = {https://doi.org/10.1145/3770855.3817978}
}
```
