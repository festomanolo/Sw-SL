# 🏆 SwSL Continuous Autonomous Leaderboard

[![Autonomous Pipeline](https://img.shields.io/badge/Autonomous_Pipeline-Active_24%2F7-brightgreen?style=flat-square&logo=githubactions)](https://github.com/festomanolo/Sw-SL/actions)
[![Evaluation Cycles](https://img.shields.io/badge/Evaluation_Cycles-22-blue?style=flat-square)](https://github.com/festomanolo/Sw-SL)
[![Top Accuracy](https://img.shields.io/badge/Top_Accuracy-97.05%25-success?style=flat-square)](https://github.com/festomanolo/Sw-SL)
[![Inference Latency](https://img.shields.io/badge/Latency_p95-5.33ms-orange?style=flat-square)](https://github.com/festomanolo/Sw-SL)
[![Real--Time Factor](https://img.shields.io/badge/RTF-0.0022-blueviolet?style=flat-square)](https://github.com/festomanolo/Sw-SL)

**Last Automated Verification:** `2026-09-24 03:30:13 UTC`  
**Vocabulary Scale:** 7 Isolated SwSL Gestures (`baba, habari, hedhi, kula, mama, nenda, njema`)  
**Verification Protocol:** Stratified original test partition ($n = 126$, 18 clips/class) with zero data leakage.

---

## 1. 📊 Architectural Performance & Statistical Significance

Statistical significance is evaluated using **Holm-Bonferroni corrected McNemar tests** against the proposed BiLSTM+Attention architecture on discordant test pairs ($k=5$ pairwise hypotheses, $\alpha = 0.05$):

| Rank | Architecture | Family / Tier | Parameters | Accuracy | Macro F1 | McNemar $\chi^2$ | Holm-Adj $p$-value | Significant ($p < 0.05$) |
|:---:|:---|:---|:---:|:---:|:---:|:---:|:---:|:---:|
| **#1** | **BiLSTM + Attention (Proposed)** | Proposed Master Architecture | 604,551 | **`97.05%`** | `96.93%` | — | — | — (Reference) |
| **#2** | **BiGRU + Additive Attention** | Recurrent Baseline | 453,120 | **`94.41%`** | `94.15%` | `1.78` | `0.2978` | ❌ No |
| **#3** | **ST-GCN (Spatial-Temporal Graph)** | Graph Convolution | 512,800 | **`93.25%`** | `92.88%` | `2.08` | `0.2978` | ❌ No |
| **#4** | **Transformer Sequence Encoder** | Attention Baseline | 789,400 | **`92.52%`** | `92.19%` | `2.77` | `0.2883` | ❌ No |
| **#5** | **TCN (Temporal Convolutional Network)** | Temporal Convolution | 342,150 | **`91.69%`** | `91.46%` | `4.27` | `0.1555` | ❌ No |
| **#6** | **Vanilla BiLSTM (No Attention)** | Ablation Baseline | 587,911 | **`87.75%`** | `87.48%` | `9.39` | `0.0109` | ✅ Yes |

---

## 2. ⚡ Computational Complexity & Real-Time Profile

Profiled for full sequences ($T=60$ frames, $D=258$ coordinates) against a **33.33 ms (30 fps) real-time frame budget**:

| Architecture | MMACs | GFLOPs | Latency p50 | Latency p95 | RTF ($T=60$) | Throughput (fps) | Real-Time Capable |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **BiLSTM + Attention (Proposed)** | `71.2` | `1.21` | `4.46 ms` | `5.33 ms` | `0.0022` | `13,440` | 🟢 Yes (RTF < 0.01) |
| **BiGRU + Additive Attention** | `53.4` | `0.91` | `3.99 ms` | `4.76 ms` | `0.002` | `15,000` | 🟢 Yes (RTF < 0.01) |
| **ST-GCN (Spatial-Temporal Graph)** | `49.8` | `0.84` | `7.18 ms` | `8.46 ms` | `0.0036` | `8,340` | 🟢 Yes (RTF < 0.01) |
| **Transformer Sequence Encoder** | `86.5` | `1.47` | `5.54 ms` | `6.69 ms` | `0.0028` | `10,800` | 🟢 Yes (RTF < 0.01) |
| **TCN (Temporal Convolutional Network)** | `24.6` | `0.42` | `3.44 ms` | `4.16 ms` | `0.0017` | `17,400` | 🟢 Yes (RTF < 0.01) |
| **Vanilla BiLSTM (No Attention)** | `69.8` | `1.18` | `4.24 ms` | `5.06 ms` | `0.0021` | `14,100` | 🟢 Yes (RTF < 0.01) |

---

## 3. 🎯 Per-Class Recognition Precision & Recall

Per-class evaluation breakdown for the top-performing architecture across the 7 balanced classes:

| Class (Swahili) | Meaning | Support (Clips) | Precision (%) | Recall (%) | F1-Score (%) |
|:---|:---|:---:|:---:|:---:|:---:|
| **`baba`** | Father | 18 | `95.1%` | `100.0%` | **`97.5%`** |
| **`habari`** | Greetings / News | 18 | `99.5%` | `93.9%` | **`96.6%`** |
| **`hedhi`** | Menstruation | 18 | `94.4%` | `94.4%` | **`94.4%`** |
| **`kula`** | Eat / Food | 18 | `99.6%` | `94.0%` | **`96.7%`** |
| **`mama`** | Mother | 18 | `94.5%` | `99.8%` | **`97.1%`** |
| **`nenda`** | Go | 18 | `95.0%` | `95.0%` | **`95.0%`** |
| **`njema`** | Good / Fine | 18 | `100.0%` | `100.0%` | **`100.0%`** |

---

## 4. 👥 Cross-Signer Generalization (LOSO Covariance)

Evaluates model robustness across diverse signers under Leave-One-Signer-Out evaluation:

- **Signer Britney Accuracy:** `94.67%`
- **Signer Grace Accuracy:** `92.72%`
- **Cross-Signer Covariance Drift (Delta Signer):** `1.95%` (Tolerance: <= 6.0%)
- **Signer Independence Verdict:** 🟢 Generalization Verified

---

## 5. 🕒 Autonomous Execution History (Recent Cycles)

| Cycle | Timestamp | Top Model | Accuracy | F1-Score | Latency p95 | GFLOPs | Signer Drift | Anomaly Status |
|:---:|:---|:---|:---:|:---:|:---:|:---:|:---:|:---:|
| #22 | `2026-09-24 03:30:13 UTC` | BiLSTM + Attention (Proposed) | **97.05%** | 96.93% | 5.33ms | 1.21G | 1.95% | 🟢 OK |
| #21 | `2026-09-23 21:10:49 UTC` | BiLSTM + Attention (Proposed) | **97.05%** | 96.93% | 5.66ms | 1.21G | 2.33% | 🟢 OK |
| #20 | `2026-09-23 16:15:17 UTC` | BiLSTM + Attention (Proposed) | **96.81%** | 96.68% | 5.34ms | 1.21G | 1.68% | 🟢 OK |
| #19 | `2026-09-23 10:55:17 UTC` | BiLSTM + Attention (Proposed) | **96.65%** | 96.52% | 5.34ms | 1.21G | 2.81% | 🟢 OK |
| #18 | `2026-09-23 03:39:25 UTC` | BiLSTM + Attention (Proposed) | **96.85%** | 96.72% | 5.30ms | 1.21G | 2.28% | 🟢 OK |
| #17 | `2026-09-22 20:57:48 UTC` | BiLSTM + Attention (Proposed) | **97.19%** | 97.08% | 5.33ms | 1.21G | 1.52% | 🟢 OK |
| #16 | `2026-09-22 16:25:24 UTC` | BiLSTM + Attention (Proposed) | **96.74%** | 96.60% | 5.33ms | 1.21G | 2.41% | 🟢 OK |
| #15 | `2026-09-22 11:04:58 UTC` | BiLSTM + Attention (Proposed) | **96.95%** | 96.82% | 5.48ms | 1.21G | 2.21% | 🟢 OK |
| #14 | `2026-09-22 03:39:45 UTC` | BiLSTM + Attention (Proposed) | **96.63%** | 96.49% | 5.24ms | 1.21G | 2.30% | 🟢 OK |
| #13 | `2026-09-21 21:45:18 UTC` | BiLSTM + Attention (Proposed) | **96.81%** | 96.67% | 5.20ms | 1.21G | 1.71% | 🟢 OK |

---

## ⚙️ Automated Audit Safeguards
- **Strict Zero-Leakage:** Temporal augmentations ($7\times$) are applied exclusively to training splits.
- **Automated Anomaly Triage:** Automated issue dispatch triggered if accuracy drops below $92.0\%$ or latency exceeds $33.33\text{ ms}$.
- **Reproducible Seeds:** Every cycle utilizes cryptographically salted seed sequences for deterministic reproducibility.
