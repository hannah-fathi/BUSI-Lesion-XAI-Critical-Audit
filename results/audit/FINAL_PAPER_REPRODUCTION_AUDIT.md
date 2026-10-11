# Final Paper-Reproduction Audit

- Audit time (UTC): 2026-10-11T16:02:57.296065+00:00
- DOI: 10.3390/jimaging11050135
- Scope: feature extraction, classification, evaluation, model artifacts, and XAI
- Exact paper reproduction certified: **No**

## 1. Author-CSV authorization gate

```json
{
  "gate_inputs_present": {
    "AUTHOR_CSV_REFERENCE_EXPERIMENTS_ALLOWED": true,
    "AUTHOR_CSV_REFERENCE_EXPERIMENTS_READY": true,
    "AUTHOR_CSV_CROSS_TABLE_BLOCKER": true,
    "AUTHOR_CSV_BLOCK_REASONS": true
  },
  "gate_inputs_complete": true,
  "reference_experiments_allowed": false,
  "reference_experiments_ready": false,
  "cross_table_blocker": true,
  "block_reasons": [
    "The two author tables contain identical multisets of eight-dimensional feature vectors. They cannot currently be treated as independent feature representations.",
    "For every matched feature-vector occurrence, the mapped class label in authors_CH is opposite to the label in authors_C. Do not flip labels automatically; verify the original data definitions and sample correspondence.",
    "The numerical rows in authors_C do not behave like normalized histogram ratios: fewer than the required proportion of valid rows sum to one within tolerance 0.001. Inspect the paper's definition and the source CSV before interpreting C1-C8 as normalized density ratios."
  ],
  "author_csv_reference_gate_passed": false,
  "interpretation": "The gate did not explicitly pass. Author-CSV-based results remain unapproved for direct numerical comparison with the paper."
}
```

## 2. Experiment and model-artifact audit

     experiment  result_available  trained_model_count  holdout_prediction_model_count  expected_model_count  all_expected_models_present_in_results  all_holdout_predictions_present  saved_model_files_present  expected_model_files  model_files_missing  saved_model_hash_verified_count  saved_model_hash_failed_count  saved_model_roundtrip_verified_count  saved_model_roundtrip_failed_count  all_expected_files_present  all_existing_file_hashes_verified  all_expected_roundtrips_verified
     authors_CH             False                    0                               0                     4                                   False                            False                          0                     4                    4                                0                              0                                     0                                   0                       False                              False                             False
      authors_C             False                    0                               0                     4                                   False                            False                          0                     4                    4                                0                              0                                     0                                   0                       False                              False                             False
     own_CH_raw              True                    4                               4                     4                                    True                             True                          4                     4                    0                                4                              0                                     4                                   0                        True                               True                              True
    own_C_ratio              True                    4                               4                     4                                    True                             True                          4                     4                    0                                4                              0                                     4                                   0                        True                               True                              True
own_CV_extended              True                    4                               4                     4                                    True                             True                          4                     4                    0                                4                              0                                     4                                   0                        True                               True                              True

