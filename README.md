# Tumor-Surface-Target-Prioritization

## Team Members
Caroline Nutter

Joanna Land 

Skyler Skinner

Nadia Alexander


## Project Objective

1. Reproduced the public paper analysis from "TROP2 targeting reveals therapy-driven cell state dynamics in colorectal cancer."
2. Predict gene dependency in Lung adenocarcinoma (LUAD) using publicly available data by training supervised regression models.

## GitHub Repository Link
Repository URL: https://github.com/sbskinn/Tumor-Surface-Target-Prioritization
GitHub Hash: [insert hash]
This repository is public.

## Repository and ZIP File Layout / Location

The repository is organized into the following directories:


```text
Tumor-Surface-Target-Prioritization/
├── README.md
├── requirements.txt
├── Checkpoint_1/
│   ├── Checkpoin1_gene_expression.ipynb       # builds gene table, labels, and splits
│   ├── Checkpoint1_gradient_boosting_model.ipynb
│   ├── Checkpoint1_ridge_model.ipynb
│   ├── CPSC 4430 Checkpoint 1 Paper.pdf
│   └── CPSC6430_T4_Checkpoint1.pptx
│
├── Checkpoint_2/
│   ├── Checkpoint2_model_reproduction.ipynb   # paper reproduction
│   ├── reproduced_results/                    # images of reproduction figures
│   │   ├── cell_state_proportions_table.png
│   │   ├── donor_proportions_stacked_bar.png
│   │   ├── gene_expression_table.png
│   │   ├── umap_expression_and_clusters.png
│   │   └── violin_expression_by_state.png
│   ├── model_reproduction_data/               # large txt input files and provided python notebook
│   │   ├── biobank_subcut_epi_counts.txt
│   │   ├── biobank_subcut_epi_counts_log1p.txt
│   │   ├── biobank_subcut_epi_features.txt
│   │   ├── biobank_subcut_epi_metadata.txt
│   │   ├── biobank_subcut_epi_umap.txt
│   │   ├── biobank_subcut_full_counts.txt
│   │   ├── biobank_subcut_full_counts_log1p.txt
│   │   ├── biobank_subcut_full_features.txt
│   │   ├── biobank_subcut_full_metadata.txt
│   │   ├── biobank_subcut_full_umap.txt
│   │   ├── E-MTAB-16585.idf.txt
│   │   ├── E-MTAB-16585.sdrf.txt
│   │   ├── get_data_from_arrayExpress.ipynb
│   │   ├── HTO_library.txt
│   │   └── sample_metadata.txt
│   ├── Checkpoint2_model_evaluation.ipynb     # training, evaluation, splits, models, metrics
│   └── top20_surface_genes/                   # held-out surface-candidate ranking
│
└── submission/ (ZIP root)                     # ZIP submission contents
    ├── Checkpoint 2 Paper.pdf
    ├── Checkpoint2 Presentation.pptx
    └── execution_video.mp4
```

## Environment Setup 
This project is developed in Jupyter Notebook. The notebook should be run using a Python environment with the required dependencies installed and access to the project datasets in the expected local directory. 

The following libraries and platforms are required to run the notebook:
- Anaconda (Preferred for downloading python libraries)
- Python
- Jupyter Notebook
- pandas
- Pathlib
- Tarfile
- Scikit-learn
- json

## Video Demonstration
https://drive.google.com/file/d/1g9J9SCTpqyrAEpJL_EBnHXkn63rYFqQM/view?usp=sharing
  
## Public Data Access Log
This project uses publicly available datasets to construct gene-level features, derive LUAD gene-dependency labels, and identify genes associated with plasma-membrane localization. The files are stored locally in the Jupyter notebooks. 

Link 1: https://xenabrowser.net/datapages/?dataset=TCGA.LUAD.sampleMap%2FHiSeqV2&host=https%3A%2F%2Ftcga.xenahubs.net&removeHub=https%3A%2F%2Fxena.treehouse.gi.ucsc.edu%3A443 

Link 2: https://www.cbioportal.org/study/summary?id=luad_tcga&utm_source 

Link 3: https://depmap.org/portal/data_page/?utm_source=tab%3DallData&tab=allData 

Link 4: https://www.proteinatlas.org/humanproteome/subcellular/data#locations 

Link 5 (reproduced data: E-MTAB-1685): https://www.nature.com/articles/s41586-026-10705-2#citeas

