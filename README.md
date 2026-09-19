# Swahili Sign Language (SwSL) Recognition System

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![TensorFlow 2.16+](https://img.shields.io/badge/TensorFlow-2.16+-orange.svg)](https://tensorflow.org/)
[![Keras 3](https://img.shields.io/badge/Keras-3.0+-red.svg)](https://keras.io/)
[![Colab Ready](https://img.shields.io/badge/Colab-Ready-yellow.svg)](https://colab.research.google.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

An end-to-end deep learning pipeline for Swahili Sign Language (SwSL) recognition. The system leverages landmark coordinate extraction, bidirectional recurrent networks with additive attention, comprehensive baselines (GCN, TCN, Transformers, 3D CNNs), and real-time speech synthesis.

---

## 📂 Repository Structure

```
├── README.md                     # Main project documentation and overview
├── README_TRAINING.md            # Execution guide & Google Colab instructions
├── SwSL_Master_Pipeline.ipynb    # Master reproducible pipeline (12 models, 23 ablations)
├── SwSL_Training_Pipeline.ipynb  # Baseline training pipeline
└── .gitignore                    # Excludes large media assets, outputs, and checkpoints
```

> ℹ️ **Dataset Note:** Raw video files (`raw_videos/`, ~961 MB) are excluded from the repository. Landmark extraction runs directly on raw clips or uses the landmark bundle generated in the pipeline (`swsl_release_bundle.zip`).

---

## 🎯 Architecture & Methodology

The primary architecture combines landmark tracking with deep temporal modeling:

### 1. Feature Extraction & Standardization
- **Holistic Keypoints:** Extracts 258 landmarks (pose, left hand, right hand, face contours) per frame using MediaPipe Holistic.
- **Sequence Normalization:** Shoulder-width and centroid normalization schemes standardizing gestures across diverse signers.
- **Frame Standardization:** Temporal standardization to $T=60$ frames with masking layers to handle variable-length sign durations.

### 2. Deep Recurrent Network with Additive Attention
- **Layer 1:** Bidirectional LSTM (128 units, forward + backward = 256 output dimensions) + Batch Normalization + Spatial Dropout.
- **Layer 2:** Bidirectional LSTM (64 units, forward + backward = 128 output dimensions).
- **Attention Mechanism:** Bahdanau additive temporal attention computing alignment scores over the sequence representations.
- **Classification Head:** Dense projection with Softmax over the Swahili sign vocabulary.
- **Total Parameters:** **604,551 trainable parameters**.

### 3. Vocabulary Classes
The system is calibrated on 7 balanced isolated SwSL gestures:
- `baba` (Father)
- `habari` (News / Greetings)
- `hedhi` (Menstruation)
- `kula` (Eat / Food)
- `mama` (Mother)
- `nenda` (Go)
- `njema` (Good / Fine)

---

## 🧠 Model Zoo & Experimental Suite

`SwSL_Master_Pipeline.ipynb` provides standardized evaluation across **12 deep learning architectures**:

1. **Proposed Model:** BiLSTM (128) + BiLSTM (64) + Additive Attention
2. **Recurrent Baselines:** Vanilla BiLSTM, Unidirectional LSTM, BiGRU + Attention, Stacked 3-Layer LSTM
3. **Graph Neural Networks:** ST-GCN (Spatial-Temporal Graph Convolutional Network) built on MediaPipe skeletal graph adjacency
4. **Temporal Convolutions:** Dilated Residual Temporal Convolutional Network (TCN)
5. **Raw-Pixel Vision:** 3D CNN (Conv3D/ResNet3D) & TimeDistributed 2D CNN + LSTM
6. **Transformer:** Multi-head self-attention sequence encoder with positional encodings

### Extensive Ablation Study (23 Variants)
- **Depth & Width:** 1 vs 2 vs 3 recurrent layers; units (64-32, 128-64, 256-128)
- **Attention Scoring:** Additive (Bahdanau), Multiplicative (Luong), Dot-product, Scaled Dot-product, Cosine
- **Landmark Subsets:** Hands+Pose vs. Full Face Mesh vs. Compact Face Contour
- **Normalization Schemes:** Shoulder-width, Bounding-box, Centroid-centering, Min-max scaling
- **Temporal Alignment:** Post-padding vs. Uniform temporal linear resampling

---

## 📊 Rigorous Benchmarks & Validation

- **Statistical Testing:** Holm-Bonferroni corrected McNemar tests, 1,000-sample bootstrap confidence intervals, and 5-fold cross-validation.
- **Signer Independence:** Leave-One-Signer-Out (LOSO) cross-signer evaluation.
- **Real-Time Latency:** Wall-clock latency benchmarks (p50, p95, p99) on CPU and GPU with confidence threshold sweeps.
- **Speech Synthesis:** End-to-end Swahili audio generation using MMS-VITS translating recognized signs to spoken Swahili speech.

---

## 🚀 Getting Started

### Option A: Google Colab (Recommended for GPU Training)
1. Open `SwSL_Master_Pipeline.ipynb` in [Google Colab](https://colab.research.google.com/).
2. Select **Runtime → Change runtime type → GPU (T4 or higher)**.
3. Upload your `raw_videos/` (or `raw_videos.zip`) to your Drive or Colab workspace.
4. Set your execution preset in Section 2:
   ```python
   PRESET = 'quick'     # ~20 min: 1 seed, 40 epochs, smoke-tests every cell
   # PRESET = 'standard' # Comprehensive run: 3 seeds, 200 epochs, 5-fold CV
   # PRESET = 'full'     # Full grid: 5 seeds, 300 epochs, repeated CV
   ```

### Option B: Local Setup
```bash
# Clone the repository
git clone https://github.com/festomanolo/Sw-SL.git
cd Sw-SL

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install --upgrade pip
pip install tensorflow>=2.16 keras>=3.0 mediapipe opencv-python numpy pandas scipy matplotlib seaborn scikit-learn
```

---

## 📦 Output Artifacts

Running the master pipeline generates an `outputs/` folder with complete artifacts:
- `tables/`: Structured CSV/Markdown tables (model comparisons, McNemar tests, ablation results, inference latency).
- `figures/`: Publication-ready vector figures (PDF, SVG, 600-DPI PNG).
- `audio/`: Synthesized Swahili speech audio files for predicted gestures.
- `swsl_release_bundle.zip`: Pre-extracted landmark corpus.

---

## 📜 Citation & License

This project is licensed under the MIT License.

```bibtex
@misc{swsl_recognition_2026,
  title={Swahili Sign Language Recognition Using Deep Bidirectional Recurrent Neural Networks with Additive Attention},
  author={Manolo, Festo},
  year={2026},
  publisher={GitHub},
  howpublished={\url{https://github.com/festomanolo/Sw-SL}}
}
```