## 3. Per-model artifact verification

     experiment       model  model_present_in_experiment_results  holdout_predictions_present  artifact_exists                                                                                           artifact_path  path_resolved_from_manifest  manifest_record_found manifest_artifact_integrity_flag                                                  expected_sha256                                                    actual_sha256 checksum_verified                                 checksum_status roundtrip_verified_in_manifest            artifact_status
     authors_CH          RF                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
     authors_CH         XGB                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
     authors_CH         GBC                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
     authors_CH L1_Logistic                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
      authors_C          RF                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
      authors_C         XGB                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
      authors_C         GBC                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
      authors_C L1_Logistic                                False                        False            False                                                                                                    None                        False                  False                             None                                                             None                                                             None              None Not verifiable from an applicable manifest hash                           None                    Missing
     own_CH_raw          RF                                 True                         True             True               /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CH_raw__RF__6b032bba7057.joblib                         True                   True                             True 85a73a52f2a2cecb8710fd1cac0f2bf7353fc1ef288617341dc0327cc6439362 85a73a52f2a2cecb8710fd1cac0f2bf7353fc1ef288617341dc0327cc6439362              True                                        Verified                           True Present; checksum verified
     own_CH_raw         XGB                                 True                         True             True              /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CH_raw__XGB__37848ebf1340.joblib                         True                   True                             True 0f2a242599a0e8e91deff4c14d47dac973cd89e731736e5c5caca4ff42c492e2 0f2a242599a0e8e91deff4c14d47dac973cd89e731736e5c5caca4ff42c492e2              True                                        Verified                           True Present; checksum verified
     own_CH_raw         GBC                                 True                         True             True              /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CH_raw__GBC__c1b2d580f572.joblib                         True                   True                             True 4e688d9b9f5121b28a53c773f657b8a3d9b4d8e7c7cc356a0ed5bff43cfaa3ee 4e688d9b9f5121b28a53c773f657b8a3d9b4d8e7c7cc356a0ed5bff43cfaa3ee              True                                        Verified                           True Present; checksum verified
     own_CH_raw L1_Logistic                                 True                         True             True      /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CH_raw__L1_Logistic__678004119898.joblib                         True                   True                             True d654b36d1809bcf42a9cb19e82217eb0f7bc6bbdbd81b08c69c814f0cf3d84b8 d654b36d1809bcf42a9cb19e82217eb0f7bc6bbdbd81b08c69c814f0cf3d84b8              True                                        Verified                           True Present; checksum verified
    own_C_ratio          RF                                 True                         True             True              /content/drive/MyDrive/BUSI_paper_reproduction/models/own_C_ratio__RF__104f84f93a6f.joblib                         True                   True                             True 11fd5f42836ab0c06739e83f55f347df4bb6af346b0d9bccf47f82f907219231 11fd5f42836ab0c06739e83f55f347df4bb6af346b0d9bccf47f82f907219231              True                                        Verified                           True Present; checksum verified
    own_C_ratio         XGB                                 True                         True             True             /content/drive/MyDrive/BUSI_paper_reproduction/models/own_C_ratio__XGB__47d87f303d23.joblib                         True                   True                             True 59fb8668b54386f49deac263afde8d65474671b8d61b3d8db859ad0d24ec7ec6 59fb8668b54386f49deac263afde8d65474671b8d61b3d8db859ad0d24ec7ec6              True                                        Verified                           True Present; checksum verified
    own_C_ratio         GBC                                 True                         True             True             /content/drive/MyDrive/BUSI_paper_reproduction/models/own_C_ratio__GBC__df0d61f1029a.joblib                         True                   True                             True db63a476e51d370f9a16e6ebc8fa507d6621a2bcde4cee1df863880d64b0ab78 db63a476e51d370f9a16e6ebc8fa507d6621a2bcde4cee1df863880d64b0ab78              True                                        Verified                           True Present; checksum verified
    own_C_ratio L1_Logistic                                 True                         True             True     /content/drive/MyDrive/BUSI_paper_reproduction/models/own_C_ratio__L1_Logistic__84e0b3798c07.joblib                         True                   True                             True c331caf508b3c64fc7404b7aa8104cd1244354d18e43e21d4d9760b8ccbb3de7 c331caf508b3c64fc7404b7aa8104cd1244354d18e43e21d4d9760b8ccbb3de7              True                                        Verified                           True Present; checksum verified
