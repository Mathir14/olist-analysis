# Olist Marketplace Performance Analysis

An end-to-end data analytics project analyzing the Brazilian Olist e-commerce marketplace. This project builds a complete analytics pipeline—from raw CSV ingestion into PostgreSQL, through data profiling and normalization, to statistical analysis, business insights, and an interactive Metabase dashboard.

The objective is not merely to describe historical performance, but to identify **what drives marketplace revenue, delivery performance, and customer satisfaction** using data-driven analysis.

---

## Dashboard Preview

### Seller Performance
![Seller Dashboard](dashboard/tab1.png)

### Delivery Performance
![Delivery Dashboard](dashboard/tab2.png)

### Customer Satisfaction
![Customer Dashboard](dashboard/tab3.png)

---

# Business Questions

This project investigates three major areas of marketplace performance.

## 1. Seller Performance

- How concentrated is marketplace revenue?
- Do high-performing sellers simply receive more orders?
- How unequal is seller revenue distribution?

---

## 2. Delivery Performance

- Which product categories experience the slowest deliveries?
- Which categories have the highest late-delivery rates?
- Is slower delivery associated with unreliable delivery?

---

## 3. Customer Satisfaction

- Does delivery speed influence customer reviews?
- Are late deliveries associated with poor ratings?
- Which delivery metric matters more to customer satisfaction?

---

# Dataset

**Source**

https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

The dataset contains transactional records from the Brazilian Olist marketplace between 2016 and 2018.

## Tables Used

| Table | Description |
|--------|-------------|
| customers | Customer information |
| sellers | Seller information |
| products | Product metadata |
| orders | Order lifecycle |
| order_items | Items purchased |
| payments | Payment information |
| reviews | Customer reviews |
| geolocation | Geographic information |
| category_translation | Portuguese → English category mapping |

---

# Tech Stack

- PostgreSQL 17
- Polars
- SQLAlchemy
- SciPy
- NumPy
- Matplotlib
- Metabase
- Jupyter Notebook

---

# Project Pipeline

```
CSV Files
      │
      ▼
PostgreSQL (raw schema)
      │
      ▼
Data Profiling
      │
      ▼
Normalization
(clean schema)
      │
      ▼
Feature Engineering
      │
      ▼
Analytics Tables
(analytics schema)
      │
      ▼
Statistical Analysis
      │
      ▼
Metabase Dashboard
```

---

# Data Preparation

The project follows a relational warehouse approach rather than performing analysis directly on CSV files.

## 1. Data Ingestion

- Imported all CSV datasets into PostgreSQL
- Created separate schemas for raw, clean, and analytics data
- Preserved original source tables

---

## 2. Data Profiling

Performed comprehensive profiling of every dataset, including:

- Missing values
- Duplicate detection
- Primary key validation
- Foreign key validation
- Timestamp anomalies
- Cardinality analysis
- Distribution analysis

Several inconsistencies were identified, including timestamp ordering anomalies and incomplete product metadata.

---

## 3. Data Normalization

The raw tables were normalized before analysis.

Examples include:

- Category translation lookup
- Product normalization
- Customer normalization
- Seller normalization

Derived analytics tables were then created specifically for dashboard consumption.

---

# Analysis

## 1. Seller Performance

This section investigates revenue concentration across sellers.

### Metrics

- Seller revenue
- Order volume
- Average order value
- Average selling price
- Items sold

### Statistical Analysis

- Pearson Correlation
- Spearman Correlation
- Lorenz Curve
- Gini Coefficient

### Key Findings

- Revenue is highly concentrated among a small number of sellers.
- Order volume is the strongest predictor of seller revenue.
- Seller revenue distribution is highly unequal.

### Results

| Metric | Value |
|---------|------:|
| Pearson Correlation | 0.7997 |
| Spearman Correlation | 0.8526 |
| Gini Coefficient | 0.7915 |
| Top 5% Seller Revenue | 53% |
| Top 20% Seller Revenue | 83% |

---

## 2. Delivery Performance

This section evaluates operational efficiency across product categories.

### Metrics

- Average delivery time
- Late delivery percentage

### Analysis

Each category was compared using:

- Average delivery duration
- Percentage of late deliveries
- Delivery speed vs reliability

### Key Findings

- Office furniture experiences both the slowest deliveries and the highest late-delivery rate.
- Most categories cluster around 11–13 days average delivery time.
- Categories with faster delivery generally exhibit lower late-delivery rates.

---

## 3. Customer Satisfaction

This section studies the relationship between logistics and review scores.

### Metrics

- Median delivery time
- Late-delivery percentage
- Review score

### Statistical Tests

- Kruskal–Wallis Test
- Mann–Whitney U Test

### Key Findings

- Higher review scores are associated with faster deliveries.
- Late deliveries are substantially more likely to receive poor ratings.
- Delivery reliability appears to influence customer satisfaction more strongly than small differences in delivery speed.

---

# Dashboard

The final dashboard was developed in **Metabase** using derived analytics tables.

## Tab 1 — Seller Performance

Includes

- Revenue vs Order Volume
- Lorenz Curve
- Business insights

---

## Tab 2 — Delivery Performance

Includes

- Average Delivery Time by Category
- Late Delivery Rate by Category
- Delivery Speed vs Reliability

---

## Tab 3 — Customer Satisfaction

Includes

- Delivery Time vs Review Score
- Late Delivery Rate vs Review Score

---

# Repository Structure

```
olist-marketplace-analysis/

│
├── raw/
│   └── CSV datasets
│
├── data_preparation/
│   ├── Data ingestion
│   ├── Data profiling
│   └── Data normalization
│
├── data_analysis/
│   ├── Seller performance
│   ├── Delivery performance
│   └── Customer satisfaction
│
├── plots/
│
├── dashboard/
│
├── requirements.txt
│
└── README.md
```

---

# Key Insights

### Marketplace

- Revenue follows a strong Pareto distribution.
- A small proportion of sellers generate the majority of marketplace revenue.

### Operations

- Delivery performance varies considerably across categories.
- Office furniture consistently underperforms operationally.

### Customer Experience

- Faster deliveries correspond with higher review scores.
- Reliable delivery has a stronger relationship with customer satisfaction than marginal improvements in delivery speed.

---

# Limitations

- Analysis is limited to historical Olist marketplace data.
- External factors such as promotions, seasonality, and marketing campaigns were not included.
- Correlation should not be interpreted as causation.

---

# Future Improvements

- Build predictive models for delivery delays.
- Forecast seller revenue using historical trends.
- Incorporate customer lifetime value analysis.
- Perform geographic delivery optimization.
- Deploy an interactive analytics application.

---

# Skills Demonstrated

- PostgreSQL
- SQL
- Data Profiling
- Data Cleaning
- Data Normalization
- Polars
- Statistical Analysis
- Hypothesis Testing
- Exploratory Data Analysis
- Business Intelligence
- Metabase Dashboard Development
- Data Visualization

---

## Author

**Mathir**

Bachelor of Engineering (Computer Science & Engineering – Artificial Intelligence & Machine Learning)

Focused on Data Analytics, Business Intelligence, and Machine Learning Engineering.
