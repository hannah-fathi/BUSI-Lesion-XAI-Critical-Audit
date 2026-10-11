# Independent Re-implementation, Feature Extension and Critical Audit of "Weakly Dependent" BUSI Features with XAI

![Python](https://img.shields.io/badge/python-3.13-blue) ![License](https://img.shields.io/badge/license-MIT-green) ![Status](https://img.shields.io/badge/status-research%20audit-orange) ![Reproduction](https://img.shields.io/badge/exact%20Table%203%20reproduction-not%20achieved-red)

**An independent rebuild of the pipeline in Moldovanu et al. (2025), a hard look at the authors' public feature tables, and a tested extension of the feature space, all on the public BUSI breast-ultrasound dataset.**

> **Reference paper:** Moldovanu, S.; Munteanu, D.; Biswas, K.C.; Moraru, L. *Breast Lesion Detection Using Weakly Dependent Customized Features and Machine Learning Models with Explainable Artificial Intelligence.* **Journal of Imaging** 2025, 11, 135. [doi:10.3390/jimaging11050135](https://doi.org/10.3390/jimaging11050135) (open access, CC BY)

---

## The short version

I wanted to know whether the paper's headline result (Random Forest accuracy 0.969 on eight histogram features) holds up when the pipeline is rebuilt from the public data. The authors released two CSV feature tables and no training code, so there was nothing to "re-run". I rebuilt it instead.

What I found, in plain words:

- **The paper's accuracy did not reproduce.** With the paper's four models and hyper-parameters, my independently extracted CHi features give a Random Forest hold-out accuracy of **0.753** (5×3 nested CV: 0.805). A trivial "always benign" classifier scores **0.679** on the same hold-out.
- **The authors' public tables do not agree with each other.** Every matched feature vector carries the *opposite* label in the two files, and the file called `density_features.csv` contains raw counts, not densities. I did not "fix" them silently. I blocked them from training behind an explicit gate and documented why.
- **The qualitative XAI story partly survives.** Mid-low intensity bins matter in the tree models. The strong claims about individual bins need more care than the paper gives them.
- **A mask-aware extension works.** Adding five mask-derived descriptors to the density ratios lifts RF accuracy from **0.722 to 0.895** on the hold-out, and the gain is statistically supported with paired exact McNemar tests (Holm-corrected). It requires a ground-truth mask, so it is not end-to-end detection.

This repository is **not** a faithful numerical reproduction of Table 3, and it does not claim patient-level independence or clinical readiness. It tries to be an honest, inspectable account of what can and cannot be verified.

---

## Contents

1. [Why this repository exists](#why-this-repository-exists)
2. [What this code does better than the paper](#what-this-code-does-better-than-the-paper)
3. [Results](#results)
4. [Explainability](#explainability)
5. [What the audit found in the authors' tables](#what-the-audit-found-in-the-authors-tables)
6. [A note on "weak dependence"](#a-note-on-weak-dependence)
7. [Method overview](#method-overview)
8. [Notebook map](#notebook-map)
9. [Getting started](#getting-started)
10. [Repository structure](#repository-structure)
11. [Limitations](#limitations-please-read)
12. [Roadmap](#roadmap)
13. [References and citation](#references)

---

## Why this repository exists

The original paper proposes two feature families for ultrasound lesions in BUSI, trains classical classifiers, and explains them with LIME and SHAP:

- **CHi**, the number of lesion pixels falling into each of eight grey-level bins (32 levels per bin) inside the ground-truth mask.
- **Ci**, the same quantity expressed as a fraction of the lesion area (Equation 4 in the paper).

High reported numbers are hard to pressure-test when the only public artefacts are two CSV files and a short MATLAB note. An independent pipeline forces the real questions into the open:

- Do the released tables agree with each other?
- Do the columns labelled "density" behave like densities?
- Can a transparent pipeline built from the public images and masks recover a coherent feature story?
- Does a modest, mask-aware extension change hold-out decisions in a statistically paired way?

This repository answers those with code, audits and written limitations, rather than with a forced match to Table 3.

---

## What this code does better than the paper

These are concrete differences you can verify in the notebook and its saved outputs.

### 1. Features are recomputed, not inherited
CHi and Ci are extracted from the BUSI images and ground-truth masks by my own code. Seventeen images have more than one mask; these are combined by Boolean union in the feature-extraction step and each case is logged. Algebraic checks pass on all **647** rows: `sum(CHi) = mask area`, `Ci = CHi / mask area`, `sum(Ci) = 1`, no negative values. These checks show the implementation is internally consistent. They are not evidence of agreement with the authors' MATLAB code, because the paper's pseudo-code and its Equation 4 describe the feature differently.

### 2. The authors' CSVs are audited, not trusted
The two released tables are kept for provenance and **blocked from model training** behind an explicit authorisation gate (`Author-CSV reference gate passed: False`). Labels are never flipped automatically. That is stricter than treating the CSVs as ground truth, and it is documented in the final audit.

### 3. Data-integrity checks the paper does not report

| Check | Result in this run |
|---|---|
| Local benign + malignant images | **647** (paper: 649 = 438 + 211) |
| Unreadable images or masks | 0 |
| Images with more than one mask | 17 (combined by union, logged) |
| Perceptual near-duplicate pairs (pHash, Hamming ≤ 4) | **185** flagged for review |
| Pixel-identical image in *both* classes | **1 pair**: `benign (433)` ↔ `malignant (145)` |
| Primary modelling registry | **645** samples after quarantine |

A pixel-identical image cannot honestly carry two labels. Both are removed from the primary registry; an unfiltered copy is kept for sensitivity analysis only.

### 4. An evaluation protocol that is stated and leak-aware
- Stratified 75:25 hold-out (162 test images: 52 malignant, 110 benign).
- 5-fold × 3-repeat outer cross-validation (15 outer folds) with the paper's hyper-parameters for RF, XGBoost and GBC.
- L1-regularised logistic regression with the regularisation strength chosen by **nested** cross-validation inside each outer training fold, so the test folds never influence tuning.
- Every table reports the majority-class baseline next to the models.

### 5. More honest metrics
Beyond accuracy: ROC-AUC, F1 per class, sensitivity and specificity for the malignant class, balanced accuracy, MCC, average precision and the Brier score. On a 2:1 imbalanced task, accuracy alone flatters weak models.

### 6. Paired statistics instead of eyeballing
Exact McNemar tests on the *same* hold-out predictions, with Holm correction over all 17 paired comparisons (raw and adjusted p-values are saved, along with a pairing audit).

### 7. A wider explainability stack
Native tree importance, hold-out permutation importance (30 repeats), SHAP, LIME, and a Spearman check of how well SHAP and permutation rankings agree. Where TreeSHAP fails, the notebook says so and labels the fallback instead of presenting it as the same thing.

### 8. A reproducibility trail
| Layer | Status |
|---|---|
| Environment manifest (package versions, Python 3.13.16 compatibility mode) | recorded |
| Dataset inventory and fingerprint | recorded (SHA-256) |
| Feature identity audit | **passed** |
| Models serialised | **12 / 12** |
| File checksums verified | **12 / 12** |
| Prediction round-trip | **12 / 12** passed |
| Final audit | `Exact paper reproduction verified: False` |

File integrity (the models on disk are the models that were trained) is deliberately kept separate from scientific validity (the results match the paper). The final audit says so explicitly.

### 9. Clearer naming
The paper says "LASSO". This notebook uses **L1-regularised logistic regression (saga solver)** and says so; that is a classifier, not ordinary Lasso regression. A hyper-parameter listed in the paper (`criterion='friedman_mse'`) is not an XGBoost parameter, so it is omitted rather than forced into an API that would reject it.

### 10. An extension that is labelled as one
The `own_CV_extended` feature set is reported separately and described as an independent development, never as a reproduction of Table 3.

---

## Results

All numbers below come from the executed notebook (seed 42, label map malignant = 0 / benign = 1). The hold-out contains **162** images; **one image is worth 0.6 percentage points**, so differences of a few points on a single split are within noise.

**Majority-class baseline (always predict "benign"): hold-out accuracy 0.679.**

### Hold-out and cross-validation performance

| Feature set | Model | CV accuracy (mean ± sd) | Hold-out accuracy | Hold-out ROC-AUC | Hold-out F1 (malignant) |
|---|---|---|---|---|---|
| **CHi** (8, raw counts) | RF | 0.805 ± 0.035 | 0.753 | 0.794 | 0.643 |
| | XGBoost | 0.772 ± 0.028 | 0.735 | 0.788 | 0.632 |
| | GBC | 0.741 ± 0.044 | 0.679 | 0.739 | 0.527 |
| | L1-LR | 0.743 ± 0.045 | 0.691 | 0.725 | 0.569 |
| **Ci** (8, ratios) | RF | 0.731 ± 0.027 | 0.722 | 0.741 | 0.526 |
| | XGBoost | 0.708 ± 0.037 | 0.704 | 0.706 | 0.455 |
| | GBC | 0.688 ± 0.039 | 0.685 | 0.701 | 0.505 |
| | L1-LR | 0.559 ± 0.049 | 0.531 | 0.599 | 0.472 |
| **Extended** (13) | RF | 0.919 ± 0.013 | 0.895 | 0.950 | 0.825 |
| | XGBoost | 0.878 ± 0.022 | 0.889 | 0.953 | 0.816 |
| | GBC | 0.888 ± 0.022 | 0.889 | 0.948 | 0.830 |
| | L1-LR | 0.880 ± 0.030 | 0.895 | 0.955 | 0.841 |

For comparison, the paper reports RF accuracy **0.969** for both CHi and Ci (Table 3), while its Discussion section says **100%**. The two statements are inconsistent with each other and neither is reproduced here.

### Malignant-class detail (tree models, hold-out)

| Feature set | Model | Sensitivity (malignant) | Specificity | Balanced acc. | MCC |
|---|---|---|---|---|---|
| CHi | RF | 0.692 | 0.782 | 0.737 | 0.458 |
| CHi | XGBoost | 0.712 | 0.745 | 0.728 | 0.435 |
| CHi | GBC | 0.558 | 0.736 | 0.647 | 0.286 |
| Ci | RF | 0.481 | 0.836 | 0.659 | 0.335 |
| Ci | XGBoost | 0.385 | 0.855 | 0.620 | 0.269 |
| Ci | GBC | 0.500 | 0.773 | 0.636 | 0.274 |
| Extended | RF | 0.769 | 0.955 | 0.862 | 0.754 |
| Extended | XGBoost | 0.769 | 0.945 | 0.857 | 0.740 |
| Extended | GBC | 0.846 | 0.909 | 0.878 | 0.748 |

### How to read these numbers

- **The paper's features add modestly over the baseline.** The best CHi model beats "always benign" by about 7 points on the hold-out. That is a signal, not the near-perfect separation the paper describes.
- **CHi outperforms Ci.** One plausible explanation is that raw counts also encode lesion *size*, which Ci removes by normalising. I have not isolated this effect yet (see [Roadmap](#roadmap)), so treat it as a hypothesis.
- **L1-LR on Ci falls below the baseline** (0.531). See [A note on "weak dependence"](#a-note-on-weak-dependence).
- **The extension gives a large, statistically supported gain.** Paired exact McNemar tests, extended vs. Ci (raw p-values; Holm-adjusted values are in the CSV):

| Classifier | raw p |
|---|---|
| RF | 8.4 × 10⁻⁶ |
| XGBoost | 9.2 × 10⁻⁶ |
| GBC | 5.4 × 10⁻⁷ |
| L1-LR | 1.2 × 10⁻¹¹ |

- **Among the classifiers on the same features, differences are mostly not significant.** RF vs. GBC on CHi has a raw p of 0.029, which does not survive Holm correction over 17 comparisons. Please do not read the model rankings as established.
- **The extension needs a segmentation.** All five added descriptors come from the ground-truth mask (`mask_extent`, `bbox_aspect_ratio`, `lesion_area_fraction`, lesion mean and standard deviation of intensity). The result describes *mask-conditioned classification*, not detection on raw ultrasound. Geometry and intensity statistics are not yet separated, so the notebook does not say which part carries the gain.

---

## Explainability

Four views of "what the model uses", compared rather than trusted individually:

| Method | What it shows |
|---|---|
| Native tree importance | Impurity-based importance of RF, XGBoost and GBC |
| Permutation importance | Drop in hold-out performance when a feature is shuffled (30 repeats, 0 computation errors) |
| SHAP | Per-feature contribution to each prediction, aggregated |
| LIME | Local surrogate explanations for selected hold-out cases |

Findings that I am comfortable stating:

- Rankings from SHAP and permutation importance are positively correlated for CHi: Spearman ρ between **0.83 and 0.88** across RF, XGBoost and GBC (BH-adjusted p between 0.009 and 0.015). With only eight features this is **exploratory**, not confirmatory.
- In GBC on CHi, bin CH3 carries the largest native importance (≈ 0.56). That is consistent in direction with the paper's feature-importance figure.
- **XGBoost TreeSHAP failed in all three experiments** in this environment (`shap 0.52`, error message *"Categorical split is not yet supported"*). The notebook falls back to model-agnostic Permutation SHAP for XGBoost, with a printed warning, and passes its additivity check. XGBoost SHAP values should therefore be read as a fallback explanation. The error message itself points to `feature_perturbation="tree_path_dependent"` as the remedy; this is on the [Roadmap](#roadmap).
- LIME is applied to selected cases, which is useful for illustration but not for global claims.

---

## What the audit found in the authors' tables

The authors' tables are at [github.com/simonamoldovanu/features_breast](https://github.com/simonamoldovanu/features_breast). Both files have 647 rows. The audit notebook cell reports:

- The two files contain **identical multisets of eight-dimensional feature vectors**.
- For **every matched occurrence the class label in one file is the opposite of the label in the other**. The notebook refuses to guess which file is correct and does not flip labels automatically.
- `density_features.csv` holds large integers, not unit-sum ratios. The released data therefore contain **no Equation 4 densities**.
- The paper states 649 selected images (438 benign, 211 malignant); the public tables and the local BUSI inventory both give 647.
- Algorithm 1's pseudo-code and Equation 4 can be read as two different definitions of Ci (a fraction of the area vs. an intensity statistic). The notebook implements Equation 4, flags the ambiguity, and refuses to change the definition until it is resolved.

Because of these issues, the reference experiment on the authors' own CSVs is **not** part of the current results. The gate is closed, and the final audit records the reason.

---

## A note on "weak dependence"

The paper describes its features as *weakly dependent*. By construction, however, the eight Ci values sum to one for every lesion, which the identity audit here confirms on all 647 rows (`sum(Ci) = 1`, zero failing rows). A set of values that always sums to one is *compositional*: any one value is determined exactly by the other seven, so the centred feature matrix has at most rank 7. That is an exact linear dependence, not a weak one, and it is a mathematical consequence of Equation 4 rather than something this notebook had to estimate.

Two practical consequences follow, and I flag them as open questions rather than established results: (a) linear models with an L1 penalty can behave badly on compositional features, which fits the 0.531 hold-out accuracy for L1-LR on Ci; (b) attribution methods may split credit arbitrarily among collinear features. The notebook does not yet test either directly. A matrix-rank and VIF check plus a bootstrap test of explanation stability are on the [Roadmap](#roadmap).

---

## Method overview

```text
BUSI ultrasound image + ground-truth mask
                  │
                  ▼
   Independent feature extraction
     ┌────────────┴────────────┐
     │  CHi: 8 raw intensity   │   Ci = CHi / lesion area
     │  bin counts             │
     └────────────┬────────────┘
                  │
   Extension: + mask extent, bounding-box aspect ratio,
     lesion-area fraction, lesion mean / std intensity
                  │
                  ▼
   Quarantine exact cross-class duplicates (645 samples)
                  │
                  ▼
   Stratified 75:25 hold-out + 5×3 nested CV (sample level)
                  │
                  ▼
   RF · XGBoost · GBC · L1-regularised logistic regression
                  │
                  ▼
   Permutation · SHAP · LIME · exact McNemar (Holm)
                  │
                  ▼
   Serialisation, checksums, final audit report
```

Hyper-parameters follow the paper: RF `n_estimators=200`, `criterion='gini'`, `random_state=0`; XGBoost `learning_rate=0.02`, `max_depth=3`, `random_state=2`; GBC `n_estimators=100`, `learning_rate=1.0`, `max_depth=1`; L1 logistic with `class_weight='balanced'` and the regularisation grid tuned by nested CV.

---

## Notebook map

| Cell | Purpose |
|---|---|
| 1–2 | Reproducible environment, Drive set-up, project paths |
| 3, **3B** | Dataset inventory, image-mask pairing, reconciliation of sample counts with the paper |
| 4 | Image integrity, pixel and perceptual duplicates, mask quality |
| 5 | Image and mask loading, validation, visualisation |
| 6, **6B**, **6C** | Independent CHi/Ci extraction; audit of the feature definition and of the paper's Ci |
| 7–8 | Download of the authors' CSVs from a pinned commit; schema, label and cross-table audit |
| 9, **9B** | Model definitions and evaluation protocol; paper-to-code protocol audit |
| 10, **10B** | Data preparation; quarantine of exact cross-class pixel duplicates |
| 11, **11B** | Nested-CV experiments and hold-out metrics; duplicate-exclusion sensitivity analysis |
| 12 | Hold-out plots and per-model reports |
| 13 | Permutation and native importance |
| 14 | Algebraic identity checks and Spearman correlations |
| 15 | SHAP and LIME |
| 16 | SHAP vs. permutation rank agreement |
| 17 | Exact McNemar tests with Holm correction |
| 18 | Final model and feature-set comparison |
| 19 | Model serialisation and integrity verification |
| 20 | Final reproduction audit and report |

Cells with a **B** are audit or sensitivity cells added so that every claim has a visible check.

---

## Getting started

```bash
git clone https://github.com/<your-username>/BUSI-Independent-XAI-Study.git
cd BUSI-Independent-XAI-Study
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

1. Download BUSI from [Kaggle](https://www.kaggle.com/datasets/sabahesaraki/breast-ultrasound-images-dataset).
2. Point the notebook at the folder that contains `benign/`, `malignant/` and `normal/` (images and masks).
3. Open `notebooks/Breast_Lesion_Detection.ipynb` and run it top to bottom. It was developed in Google Colab with outputs written to Google Drive; adjust the root path in Cell 2 if you run it elsewhere.

The run records the environment, audits the data, extracts the features, applies the author-CSV gate, trains the models on the cleaned registry, runs the XAI and McNemar analyses, and writes the audit package.

---

## Repository structure

```text
├── paper/                  # reference PDF (CC BY 4.0)
├── notebooks/
│   └── Breast_Lesion_Detection.ipynb
├── report/
|   └── BUSI_Report.pdf
├── results/
│   ├── figures/            # SHAP, LIME, permutation, rank-agreement, QC plots
│   ├── tables/             # metrics, McNemar, importances (CSV)
│   ├── audit/              # final audit (.md/.json), quarantine, gate, identity checks
│   └── models_manifest/    # checksums and round-trip records (no heavy weights)
├── requirements.txt
├── CITATION.cff
├── LICENSE
└── README.md
```

Fitted model files are intentionally not committed; their checksums are.

---

## Limitations (please read)

- **No exact reproduction.** Table 3 was not reproduced and is not claimed to be. Possible reasons include a different split or feature table than the released one and undocumented preprocessing; I make no claim about which.
- **Sample-level, not patient-level.** BUSI has 780 images from about 600 patients and no patient identifier. The 185 perceptual near-duplicate pairs are flagged but are **not yet grouped** in cross-validation, so near-identical images can sit in both training and test folds and inflate scores. The duplicate-exclusion sensitivity analysis (Cell 11B) removes only the exact cross-class duplicates.
- **Single hold-out of 162 images.** One split gives wide uncertainty; hold-out numbers are indicative, and the nested-CV numbers are somewhat higher than the hold-out ones.
- **Mask-conditioned features.** All features need a ground-truth segmentation. This is not lesion detection on raw ultrasound and it is not a clinical tool.
- **XGBoost SHAP is a fallback** in this environment (see [Explainability](#explainability)).
- **Explanations from 8–13 features** are sensitive to the method and to sampling. Rank agreement is exploratory.
- **Software stack differs** from the one in the paper. The difference is logged, not hidden.
- **Single dataset.** No external validation.

---

## Roadmap

Short, concrete, in order of value:

1. **Group-aware cross-validation** using the pHash near-duplicate groups, to measure how much of the current score depends on near-identical images.
2. **Ablation ladder**: image size only, lesion area only, CHi, Ci, shape only, intensity only, and their combinations, against the majority baseline. This tests whether CHi mainly encodes lesion size and which part of the extension carries the gain.
3. **Fix XGBoost TreeSHAP** with `tree_path_dependent` (or a newer `shap`), and **aggregate LIME** over many hold-out cases so it can be compared with global SHAP.
4. **Test the dependence claim**: matrix rank and VIF for CHi, Ci and Ci without its last bin, plus the stability of SHAP rankings across bootstrap refits.
5. **Reference run on the authors' CSVs** once the label inversion is resolved with evidence, for a like-for-like comparison with Table 3.
6. **Reproduce the paper's Figure 1** (ROI and the eight bin masks) with my own loaders as a qualitative check of the bins.
7. Calibration curves, external ultrasound datasets, and a comparison with deep or foundation-model features on the same masks.

---

## References

1. Moldovanu, S.; Munteanu, D.; Biswas, K.C.; Moraru, L. Breast Lesion Detection Using Weakly Dependent Customized Features and Machine Learning Models with Explainable Artificial Intelligence. *J. Imaging* **2025**, 11, 135. https://doi.org/10.3390/jimaging11050135
2. Al-Dhabyani, W.; Gomaa, M.; Khaled, H.; Fahmy, A. Dataset of breast ultrasound images. *Data in Brief* **2020**, 28, 104863.
3. Authors' feature release: https://github.com/simonamoldovanu/features_breast
4. BUSI on Kaggle: https://www.kaggle.com/datasets/sabahesaraki/breast-ultrasound-images-dataset

## Citation

If you use this repository, please cite it **and** the original paper whenever you discuss the paper's feature definitions or reported metrics.

```bibtex
@misc{fathi_busi_independent_xai_2026,
  author       = {Fathi, Hannah},
  title        = {Independent Re-implementation, Feature Extension and Critical Audit of Weakly Dependent BUSI Features with XAI},
  year         = {2026},
  howpublished = {GitHub repository},
  note         = {Independent study based on Moldovanu et al., Journal of Imaging 2025}
}
```

## License

Code: MIT, see [LICENSE](LICENSE). The reference paper is open access under CC BY 4.0. BUSI is distributed under its own terms on Kaggle; please check them before redistributing any images.

## Author

**Hannah Fathi**: Medical AI · Computer Vision · Explainable AI · Reproducible research

# Research Timeline

*Fall 2026*
