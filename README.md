# UCL Online Retail Marketing Analysis

This project analyzes customer growth, retention, customer lifetime value (CLV), segmentation, and product association patterns using the **Online Retail** dataset from the UCI Machine Learning Repository.

The analysis is designed as an end-to-end marketing analytics workflow: starting from transaction-level data cleaning, then building customer/order/month-level analytical tables, and finally applying statistical analysis, machine learning, CLV modeling, segmentation, and association rule mining to generate business insights.

---

## Project Objectives

The project focuses on the following business questions:

1. **Growth diagnosis**  
   Is the business growing, and what factors drive revenue changes over time?

2. **Customer lifecycle analysis**  
   How do customers move between new, retained, reactivated, at-risk, and inactive states?

3. **Retention and churn prediction**  
   Can we predict whether a customer will purchase again in the next month?

4. **Customer Lifetime Value (CLV)**  
   How can future customer value be estimated using both probabilistic models and machine learning?

5. **Customer segmentation**  
   Which customer groups should be prioritized for retention, reactivation, or growth campaigns?

6. **Product association analysis**  
   Which products are frequently purchased together, and how do these patterns differ by customer segment?

---

## Dataset

The original dataset comes from the UCI Machine Learning Repository:

- **Dataset name:** Online Retail
- **Source:** https://archive.ics.uci.edu/dataset/352/online+retail
- **Donor:** Daqing Chen
- **DOI:** https://doi.org/10.24432/C5BW33
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

The dataset contains transactional records from a UK-based non-store online retailer. The transactions cover the period from **1 December 2010 to 9 December 2011**.

### Original Variables

| Column | Description |
|---|---|
| `InvoiceNo` | Invoice number. If it starts with `C`, it indicates a cancelled transaction. |
| `StockCode` | Product/item code. |
| `Description` | Product name. |
| `Quantity` | Quantity of products purchased per transaction line. |
| `InvoiceDate` | Date and time when the transaction was generated. |
| `UnitPrice` | Product price per unit in sterling. |
| `CustomerID` | Unique customer identifier. |
| `Country` | Customer country. |

---

## Repository Structure

```text
UCL-online-retail-marketing-analysis/
│
├── README.md
│
├── code/
│   ├── data_clean.ipynb
│   └── online_retail_analysis.ipynb
│
└── datasets/
    ├── Online Retail.xlsx
    └── df_clean.xlsx
```

### File Descriptions

| File | Description |
|---|---|
| `datasets/Online Retail.xlsx` | Original dataset downloaded from UCI. |
| `datasets/df_clean.xlsx` | Cleaned transaction-level dataset used for analysis. |
| `code/data_clean.ipynb` | Data cleaning notebook. It removes invalid records and creates time-related features. |
| `code/online_retail_analysis.ipynb` | Main analytics notebook for growth, retention, CLV, segmentation, and product analysis. |

---

## Data Cleaning Process

The cleaning workflow is implemented in `code/data_clean.ipynb`.

Main steps include:

1. Load the original Excel dataset.
2. Remove rows without `CustomerID`.
3. Convert `CustomerID` into string format.
4. Identify return/cancelled orders using negative `Quantity` values.
5. Keep only normal purchase records where:
   - `Quantity > 0`
   - `UnitPrice > 0`
6. Create time-based features:
   - `Date`
   - `YearMonth`
   - `Week`
   - `DayOfWeek`
   - `IsWeekend`
   - `Hour`
7. Create `Revenue` as:

```python
Revenue = Quantity * UnitPrice
```

After cleaning, the working dataset contains:

| Metric | Value |
|---|---:|
| Transaction lines | 397,884 |
| Customers | 4,338 |
| Orders | 18,532 |
| SKUs | 3,665 |
| Countries | 37 |
| Date range | 2010-12-01 to 2011-12-09 |
| Total revenue | £8,911,407.90 |

---

## Analysis Workflow

The main analysis is implemented in `code/online_retail_analysis.ipynb`.

### 1. Data Preparation

The notebook builds three core analytical tables:

- **Line-item level:** each product line in an invoice
- **Order level:** aggregated invoice-level revenue, items, SKUs, and country
- **Customer-month panel:** monthly customer activity table used for lifecycle, retention, and churn modeling

---

### 2. Growth Diagnosis

Monthly business performance is analyzed using:

- Revenue
- Number of customers
- Number of orders
- Average order value (AOV)
- Purchase frequency
- Average revenue per user (ARPU)

The notebook decomposes revenue using the formula:

```text
Revenue = Customers × Frequency × AOV
```

This allows monthly revenue changes to be explained by changes in customer base, purchase frequency, and basket value.

---

### 3. Customer Lifecycle Analysis

Customers are classified into lifecycle states based on monthly activity and recency:

| Lifecycle State | Meaning |
|---|---|
| `new` | Customer made their first purchase in the current month. |
| `retained` | Customer purchased in both the previous and current month. |
| `reactivated` | Customer returned after a period of inactivity. |
| `at_risk` | Customer is inactive but still within the calibrated churn window. |
| `inactive` | Customer has exceeded the churn window without another purchase. |

The churn window is calibrated from the empirical interpurchase-gap distribution. In the notebook, the calibrated churn boundary is **103 days**.

---

### 4. Survival Analysis

The project uses survival analysis to estimate how long customers remain active after their first purchase.

Methods used:

- Kaplan-Meier survival curves
- Log-rank test
- Cox Proportional Hazards model

