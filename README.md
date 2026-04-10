# Retail MLOps Pipeline

> End-to-end customer segmentation pipeline built on real transaction data —
> from raw events to scored segments, orchestrated and tracked in production.

---

## Problem Statement

Một retailer có hàng triệu transactions nhưng không biết customers của mình là ai.
Project này build pipeline tự động phân loại **5,878 customers** thành 4 segments
dựa trên hành vi mua hàng thực tế — giúp business đưa ra đúng action cho đúng nhóm khách.

---

## Dataset

- **Source**: [Online Retail II — UCI ML Repository](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci)
- **Raw size**: 1,067,371 transactions | 8 columns
- **After cleaning**: 805,549 rows | 5,878 unique customers
- **Period**: Dec 2009 → Dec 2011

---

## Architecture
```
Raw CSV (Kaggle)
↓
Load & Clean          [Kaggle Notebook]
↓
EDA                   [Kaggle Notebook]
↓
Spark Aggregation     [Databricks — PySpark]
↓
Feature Engineering   [Databricks]
↓
Labeling (RFM)        [Databricks]
↓
Model Training        [Databricks + MLflow]
↓
Batch Scoring         [Databricks]
↓
Monitoring            [Databricks]
```
---

## Pipeline Flow

| Phase | Notebook | Tool | Output |
|---|---|---|---|
| Load & Clean | `01_load_clean` | Pandas | `clean_transactions.parquet` |
| EDA | `02_eda` | Matplotlib | Charts |
| Spark Aggregation | `03_spark_aggregation` | PySpark | `customer_features.parquet` |
| Feature Engineering | `04_feature_engineering` | Pandas/NumPy | `customer_features_engineered.parquet` |
| Labeling | `05_labeling` | RFM Rules | `customer_labeled.parquet` |
| Training | `06_training` | XGBoost + MLflow | Registered Model |
| Scoring | `07_scoring` | MLflow | `customer_segments_scored.csv` |
| Monitoring | `09_monitoring` | Matplotlib | Distribution Report |

---

## Model Results

| Model | Accuracy | F1 Macro |
|---|---|---|
| **XGBoost** | 0.9983 | **0.9984** |
| HistGradientBoosting | 0.9983 | 0.9982 |
| RandomForest | 0.9957 | 0.9959 |

Best model registered to **Databricks Unity Catalog**:
`workspace.default.retail-customer-segmentation v1`

---

## Segmentation Output

| Segment | Count | % | Profile |
|---|---|---|---|
| **Champions** | 1,681 | 28.6% | Mua gần đây, thường xuyên, chi nhiều |
| **Loyal** | 1,422 | 24.2% | Gắn bó lâu dài, ổn định |
| **Potential** | 1,684 | 28.6% | Tiềm năng, cần nurture |
| **At Risk** | 1,091 | 18.6% | Lâu không mua, nguy cơ churn |

---

## Orchestration

Pipeline orchestrated bằng **Databricks Workflows**:

spark_aggregation → feature_engineering → labeling → training → scoring

- End-to-end run: **3 phút 53 giây**
- Có thể schedule chạy tự động hàng ngày
- Full run history + audit trail

---

## Tech Stack

| Layer | Tool |
|---|---|
| Data Processing | PySpark, Pandas |
| Feature Engineering | NumPy, Pandas |
| ML Training | XGBoost, Scikit-learn |
| Experiment Tracking | MLflow |
| Model Registry | Databricks Unity Catalog |
| Orchestration | Databricks Workflows |
| Storage | Databricks Unity Catalog Volumes |
| Notebook Environment | Kaggle, Databricks |

---

## How to Run

### 1. Setup
```bash
git clone https://github.com/lethanhconghcmus-alt/retail-mlops.git
cd retail-mlops
pip install -r requirements.txt
```

### 2. Data
Download dataset từ Kaggle:
[Online Retail II UCI](https://www.kaggle.com/datasets/mashlyn/online-retail-ii-uci)

### 3. Run Pipeline
Chạy toàn bộ pipeline qua **Databricks Workflows**:
`Jobs & Pipelines → retail-mlops-pipeline → Run now`

Hoặc chạy từng notebook theo thứ tự:

03_spark_aggregation
04_feature_engineering
05_labeling
06_training
07_scoring
09_monitoring

---

##  Project Structure

```
retail-mlops/
├── data/
│   └── processed/
│       └── clean_transactions.parquet
├── notebooks/
│   ├── 01_load_clean.ipynb
│   ├── 02_eda.ipynb
│   ├── 03_spark_aggregation.ipynb
│   ├── 04_feature_engineering.ipynb
│   ├── 05_labeling.ipynb
│   ├── 06_training.ipynb
│   ├── 07_scoring.ipynb
│   └── 09_monitoring.ipynb
├── README.md
└── requirements.txt
```
