# 📊 Retail Growth & Customer Value Analysis

**business growth diagnosis, customer value modeling, and product bundling strategy** using transactional data.

---

## 🚀 Project Overview

This project analyzes an **e-commerce retail dataset (2010–2011)** to answer three core business questions:

1. **Is the business growing? What drives the growth?**
2. **Who are the most valuable customers (now & in the future)?**
3. **How can we optimize product combinations to increase revenue?**

To address these, the project is structured into three analytical modules:

* 📈 Business Growth & Revenue Analysis
* 👥 Customer Segmentation & CLV Modeling
* 🛍️ Market Basket Analysis (Product Bundling)

---

## 📂 Dataset

* Source: Online Retail Dataset
* Granularity: Transaction-level (Invoice-item level)
* Time Range: Dec 2010 – Dec 2011

**Key Fields:**

* `CustomerID`, `InvoiceNo`, `InvoiceDate`
* `StockCode`, `Quantity`, `UnitPrice`
* `Revenue = Quantity × UnitPrice`

---

## 🧹 Data Preprocessing

* Removed:

  * Cancelled orders (`InvoiceNo` starts with "C")
  * Invalid values (`Quantity <= 0`, `UnitPrice <= 0`)
* Feature Engineering:

  * Revenue calculation
  * Time features (`YearMonth`, `Week`, `Hour`, `IsWeekend`)

---

# 📈 1. Business Growth Analysis

## 1.1 Core Metrics

* Order Volume
* Revenue
* Average Order Value (AOV)
* Month-over-Month Growth (MoM)

### 🔍 Key Findings

* Revenue shows **stable early performance + rapid growth in Q4**
* Peak occurs in **Nov 2011 (Black Friday & Christmas promotion)**
* AOV fluctuates but **does not drive growth**

👉 **Conclusion:**

> Revenue growth is primarily driven by **increasing order volume**, not higher spending per order.

---

## 1.2 Customer Growth & Retention

Metrics:

* Monthly New Users
* Monthly Active Users
* Retention Rate

### 🔍 Key Findings

* **H1 (Jan–Aug):**

  * New users ↓
  * Retention ↑
  * Growth relies on existing users

* **H2 (Sep–Nov):**

  * New users ↑
  * Active users ↑↑
  * Retention ↓

👉 **Conclusion:**

> Growth shifts from **retention-driven → acquisition-driven**, especially during peak season.

---

## 1.3 Revenue Decomposition

Revenue is decomposed as:

> Revenue = Users × Orders per User × AOV

### 🔍 Insights

* Users ↑ → ✅ **Main driver**
* Orders per user → ~stable
* AOV → fluctuating

👉 **Core Insight:**

> Business growth is **user-driven**, not monetization-driven.

---

## 1.4 Time Series Forecasting

* Model: **Holt Exponential Smoothing**
* Granularity: Weekly

**Performance:**

* MAPE ≈ **10.7%**

### ⚠️ Limitation

* Underestimates peak season (holiday spikes)

👉 **Conclusion:**

> Model captures trend well but struggles with **seasonality & sudden demand surges**.

---

# 👥 2. Customer Value Analysis (RFM + CLV)

## 2.1 RFM Segmentation (Current Value)

* **Recency** → How recent the purchase
* **Frequency** → Purchase frequency
* **Monetary** → Total spending

### 🎯 Output

Customers are segmented into:

* High-value customers
* Potential customers
* Low-activity customers

👉 Answers:
**“Who are the most valuable customers today?”**

---

## 2.2 CLV Modeling (Future Value)

### 🔵 Method 1: Probabilistic Model

* BG/NBD → Predict purchase frequency
* Gamma-Gamma → Predict monetary value

👉 CLV = Expected Frequency × Expected Value

---

### 🟢 Method 2: Machine Learning (XGBoost)

* Features:

  * RFM metrics
  * AOV, purchase interval
  * Product diversity
* Target:

  * Future revenue (30/60/90 days)

---

## ⚖️ Model Comparison

| Model             | R²    |
| ----------------- | ----- |
| Probabilistic CLV | ~0.50 |
| XGBoost           | ~0.28 |

### 🔍 Key Insight

> Probabilistic CLV significantly outperforms ML model in this dataset.

### 🧠 Interpretation

* Data is **highly skewed (long-tail)**
* Limited feature space
* Transaction behavior fits probabilistic assumptions

👉 **Conclusion:**

> BG/NBD + Gamma-Gamma is a **strong baseline for transaction-only data**.

---

# 🛍️ 3. Market Basket Analysis

## 3.1 Methods

* Apriori
* FP-Growth

## 3.2 Metrics

* Support
* Confidence
* Lift

---

## 3.3 Key Findings

### 1️⃣ Same-Series Products

* Customers buy variations of similar items

### 2️⃣ Complementary Products ⭐（Most Valuable）

* Example: Gift bag → Wrapping paper

👉 Strong **usage-based association**

### 3️⃣ Scenario-Based Purchasing

* Customers buy full sets (e.g., tableware)

---

## 💼 Business Applications

* Product bundling
* Cross-selling recommendations
* Promotion design
* Store layout optimization

---

# 🧠 Final Business Insights

* Growth is **user acquisition driven**
* Existing users contribute most revenue
* Customer quality decreases during high acquisition periods
* Monetization (AOV, frequency) is under-optimized
* Complementary product bundling offers strong revenue potential

---

# 🛠️ Tech Stack

* Python
* Pandas / NumPy
* Scikit-learn
* XGBoost
* Lifetimes (CLV)
* Statsmodels (Holt Model)
* mlxtend (Apriori / FP-Growth)

---

# 📁 Project Structure

```bash
├── data/
│   ├── online_retail.xlsx
│   └── df_clean.xlsx
├── notebooks/
│   ├── data_clean.ipynb
│   ├── RFM_CLV.ipynb
│   ├── associateRules.ipynb
│   └── TimeAggregated.ipynb
├── README.md
```

---

# 🚀 How to Run

```bash
git clone <your-repo-url>
cd project

pip install pandas numpy matplotlib statsmodels lifetimes xgboost mlxtend

jupyter notebook
```

---

# 📌 Future Improvements

* Multi-year data → better seasonality modeling
* Advanced forecasting (SARIMA / Prophet)
* Add behavioral & channel features to improve CLV
* Build recommendation system from association rules

---

# 🎯 Takeaway

> This project demonstrates how to move from **raw transaction data → business insights → actionable strategies** by combining:

* Time series analysis
* Customer behavior modeling
* Revenue decomposition
* Association rule mining

---