The survival analysis compares customer groups by first-order value and examines how early purchase behavior relates to churn risk.

Key model result:

- The log-rank test shows a statistically significant survival difference between high and low first-order-value customers.
- The Cox model achieves a concordance score of approximately **0.86**, indicating strong ranking performance for churn-risk timing.

---

### 5. Retention Prediction

The project predicts whether a customer will purchase again in the next month using a customer-month modeling dataset.

Models used:

- Logistic Regression
- XGBoost Classifier
- Calibrated XGBoost Classifier

The features include:

- Current-month orders and revenue
- Recency
- Customer age
- Cumulative historical behavior
- Rolling 3-month and 6-month behavior
- Lifecycle state
- Recent behavioral trends

In the test month, XGBoost and calibrated XGBoost outperform logistic regression in F1 score and calibration quality.

---

### 6. Customer Lifetime Value Modeling

The project compares two CLV approaches under a time-based holdout framework:

1. **Probabilistic CLV model**
   - BG/NBD for expected future purchase frequency
   - Gamma-Gamma model for expected average order value

2. **Machine learning model**
   - XGBoost Regressor for future holdout revenue prediction

The CLV evaluation uses a 30-day holdout window and a 180-day forward CLV horizon.

Latest holdout comparison:

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| BG/NBD + Gamma-Gamma | 246.04 | 850.42 | 0.506 |
| XGBoost | 233.75 | 663.10 | 0.700 |

The final production-style scoring table includes:

- Probability alive
- Predicted future orders
- Predicted average order value
- Undiscounted future revenue
- Discounted CLV
- Lifecycle state

---

### 7. Customer Segmentation

The project uses both rule-based segmentation and KMeans clustering.

The final business-facing segmentation uses rule-based labels because they are easier to interpret and act on.

Primary segments include:

| Segment | Description |
|---|---|
| `champions` | High-value, active, and recently engaged customers. |
| `high_value_at_risk` | High-value customers who show signs of inactivity or churn risk. |
| `loyal_mid_value` | Stable medium-value customers with healthy engagement. |
| `growth_potential` | Lower-value but active customers with potential to grow. |
| `mid_value` | Moderate customers that do not strongly fit other groups. |
| `dormant` | Inactive or low-recency customers. |

Customer segment counts:

| Segment | Customers |
|---|---:|
| Dormant | 1,362 |
| High value at risk | 917 |
| Loyal mid value | 700 |
| Champions | 670 |
| Mid value | 243 |
| Growth potential | 175 |

Segment-level revenue summary:

| Segment | Revenue |
|---|---:|
| Champions | £4,245,836.08 |
| High value at risk | £1,978,239.51 |
| Dormant | £632,378.16 |
| Loyal mid value | £487,518.27 |
| Mid value | £104,616.17 |
| Growth potential | £77,464.10 |

---

### 8. Product and Association Rule Analysis

The product analysis focuses on top revenue-generating SKUs and customer segments.

Methods used:

- Top revenue products by customer segment
- Product penetration rate by segment
- Apriori frequent itemset mining
- Association rule mining
- Segment-level product association networks

Association rules are filtered using:

- Minimum support: `0.01`
- Minimum confidence: `0.20`
- Minimum lift: `1.20`

This part of the analysis helps identify product bundles and cross-selling opportunities for different customer segments.

---

## Business Implications

The results can support several marketing decisions:

1. **Retention campaigns**  
   Prioritize `high_value_at_risk` customers because they have strong historical value but show signs of churn risk.

2. **VIP customer management**  
   Maintain engagement with `champions`, who contribute the largest revenue share.

3. **Growth campaigns**  
   Target `growth_potential` and `loyal_mid_value` customers with personalized recommendations and basket-building offers.

4. **Reactivation campaigns**  
   Use lifecycle and churn-risk scores to identify inactive customers who may still be worth re-engaging.

5. **Cross-selling strategy**  
   Use association rules to design product bundles and recommendations by customer segment.

---

## How to Run the Project

### 1. Clone the repository

```bash
git clone https://github.com/longbingjun/UCL-online-retail-marketing-analysis.git
cd UCL-online-retail-marketing-analysis
```

### 2. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost lifelines lifetimes mlxtend networkx openpyxl
```

### 3. Prepare the data

Place the original dataset in the `datasets/` folder:

```text
datasets/Online Retail.xlsx
```

Run:

```text
code/data_clean.ipynb
```

This produces the cleaned dataset:

```text
datasets/df_clean.xlsx
```

### 4. Run the analysis notebook

Run:

```text
code/online_retail_analysis.ipynb
```

> Note: If the notebook reads `df_clean.csv`, either export `df_clean.xlsx` as `df_clean.csv`, or update the loading code to:

```python
df = pd.read_excel("datasets/df_clean.xlsx")
```

---

## Main Python Libraries

- `pandas`
- `numpy`
- `matplotlib`
- `seaborn`
- `scikit-learn`
- `xgboost`
- `lifelines`
- `lifetimes`
- `mlxtend`
- `networkx`
- `openpyxl`

---

## Citation

If using this dataset, please cite the UCI source:

```text
Chen, D. (2015). Online Retail [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33
```

---

## License

The dataset is licensed under **Creative Commons Attribution 4.0 International (CC BY 4.0)**. Please credit the original dataset source when using or adapting the data.

This repository is intended for educational and analytical purposes.
