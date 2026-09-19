# SwSL Model Training & Execution Guide

`SwSL_Master_Pipeline.ipynb` provides the complete training, evaluation, and benchmark pipeline from raw videos to trained models and evaluation metrics. Run it in Google Colab (with a GPU runtime) or on a local machine with CUDA support.

`SwSL_Training_Pipeline.ipynb` contains the initial baseline training setup. The master notebook supersedes it with full architectural comparisons (12 models) and 23 ablation variants.

---

## 🛠️ Environment & Prerequisites

### 1. Preparing the Video Data
Upload a `.zip` of `raw_videos/` or the uncompressed directory to Google Drive or your working directory:

```
raw_videos/
  baba/     Britney_001.mp4 ...
  habari/
  hedhi/
  kula/
  mama/
  nenda/
  njema/
  siku/
```

If auto-discovery needs manual configuration, adjust `DRIVE_ZIP` or `DRIVE_FOLDER` in Section 2.

### 2. GPU Runtime
In Google Colab, select **Runtime → Change runtime type → GPU (T4 or higher)**. Landmark extraction can run on CPU, but training deep recurrent, GCN, and 3D CNN architectures is 10–20× faster on GPU.

### 3. Execution Presets

| `PRESET` | Seeds | Max Epochs | Cross-Validation | Purpose |
|---|---|---|---|---|
| `'quick'` | 1 | 40 | off | Fast smoke-test exercising every cell end-to-end (~20 min) |
| `'standard'` | 3 | 200 | 5-fold | Standard benchmark with multi-seed statistical significance |
| `'full'` | 5 | 300 | 5-fold × 2 | Exhaustive evaluation across all initializations |

> **Recommendation:** Run `'quick'` first to verify file paths and environment dependencies before launching `'standard'`.

### 4. Checkpoint & Resume Safety
The pipeline automatically caches completed runs to disk under `runs/`. If a training session is interrupted or times out, re-running the notebook skips already-completed runs and resumes seamlessly.

To optimize training time:
- Set `RUN_RAW_FRAME_BASELINES = False` to skip compute-heavy 3D CNN / ConvNet baselines.
- Set `RUN_CROSS_VALIDATION = False` to run hold-out evaluation without multi-fold cross-validation.

---

## ⚙️ Dataset & Configuration Options

- **Vocabulary Classes (`CLASSES`):** The default configuration uses the 7 balanced classes (`baba`, `habari`, `hedhi`, `kula`, `mama`, `nenda`, `njema`).
- **Data Partitioning (`BALANCE_PER_SIGNER = 60`):** Samples a balanced split across signers, producing 587 training, 126 validation, and 126 test clips (18 test clips per class).
- **Data Augmentation:** Temporal stretching, spatial rotation, coordinate translation, and additive jittering are applied strictly to the training partition to eliminate data leakage.
- **Sequence Standardization:** Variable length clips are padded/standardized to $T=60$ frames with masking layers to handle sequence length variance.

---

## 📦 Pipeline Outputs

All generated tables, figures, and serialized weights are written to `outputs/` and bundled into `swsl_results.zip`:

| Folder / File | Description |
|---|---|
| `tables/` | ~45 structured CSV and Markdown metric tables (accuracies, F1, McNemar tests, ablation results) |
| `figures/` | Publication-ready figures (confusion matrices, attention distributions, architecture diagrams) |
| `histories/` | Per-epoch training and validation loss/accuracy curves for all seeds and models |
| `audio/` | Synthesized Swahili speech audio clips generated via MMS-VITS |
| `swsl_release_bundle.zip` | Pre-extracted, normalized landmark arrays for reproducible benchmarking |
