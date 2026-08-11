# Similarity Analysis on Fraudulent Amazon Reviews

> **Investigating the Impact of AI Technologies on Fraudulent Amazon Reviews Using Similarity Analysis**

---

## Overview

This project examines whether AI-generated or coordinated Amazon reviews can be detected through near-duplicate content analysis. The core observation is that fraudulent or AI-assisted reviews often share repeated phrasing, duplicated titles, and highly similar text patterns. By measuring content similarity at scale, this project builds evidence that similarity-based methods can help flag suspicious review behavior.

The analysis is built on large Amazon review and product metadata datasets processed with **Apache Spark (PySpark)** on **Google Cloud Storage (GCS)**. Similarity detection relies on **MinHash Locality Sensitive Hashing (MinHashLSH)**, a scalable approximation of Jaccard similarity.

---

## Project Goals

- Clean and prepare large-scale Amazon review and product metadata datasets.
- Explore review volume patterns over time and across product categories.
- Identify products and review groups with unusually similar content.
- Detect near-duplicate review titles and review text bodies.
- Compare duplication patterns across time periods to reveal temporal trends.
- Demonstrate that similarity-based methods can surface potentially fraudulent or AI-assisted reviews.

---

## Datasets

| Dataset | Contents |
|---|---|
| **Amazon Reviews** | Review text, ratings, timestamps, helpful votes, verification status, product identifiers |
| **Amazon Product Metadata** | Product titles, categories, pricing, rating counts, average ratings |

Both datasets are stored in Google Cloud Storage and processed with PySpark throughout the project.

---

## Notebook Guide

Run the notebooks **in order**; each notebook depends on the outputs of the one before it.

### 1. `Kireeti_Josyula_Final_Project.ipynb` — Data Ingestion and Cleaning

- Loads raw review and metadata parquet files from GCS.
- Inspects schema and dataset size.
- Cleans reviews by filtering for verified purchases, valid ratings, non-empty text, and deduplication.
- Cleans metadata by dropping unnecessary columns, normalizing price, and removing invalid values.
- Writes cleaned datasets back to GCS for use in all downstream notebooks.

### 2. `Kireeti_Josyula_Final_Project_Notebook_2.ipynb` — Exploratory Data Analysis

- Builds a monthly review timeline and identifies activity peaks and valleys.
- Finds the most-reviewed products and examines their review trends over time.
- Compares review volume across product categories.
- Explores relationships between ratings, prices, and review counts.

### 3. `Kireeti_Josyula_Final_Project_Notebook_3.ipynb` — Category Sampling

- Joins the cleaned review and metadata datasets.
- Filters to a target product category (Books).
- Creates a focused review sample for downstream similarity analysis.
- Saves the category sample to GCS for use in Notebooks 3a–3c.

### 4. `Kireeti_Josyula_Final_Project_Notebook_3a.ipynb` — Review-Title Similarity Analysis

- Tokenizes review titles and removes stop words.
- Converts text to hashed binary feature vectors.
- Applies **MinHashLSH** to find near-duplicate review titles.
- Measures duplication rates across reviews and products.
- Visualizes the duplication distribution within the sample.

### 5. `Kireeti_Josyula_Final_Project_Notebook_3b.ipynb` — Extended Title-Duplication Analysis

- Repeats title similarity detection on the sampled category data.
- Computes per-product duplication summaries for the most-reviewed products.
- Visualizes the fraction of duplicate titles concentrated among top products.

### 6. `Kireeti_Josyula_Final_Project_Notebook_3c.ipynb` — Review-Text Similarity Analysis

- Tokenizes full review text bodies and removes stop words.
- Applies **MinHashLSH** to detect near-duplicate review text pairs.
- Compares duplication rates between older and newer reviews.
- Visualizes duplication trends over time and by product.

---

## Methodology

```
Raw data (GCS)
    │
    ▼
1. Data cleaning & deduplication
    │
    ▼
2. Exploratory analysis (temporal trends, category patterns)
    │
    ▼
3. Category filtering → category sample
    │
    ├──▶ 4. Title tokenization → MinHashLSH → duplicate title detection
    │
    ├──▶ 5. Extended title analysis → per-product duplication summary
    │
    └──▶ 6. Text tokenization → MinHashLSH → duplicate review-text detection
```

**Similarity detection steps:**

1. Tokenize text (review title or body) and remove stop words.
2. Convert token sets to hashed binary feature vectors.
3. Index vectors with **MinHashLSH** to efficiently retrieve candidate near-duplicates.
4. Compute approximate Jaccard similarity for candidate pairs.
5. Aggregate and visualize duplication patterns by product, category, and time period.

---

## Why This Matters

Similarity analysis can surface suspicious review patterns associated with:

- Coordinated review campaigns (multiple reviews sharing identical or near-identical text).
- Spam or bot-generated content.
- AI-assisted fake reviews that produce templated phrasing.
- Repeated copy-paste review submissions.

Detecting these patterns at scale provides a data-driven approach to studying how AI technologies affect the authenticity of Amazon product reviews.

---

## Tools and Technologies

| Tool / Library | Purpose |
|---|---|
| **PySpark** | Distributed data processing |
| **Apache Spark MLlib** | MinHashLSH implementation |
| **Google Cloud Storage** | Data storage and I/O |
| **Pandas** | Local data manipulation and aggregation |
| **Matplotlib / Seaborn** | Visualization |
| **Jupyter Notebooks** | Interactive analysis environment |

---

## Reproducibility

The notebooks expect cleaned parquet datasets stored in GCS. To reproduce the full analysis, run the notebooks in the following order:

1. `Kireeti_Josyula_Final_Project.ipynb`
2. `Kireeti_Josyula_Final_Project_Notebook_2.ipynb`
3. `Kireeti_Josyula_Final_Project_Notebook_3.ipynb`
4. `Kireeti_Josyula_Final_Project_Notebook_3a.ipynb`
5. `Kireeti_Josyula_Final_Project_Notebook_3b.ipynb`
6. `Kireeti_Josyula_Final_Project_Notebook_3c.ipynb`

Each notebook reads from and writes to GCS paths configured within it. Update the GCS bucket paths as needed for your own environment.

---

## Presentation

A final PDF presentation summarizing the project motivation, methodology, and findings is included in the repository:

📄 **[BDCC Final Presentation - Kireeti Josyula.pdf](./BDCC%20Final%20Presentation%20-%20Kireeti%20Josyula.pdf)**
