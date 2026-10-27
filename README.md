# Next-Day Extreme Weather Warning with PySpark: NOAA GSOD 2020 to 2023

**Module:** 7006SCN Machine Learning and Big Data (Coventry University)
**Author:** Perli Sri Chaitanya (SID:)
**Tableau Public workbook:** 

## 1. Problem
Predict whether a weather station will record an **extreme weather day tomorrow**, using only today's and recent observations.
"Extreme" = any of: heat (MAX ≥ 95 °F), cold (MIN ≤ −4 °F), heavy precipitation (≥ 1 in), gale wind (sustained ≥ 34 kn or gust ≥ 50 kn), or convective storm (hail / thunder / tornado flag).
This is a binary, imbalanced classification problem with a time-based split (train 2020–2022, test 2023).

## 2. Dataset
| Item | Value |
| Source | NOAA NCEI Global Surface Summary of the Day (GSOD), https://www.ncei.noaa.gov/products/land-based-station/global-surface-summary-of-the-day |
| Download | `https://www.ncei.noaa.gov/data/global-summary-of-the-day/archive/{year}.tar.gz` |
| Years | 2020, 2021, 2022, 2023 |
| Size | 16,179,201 million rows, 29 raw columns, 3.50 GB CSV in 49,204 files |
| Licence | U.S. Government work no copyright restrictions in the U.S., NOAA NCEI asks for attribution |
| Personal data | None (station level environmental measurements) |
| Not from Kaggle / not a teaching dataset

## 3. Repository structure
```
notebooks/
  Task1.ipynb   Problem & dataset: SparkSession, download, file-size check, schema, CSV to Parquet
  Task2.ipynb   Data engineering: sentinels to null, cleaning, window features, label, ML Pipeline
  Task3.ipynb   Four models: LR, Decision Tree, Random Forest, GBT — baseline, CrossValidator, test
  Task4.ipynb   Distributed computing: Spark UI, config, shuffle/repartition/caching experiments, imbalance, scalability
  Task5.ipynb   Evaluation: metrics, confusion matrices, ROC/PR, perturbation + retraining stability, fairness, SHAP
  Task6.ipynb   Tableau extracts for the four dashboards
README.md
```

## 4. How to run (Google Colab)
Run the notebooks **in order**; each one reads the previous one's outputs.
1. Open each notebook in Google Colab and use **Runtime to Run all**.
2. The first cell mounts Google Drive and starts Spark in local mode on all Colab cores; outputs are saved to `MyDrive/7006SCN_GSOD/`.
3. Task 1 downloads the four yearly archives from NOAA and extracts the CSVs to Colab's local disk, so no manual download is needed.

## 5. Pipeline
```mermaid
flowchart LR
  A[NOAA yearly tar.gz] to B[CSV files]
  B -->|explicit string schema, try_cast| C[Parquet partitioned by YEAR]
  C --> D[Sentinels -> null, range checks, dropDuplicates]
  D -->|repartition by STATION| E[Window features: lag, 3-day rolling, pressure tendency]
  E --> F[Label = tomorrow is extreme]
  F --> G[Feature table Parquet]
  G --> H[Pipeline: StringIndexer -> OneHotEncoder -> Imputer -> VectorAssembler -> StandardScaler]
  H --> I[LR / DT / RF / GBT + CrossValidator, class weights, PR-AUC]
  I --> J[Evaluation, stability, SHAP]
  J --> K[Tableau extracts -> 4 dashboards]
```

## 6. Results (fill in from Task 3 / Task 5)
| Model | Key hyperparameters | Train time (s) | PR-AUC | ROC-AUC | Recall | Precision | F1 |
|---|---|---|---|---|---|---|---|
| Persistence (naive) | – | 0 | – | – | | | |
| Logistic Regression | | | | | | | |
| Decision Tree | | | | | | | |
| Random Forest | | | | | | | |
| GBT | | | | | | | |

## 7. Version control log
Each commit names the task, what changed, and the main PySpark method used, for example:
"task2: convert sentinel codes to null (when/otherwise)".

## 8. AI use
Notebook code was generated with Claude (Anthropic) and then run, checked and adapted by me; Claude was also used for debugging and to draft documentation. See the AI-use declaration in the report.

## 9. References
NOAA NCEI (2024) *Global Surface Summary of the Day GSOD*. Apache Spark (2024) *MLlib guide*. Lundberg & Lee (2017) *A unified approach to interpreting model predictions*, NeurIPS.
