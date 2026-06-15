# Customer Segmentation — E-Commerce (Online Retail II)

> End-to-end customer segmentation project on transactional e-commerce data : data cleaning, RFM feature engineering enriched with cancel_rate, and K-Means behavioral clustering.

---

## Context

This project analyzes transactional data from a UK-based online retailer selling gift items to B2C and B2B customers across 38 countries (2009–2011).

**Business problem** : the company has no visibility on customer profiles. All customers receive the same marketing communications regardless of their value, loyalty, or satisfaction level.

**Objective** : build actionable customer segments to enable targeted marketing actions.

---

## Dataset

**Source** : [Online Retail II — UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/Online+Retail+II)

| Field | Value |
|-------|-------|
| Transactions (raw) | ~1,000,000 |
| Period | December 2009 – December 2011 |
| Countries | 38 |
| Customers (after cleaning) | ~5,900 |

**Columns :**

| Column | Description |
|--------|-------------|
| `Invoice` | Invoice number — starts with 'C' if cancelled |
| `StockCode` | Product code |
| `Description` | Product name |
| `Quantity` | Units per transaction (negative = cancelled) |
| `InvoiceDate` | Transaction date |
| `Price` | Unit price (GBP) |
| `CustomerID` | Unique customer identifier |
| `Country` | Customer country |

---

## What makes this project different from a standard RFM

Most segmentation projects stop at RFM (Recency, Frequency, Monetary).

This project adds a **cancel_rate** feature — the proportion of orders cancelled per customer.

**Why it matters :**

A customer who cancels 40% of their orders has a fundamentally different profile from a customer with the same RFM but zero cancellations. The first is signaling dissatisfaction or friction in the purchase process. The second is a reliable buyer.

Without cancel_rate, both would fall into the same "dormant" or "at-risk" segment and receive the same reactivation campaign — which is the wrong action for the dissatisfied customer, who needs customer service intervention first.

---

## Project Structure

```
customer-segmentation-ecommerce/
│
├── segmentation_ecommerce_v2.ipynb     # Main notebook — full analysis
├── README.md                            # This file
│
├── data/
│   ├── online_retail_II.csv            # Raw dataset (from UCI)
│   ├── online_retail_cleaned.csv       # Cleaned dataset (output)
│   └── master_customer_segmented.csv   # Enriched customer table + segments
│
└── viz/
    ├── viz_01_countries.png            # Geographic distribution
    ├── viz_02_features.png             # Feature engineering overview
    ├── viz_03_elbow.png                # Elbow + silhouette for k selection
    ├── viz_04_segments.png             # Segment profiles
    └── viz_05_cancel_rate.png          # Cancel rate contribution
```

---

## Methodology

### 1. Data Cleaning

The raw dataset required significant cleaning before any analysis :

| Step | Issue | Action |
|------|-------|--------|
| Missing CustomerID | 22% of rows | Dropped — cannot be attributed to any customer |
| Duplicates | ~5,000 rows | Removed |
| Special StockCodes | Discounts, postage, fees | Extracted as customer-level features before removal |
| Cancelled orders | Negative quantities | Matched to original orders and removed from transaction history |

**Cancelled order logic** : when an order is cancelled, a mirrored row with negative quantity appears. We match each cancellation to its original order, track the cancelled quantity on the original row, then remove the cancellation row. Unmatched cancellations (no counterpart) are flagged as "doubtful" and removed.

---

### 2. Feature Engineering

One row per customer with 7 behavioral variables :

| Variable | Description | Axis |
|----------|-------------|------|
| `Recency` | Days since last purchase | Loyalty |
| `Frequency` | Number of distinct invoices | Loyalty |
| `Monetary` | Total revenue generated | Value |
| `AvgBasket` | Average revenue per transaction | Value |
| `cancel_rate` | Proportion of cancelled orders | Satisfaction |
| `nb_products_distinct` | Number of distinct SKUs purchased | Engagement |
| `CustomerAgeDays` | Days since first purchase | Seniority |

---

### 3. Segmentation

**Algorithm** : K-Means with StandardScaler normalization.

K-Means is sensitive to scale — without normalization, `Monetary` (0–50,000) would dominate the distance calculation over `cancel_rate` (0–1).

**k selection** : evaluated both elbow method and silhouette score for k=2 to k=8. Final choice balances mathematical score with business interpretability.

**4 segments identified :**

| Segment | Profile | Priority |
|---------|---------|----------|
| **Champion** | Recent, frequent, high value, low cancel rate | Protect and reward |
| **Loyal** | Regular buyers, moderate value, low cancel rate | Maintain and upsell |
| **Dormant** | Inactive for 90+ days, previously good profile | Reactivation campaign |
| **Dissatisfied** | High cancel rate regardless of purchase frequency | Customer service first |

---

## Key Recommendations

**Champions** — VIP program, exclusive early access, personalized offers. Do not over-solicit.

**Loyal** — cross-sell complementary products, loyalty rewards, gentle nudge toward higher frequency.

**Dormant** — single reactivation email at 90 days inactivity with an incentive offer. If no response at 180 days, exclude from regular campaigns.

**Dissatisfied** — do NOT send promotional campaigns before resolving the underlying issue. Route to customer service, investigate cancellation reasons, address friction points first.

---

## Tech Stack

```
Python 3.10+
pandas, numpy        — data manipulation
matplotlib, seaborn  — static visualizations
plotly               — interactive charts
scikit-learn         — StandardScaler, KMeans, silhouette_score
```

---

## How to Run

```bash
# 1. Clone the repository
git clone https://github.com/your-username/customer-segmentation-ecommerce.git
cd customer-segmentation-ecommerce

# 2. Install dependencies
pip install pandas numpy matplotlib seaborn plotly scikit-learn jupyter

# 3. Download the dataset from UCI and place it in data/
# https://archive.ics.uci.edu/ml/datasets/Online+Retail+II

# 4. Launch the notebook
jupyter notebook segmentation_ecommerce_v2.ipynb
```

---

## Author

**Nasserine Benchettara**
Data Scientist — Customer Analytics | E-Commerce
PhD in Artificial Intelligence (Paris 13) | 10+ years experience

[LinkedIn](https://linkedin.com) · [Malt](https://malt.fr)

---

*This project is part of a customer analytics portfolio demonstrating end-to-end segmentation skills on real retail data.*
