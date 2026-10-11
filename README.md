# Independent Reimplementation, Feature Extension & Critical Audit of Weakly Dependent BUSI Features with XAI

**Independent implementation · mask-derived feature extension · open methodological audit**

Based on: Moldovanu, S.; Munteanu, D.; Biswas, K.C.; Moraru, L.  
*Breast Lesion Detection Using Weakly Dependent Customized Features and Machine Learning Models with Explainable Artificial Intelligence*  
**Journal of Imaging, 2025, 11, 135** · [DOI: 10.3390/jimaging11050135](https://doi.org/10.3390/jimaging11050135)

> **Positioning in one line:** This is an independent rebuild, a hard look at the public feature tables, and a tested extension of the feature space.  
> It is **not** an exact numerical reproduction of Table 3, and it does not claim patient-level independence or clinical readiness.

---

## What this project is

The original paper proposes two weakly dependent feature families for breast ultrasound lesions in the BUSI dataset, trains classical classifiers, and explains them with LIME and SHAP. What it does **not** release is training code—only two CSV feature tables and a MATLAB note.

That gap is why this repository exists.

This study delivers three concrete contributions:

1. **Independent extraction** of bounded-histogram features (**CHi**) and grayscale-density ratios (**Ci**) from public BUSI images and ground-truth masks, with algebraic checks that the implementation is internally consistent  
2. **Critical audit** of the authors’ public tables — complete cross-table label disagreement on matched vectors, and numerical behavior that does not match normalized density ratios  
3. **Feature extension** that adds mask-derived geometric descriptors and lesion-intensity statistics, then evaluates the gain with paired McNemar tests  

Primary analysis uses a **645-sample** registry after quarantining an exact pixel-identical cross-class conflict. On the current **162-sample** hold-out, the extended family improves accuracy over pure density ratios for all four classifiers; Holm-adjusted McNemar tests support those differences. Results remain sample-level, not patient-level.

---

## Links

| Resource | URL |
|----------|-----|
| Reference paper (open access) | [DOI 10.3390/jimaging11050135](https://doi.org/10.3390/jimaging11050135) · PDF in `paper/` |
| Study notebook | `notebooks/Breast_Lesion_Detection.ipynb` |
| Authors’ feature tables | [github.com/simonamoldovanu/features_breast](https://github.com/simonamoldovanu/features_breast) |
| BUSI dataset | [Kaggle — Breast Ultrasound Images](https://www.kaggle.com/datasets/sabahesaraki/breast-ultrasound-images-dataset) |

---

## Why this repository is stronger than a silent “reproduction”

High reported numbers (RF accuracy **0.969** in Table 3; the discussion also mentions **100%** in places) are difficult to pressure-test when the only public artifacts are two CSVs. An independent pipeline forces the real questions into the open:

- Do the released tables agree with each other?  
- Do columns labeled “density” behave like densities?  
- Can a transparent pipeline from public images and masks recover a coherent feature story?  
- Does a modest, mask-aware extension change hold-out decisions in a statistically paired way?  

This repo answers those questions with code, audits, and limitations written clearly—not with a forced match to Table 3.

---

## What this code improves relative to the paper

These are concrete upgrades present in the notebook and run artifacts—not marketing language.

### 1. Independent feature extraction instead of opaque CSVs

Features are recomputed from BUSI images and masks:

| Family | Definition used here |
|--------|----------------------|
| **CHi** | Raw pixel counts in eight 32-level gray bins `[0–31] … [224–255]` inside the mask |
| **Ci** | `Ci = CHi / mask_area` (ratios sum to 1 within tolerance) |
| **own_CV_extended** | Ci + geometric cues (`mask_extent`, `bbox_aspect_ratio`, `lesion_area_fraction`, …) + lesion-intensity statistics |

Algebraic identity checks pass on **647** rows (`sum(CH) = mask area`, `C = CH/area`, `sum(C) = 1`, non-negative features). That shows the reimplementation is self-consistent. It does **not** claim numerical identity with the authors’ MATLAB code (the paper mixes normalized-histogram language with ambiguous count/frequency pseudocode).

### 2. Honest handling of the authors’ public tables

Both released CSVs have 647 rows. Matched eight-dimensional vectors appear with **swapped class labels** across the two files. The file named `density_features.csv` stores large integers, not unit-sum ratios. The article refers to 649 selected benign/malignant images; the public tables and the local two-class inventory both show 647.

**Decision in this codebase:** author tables are kept for provenance and **blocked from training** by an explicit authorization gate (`Author-CSV reference gate passed: False`). Labels are not auto-reversed; no row is assumed to map to a specific local image. That is stricter and more transparent than treating the CSVs as ground truth.

### 3. Data integrity that the paper does not document

| Check | Result in this run |
|-------|---------------------|
| Local B + M originals | **647** (paper selected: 649) |
| Unreadable images / masks | 0 |
| Exact cross-class pixel duplicate | **1 pair** — `benign (433)` ↔ `malignant (145)` |
| Primary registry after quarantine | **645** samples |
| Multi-mask images | 17 (not merged; documented) |

A pixel-identical image cannot honestly support both labels. Those records are quarantined from the primary registry; an unfiltered copy is retained only for sensitivity analysis. Primary metrics therefore use the cleaned set.

### 4. Methodological extension with paired statistics

Registered experiments:

- `own_CH_raw` — histogram counts only  
- `own_C_ratio` — density ratios only  
- `own_CV_extended` — ratios + geometry + intensity statistics  

On the current hold-out, **own_CV_extended** improves accuracy relative to `own_C_ratio` for **all four** classifiers. Paired exact McNemar tests with Holm adjustment support the difference for each baseline-versus-extension comparison.

Interpretation, kept deliberately careful:

- Mask-derived shape, extent, and intensity statistics can complement normalized intensity bins  
- Geometry is **not** isolated (intensity statistics are part of the extension)  
- Every extension feature requires a ground-truth mask → this is **mask-conditioned classification**, not end-to-end detection on raw ultrasound  

### 5. Fuller explainability stack

Beyond the paper’s LIME + SHAP pair:

- Native tree importance  
- Permutation importance on hold-out  
- SHAP (TreeSHAP when possible; model-agnostic fallback when needed)  
- LIME on selected cases  
- Spearman rank agreement between SHAP and permutation rankings  

With 8–13 features, rank tests are **exploratory**. They support qualitative discussion of stability across explainers; they are not sold as confirmatory statistics.

### 6. Reproducibility engineering the paper does not provide

From the executed run:

| Artifact layer | Status |
|----------------|--------|
| Environment manifest | Recorded (Python 3.13-compatible constraints; **not** paper’s 3.10 stack) |
| Dataset inventory + fingerprint | SHA-256 fingerprint saved |
| Author CSV gate audit | Gate closed; reason logged |
| Feature identity audit | **Passed** |
| Model serialization | **12 / 12** models saved |
| SHA-256 integrity | **12 / 12** verified |
| Prediction round-trip | **12 / 12** passed |
| Final audit | `Exact paper reproduction verified: **False**` |

Integrity of files is separated from methodological validity. The final audit states that separation explicitly.

### 7. Clearer estimator naming

The paper refers to “LASSO.” This notebook uses **L1-regularized logistic regression (saga)** and states the distinction: L1 logistic classification is not ordinary Lasso regression. Invalid paper hyperparameters (for example `criterion` on XGBoost) are not forced into APIs that reject them.

---

## Method overview

```text
BUSI ultrasound image + ground-truth mask
                 │
                 ▼
      Independent feature extraction
       ┌─────────┴─────────┐
       │  CHi (raw bins)   │   Ci (density ratios)
       └─────────┬─────────┘
                 │
    Extension: geometry + lesion intensity stats
                 │
                 ▼
    Stratified hold-out + CV  (sample-level)
                 │
                 ▼
    RF · XGBoost · GBC · L1-Logistic (saga)
                 │
                 ▼
    Permutation · SHAP · LIME · McNemar (Holm)
                 │
                 ▼
              Full audit trail
```

---

## Run facts (from the executed notebook)

| Item | Value |
|------|--------|
| Python | 3.13.16 (compatibility mode; paper stack match = False) |
| Seed | 42 |
| Label map | malignant = 0, benign = 1 |
| Local B + M images | 647 |
| After quarantine | 645 |
| Experiments with full results | `own_CH_raw`, `own_C_ratio`, `own_CV_extended` |
| Author-CSV experiments | Not trained (gate closed) |
| Models serialized & verified | 12 / 12 |
| Feature identity | Passed |
| Exact Table 3 reproduction | **False** |

Paper reference metrics (Table 3) are stored only as **descriptive references**. They are not used as acceptance thresholds.

---

## Repository structure

```text
├── paper/                         # Reference PDF (CC BY)
├── report/                        # Technical research report
├── notebooks/
│   └── Breast_Lesion_Detection.ipynb
├── results/
│   ├── figures/                   # SHAP, LIME, rank plots, QC
│   ├── tables/                    # Metrics, McNemar, importance
│   ├── models_manifest/           # Checksums / round-trip (not heavy weights)
│   └── audit/                     # Final audit, quarantine, gate, identity
├── docs/
├── requirements.txt
├── CITATION.cff
├── LICENSE
└── README.md
```

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
2. Point the notebook at a folder with `benign/`, `malignant/`, and `normal/` (images + masks).  
3. Open `notebooks/Breast_Lesion_Detection.ipynb` and run top to bottom.

The notebook records the environment, audits the data, extracts features, applies the author-CSV gate, trains models on the cleaned registry, runs XAI and McNemar tests, and writes the audit package under `results/`.

---

## Limitations (please read)

- Exact numerical reproduction of Table 3 was not achieved and is not asserted  
- Author feature tables are internally inconsistent and cannot anchor a faithful replay of the published numbers  
- Sample-level stratified splits do not imply patient-level independence  
- Mask-based features are not equivalent to detecting lesions on unmasked images  
- XAI rankings with few features remain sensitive to sampling and method choice  
- Discussion text that cites 100% RF accuracy conflicts with Table 3 (0.969) and is not treated as a target  
- Software stack differs from the paper’s reported versions; the difference is logged, not hidden  

---

## Possible next steps

Patient-grouped evaluation if identifiers become available; nested CV and calibration; isolating geometry-only versus intensity-statistic contributions; external ultrasound datasets beyond BUSI; comparison with deep or foundation features on the same masks.

---

## References

1. Moldovanu et al., *J. Imaging* 2025, 11, 135 — [DOI](https://doi.org/10.3390/jimaging11050135)  
2. Al-Dhabyani et al., Dataset of breast ultrasound images, *Data Brief* 2020  
3. Authors’ feature release: [simonamoldovanu/features_breast](https://github.com/simonamoldovanu/features_breast)  
4. BUSI on Kaggle: [sabahesaraki/breast-ultrasound-images-dataset](https://www.kaggle.com/datasets/sabahesaraki/breast-ultrasound-images-dataset)  

---

## Citation

```bibtex
@misc{fathi_busi_independent_xai_2026,
  author       = {Fathi, Hannah},
  title        = {Independent Reimplementation, Feature Extension and Critical Audit of Weakly Dependent BUSI Features with XAI},
  year         = {2026},
  howpublished = {GitHub repository},
  note         = {Independent study based on Moldovanu et al., Journal of Imaging 2025}
}
```

Please also cite the original paper when discussing its feature definitions or reported metrics.

---

## License

MIT — see [LICENSE](LICENSE).  
The reference paper is open access under **CC BY 4.0**.

---

## Author

**Hannah Fathi**  
Medical AI · Computer Vision · Explainable AI · Reproducible research  

**Fall 2026**
