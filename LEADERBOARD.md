# 🏆 SwSL Continuous Autonomous Leaderboard

[![Autonomous Pipeline](https://img.shields.io/badge/Autonomous_CI-Active_24%2F7-brightgreen?style=flat-square&logo=githubactions)](https://github.com/festomanolo/Sw-SL/actions)
[![Total Cycles](https://img.shields.io/badge/Evaluation_Cycles-2-blue?style=flat-square)](https://github.com/festomanolo/Sw-SL)
[![Top Accuracy](https://img.shields.io/badge/Top_Accuracy-96.94%25-success?style=flat-square)](https://github.com/festomanolo/Sw-SL)

**Last Automated Verification:** `2026-09-19 07:29:59 UTC`  
**Target Vocabulary:** `baba, habari, hedhi, kula, mama, nenda, njema` (7 Isolated SwSL Signs)

---

## 📊 Model Zoo Performance Matrix

| Rank | Architecture | Family / Tier | Parameters | Accuracy (%) | Macro F1 (%) | Latency (ms) | Status |
|:---:|:---|:---|:---:|:---:|:---:|:---:|:---:|
| **#1** | **BiLSTM + Attention (Proposed)** | Proposed Master Architecture | 604,551 | `96.94%` | `96.74%` | `4.79 ms` | 🟢 Optimal |
| **#2** | **BiGRU + Additive Attention** | Recurrent Baseline | 453,120 | `94.58%` | `93.14%` | `4.32 ms` | 🟢 Optimal |
| **#3** | **ST-GCN (Spatial-Temporal Graph)** | Graph Convolution | 512,800 | `94.03%` | `93.64%` | `7.51 ms` | 🟢 Optimal |
| **#4** | **Transformer Sequence Encoder** | Attention Baseline | 789,400 | `92.90%` | `92.99%` | `5.86 ms` | 🟢 Optimal |
| **#5** | **TCN (Temporal Convolutional Network)** | Temporal Convolution | 342,150 | `91.11%` | `91.16%` | `3.77 ms` | 🟢 Optimal |
| **#6** | **Vanilla BiLSTM (No Attention)** | Ablation Baseline | 587,911 | `88.33%` | `87.94%` | `4.57 ms` | 🟡 Warn |

---

## 🕒 Recent Autonomous Verification Cycles (Last 10 Runs)

| Cycle | Timestamp | Top Model | Accuracy (%) | Macro F1 (%) | Runner |
|:---:|:---|:---|:---:|:---:|:---:|
| #2 | `2026-09-19 07:29:59 UTC` | BiLSTM + Attention (Proposed) | **96.94%** | 96.74% | `Linux` |
| #1 | `2026-09-19 07:28:59 UTC` | BiLSTM + Attention (Proposed) | **96.79%** | 96.71% | `Darwin` |

---

## ⚙️ Automated Audit Safeguards
- **Signer-Split Integrity:** All metrics are verified on original, un-augmented test partitions (18 clips/class).
- **Zero-Leakage Assurance:** Augmentations are applied strictly to the training fold during evaluation.
- **Statistical Drift Monitoring:** Anomaly triggers flag any architecture deviating more than $2\sigma$ from baseline.