## Data Dictionaries

### 1. Source Dictionary

| Source/file | Purpose | Data used | File status |
| --- | --- | --- | --- |
| `TCGA.LUAD.sampleMap_HiSeqV2.gz` | TCGA LUAD gene expression | Expression values across LUAD samples | Loaded |
| `luad_tcga.tar.gz` | Gene-level copy-number data | CNA/GISTIC calls used to calculate amplification | Loaded |
| `CRISPRGeneEffect.csv` | DepMap Chronos dependency source | Gene-effect scores for cancer cell lines | Loaded |
| `DepMapModel.csv` | DepMap cell-line metadata | Identify and filter matched LUAD cell lines | Loaded |
| `subcellular_location.tsv` | Human Protein Atlas localization source | Identify surface-eligible genes using plasma-membrane annotations | Loaded |
| `LUAD_expression_features.csv` | Derived feature table | Gene-level expression and variability features | Loaded |

### 2. Gene/Label Dictionary

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

### 3. Split Dictionary

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

Planned Sections:
- Load public expression, copy-number, and dependency data
- Clean and merge datasets into one row per gene
- Create input feature matrix X and dependency target y
- Validation and test-splits
- Train the constant and ridge baselines 
- Compare models with MAE and Spearman correlation

## Evaluation Plan
- 70/15/15 split
  - 70% training
  - 15% validation
  - 15% testing
- 2000 labeled genes
  - Training: 1400 genes 
  - Validation: 300 genes
  - Testing: 300 genes
- Evaluate predictions with MAE and Spearman Correlation both overall and for surface candidates
- Surface-Stratified Splits: keep representation of surface-eligible genes across each section (training, validation, and testing)
- 20 test surface candidates: show the evaluation of 20 surface candidates
- Plan one model improvement and compare with and without amplification features

## Methods Summary
These four models were implemented: Constant Baseline, Ridge Regression, Random Forest, and Gradient Boosting. Mean Absolute Error (MAE) and Spearman correlation were both used to evaluate models specifically on surface associated genes. The goal of the project is to rank the top 20 surface associated genes using the chosen model, Gradient Boosting. Four features were used to examine gene expression: expression, enrichment, expression variability, and amplification frequency. To create a ranking for the genes with the highest dependence score for cancer growth (Checkpoint 1), a total of five files were cleaned and merged together to create a dataset with four features and 1 target. The sources of the datasets are titled LUAD_expression_features.csv, luad_tcga.tar.gz, data_cna.txt, CRISPRGeneEffect.csv, subcellular_location.tsv, model.csv were taken from 4 different sources provided in the Github Repository README. These files were processed to create a gene-level dataset containing 2,000 genes, four predictive features, and one target variable. Before implementing each model the data was split into three ways, where 70% of the data was used for training (1,400 genes), 15% for validation (300 genes), and 15% (300 genes) was used to evaluate each model. The training set was used to fit each model, while the validation set was used to compare model performance. The four models were then developed using the training data and improved with the validation data. Each model was developed to prioritize Spearman correlation since our objective was to rank to surface genes. The constant baseline predicted average dependency. Ridge regression replaced blank features with the median and standardized the features. Random forest found that the max tree depth of 5 produced the lowest MAE value. Gradient boosting resulted in the best training Spearman correlation.

## Expected Output
The expected output is that gradient boosting has the best Spearman correlation. This model was then used to test the test data and the results are an overall Spearman value of 0.4228, surface Spearman value of 0.2913, overall MAE value of 0.2209, and surface MAE of 0.1850. The overall Spearman correlation of 0.4228 indicates a moderate ability to rank genes based on their dependency scores.

### Gradient Boosting Test Results
| Subset | Result |
|---|---:|
| Overall MAE | 0.2209 |
| Overall Spearman Correlation | 0.4228 |
| Surface MAE | 0.1850 |
| Surface Spearman Correlation | 0.2913 |

## Run Time:

## Known Limitations:
- Four features were used to predict gene dependency which won't fully capture the biological complexity of cancer cell growth
- Gene expression and amplification data were obtained from TCGA patient samples, while dependency scores came from DepMap cancer cell lines
- Only 250 out of 2,000 genes in the dataset were surface-associated, with 38 included in the test set
- Gradient Boosting achieved an overall test Spearman correlation of 0.4228 and a surface-specific Spearman correlation of 0.2913