own_CV_extended          RF                                 True                         True             True          /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CV_extended__RF__642afe74a89d.joblib                         True                   True                             True db2dfcb37ce9228a83c850fc76b8d7ffe320416c6ea9058d05a08fb9934bdb95 db2dfcb37ce9228a83c850fc76b8d7ffe320416c6ea9058d05a08fb9934bdb95              True                                        Verified                           True Present; checksum verified
own_CV_extended         XGB                                 True                         True             True         /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CV_extended__XGB__8e9cd89eda01.joblib                         True                   True                             True 7c1c206bc2e424be314f883983704a3fa1589ccd39f41006c724846141e53ddf 7c1c206bc2e424be314f883983704a3fa1589ccd39f41006c724846141e53ddf              True                                        Verified                           True Present; checksum verified
own_CV_extended         GBC                                 True                         True             True         /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CV_extended__GBC__8fd610988f82.joblib                         True                   True                             True 785e673ad0dbb69045a89d09f4d5c4e323aa1a29039ca4929d921e85ae037e66 785e673ad0dbb69045a89d09f4d5c4e323aa1a29039ca4929d921e85ae037e66              True                                        Verified                           True Present; checksum verified
own_CV_extended L1_Logistic                                 True                         True             True /content/drive/MyDrive/BUSI_paper_reproduction/models/own_CV_extended__L1_Logistic__d658db87278c.joblib                         True                   True                             True c8cc7cad655c957833abfb3f1527ecbf8630e7f216e445e37a5a1e5373c75eba c8cc7cad655c957833abfb3f1527ecbf8630e7f216e445e37a5a1e5373c75eba              True                                        Verified                           True Present; checksum verified

## 4. Published reference metrics

paper_feature_family       model  paper_cv_accuracy  paper_f1_reported  paper_roc_auc_reported                                              reference_source                                                                                                                 comparison_caveat
                  CH         XGB              0.851              0.739                   0.728 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.
                  CH          RF              0.969              0.950                   0.965 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.
                  CH         GBC              0.919              0.863                   0.872 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.
                  CH L1_Logistic              0.776              0.834                   0.798 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.
                   C         XGB              0.882              0.822                   0.816 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.
                   C          RF              0.969              0.957                   0.967 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.
                   C         GBC              0.938              0.901                   0.916 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.
                   C L1_Logistic              0.761              0.818                   0.812 Moldovanu et al., Journal of Imaging (2025), 11, 135, Table 3 Reported paper values are reference metrics. The notebook's split, feature provenance, preprocessing, and CV protocol may differ.

## 5. Notebook-versus-paper accuracy audit

experiment feature_family       model  result_available  author_csv_gate_passed  paper_cv_accuracy  notebook_cv_accuracy  accuracy_difference_descriptive           comparison_status
authors_CH             CH         XGB             False                   False              0.851                   NaN                              NaN Notebook result unavailable
authors_CH             CH          RF             False                   False              0.969                   NaN                              NaN Notebook result unavailable
authors_CH             CH         GBC             False                   False              0.919                   NaN                              NaN Notebook result unavailable
authors_CH             CH L1_Logistic             False                   False              0.776                   NaN                              NaN Notebook result unavailable
 authors_C              C         XGB             False                   False              0.882                   NaN                              NaN Notebook result unavailable
 authors_C              C          RF             False                   False              0.969                   NaN                              NaN Notebook result unavailable
 authors_C              C         GBC             False                   False              0.938                   NaN                              NaN Notebook result unavailable
 authors_C              C L1_Logistic             False                   False              0.761                   NaN                              NaN Notebook result unavailable

## 6. Independent feature identity audit

