# SwSL reviewer-response retraining pipeline

`SwSL_Reviewer_Response_Pipeline.ipynb` retrains the whole system from the raw videos and
produces the evidence for every reviewer comment that needs an experiment. Open it in Google
Colab, set a GPU runtime, and run the cells in order.

`SwSL_Training_Pipeline.ipynb` is the earlier, smaller version. The new notebook supersedes
it and keeps the same section structure.

---

## Before you start

**1. Put the videos in Google Drive.** Either a `.zip` of the `raw_videos/` tree or the
extracted folder. Section 3 finds it automatically, unpacks it to local disk (Drive is far
too slow to read 900 videos from) and verifies the structure:

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

If auto-discovery picks the wrong archive, set `DRIVE_ZIP` or `DRIVE_FOLDER` in Section 2.

**2. Runtime → Change runtime type → GPU.** The pipeline runs on CPU but the recurrent
models are 10–20× slower there.

**3. Choose a preset in Section 2.**

| `PRESET` | Seeds | Max epochs | Cross-validation | Use it for |
|---|---|---|---|---|
| `'quick'` | 1 | 40 | off | Validating the whole path end to end (~20 min) |
| `'standard'` | 3 | 200 | 5-fold | **The publishable run** |
| `'full'` | 5 | 300 | 5-fold × 2 | Everything, if you have the time |

**Run `'quick'` first.** It exercises every cell, writes every artifact and takes under half
an hour, so any path or configuration problem surfaces cheaply.

**4. This is a multi-session job, and that is fine.** The full grid is about 100 training
runs — several hours on a T4, more than a free Colab session allows. Every completed run is
cached to Drive under `SwSL_JCTA_Revision/runs/`, so the intended workflow is: run the
notebook, let the session end whenever it ends, re-run. Finished work is skipped. Section 13
estimates the cost from a 3-epoch probe before you commit to it.

To shorten it, the two biggest savings are `RUN_RAW_FRAME_BASELINES = False` and
`RUN_CROSS_VALIDATION = False`, which together are roughly a third of the total.

---

## Two decisions to make deliberately

