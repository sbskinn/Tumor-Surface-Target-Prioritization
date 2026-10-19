# Tumor-Surface-Target-Prioritization

## Project objective

Predict gene dependency in lung adenocarcinoma (LUAD) from publicly available
gene-level features.

## Datasets used

Link 1:
Link 2:
Link 3:
Link 4:

## Data dictionaries

### 1. Source dictionary

| Source/file | Purpose | Data used | File status |
| --- | --- | --- | --- |
| `TCGA.LUAD.sampleMap_HiSeqV2.gz` | TCGA LUAD gene expression | Expression values across LUAD samples | Loaded |
| `luad_tcga.tar.gz` | Gene-level copy-number data | CNA/GISTIC calls used to calculate amplification | Loaded |
| `CRISPRGeneEffect.csv` | DepMap Chronos dependency source | Gene-effect scores for cancer cell lines | Loaded |
| `DepMapModel.csv` | DepMap cell-line metadata | Identify and filter matched LUAD cell lines | Loaded |
| `subcellular_location.tsv` | Human Protein Atlas localization source | Identify surface-eligible genes using plasma-membrane annotations | Loaded |
| `LUAD_expression_features.csv` | Derived feature table | Gene-level expression and variability features | Loaded |

### 2. Gene/label dictionary

| Field | Role | Type | Definition | Status |
| --- | --- | --- | --- | --- |
| `gene` | Identifier | String | HGNC-style gene symbol used to align expression, CNA, DepMap, and HPA records | Defined |
| `expression` | Predictor | Numeric | Mean expression across the currently included TCGA LUAD samples; the source may contain both tumor and normal samples | Defined |
| `variability` | Predictor | Numeric | Sample standard deviation of expression across the included TCGA LUAD samples; currently stored as `expression_variability` | Defined |
| `amplification` | Predictor | Numeric | Number of CNA sample calls equal to 2 divided by the number of non-missing calls for each gene | Defined |
| `enrichment` | Predictor | Numeric | Mean tumor expression minus mean normal expression; positive values indicate higher average expression in tumor samples | Defined |
| `surface_level` | Eligibility metadata | Boolean | `True` when the gene symbol appears in HPA records with `Plasma membrane` | Defined |
| `chronos_effect_median` | Intermediate label | Numeric | Median DepMap Chronos effect across matched LUAD cell lines | Defined |
| `dependency_score` | Target label | Numeric | Negative median Chronos effect: `-median(Chronos effect)`; larger values indicate stronger dependency under this convention | Defined |
| `predicted_dependency` | Model output | Numeric | Dependency score predicted by a fitted regression model | Defined |
| `valid_chronos_count` | Label eligibility metadata | Integer | Number of non-missing Chronos measurements among matched LUAD cell lines | Defined |
| `label_status` | Label eligibility metadata | Categorical | Indicates whether a gene receives a label; labeled when `valid_chronos_count >= 3` | Defined |

### 3. Split dictionary

| Variable | Current value or definition |
| --- | --- |
| `split_unit` | Gene; each gene appears in exactly one partition |
| `target_variable` | `dependency_score` |
| `requested_total_genes` | 2,000 labeled genes |
| `training_proportion` | 0.70 (70%) |
| `validation_proportion` | 0.15 (15%) |
| `test_proportion` | 0.15 (15%) |
| `random_state` | 42 |
| `stratification_variable` | `surface_level` |
| `training_size` | 1,400 genes |
| `validation_size` | 300 genes |
| `test_size` | 300 genes |
| `training_surface_count` | 175 |
| `validation_surface_count` | 38 |
| `test_surface_count` | 37 |
| `saved_split_file` | `gene_splits.json` |
| `overlap_check` | Verify that no gene occurs in more than one split |
| `preprocessing_rule` | Fit preprocessing steps on training data only, then apply them unchanged to validation and test data |
| `subgroup_evaluation` | Evaluate performance overall and for `surface_level == True` genes |
| `candidate_goal` | Evaluate and rank 20 surface-level candidates from the held-out test set |
