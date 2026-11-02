# 🛒 E-Commerce Customer Segmentation: RFM, K-Means, DBSCAN, PCA & t-SNE

Segmenting the customers of a UK-based online gift retailer using **RFM analysis** (Recency, Frequency, Monetary), comparing **K-Means** and **DBSCAN**, reducing dimensions with **PCA**, visualising segments with **t-SNE**, and turning each cluster into an actionable **marketing persona**.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-orange)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626)

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [Dataset](#-dataset)
- [Methodology](#-methodology)
- [Key Results](#-key-results)
- [Marketing Personas](#-marketing-personas)
- [Repository Structure](#-repository-structure)
- [How to Run](#-how-to-run)
- [Tech Stack](#-tech-stack)
- [Limitations & Next Steps](#-limitations--next-steps)
- [Author](#-author)

---

## 🎯 Project Overview

Marketing budgets are wasted when every customer gets the same message. This project groups customers by purchasing behaviour so that each group can be targeted differently.

**Goals**
1. Build RFM features from raw transaction data
2. Cluster customers with K-Means and DBSCAN and compare them
3. Use PCA and t-SNE to visualise and interpret the clusters
4. Convert clusters into named personas with recommended marketing actions

---

## 📂 Dataset

**Online Retail Dataset** (UCI Machine Learning Repository)
All transactions between 01/12/2010 and 09/12/2011 for a UK-registered, non-store online retailer (541,909 rows, 8 columns).

| Column | Description |
|---|---|
| `InvoiceNo` | Invoice number (starts with **C** for cancellations) |
| `StockCode` | Product code |
| `Description` | Product name |
| `Quantity` | Units per transaction |
| `InvoiceDate` | Date and time of the transaction |
| `UnitPrice` | Price per unit (£) |
| `CustomerID` | Unique customer identifier |
| `Country` | Customer country |

### 🔗 Dataset Link

> **Download the dataset here:** `PASTE_YOUR_DATASET_LINK_HERE`

Original source: <https://archive.ics.uci.edu/dataset/352/online+retail>

After downloading, place the file in the project root as **`Online_Retail.xlsx`** (the dataset is not stored in this repo because of its size).

---

## 🧪 Methodology

| Step | What was done |
|---|---|
| 1. Cleaning | Dropped missing `CustomerID`, duplicates, cancellations, and rows with `Quantity <= 0` or `UnitPrice <= 0` → **4,338 customers** |
| 2. RFM features | **Recency** = days since last purchase, **Frequency** = number of distinct invoices, **Monetary** = total spend |
| 3. Preprocessing | `log1p` transform (skew of Monetary: 19.3 → 0.4) followed by `StandardScaler` |
| 4. K-Means | Tested k = 2–10 using Elbow, Silhouette, Davies-Bouldin and Calinski-Harabasz → chose **k = 4** |
| 5. DBSCAN | k-distance plot + grid search over `eps` and `min_samples` → chose **eps = 0.4, min_samples = 20** |
| 6. PCA | 2 components retain **93.9%** of variance; loadings interpreted |
| 7. t-SNE | Perplexity sensitivity check (10, 30, 60), final map at perplexity 30 |
| 8. Comparison | Adjusted Rand Index and cross-tab between K-Means and DBSCAN |
| 9. Personas | Cluster profiling in original units, rule-based persona naming, marketing actions and KPIs |

---

## 📊 Key Results

| Method | Setting | Outcome |
|---|---|---|
| **K-Means** | k = 4 | Silhouette 0.337, Davies-Bouldin 1.011 |
| **DBSCAN** | eps = 0.4, min_samples = 20 | 3 clusters, 7% noise, silhouette 0.186 |
| **PCA** | 2 components | PC1 = customer value axis (75%), PC2 = recency / staleness axis (19%) |
| **t-SNE** | perplexity 30 | Customers form a continuous gradient of value rather than isolated islands |
| **K-Means vs DBSCAN** | non-noise points | ARI ≈ 0.34 (partial agreement) |

**Main takeaways**
- **K-Means is the better segmenter** for RFM data. The customer base is a continuum with no clear density gaps, so DBSCAN struggles and is very sensitive to `eps`.
- **DBSCAN works well as an outlier detector.** Its noise group is dominated by very high-spend accounts, which are useful for key-account review.
- **Revenue is highly concentrated.** The top 10% of customers generate about 61% of revenue.

---

## 👥 Marketing Personas

| Persona | Customers | Revenue share | Typical customer | Strategy |
|---|---|---|---|---|
| 🏆 **Champions** | 16% | 65% | Last bought 8 days ago, 10 orders, about £3.7k | Retain and reward: VIP / trade programme, account manager, referrals |
| 🌱 **Potential Loyalists** | 27% | 24% | 55 days, 4 orders, about £1.3k | Increase frequency: bundles, replenishment reminders, spend-threshold rewards |
| 🆕 **New & Recent Shoppers** | 19% | 5% | 17 days, 2 orders, about £470 | Convert to repeat buyers: welcome journey, time-limited second-purchase offer |
| 💤 **Hibernating / Lapsed** | 37% | 6% | 177 days, 1 order, about £300 | Low-cost win-back sequence, cap spend on non-responders |

Full persona cards with goals, actions and KPIs are in the notebook (Section 11).

---

## 🗂 Repository Structure

```
.
├── Customer_Segmentation_RFM.ipynb   # Main notebook (with outputs)
├── requirements.txt                  # Python dependencies
├── .gitignore
├── README.md
├── Online_Retail.xlsx                # NOT included, download from the dataset link above
├── images/                           # Figures generated by the notebook
└── outputs/                          # CSVs generated by the notebook
    ├── customer_segments.csv         #   per-customer RFM, cluster labels, persona, PCA & t-SNE coordinates
    ├── persona_summary.csv           #   persona-level summary table
    └── kmeans_model_selection.csv    #   metrics for k = 2..10
```

---

## 🚀 How to Run

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>

# 2. (Optional) create a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Download the dataset (see link above) and place it here as Online_Retail.xlsx

# 5. Launch the notebook
jupyter notebook Customer_Segmentation_RFM.ipynb
```

> The first run takes about 30 seconds longer because the Excel file is read and cached as a CSV. t-SNE adds roughly a minute.

---

## 🛠 Tech Stack

Python · pandas · NumPy · scikit-learn · matplotlib · seaborn · Jupyter

---

## ⚠️ Limitations & Next Steps

- RFM ignores *what* customers buy. Adding product-category mix, basket size, return rate and tenure would enrich the segments.
- The data covers about one year, so Q4 gifting seasonality affects Recency. Re-run on rolling windows.
- Validate personas with an **A/B test**, for example a win-back offer versus a control group for Hibernating customers.
- Try Gaussian Mixture Models or HDBSCAN for soft or variable-density clusters, and measure cluster stability with bootstrapped ARI.
- Productionise: score new customers monthly using the saved scaler and K-Means model.

---

⭐ If you found this project useful, consider giving the repo a star!