**Seven classes or eight?** The archive has eight folders; the manuscript reports seven, with
`siku` the extra one. `siku` is also the only unbalanced class (Grace recorded 29 clips
against Britney's 61), so the default `CLASSES` list is the manuscript's seven. Whatever you
choose, every count in the paper must match it.

**Reproduce 840, or use everything?** The archive holds about 120 clips per class per signer,
not 60, so the pool is larger than the paper describes. `BALANCE_PER_SIGNER = 60` (the
default) draws a reproducible balanced subsample and lands on **839 clips → 587 / 126 / 126**,
which is 18 test clips per class — exactly the arithmetic Reviewer A questioned, now
reproducible end to end. (839 rather than 840 because `kula/Grace` has 59 usable clips.)
`BALANCE_PER_SIGNER = None` uses all 847 clips of the seven classes, which trains a stronger
model but requires rewriting every count in Sections 3.2 and 4. Section 4 reports both
inventories so the choice is informed.

---

## What comes out

Everything lands in `outputs/` and downloads as `swsl_results.zip` (also copied to Drive).
Start with these three files:

| File | What it is |
|---|---|
| `MANUSCRIPT_NUMBERS.md` | Every scalar the revision needs, each with a note saying where in the manuscript it belongs, plus the corrections this run forces |
| `REVIEWER_EVIDENCE_MAP.csv` | All 32 comments, each with its status and the artifact that answers it — the spine of the response letter |
| `tables/table_manuscript_vs_measured.csv` | Every quantitative claim in Sections 3.2–3.4 beside what the files actually contain |

Then the experiment output: ~45 tables in `tables/` (CSV + Markdown, and `all_tables.xlsx`),
~20 figures in `figures/` (PNG at 600 dpi + PDF + SVG), per-epoch histories for every run,
per-sample predictions for every architecture, synthesized audio, and
`swsl_release_bundle.zip` — a Zenodo-ready release of the landmark corpus.

### Evidence by comment

| Comment | Produced by |
|---|---|
| **A4** justify depth/width/regularization | `table09_ablation_study.csv` — depth 1/2/3, width 64-32/128-64/256-128, BN, dropout, L2, each as a single-variable change |
| **A5** specify the attention mechanism | `attention_specification.json`, `fig_attention_block`, `fig07_attention_weights`, `table_attention_concentration`, plus five scoring alternatives measured |
| **A6** the 840 / 5,880 / 126 arithmetic | `table_split_and_augmentation.csv`, `table_split_per_class.csv` |
| **A7** compare the architecture families | `table07_baseline_comparison.csv` — 12 architectures, one protocol |
| **A9** same-dataset superiority claims | `table_mcnemar_tests.csv` (Holm-corrected), `table_bootstrap_confidence_intervals.csv`, `table_cross_validation_summary.csv` |
| **A10** the face-landmark exclusion | ablation rows for the full 468-landmark and a compact 40-landmark face stream |
| **A14** public release | `swsl_release_bundle.zip` |
| **A15** architecture diagram | `fig_system_architecture.*` |
| **B4** gesture characteristics | `table_gesture_characteristics.csv` — handedness, location, movement, handshape, orientation, non-manual activity, all measured; `table_characteristics_vs_ablation.csv` correlates them |
| **B5** landmark pipeline diagram | `fig_landmark_pipeline.*`, `fig_landmark_overlay.*` |
| **B6** normalization scope | `config.json` + four schemes measured in the ablation |
| **B7** MMS-VITS configuration | `tts_config.json`, `table_speech_synthesis.csv`, `audio/` |
| **B10** the parameter count | `table05_parameter_audit.csv`, `table_parameter_reconciliation.csv` |
| **B11** class weighting | `table_class_balance_by_stage.csv` — imbalance at seven pipeline stages |
| **B13** confusion matrix as counts | `table_confusion_matrix_counts.csv` in `17 (94.4%)` format |
| **B14** real-time claims | `table_runtime_benchmark.csv` — CPU and GPU, p50/p95/p99, 30 fps verdict; `table_confidence_threshold_sweep.csv` |
| **B15** signer independence | `table_signer_independent.csv`, `table_protocol_comparison.csv` |
| **B16** cautious feature ablation | `table_ablation_per_class_recall.csv` |

The text-only comments (A1–A3, A8, A11–A13, B1–B3, B8, B9, B12, B17) are writing changes.
Where supporting material exists the map names it; A2 and A3 get column skeletons.

---

## What this pipeline already settles

**A6 — the split order.** Splitting the original clips 70/15/15 and augmenting only the
training partition gives 587 / 126 / 126 and 18 test clips per class, which is what the
published confusion matrix shows. The augmented training set is therefore **587 × 7 = 4,109**,
not 5,880. The figure 5,880 (= 840 × 7) describes augmenting every partition, which is not
what produced the published test set. The notebook enforces the correct order structurally.

**B10 — the parameter count.** The architecture in Table 5, built exactly as specified, has
**604,551** parameters, not 177,607 — a factor of 3.4. BiLSTM-1 alone is 396,288, which
matches the published table; the additive attention block is 16,640 against the 16,577 listed.
The "weight sharing" footnote describes nothing in this architecture and should be deleted.

**B11 — class weighting.** The corpus is balanced by design, so after a stratified split the
imbalance ratio is ≈1.00 and inverse-frequency weighting is a no-op. The notebook measures it
at seven stages and sets the policy from the measurement.

**B15 — signer independence.** Leave-one-signer-out *is* feasible with two signers. It gives a
two-fold estimate whose precision is poor, and the notebook says so — but the manuscript's
current claim that the measurement cannot be made has to go.

---

## Three things the files disagree with the manuscript about

Found by running Section 4 against the actual archive. Check each before revising.

1. **Frame rate.** Section 3.2.2 states 30 fps throughout. The files contain **both 30 and
   60 fps**. This matters because sequence length is standardized in *frames*, so a 60 fps
   clip covers half the real time of a 30 fps clip over the same window — and the temporal
   augmentation of Eq. (2) is defined in frames too.
   See `table_recording_conditions_measured.csv`.

2. **Sequence length.** Section 3.3 states a raw range of 15–300 frames. Measured:
   **0–176 frames, mean ≈ 26**. So most of the 60-frame window is zero padding, which makes
   the input masking layer and the attention mask load-bearing rather than incidental, and
   means `T = 60` needs a sentence of justification. The ablation measures uniform temporal
   resampling as the alternative. See `table_sequence_standardization.csv`.

3. **Two unreadable clips.** Two files report zero frames and zero resolution. They are
   excluded before sampling rather than silently contributing all-zero sequences.
   See `table_unreadable_clips.csv`.

---

## Honest note on the published numbers

96.85%, 91.03% and 79.02% came from a pipeline that can no longer be inspected. This notebook
is a clean reimplementation of what the manuscript describes, so its results will differ.
Report what this pipeline produces. A reviewer-driven re-run that moves the numbers is the
correct outcome, and far safer than defending figures nobody can reproduce.