```json
{
  "identity_check_available": true,
  "identity_check_passed": true,
  "feature_source": "own_df",
  "n_rows_total": 647,
  "n_valid_rows": 647,
  "n_invalid_rows": 0,
  "max_abs_CH_sum_minus_mask_area": 0.0,
  "max_abs_C_minus_CH_over_area": 0.0,
  "min_sum_C": 0.9999999999999999,
  "max_sum_C": 1.0000000000000002,
  "all_CH_bins_sum_to_mask_area": true,
  "all_C_rows_match_CH_over_area": true,
  "all_C_rows_sum_to_one": true,
  "all_features_nonnegative": true,
  "identity_summary_from_previous_cell": {
    "feature_source": "own_df",
    "n_rows_total": 647,
    "n_rows_numeric_valid": 647,
    "n_rows_with_invalid_numeric_values": 0,
    "n_rows_eligible_for_algebraic_checks": 647,
    "n_rows_passing_all_checks": 647,
    "n_rows_failing_or_not_evaluated": 0,
    "n_CH_features": 8,
    "n_C_features": 8,
    "identity_tolerance_atol": 1e-08,
    "identity_tolerance_rtol": 0.0,
    "max_abs_C_minus_CH_over_area": 0.0,
    "mean_abs_C_minus_CH_over_area": 0.0,
    "min_sum_C": 0.9999999999999999,
    "max_sum_C": 1.0000000000000002,
    "max_abs_sum_CH_minus_mask_area": 0.0,
    "all_evaluable_C_match_CH_over_area": true,
    "all_evaluable_CH_sums_match_mask_area": true,
    "all_evaluable_C_rows_sum_to_1": true,
    "n_identity_failures": 0,
    "n_histogram_area_failures": 0,
    "n_ratio_sum_failures": 0,
    "n_ratio_range_failures": 0,
    "n_noninteger_histogram_rows": 0,
    "n_noninteger_mask_area_rows": 0,
    "all_evaluable_rows_pass_all_checks": true
  },
  "interpretation": "All evaluated algebraic checks passed for the independent feature table. This does not prove numerical correspondence with the authors' CSV files or exact reproduction of the paper."
}
```

The feature-identity checks evaluate algebraic consistency in the independently extracted feature table. They do not prove that the independent features numerically reproduce the authors' CSV features.

## 7. XAI and statistical artifact audit

     experiment  shap_rows_in_memory  rank_agreement_rows_in_memory  mcnemar_rows_in_memory  pairing_audit_rows_in_memory  shap_csv_file_count  shap_plot_file_count  lime_png_count  lime_html_count  permutation_summary_file_count  mcnemar_file_count  shared_permutation_summary_exists  shared_permutation_repeats_exists  shared_mcnemar_results_exists  shared_mcnemar_pairing_audit_exists  shared_artifact_count_run_level                                                                      shared_artifact_status_json  related_xai_and_statistical_artifact_count
     authors_CH                    0                              0                       0                             0                    0                     0               0                0                               0                   0                               True                               True                           True                                 True                                4 /content/drive/MyDrive/BUSI_paper_reproduction/csv/shared_statistical_artifact_audit_cell20.json                                           0
      authors_C                    0                              0                       0                             0                    0                     0               0                0                               0                   0                               True                               True                           True                                 True                                4 /content/drive/MyDrive/BUSI_paper_reproduction/csv/shared_statistical_artifact_audit_cell20.json                                           0
     own_CH_raw                   24                              3                       3                             7                    3                    18               6                6                               0                   0                               True                               True                           True                                 True                                4 /content/drive/MyDrive/BUSI_paper_reproduction/csv/shared_statistical_artifact_audit_cell20.json                                          44
    own_C_ratio                   24                              3                       3                            11                    3                    18               6                6                               0                   0                               True                               True                           True                                 True                                4 /content/drive/MyDrive/BUSI_paper_reproduction/csv/shared_statistical_artifact_audit_cell20.json                                          44
own_CV_extended                   39                              3                       3                             7                    3                    18               6                6                               0                   0                               True                               True                           True                                 True                                4 /content/drive/MyDrive/BUSI_paper_reproduction/csv/shared_statistical_artifact_audit_cell20.json                                          44

## 8. Methodological caveats

### BUSI sample counts
**Status:** Requires dataset-level verification

The paper reports 438 benign and 211 malignant samples. The local dataset inventory may differ because of dataset version, excluded files, image-mask naming, and selection rules. Report the counts actually used by the experiment.

### CH feature definition
**Status:** Explicit independent implementation

CH1-CH8 are defined in this notebook as pixel counts in eight non-overlapping 32-level grayscale bins within the lesion mask. Exact equivalence to the article's implementation remains unverified.

### C feature definition
**Status:** Algebraically audited

The independent pipeline calculates C_i = CH_i / mask_area_px. These ratios should sum to one when the mask is non-empty and all grayscale pixels fall into exactly one histogram bin. This identity does not establish equivalence to the authors' CSV.

### Author CSV correspondence
**Status:** Not authorized by the current gate

Author-derived feature tables must not be considered aligned to the independent dataset by row order alone. A passed gate allows the configured reference experiment, but does not by itself prove that every sample, label, or preprocessing choice matches the paper.

### Train/test protocol
**Status:** Protocol equivalence unverified

The notebook uses the configured stratified holdout and cross-validation workflow. Matching the reported 75:25 split and five-fold wording does not prove identical split membership or identical evaluation nesting.

### Patient-level independence
**Status:** Not established unless verified patient identifiers exist

A sample-level stratified split is not evidence of patient-level independence. Use patient-grouped splitting only when reliable patient identifiers and their mapping are available.

### LASSO classifier
**Status:** Estimator terminology distinction

The notebook's L1-regularized logistic regression classifier is not the same estimator as ordinary Lasso regression. Preserve this distinction when describing the implementation.

### Feature-importance methods
**Status:** Separate interpretation required

Native tree importance, permutation importance, SHAP values, and LIME local weights measure different quantities and should not be reported as interchangeable scores.

### Model artifact verification
**Status:** Reported per artifact

File existence, checksum equality, and prediction round-trip verification are separate checks. A missing checksum or missing round-trip status is reported as unverified, not as passed.

### Clinical interpretation
**Status:** Research-only

Classification based on ground-truth mask-derived features is not equivalent to detecting lesions from unmasked images. This workflow does not establish clinical validity.

## 9. Interpretation

This audit documents which notebook results and artifacts are available and which checks passed. It does not certify exact reproduction of the paper's reported performance.

A valid reproduction claim requires evidence that the sample selection, feature definitions, label mapping, preprocessing, split construction, cross-validation protocol, model configuration, and metric definitions are equivalent to those used in the paper.

For the independent pipeline, the reported classification task must be described in accordance with its inputs: lesion features are extracted using ground-truth masks. The experiment is therefore not equivalent to end-to-end lesion detection from unmasked images.

Model-file existence, checksum verification, and serialization round-trip verification are reported separately. Missing verification evidence is not treated as a successful check.

## 10. Output files

- Final JSON audit: `/content/drive/MyDrive/BUSI_paper_reproduction/final_paper_reproduction_audit.json`
- Model artifact audit: `/content/drive/MyDrive/BUSI_paper_reproduction/csv/model_artifact_verification_audit.csv`
- Experiment audit: `/content/drive/MyDrive/BUSI_paper_reproduction/csv/final_experiment_artifact_audit.csv`
- Paper reference metrics: `/content/drive/MyDrive/BUSI_paper_reproduction/csv/paper_published_reference_metrics.csv`
- Accuracy comparison: `/content/drive/MyDrive/BUSI_paper_reproduction/csv/paper_vs_notebook_accuracy_audit.csv`
- Feature identity audit: `/content/drive/MyDrive/BUSI_paper_reproduction/feature_identity_audit.json`
- XAI artifact audit: `/content/drive/MyDrive/BUSI_paper_reproduction/csv/final_xai_artifact_audit.csv`
- Shared statistical artifact audit: `/content/drive/MyDrive/BUSI_paper_reproduction/csv/shared_statistical_artifact_audit_cell20.json`
- Methodology audit: `/content/drive/MyDrive/BUSI_paper_reproduction/csv/methodology_reproduction_audit.csv`
