# Olist Marketplace Performance Analysis

### An End-to-End Data Analytics Project on Marketplace Operations, Delivery Performance, and Customer Satisfaction

**Author**

Mathir

Bachelor of Engineering

Computer Science and Engineering (Artificial Intelligence & Machine Learning)

---

## Abstract

Modern e-commerce marketplaces generate large volumes of transactional data that can be used to evaluate operational efficiency, seller performance, and customer experience. However, meaningful business insights depend not only on analysis but also on careful data preparation, validation, and modeling.

This project presents an end-to-end analytics workflow using the Brazilian Olist Marketplace dataset. Rather than analyzing raw CSV files directly, the project first constructs a structured analytical environment by ingesting the data into PostgreSQL, profiling data quality, validating relationships, normalizing the relational schema, and creating reusable analytics tables for downstream analysis.

Following data preparation, three major business domains are investigated:

* Seller performance and revenue concentration
* Marketplace delivery performance
* Customer satisfaction and its relationship with logistics

Statistical techniques including Pearson correlation, Spearman rank correlation, the Mann–Whitney U test, the Kruskal–Wallis test, Lorenz curves, and the Gini coefficient are employed to support analytical findings.

The analysis reveals that marketplace revenue is highly concentrated among a relatively small proportion of sellers, with the top 20% generating approximately 83% of total revenue. Order volume is identified as the primary driver of seller revenue, while seller delivery performance shows little relationship with commercial success.

Delivery performance analysis demonstrates that logistics complexity, represented by freight cost, explains delivery-time variation more effectively than physical product dimensions. Furthermore, customer satisfaction exhibits a statistically significant relationship with delivery performance, with late deliveries associated with substantially lower review scores.

To support business exploration, curated analytical tables are published to PostgreSQL and visualized through an interactive Metabase dashboard covering seller performance, delivery operations, and customer satisfaction.

This project demonstrates a complete analytics workflow encompassing database engineering, data validation, statistical analysis, visualization, and business intelligence, emphasizing the importance of trustworthy data foundations before business analysis.


# Chapter 2 — Introduction

## 2.1 Background

The rapid growth of e-commerce platforms has generated vast amounts of transactional data, enabling organizations to make informed operational and strategic decisions. Every customer interaction—from placing an order to submitting a review—creates valuable information that can be analyzed to improve marketplace performance.

While dashboards often present business metrics such as revenue, delivery time, and customer ratings, these metrics are only meaningful when supported by reliable and well-structured data. In practice, data analysts spend a significant portion of a project's lifecycle validating, cleaning, and modeling data before any business analysis begins.

This project demonstrates an end-to-end analytics workflow using the Olist Brazilian E-Commerce dataset. Rather than focusing solely on visualization, the project emphasizes the complete analytical process, including database construction, data quality assessment, relational modeling, statistical analysis, and business intelligence reporting.

---

## 2.2 Problem Statement

E-commerce marketplaces involve interactions between customers, sellers, products, logistics providers, and payment systems. Understanding how these entities contribute to overall marketplace performance requires more than isolated descriptive statistics.

The central objective of this project is to answer the following business questions:

* How is marketplace revenue distributed among sellers?
* What operational factors influence delivery performance?
* Does delivery performance affect customer satisfaction?
* Which factors appear to explain marketplace performance differences?

To answer these questions reliably, the underlying dataset must first be validated to ensure that business metrics are calculated on trustworthy data.

---

## 2.3 Objectives

The project was designed around four primary objectives.

### Objective 1 — Build a Reliable Analytical Database

Construct a PostgreSQL-based analytical environment by:

* Importing raw CSV datasets
* Validating relational integrity
* Normalizing the database schema
* Creating reusable analytics tables

---

### Objective 2 — Evaluate Seller Performance

Investigate how marketplace revenue is distributed across sellers by measuring:

* Revenue concentration
* Order volume
* Product pricing
* Category dominance

The goal is to determine why certain sellers outperform others rather than simply identifying the highest earners.

---

### Objective 3 — Analyze Delivery Performance

Study operational efficiency throughout the marketplace by examining:

* Delivery duration
* Delivery reliability
* Category-level logistics
* Seller delivery behavior
* Freight characteristics

The objective is to identify the operational factors that explain delivery-time variation.

---

### Objective 4 — Understand Customer Satisfaction

Evaluate whether delivery performance influences customer reviews by analyzing:

* Delivery speed
* Delivery delays
* Late-delivery rates
* Customer review scores

Statistical testing is used to determine whether observed relationships are supported by the data.

---

## 2.4 Scope

This project focuses on operational marketplace analytics using transactional data from the Olist Brazilian E-Commerce dataset.

The analysis includes:

* Seller performance
* Revenue concentration
* Delivery performance
* Logistics characteristics
* Customer satisfaction

The project does **not** include:

* Demand forecasting
* Recommendation systems
* Customer segmentation
* Predictive machine learning models
* Marketing attribution

Restricting the scope allows greater emphasis on producing evidence-based business insights supported by validated data.

---

## 2.5 Analytical Workflow

Unlike many exploratory data analysis projects that begin directly with visualization, this project follows a structured analytics workflow designed to improve data reliability before analysis.

The workflow consists of six major stages:

1. Data ingestion into PostgreSQL
2. Dataset understanding and relationship discovery
3. Data profiling and quality validation
4. Database normalization
5. Business analysis
6. Dashboard development

This progression ensures that business conclusions are derived from validated and well-defined analytical datasets rather than directly from raw source files.

---

## 2.6 Expected Outcomes

Upon completion, the project aims to deliver:

* A normalized PostgreSQL database
* Documented data quality findings
* Reusable analytical tables
* Evidence-backed business insights
* Statistical validation of key relationships
* An interactive Metabase dashboard summarizing marketplace performance

Together, these deliverables provide a complete analytical workflow suitable for business reporting and decision support while demonstrating practical data engineering and analytics skills commonly required in modern data analyst roles.

# Chapter 3 — Dataset Description & Business Context

## 3.1 Dataset Overview

This project utilizes the **Olist Brazilian E-Commerce Public Dataset**, which contains transactional records collected from the Olist marketplace between 2016 and 2018.

Unlike datasets consisting of a single flat table, the Olist dataset models an operational e-commerce system through multiple related entities. Customer purchases, products, sellers, payments, logistics, and reviews are stored separately, requiring relational modeling before meaningful analysis can be performed.

The dataset represents the complete order lifecycle, from purchase through delivery and customer feedback, making it suitable for studying marketplace performance from both operational and commercial perspectives.

---

## 3.2 Business Context

An online marketplace connects multiple independent sellers with customers through a shared platform.

Every completed purchase involves several business entities:

* A customer places an order.
* One or more products are purchased.
* Products are supplied by one or more sellers.
* Payments are recorded.
* Orders progress through multiple delivery stages.
* Customers may submit reviews after receiving their purchases.

These interactions generate data that can be used to evaluate marketplace performance.

For this project, marketplace performance is examined through three complementary perspectives:

* Commercial performance
* Operational performance
* Customer experience

These perspectives form the analytical narrative developed throughout the report.

---

## 3.3 Dataset Structure

The dataset contains nine relational tables.

| Table                | Description                                     |
| -------------------- | ----------------------------------------------- |
| customers            | Customer identifiers and location information   |
| orders               | Order lifecycle and delivery timestamps         |
| order_items          | Individual products purchased within each order |
| products             | Product metadata and physical characteristics   |
| sellers              | Seller information                              |
| payments             | Payment transactions for each order             |
| reviews              | Customer review scores and optional comments    |
| geolocation          | Geographic reference data                       |
| category_translation | Portuguese to English category mapping          |

Together, these tables describe the complete operational workflow of an online marketplace.

---

## 3.4 Entity Relationships

The dataset follows a relational structure centered around the **orders** table.

```
Customers
     │
     ▼
Orders
     │
     ├──────────────┐
     ▼              ▼
Order Items     Payments
     │
     ├──────────────┐
     ▼              ▼
Products        Sellers
     │
     ▼
Categories

Orders
     │
     ▼
Reviews
```

Orders serve as the central transaction linking customers, purchased products, sellers, payments, and customer reviews.

Understanding these relationships was a prerequisite for constructing reliable analytical datasets.

---

## 3.5 Initial Analytical Challenges

Although the dataset is well documented, several assumptions commonly made during exploratory analysis required validation before business questions could be investigated.

Examples include:

* Which columns truly represent primary keys?
* Do all foreign-key relationships remain valid?
* Does each review represent exactly one order?
* Are customer identifiers unique?
* Do timestamp columns follow expected chronological order?
* Are missing values random or systematic?

Answering these questions formed a substantial portion of the project's preparation phase.

Rather than assuming the published schema was sufficient, every major relationship was verified empirically before downstream analysis.

---

## 3.6 Data Granularity

One of the most important steps in analytical modeling is determining the **grain** of each table—that is, what a single row actually represents.

The investigation identified the following table grains:

| Table                | Grain                                                                                 |
| -------------------- | ------------------------------------------------------------------------------------- |
| customers            | One row per customer record                                                           |
| orders               | One row per order                                                                     |
| order_items          | One row per purchased item within an order                                            |
| products             | One row per product                                                                   |
| sellers              | One row per seller                                                                    |
| payments             | One row per payment event                                                             |
| reviews              | One review record associated with an order (effective key validated during profiling) |
| geolocation          | Geographic observations for ZIP-code prefixes                                         |
| category_translation | One product category translation                                                      |

Establishing table grain prevented incorrect aggregations during later analysis.

---

## 3.7 Why Data Preparation Was Necessary

Many introductory analytics projects begin directly with exploratory analysis after loading CSV files into memory.

However, doing so introduces several risks:

* Incorrect joins
* Duplicate counting
* Misinterpreted identifiers
* Invalid assumptions about uniqueness
* Metrics calculated from inconsistent data

To minimize these risks, this project first established a structured analytical environment within PostgreSQL before performing any business analysis.

The workflow consisted of:

1. Raw data ingestion
2. Dataset understanding
3. Data profiling
4. Normalization
5. Analytics table creation
6. Business analysis

Only after these stages were completed were business metrics considered sufficiently trustworthy for interpretation.

---

## 3.8 Analytical Scope

The dataset supports a wide range of analyses, including customer segmentation, marketing attribution, demand forecasting, recommendation systems, and predictive modeling.

This project intentionally limits its scope to descriptive and diagnostic analytics.

Three business domains are investigated:

### Seller Performance

Understanding how revenue is distributed across marketplace sellers and identifying factors associated with commercial success.

---

### Delivery Performance

Evaluating operational efficiency across sellers, products, and categories while investigating factors influencing delivery duration and reliability.

---

### Customer Satisfaction

Examining whether delivery performance is associated with customer review behavior and identifying logistics metrics that best explain review outcomes.

Restricting the analytical scope allows each business question to be investigated in greater depth while maintaining a coherent narrative throughout the report.

---

## 3.9 Chapter Summary

The Olist dataset represents a realistic relational marketplace rather than a single analytical table.

Before meaningful business analysis could begin, the project required:

* Understanding entity relationships
* Validating table grains
* Identifying analytical assumptions
* Constructing a relational analytical database

These activities established the foundation for the subsequent data preparation stages, where the dataset was systematically profiled, validated, and normalized before business insights were generated.

# Chapter 4 — System Architecture & Analytical Methodology

## 4.1 Overview

A major objective of this project was to construct a reproducible analytics workflow rather than perform analysis directly on raw CSV files.

Instead of loading datasets into memory and immediately producing visualizations, the project follows a layered analytical architecture that separates raw data, validated data, and business-ready analytical datasets.

This approach improves data quality, reproducibility, and maintainability while closely resembling workflows commonly used in production analytics environments.

---

## 4.2 System Architecture

The analytical workflow consists of three logical database layers.

```
                Olist CSV Files
                      │
                      ▼
            PostgreSQL (raw schema)
                      │
                      ▼
         Data Profiling & Validation
                      │
                      ▼
          PostgreSQL (clean schema)
                      │
                      ▼
     Feature Engineering & Aggregation
                      │
                      ▼
        PostgreSQL (analytics schema)
                      │
                      ▼
        Polars Statistical Analysis
                      │
                      ▼
      Metabase Interactive Dashboard
```

Each layer has a clearly defined responsibility.

---

## 4.3 Raw Layer

The **raw** schema stores the datasets exactly as provided.

Characteristics of this layer include:

* No cleaning
* No modification
* No filtering
* No derived metrics

Its purpose is to preserve an immutable copy of the original data while providing a reliable source for future transformations.

The raw layer contains the following tables:

* customers
* orders
* order_items
* products
* sellers
* payments
* reviews
* geolocation
* category_translation

Maintaining an untouched raw layer ensures that all downstream transformations remain reproducible.

---

## 4.4 Data Profiling

Before designing the analytical database, the raw data was systematically examined to understand its structure and quality.

Unlike exploratory analysis, profiling focuses on answering questions about the data itself rather than the business.

Examples include:

* Is every identifier unique?
* What does one row represent?
* Which columns contain missing values?
* Do foreign-key relationships remain valid?
* Are timestamps logically consistent?
* Are there hidden data-quality issues?

Only after these questions were answered was the dataset considered suitable for business analysis.

---

## 4.5 Metadata-Driven Profiling

Rather than manually inspecting each table, profiling queries were generated programmatically using PostgreSQL system metadata.

The workflow utilized:

* `information_schema.columns`
* `format()`
* `string_agg()`

to automatically generate profiling queries covering every column in the database.

```
information_schema.columns
            │
            ▼
      format()
            │
            ▼
Generated SQL Statements
            │
            ▼
      string_agg()
            │
            ▼
Single Executable Profiling Query
```

This approach provided several advantages:

* Reduced repetitive SQL
* Improved reproducibility
* Consistent profiling across all tables
* Easy adaptation to future datasets

Instead of writing dozens of individual profiling queries, a single metadata-driven workflow was used to audit all columns systematically.

---

## 4.6 Data Normalization

Following profiling, the raw schema was transformed into a normalized relational model.

Normalization involved:

* Defining primary keys
* Establishing foreign-key relationships
* Converting timestamp columns to appropriate data types
* Preserving referential integrity
* Creating lookup tables where appropriate

The resulting **clean** schema represents the validated analytical source for subsequent analysis.

Examples include:

* Clean customer table
* Clean products table
* Clean orders table
* Clean payments table
* Clean reviews table

Unlike the raw schema, the clean schema enforces relational consistency while preserving the original business information.

---

## 4.7 Analytics Layer

Business intelligence tools often require repeated joins and aggregations.

Performing these operations dynamically can:

* increase dashboard latency,
* duplicate analytical logic,
* and make business metrics difficult to maintain.

To address this, the project introduces an **analytics** schema containing curated analytical tables generated during the investigation.

Examples include:

* seller metrics
* revenue concentration
* Lorenz curve data
* category delivery metrics
* review delivery summary

Each table represents a reusable business artifact rather than a temporary notebook output.

This separation allows dashboard development to focus on visualization instead of repeated data preparation.

---

## 4.8 Analytical Workflow

Each business question followed the same analytical process.

```
Business Question
        │
        ▼
Hypothesis Formation
        │
        ▼
Data Preparation
        │
        ▼
Statistical Analysis
        │
        ▼
Visualization
        │
        ▼
Business Interpretation
        │
        ▼
Analytics Table
        │
        ▼
Metabase Dashboard
```

Rather than producing charts immediately, every visualization was preceded by:

* metric definition,
* validation,
* statistical testing,
* and interpretation.

This workflow encourages evidence-based conclusions instead of descriptive reporting alone.

---

## 4.9 Technology Stack

The project combines database engineering, statistical computing, and business intelligence tools.

| Component               | Technology           |
| ----------------------- | -------------------- |
| Database                | PostgreSQL 17        |
| Containerization        | Podman               |
| Data Manipulation       | Polars               |
| Database Connectivity   | SQLAlchemy + psycopg |
| Statistical Analysis    | SciPy                |
| Visualization           | Matplotlib           |
| Dashboard               | Metabase             |
| Development Environment | Jupyter Notebook     |

Each technology was selected for a specific role within the analytical workflow rather than using a single tool for every task.

---

## 4.10 Design Decisions

Several architectural decisions influenced the final workflow.

### PostgreSQL Before Analysis

Instead of analyzing CSV files directly, all data was first imported into PostgreSQL.

Benefits include:

* relational integrity,
* reusable SQL,
* scalable joins,
* reproducible transformations.

---

### Separation of Database Layers

Keeping **raw**, **clean**, and **analytics** schemas separate prevents accidental modification of source data while allowing progressively refined analytical datasets.

---

### Polars Instead of Pandas

Polars was used for data manipulation because of its efficient query execution, expressive syntax, and seamless integration with analytical workflows.

---

### Analytics Tables Instead of Dashboard Queries

Business metrics were materialized as analytical tables before dashboard development.

This reduced dashboard complexity and ensured that every visualization used the same validated definitions.

---

## 4.11 Methodological Contribution

Although the final deliverables include visualizations and dashboards, a substantial portion of the project focused on activities that are often invisible in portfolio projects.

These include:

* relational modeling,
* data validation,
* key verification,
* profiling,
* normalization,
* analytical table construction,
* and metric definition.

These preparation stages establish confidence in the subsequent analyses by ensuring that reported business metrics are derived from validated and well-understood data.

---

## 4.12 Chapter Summary

This chapter presented the technical architecture underlying the project.

Rather than treating data preparation as a preliminary task, it was integrated as a fundamental component of the analytical workflow.

The resulting architecture provides a reproducible pipeline that transforms raw operational data into validated business intelligence assets suitable for statistical analysis and interactive reporting.

The following chapter describes the data preparation process in detail, including data ingestion, dataset understanding, profiling, normalization, and the construction of the analytical database.

# Chapter 5 — Data Preparation

## 5.1 Overview

The quality of any analytical result depends on the quality of the underlying data.

Rather than beginning directly with exploratory analysis, this project dedicated a substantial portion of its workflow to understanding, validating, and preparing the dataset before investigating business questions.

The preparation stage consisted of four major phases:

1. Data ingestion
2. Dataset understanding
3. Data profiling
4. Data normalization

These phases established a reliable analytical foundation by ensuring that business metrics were computed from validated and well-understood data.

---

# 5.2 Data Ingestion

The Olist dataset was provided as nine independent CSV files representing different business entities.

Instead of loading these files directly into memory, each dataset was imported into PostgreSQL to create a centralized analytical environment.

The ingestion process consisted of:

* Creating a PostgreSQL database
* Creating separate schemas (`raw`, `clean`, and `analytics`)
* Importing each CSV into the `raw` schema
* Verifying successful ingestion through row-count validation

The following tables were successfully loaded.

| Table                |      Rows |
| -------------------- | --------: |
| customers            |    99,441 |
| orders               |    99,441 |
| order_items          |   112,650 |
| products             |    32,951 |
| sellers              |     3,095 |
| payments             |   103,886 |
| reviews              |    99,224 |
| geolocation          | 1,000,163 |
| category_translation |        71 |

Successful ingestion established a reproducible source layer from which all subsequent transformations were derived.

---

# 5.3 Dataset Understanding

Before performing any cleaning or statistical analysis, the structure of the dataset was investigated.

The objective was to understand:

* table grains,
* candidate primary keys,
* foreign-key relationships,
* and entity interactions.

Understanding the relational structure prevented incorrect joins and duplicate counting during later analysis.

---

## 5.3.1 Primary Key Validation

Rather than assuming identifier columns represented primary keys, uniqueness was verified empirically.

Several important observations emerged.

### Customers

Although both **customer_id** and **customer_unique_id** appear to represent customers, they serve different purposes.

The investigation showed:

* `customer_id` is unique.
* `customer_unique_id` appears multiple times.

This indicates that the dataset distinguishes between customer records associated with orders and real-world customer identities.

Consequently, analyses involving repeat customers should use **customer_unique_id**, whereas order-level analyses should reference **customer_id**.

---

### Order Items

The column `order_item_id` was found **not** to be globally unique.

Instead, uniqueness is achieved through the composite key:

```
(order_id, order_item_id)
```

This reflects the business interpretation that each row represents one purchased item within a specific order.

---

### Payments

Orders may contain multiple payment records.

The effective primary key was validated as:

```
(order_id, payment_sequential)
```

This allows a single order to contain multiple payment events while maintaining uniqueness.

---

### Reviews

The reviews table required special attention.

Neither:

* review_id

nor

* order_id

proved to be individually unique.

Instead, uniqueness was achieved only by:

```
(review_id, order_id)
```

This finding demonstrates why assumptions about identifier columns should always be validated before analysis.

---

# 5.4 Relationship Validation

After identifying candidate keys, relationships between tables were verified.

The following foreign-key relationships were investigated.

| Child Table | Parent Table |
| ----------- | ------------ |
| orders      | customers    |
| order_items | orders       |
| order_items | products     |
| order_items | sellers      |
| payments    | orders       |
| reviews     | orders       |

No orphan records were detected during validation.

This confirms that the dataset maintains referential integrity across all major transactional entities.

---

# 5.5 Data Profiling

Following structural validation, a comprehensive profiling process was performed to evaluate data quality.

Unlike exploratory analysis, profiling focuses on understanding the characteristics of the data itself rather than answering business questions.

The profiling process investigated:

* missing values,
* duplicate records,
* referential integrity,
* timestamp consistency,
* entity cardinality,
* and table grain.

---

## 5.5.1 Metadata-Driven Profiling

Rather than manually auditing each column, a metadata-driven workflow was developed using PostgreSQL's system catalog.

The workflow automatically generated profiling queries covering all:

* 9 tables
* 52 columns

using:

* `information_schema.columns`
* `format()`
* `string_agg()`

This approach significantly reduced repetitive SQL while ensuring every column was profiled consistently.

---

# 5.6 Missing Value Investigation

The null-value audit revealed that the dataset is generally complete.

Of the 52 audited columns:

* 39 contained no missing values.
* 13 contained missing values.

However, the investigation demonstrated that missing values are not uniformly problematic.

Instead, several distinct forms of missingness were identified.

---

## Business-Driven Missingness

Review comment fields exhibited substantial missingness.

| Column                 | Missing |
| ---------------------- | ------: |
| review_comment_title   |  88.34% |
| review_comment_message |  58.70% |

These fields are optional.

Consequently, their missing values represent user behavior rather than poor data quality.

---

## Business-Process Missingness

Several delivery timestamps were missing because orders never progressed to later fulfillment stages.

These missing values primarily occurred in orders with statuses such as:

* cancelled
* unavailable
* created
* approved
* processing

Such missingness reflects normal business operations rather than data corruption.

---

## Historical Data Anomalies

A more interesting anomaly involved `order_approved_at`.

Among the missing values:

* 141 belonged to cancelled orders,
* 5 belonged to created orders,
* 14 belonged to delivered orders.

The delivered anomalies were investigated further.

All fourteen occurred exclusively during **January and February 2017** while still containing valid shipping and delivery timestamps.

This strongly suggests an early historical logging or migration issue rather than an ongoing business-process failure.

---

## Systematic Entity-Level Missingness

Product metadata revealed another important pattern.

Exactly **610 products** were simultaneously missing:

* category
* product name length
* description length
* photo count

These products:

* were sold,
* appeared in 1,603 order items,
* and involved 257 sellers.

The aligned missingness indicates a marketplace-wide catalog quality issue rather than random row-level omission.

---

# 5.7 Timestamp Validation

Several chronological relationships between timestamps were evaluated.

Examples included:

* Purchase before approval
* Approval before carrier handoff
* Carrier handoff before customer delivery

Most timestamps followed expected ordering.

However, several anomalies were identified.

Most notably:

* 23 cases where carrier delivery timestamps occurred after customer delivery timestamps.
* 1,359 cases where approval timestamps followed carrier handoff timestamps.

Because these inconsistencies occurred despite otherwise valid order histories, they were documented as timestamp caveats rather than automatically treated as erroneous records.

---

# 5.8 Data Normalization

After profiling, the dataset was transformed into a normalized relational schema.

The normalization process included:

* Primary-key creation
* Foreign-key enforcement
* Timestamp datatype conversion
* Relationship validation
* Lookup-table construction

Eight clean tables were created.

| Table       |
| ----------- |
| customers   |
| orders      |
| order_items |
| products    |
| sellers     |
| payments    |
| reviews     |
| categories  |

This layer became the authoritative analytical source for all subsequent business investigations.

---

## Foreign-Key Resolution

During normalization, foreign-key creation identified two product categories that existed within the products table but were absent from the category translation table.

Instead of removing these products, the missing categories were inserted into the lookup table with unknown English translations.

This preserved referential integrity while maintaining the completeness of the original dataset.

---

# 5.9 Analytics Layer

Rather than querying normalized tables directly inside the dashboard, reusable analytical tables were constructed throughout the investigation.

Examples include:

* seller_metrics
* revenue_concentration
* seller_revenue_lorenz
* category_delivery_metrics
* review_delivery_summary

These tables encapsulate validated business metrics and provide a stable interface for downstream visualization.

This separation reduces dashboard complexity while ensuring consistent metric definitions across analyses.

---

# 5.10 Chapter Summary

Data preparation represented a substantial portion of the overall project.

Rather than treating ingestion, profiling, and normalization as preliminary tasks, they formed the foundation upon which all subsequent analyses were built.

The preparation process established:

* validated relational structures,
* trustworthy business metrics,
* documented data-quality caveats,
* and reusable analytical datasets.

With the analytical environment in place, the project proceeds to investigate marketplace performance, beginning with the distribution of seller revenue and the factors contributing to marketplace concentration.

# Chapter 6 — Revenue Concentration Analysis

## 6.1 Overview

Revenue distribution is one of the most important indicators of marketplace health. A balanced marketplace typically generates revenue from a broad base of sellers, whereas a highly concentrated marketplace depends disproportionately on a relatively small number of businesses.

Understanding whether revenue concentration exists—and identifying the factors associated with seller success—provides valuable insights for marketplace operators. If revenue is concentrated among only a few sellers, platform growth and resilience may depend heavily on retaining these businesses while also helping smaller sellers improve their performance.

This chapter investigates the distribution of seller revenue and examines several hypotheses that may explain why certain sellers consistently outperform others.

---

# 6.2 Research Questions

The analysis was guided by the following questions:

1. Is marketplace revenue evenly distributed among sellers?
2. How unequal is seller revenue distribution?
3. Do successful sellers simply process more orders?
4. Does higher pricing explain greater revenue?
5. Do top sellers dominate specific product categories?

These questions move beyond descriptive reporting and seek to explain the underlying drivers of marketplace performance.

---

# 6.3 Seller-Level Metric Construction

The raw transactional tables operate at the order-item level, where each row represents an individual product purchased within an order.

To analyze seller performance, these records were aggregated into a seller-level analytical dataset.

Each row of the derived table represents one seller and contains the following metrics:

| Metric              | Description                         |
| ------------------- | ----------------------------------- |
| Revenue             | Total sales generated by the seller |
| Orders              | Number of unique customer orders    |
| Items Sold          | Total quantity of products sold     |
| Average Price       | Mean selling price per item         |
| Average Order Value | Average revenue generated per order |

This aggregation produced a reusable analytical table (`seller_metrics`) that served as the foundation for all subsequent seller analyses.

---

# 6.4 Revenue Distribution

The first step was to examine how marketplace revenue is distributed among sellers.

Rather than inspecting individual seller revenues, cumulative revenue distribution was analyzed to determine whether revenue is shared evenly across the marketplace or concentrated among a relatively small subset of sellers.

The cumulative analysis revealed a pronounced imbalance.

Key observations include:

* The top **5%** of sellers generated approximately **53%** of total marketplace revenue.
* The top **20%** of sellers generated approximately **83%** of total revenue.
* The remaining **80%** of sellers collectively contributed only about **17%** of marketplace revenue.

These findings closely resemble the Pareto Principle, where a minority of participants account for the majority of outcomes.

The results indicate that commercial activity within the marketplace is highly concentrated rather than uniformly distributed.

---

# 6.5 Lorenz Curve Analysis

To visualize inequality in seller revenue, a Lorenz Curve was constructed.

The Lorenz Curve compares:

* the cumulative percentage of sellers, and
* the cumulative percentage of revenue they generate.

If every seller generated identical revenue, the Lorenz Curve would coincide with the 45-degree line of perfect equality.

Instead, the observed curve bowed substantially below the equality line, indicating that revenue accumulates much more slowly for the majority of sellers before increasing sharply among the highest-performing sellers.

This pattern confirms that marketplace revenue is concentrated within a relatively small proportion of businesses.

*Figure 6.1. Lorenz Curve illustrating cumulative seller revenue distribution.*

---

# 6.6 Gini Coefficient

While the Lorenz Curve provides a visual representation of inequality, the Gini Coefficient offers a quantitative measure.

The Gini Coefficient ranges from:

* **0**, representing perfect equality, where every seller earns identical revenue,
* to **1**, representing complete inequality, where a single seller generates all revenue.

The calculated Gini Coefficient for seller revenue was:

| Metric           |      Value |
| ---------------- | ---------: |
| Gini Coefficient | **0.7915** |

A value of **0.7915** indicates a high degree of revenue concentration.

Although the Gini Coefficient is commonly associated with income inequality, it is equally applicable to marketplace analysis, where it measures the distribution of commercial activity among sellers.

---

# 6.7 Hypothesis 1 — Do More Orders Lead to Higher Revenue?

The first explanatory hypothesis investigated whether successful sellers simply process more customer orders.

To evaluate this relationship, two complementary correlation measures were calculated:

* Pearson Correlation
* Spearman Rank Correlation

The results are summarized below.

| Statistic            |      Value |
| -------------------- | ---------: |
| Pearson Correlation  | **0.7997** |
| Spearman Correlation | **0.8526** |

Both statistics indicate a strong positive relationship between order volume and seller revenue.

The Pearson coefficient suggests that sellers processing larger numbers of orders generally generate greater revenue, while the slightly higher Spearman coefficient indicates that this relationship remains strong even when considering seller rankings rather than assuming strict linearity.

These findings support the hypothesis that order volume is a primary driver of commercial success within the marketplace.

*Figure 6.2. Relationship between seller order volume and revenue (logarithmic scale).*

---

# 6.8 Why a Logarithmic Scale Was Used

Seller revenue spans several orders of magnitude.

Without transformation, a small number of exceptionally large sellers dominate the visualization, compressing the majority of observations into a narrow region near the origin.

To improve interpretability, both revenue and order count were displayed using logarithmic axes.

This transformation preserves the underlying relationships while making patterns across both small and large sellers simultaneously visible.

The log-scale visualization reveals that the positive association between order volume and revenue persists across the full range of seller sizes rather than being driven solely by the largest marketplace participants.

---

# 6.9 Interpreting Revenue Concentration

High revenue concentration should not necessarily be interpreted as evidence of marketplace imbalance or failure.

Several factors may naturally contribute to concentration:

* differences in seller scale,
* inventory availability,
* customer trust,
* product assortment,
* operational maturity,
* and marketplace reputation.

The analysis demonstrates that larger sellers consistently process substantially more orders than smaller competitors.

However, the available dataset does not provide information about marketing expenditure, advertising strategy, or external commercial factors.

Consequently, while order volume is strongly associated with revenue, the mechanisms that generate higher order volume cannot be determined from the available data.

---

# 6.10 Business Implications

The findings have several practical implications for marketplace management.

### Marketplace Risk

Heavy dependence on a relatively small proportion of sellers increases operational risk.

The departure of several major sellers could disproportionately affect marketplace revenue.

---

### Seller Development

Since most sellers contribute only a small fraction of marketplace revenue, programs that improve seller visibility, operational efficiency, or conversion rates may broaden the marketplace's commercial base.

---

### Resource Allocation

High-performing sellers represent strategically important marketplace partners.

Operational support, logistics optimization, and seller retention initiatives may yield disproportionate business benefits.

---

# 6.11 Limitations

Several limitations should be considered when interpreting these findings.

The analysis demonstrates strong associations between order volume and revenue but does not establish causation.

Furthermore, several potentially important explanatory variables were unavailable, including:

* advertising expenditure,
* promotional campaigns,
* marketplace search ranking,
* seller reputation outside review scores,
* inventory availability,
* and product demand.

These factors may also contribute to seller performance but cannot be evaluated using the available dataset.

---

# 6.12 Chapter Summary

This chapter established that seller revenue within the Olist marketplace is highly concentrated.

The Lorenz Curve and Gini Coefficient demonstrated substantial inequality in commercial activity, while correlation analysis showed that order volume is strongly associated with seller revenue.

These findings suggest that marketplace success is driven primarily by sustained transaction volume rather than evenly distributed participation across sellers.

Having established how revenue is distributed, the next chapter investigates marketplace operations by examining delivery performance across product categories and identifying factors associated with slower or less reliable deliveries.

# Chapter 7 — Delivery Performance Analysis

## 7.1 Overview

Delivery performance is a critical component of e-commerce operations. Delays in order fulfillment not only increase operational costs but also influence customer satisfaction and marketplace reputation.

Unlike seller revenue, which primarily reflects commercial performance, delivery metrics measure the efficiency of the marketplace's logistics network.

This chapter investigates delivery performance across product categories and explores several hypotheses regarding the factors associated with delivery duration and reliability.

---

# 7.2 Research Questions

The delivery analysis was guided by the following questions:

1. Which product categories experience the longest delivery times?
2. Which categories experience the highest proportion of late deliveries?
3. Are slower deliveries generally less reliable?
4. What factors explain differences in delivery performance?
5. Does freight cost or product size better explain delivery duration?

These questions focus on identifying operational bottlenecks rather than simply reporting average delivery times.

---

# 7.3 Delivery Metrics

Two complementary metrics were defined.

### Delivery Time

Delivery time represents the number of days between customer purchase and successful order delivery.

This continuous metric measures logistical efficiency.

---

### Late Delivery Rate

Late delivery rate measures the proportion of delivered orders arriving after their estimated delivery date.

Unlike delivery duration, this metric evaluates delivery reliability relative to customer expectations.

Both metrics were analyzed because a marketplace can deliver orders slowly while remaining reliable if delivery estimates are accurate.

---

# 7.4 Delivery Performance by Product Category

Average delivery time was calculated for each product category.

The results revealed considerable variation between categories.

Some categories consistently required substantially longer delivery times than others, indicating that logistics complexity differs across products.

Among all categories, **office furniture** exhibited the longest average delivery duration.

Several factors may contribute to this observation:

* larger package dimensions,
* higher handling complexity,
* specialized transportation requirements,
* increased delivery coordination.

Conversely, many consumer goods clustered around relatively similar delivery durations, suggesting standardized fulfillment processes.

*Figure 7.1. Average delivery time by product category.*

---

# 7.5 Late Delivery by Product Category

Delivery duration alone does not indicate whether customer expectations are being met.

Therefore, the proportion of late deliveries was examined separately.

The analysis showed that late-delivery rates also varied considerably across categories.

Office furniture again recorded one of the highest proportions of delayed deliveries, indicating that the category experiences challenges not only in delivery speed but also in delivery reliability.

This consistency across both operational metrics suggests that logistics complexity differs systematically between product categories rather than arising from isolated events.

*Figure 7.2. Late delivery percentage by product category.*

---

# 7.6 Delivery Speed vs Reliability

To investigate whether slower categories are also less reliable, average delivery duration was compared with late-delivery percentage.

A positive relationship was observed.

Categories with longer average delivery times generally exhibited higher late-delivery rates.

Although the relationship is not perfectly linear, the overall trend suggests that operational inefficiencies affecting delivery speed also increase the likelihood of missing promised delivery dates.

This finding highlights the importance of evaluating both metrics together rather than independently.

*Figure 7.3. Relationship between delivery duration and late-delivery rate.*

---

# 7.7 Investigating the Causes of Slow Deliveries

Identifying the slowest categories answers **what** happens but not **why** it happens.

To explore potential explanations, two hypotheses were investigated.

### Hypothesis 1

Products with larger physical dimensions require longer delivery times.

---

### Hypothesis 2

Higher freight costs are associated with longer delivery times because they reflect more complex logistics.

These hypotheses were evaluated independently.

---

# 7.8 Product Dimensions

Product dimensions, including:

* weight,
* length,
* width,
* and height,

were initially considered as potential explanatory variables.

However, statistical analysis indicated that physical size alone does not adequately explain observed delivery durations.

Many relatively small products still experienced lengthy deliveries, while several larger products were delivered within expected timeframes.

These findings suggest that package dimensions alone are insufficient for explaining marketplace logistics performance.

---

# 7.9 Freight Cost

Freight value demonstrated a clearer relationship with delivery performance.

Orders with higher freight charges generally required longer delivery times.

Unlike physical dimensions, freight value captures several logistical characteristics simultaneously, including:

* transportation distance,
* carrier selection,
* shipment complexity,
* and operational handling costs.

Consequently, freight cost serves as a more comprehensive proxy for logistical difficulty than product size alone.

---

# 7.10 Statistical Evaluation

To compare the influence of freight value and product dimensions, non-parametric statistical testing was performed.

The analysis produced two contrasting results.

Physical product dimensions showed no statistically significant relationship with delivery duration.

In contrast, freight value exhibited a statistically significant association with delivery performance.

These findings support the conclusion that logistics complexity is better represented by freight characteristics than by physical dimensions alone.

This distinction is important because freight value incorporates operational factors beyond package size.

---

# 7.11 Interpretation

The analysis suggests that delivery performance is influenced by a combination of operational factors rather than a single physical characteristic.

Although larger products may occasionally require additional handling, the available evidence indicates that freight value better captures the practical complexity of marketplace logistics.

Examples of contributing factors may include:

* transportation distance,
* regional infrastructure,
* carrier availability,
* delivery scheduling,
* and shipment consolidation.

Because these variables are not explicitly recorded within the dataset, freight value functions as an aggregate indicator of logistical complexity.

---

# 7.12 Business Implications

The findings provide several practical implications for marketplace operations.

### Category-Specific Logistics

Operational improvements should prioritize categories consistently exhibiting poor delivery performance rather than applying identical interventions across all products.

---

### Delivery Forecasting

Freight value may provide a more informative feature than product dimensions when developing predictive models for delivery duration.

---

### Customer Expectations

Categories associated with complex logistics may benefit from more conservative delivery estimates, reducing the proportion of orders classified as late.

---

### Operational Monitoring

Monitoring both delivery duration and late-delivery rate provides a more complete assessment of logistics performance than relying on either metric independently.

---

# 7.13 Limitations

Several factors influencing delivery performance were unavailable within the dataset.

Examples include:

* geographic delivery distance,
* warehouse inventory levels,
* carrier capacity,
* seasonal demand,
* weather conditions,
* and transportation disruptions.

Consequently, the observed relationships should be interpreted as operational associations rather than causal mechanisms.

Furthermore, freight value should not be interpreted as the direct cause of slower deliveries. Instead, it serves as an observable indicator of broader logistical complexity.

---

# 7.14 Chapter Summary

This chapter investigated operational delivery performance across the marketplace.

The analysis identified substantial variation in both delivery duration and delivery reliability across product categories. Office furniture consistently exhibited slower and less reliable deliveries than most other categories.

Further investigation demonstrated that freight value explains delivery performance more effectively than product dimensions, suggesting that logistics complexity extends beyond the physical characteristics of individual products.

The next chapter examines the customer perspective by investigating whether delivery performance influences review scores and overall customer satisfaction.

# Chapter 8 — Customer Satisfaction Analysis

## 8.1 Overview

Customer satisfaction is a key indicator of marketplace quality. While revenue and delivery performance measure commercial and operational success, customer reviews provide direct feedback on the purchasing experience.

Among the many factors influencing customer satisfaction, delivery performance is frequently considered one of the most important. Delayed or unreliable deliveries can negatively affect customer perception even when the purchased product meets expectations.

This chapter investigates the relationship between logistics performance and customer review scores to determine whether delivery behavior is associated with customer satisfaction.

---

# 8.2 Research Questions

The customer satisfaction analysis was designed to answer the following questions:

1. Do customers receiving faster deliveries provide higher review scores?
2. Are late deliveries associated with poorer customer ratings?
3. Which delivery metric better explains customer satisfaction?
4. Are the observed differences statistically significant?

Rather than relying solely on visual trends, formal statistical testing was used to evaluate these relationships.

---

# 8.3 Review Score Distribution

Customer satisfaction was measured using the marketplace's five-point review system.

Review scores range from:

| Score | Interpretation    |
| ----: | ----------------- |
|     1 | Very dissatisfied |
|     2 | Dissatisfied      |
|     3 | Neutral           |
|     4 | Satisfied         |
|     5 | Very satisfied    |

These ratings provide an ordinal measure of customer satisfaction, making non-parametric statistical methods more appropriate than techniques designed for continuous variables.

---

# 8.4 Delivery Time and Review Scores

The first analysis examined how delivery duration varied across customer review scores.

Median delivery time was calculated for each review category.

A clear pattern emerged.

Customers awarding five-star ratings generally experienced shorter delivery times, whereas lower ratings were associated with progressively longer deliveries.

Although differences between adjacent review scores were modest, the overall trend remained consistent across the full rating scale.

This suggests that customers receiving faster deliveries tend to report higher levels of satisfaction.

*Figure 8.1. Median delivery time grouped by review score.*

---

# 8.5 Late Delivery Rate and Review Scores

Delivery duration alone does not indicate whether an order met customer expectations.

Consequently, late-delivery percentage was analyzed separately.

The results demonstrated a substantially stronger relationship.

Orders receiving one-star reviews exhibited the highest proportion of late deliveries, while five-star reviews showed the lowest late-delivery rates.

Unlike delivery duration, which varied gradually, late-delivery percentage displayed a much steeper gradient across review scores.

This indicates that failing to meet promised delivery dates has a stronger association with customer dissatisfaction than relatively small differences in overall delivery duration.

*Figure 8.2. Late-delivery percentage grouped by review score.*

---

# 8.6 Statistical Testing

Because review scores represent ordinal data and delivery metrics did not satisfy assumptions required for parametric analysis, non-parametric statistical tests were employed.

Two complementary tests were performed.

### Kruskal–Wallis Test

The Kruskal–Wallis test evaluates whether multiple independent groups originate from the same distribution.

In this project, the test assessed whether delivery performance differed across the five review-score groups.

The analysis indicated statistically significant differences in delivery performance between review categories.

This result suggests that review scores are associated with systematic differences in delivery experience rather than random variation.

---

### Mann–Whitney U Test

To investigate specific group differences, pairwise comparisons were conducted using the Mann–Whitney U test.

The results demonstrated that customers providing lower review scores generally experienced poorer delivery performance than those providing higher ratings.

Together with the Kruskal–Wallis analysis, these findings provide statistical evidence supporting the observed trends in the visualizations.

---

# 8.7 Interpreting the Results

Although the analyses reveal a clear relationship between logistics and customer satisfaction, it is important to distinguish association from causation.

Customer reviews are influenced by numerous factors beyond delivery performance, including:

* product quality,
* packaging,
* customer service,
* pricing,
* seller communication,
* and post-purchase support.

Consequently, delivery performance should be viewed as one contributing factor among many.

Nevertheless, the consistent statistical relationships observed throughout the analysis indicate that logistics plays a meaningful role in shaping customer perceptions.

---

# 8.8 Comparing Delivery Metrics

Both delivery metrics demonstrated relationships with customer reviews.

However, their explanatory strength differed.

### Delivery Duration

Delivery duration exhibited a gradual improvement as review scores increased.

While statistically significant, differences between neighboring review groups were relatively modest.

---

### Late Delivery Rate

Late-delivery percentage displayed a substantially stronger relationship with customer satisfaction.

Customers appeared more sensitive to missed delivery promises than to small differences in overall delivery speed.

For example, an order delivered one day later than promised may negatively affect customer perception even if its total delivery duration is comparable to similar orders.

This finding highlights the importance of delivery reliability alongside delivery speed.

---

# 8.9 Business Implications

The results suggest several practical recommendations for marketplace operators.

### Improve Delivery Reliability

Reducing late deliveries may have a greater impact on customer satisfaction than attempting to marginally reduce average delivery time.

---

### Set Realistic Delivery Estimates

Accurate delivery predictions help align customer expectations with actual fulfillment performance.

Meeting promised delivery dates may improve customer perception even when delivery durations remain unchanged.

---

### Prioritize Operational Consistency

Investments that reduce variability in delivery performance may provide greater customer satisfaction benefits than improvements targeting only average delivery speed.

---

### Monitor Logistics as a Customer Experience Metric

Delivery performance should be monitored not only as an operational indicator but also as a driver of customer experience.

Integrating logistics metrics with customer feedback enables earlier identification of operational issues affecting satisfaction.

---

# 8.10 Limitations

Several limitations should be considered.

The available dataset records only final review scores and optional review comments.

It does not capture:

* customer expectations,
* previous purchasing history,
* product quality assessments,
* communication quality,
* or post-purchase interactions.

Consequently, the analysis cannot isolate delivery performance as the sole determinant of customer satisfaction.

Furthermore, while statistical significance indicates that observed differences are unlikely to be due to random chance, it does not measure the practical magnitude of customer dissatisfaction.

These findings should therefore be interpreted as evidence of meaningful association rather than proof of direct causation.

---

# 8.11 Chapter Summary

This chapter examined the relationship between logistics performance and customer satisfaction.

Both delivery duration and late-delivery percentage exhibited statistically significant associations with review scores. However, delivery reliability emerged as the stronger indicator of customer satisfaction.

Customers were generally more tolerant of modest variations in delivery speed than of deliveries arriving after the promised date.

These findings reinforce the importance of reliable logistics in maintaining positive customer experiences and demonstrate that operational performance extends beyond efficiency alone—it directly influences how customers perceive the marketplace.

Having examined commercial performance, operational efficiency, and customer experience, the following chapter presents the interactive Metabase dashboard developed to communicate these findings through business intelligence visualizations.

# Chapter 9 — Dashboard Design & Business Intelligence

## 9.1 Overview

The final stage of the project involved transforming the analytical results into an interactive business intelligence dashboard using Metabase.

While the preceding chapters focused on statistical analysis and hypothesis testing, the dashboard was designed to communicate key findings in an accessible format suitable for business stakeholders.

Rather than querying normalized tables directly, the dashboard consumes curated analytical tables created during the analysis phase. This approach separates analytical computation from visualization, resulting in improved maintainability, faster dashboard performance, and consistent business metrics.

---

# 9.2 Dashboard Objectives

The dashboard was designed around three primary objectives:

* Summarize the major analytical findings.
* Enable rapid exploration of marketplace performance.
* Present technical analyses in a business-oriented format.

Instead of functioning as a collection of unrelated charts, each dashboard page tells a specific part of the overall marketplace story.

---

# 9.3 Dashboard Architecture

The dashboard consumes data through the following workflow.

```text
Raw CSV Files
      │
      ▼
PostgreSQL (Raw Schema)
      │
      ▼
Data Profiling
      │
      ▼
Normalized Database
      │
      ▼
Analytics Tables
      │
      ▼
Metabase Dashboard
```

Analytical tables generated during the investigation were published to the `analytics` schema and connected directly to Metabase.

This design ensures that all visualizations reference validated metrics rather than embedding business logic within the dashboard itself.

---

# 9.4 Dashboard Organization

The dashboard consists of three independent analytical pages.

| Dashboard             | Business Focus                                        |
| --------------------- | ----------------------------------------------------- |
| Seller Performance    | Revenue concentration and seller success              |
| Delivery Performance  | Operational efficiency and logistics                  |
| Customer Satisfaction | Relationship between delivery performance and reviews |

Each dashboard corresponds directly to one analytical chapter of this report.

---

# 9.5 Seller Performance Dashboard

The first dashboard investigates marketplace revenue concentration.

It contains three primary visualizations:

* Revenue versus Order Volume
* Lorenz Curve
* Revenue Concentration Summary

The accompanying insight cards summarize the principal findings:

* Revenue is highly concentrated among a relatively small proportion of sellers.
* Order volume is strongly associated with seller revenue.
* Approximately 83% of marketplace revenue is generated by the top 20% of sellers.

Together, these visualizations demonstrate both the magnitude of marketplace inequality and one of its strongest observable drivers.

---

# 9.6 Delivery Performance Dashboard

The second dashboard focuses on operational performance.

Three complementary visualizations are presented:

* Average Delivery Time by Product Category
* Late Delivery Rate by Product Category
* Delivery Speed versus Delivery Reliability

These charts illustrate that delivery performance varies substantially across categories.

Office furniture consistently appears among the poorest-performing categories in terms of both delivery duration and late-delivery rate.

By presenting delivery speed alongside reliability, the dashboard highlights that operational efficiency cannot be evaluated using a single metric.

---

# 9.7 Customer Satisfaction Dashboard

The final dashboard examines customer experience.

Two visualizations summarize the statistical analyses presented in Chapter 8.

* Median Delivery Time by Review Score
* Late Delivery Percentage by Review Score

These visualizations reveal that:

* higher review scores are generally associated with shorter delivery times,
* lower review scores experience substantially higher late-delivery rates.

The dashboard therefore reinforces the conclusion that delivery reliability is an important component of customer satisfaction.

---

# 9.8 Dashboard Design Principles

Several design principles guided dashboard development.

## Simplicity

Only visualizations that directly supported the analytical narrative were included.

Charts providing redundant information were intentionally omitted to reduce cognitive load.

---

## Business Orientation

Every visualization answers a specific business question rather than presenting descriptive statistics without context.

Each page includes concise textual insights summarizing the practical implications of the analysis.

---

## Consistent Metrics

All charts derive their values from analytical tables created during the investigation.

Consequently, identical business definitions are used throughout the dashboard, reducing the risk of inconsistent calculations.

---

## Logical Progression

The dashboard follows the same narrative as the report:

1. Marketplace revenue
2. Operational logistics
3. Customer experience

This progression allows stakeholders to understand how commercial performance, operational efficiency, and customer satisfaction interact.

---

# 9.9 Benefits of the Analytics Layer

A deliberate design decision was to create reusable analytical tables before developing the dashboard.

Compared with performing complex joins inside Metabase, this approach provides several advantages.

* Improved dashboard responsiveness.
* Simpler visualization queries.
* Centralized business logic.
* Reusable metrics across future analyses.
* Easier maintenance.

The dashboard therefore serves primarily as a presentation layer rather than an analytical computation engine.

---

# 9.10 Limitations

The dashboard summarizes validated analytical findings but is not intended to replace detailed statistical analysis.

Several limitations should be noted.

* Visualizations communicate associations rather than causal relationships.
* Dashboard metrics are derived from historical transactional data.
* The dashboard focuses on descriptive and diagnostic analytics and does not include predictive modeling.

Consequently, business decisions should consider both dashboard summaries and the detailed analyses presented throughout this report.

---

# 9.11 Chapter Summary

The Metabase dashboard represents the final stage of the analytical workflow.

By separating data engineering, statistical analysis, and business intelligence into distinct layers, the project produces a dashboard that is both maintainable and analytically consistent.

The dashboard enables stakeholders to quickly explore seller performance, delivery operations, and customer satisfaction while remaining grounded in validated analytical tables and statistically supported findings.

Having completed the presentation layer, the final chapters consolidate the business recommendations, discuss project limitations, and summarize the overall conclusions of the investigation.

# Chapter 10 — Business Recommendations

## 10.1 Overview

The analyses presented throughout this report provide evidence-based insights into seller performance, delivery operations, and customer satisfaction within the Olist marketplace.

This chapter translates those findings into practical business recommendations. These recommendations are intended to improve marketplace performance while recognizing the limitations of the available data.

The recommendations are organized into three strategic areas:

* Marketplace Growth
* Operational Excellence
* Customer Experience

---

# 10.2 Marketplace Growth

## Recommendation 1 — Reduce Revenue Concentration

The analysis demonstrated that approximately **83% of marketplace revenue is generated by the top 20% of sellers**, indicating substantial commercial concentration.

While high-performing sellers are valuable assets, excessive dependence on a small number of businesses introduces operational risk.

Marketplace operators should therefore invest in expanding the commercial success of smaller sellers rather than relying exclusively on existing high performers.

Potential initiatives include:

* improved seller onboarding,
* educational resources,
* promotional campaigns,
* increased marketplace visibility,
* and performance coaching.

Broadening the revenue base improves marketplace resilience while reducing dependence on a limited number of sellers.

---

## Recommendation 2 — Identify Growth Opportunities Among Mid-Tier Sellers

The analysis showed that order volume is strongly associated with seller revenue.

Rather than focusing solely on the largest sellers, marketplace operators should identify medium-sized sellers demonstrating consistent growth and provide targeted support.

Potential strategies include:

* preferred search placement,
* logistics incentives,
* advertising credits,
* and seller development programs.

Increasing order volume among established sellers may generate greater marketplace growth than concentrating resources exclusively on already dominant businesses.

---

# 10.3 Operational Excellence

## Recommendation 3 — Prioritize Underperforming Product Categories

Delivery analysis identified considerable variation in logistics performance across product categories.

Office furniture consistently exhibited:

* longer delivery durations,
* higher late-delivery rates,
* and reduced operational reliability.

Instead of implementing marketplace-wide logistics changes, improvement efforts should prioritize categories exhibiting persistent operational inefficiencies.

Category-specific logistics reviews may identify opportunities related to:

* packaging,
* warehousing,
* transportation,
* carrier selection,
* or inventory placement.

---

## Recommendation 4 — Improve Delivery Reliability

Customer satisfaction analysis demonstrated that late deliveries exhibit a stronger relationship with review scores than modest differences in delivery speed.

Consequently, operational efforts should prioritize reducing late deliveries rather than attempting to minimize average delivery duration alone.

Examples include:

* improving delivery forecasting,
* enhancing carrier coordination,
* increasing shipment visibility,
* and proactively managing delayed orders.

Improving reliability may produce larger customer satisfaction gains than marginal improvements in delivery speed.

---

## Recommendation 5 — Use Freight Characteristics in Logistics Planning

The analysis found that freight value better explains delivery performance than physical product dimensions alone.

Freight value reflects multiple aspects of logistical complexity, including transportation distance, carrier selection, and shipment handling.

Future logistics planning should therefore incorporate freight-related metrics when:

* estimating delivery times,
* allocating operational resources,
* and identifying potentially high-risk deliveries.

---

# 10.4 Customer Experience

## Recommendation 6 — Set Accurate Delivery Expectations

Customers generally appear more tolerant of slower deliveries than of missed delivery promises.

This suggests that expectation management is an important component of customer satisfaction.

Providing realistic delivery estimates may reduce dissatisfaction even when operational improvements are limited.

Where uncertainty exists, conservative delivery estimates may improve customer trust by increasing the likelihood of on-time delivery.

---

## Recommendation 7 — Integrate Operational and Customer Metrics

Delivery performance should not be monitored independently of customer satisfaction.

Operational dashboards should combine logistics indicators with customer feedback metrics to identify emerging service issues more rapidly.

Examples include:

* late-delivery rate,
* average delivery duration,
* review scores,
* and category-level satisfaction trends.

Monitoring these indicators together enables earlier intervention before operational problems become widespread.

---

# 10.5 Data Strategy Recommendations

The project also identified opportunities to improve future analytical capability.

Marketplace operators should consider collecting additional operational variables, including:

* shipment distance,
* warehouse location,
* carrier information,
* inventory availability,
* delivery route characteristics,
* and promotional campaign data.

These variables would enable more comprehensive causal analysis and support predictive modeling.

---

# 10.6 Summary of Recommendations

| Business Area         | Recommendation                                                       |
| --------------------- | -------------------------------------------------------------------- |
| Marketplace Growth    | Reduce dependence on a small number of sellers                       |
| Marketplace Growth    | Invest in high-potential mid-tier sellers                            |
| Logistics             | Prioritize operational improvements for poorly performing categories |
| Logistics             | Focus on reducing late deliveries                                    |
| Logistics             | Incorporate freight characteristics into delivery planning           |
| Customer Experience   | Improve delivery expectation management                              |
| Business Intelligence | Integrate logistics and customer metrics                             |
| Data Strategy         | Collect additional operational variables                             |

---

# 10.7 Chapter Summary

The recommendations presented in this chapter are directly supported by the analytical findings developed throughout the project.

Rather than proposing generic marketplace improvements, each recommendation addresses a specific pattern observed within the Olist dataset.

The analyses suggest that marketplace performance may benefit from:

* broadening the seller revenue base,
* improving logistics reliability,
* prioritizing category-specific operational improvements,
* and strengthening the integration between operational monitoring and customer experience measurement.

The following chapter discusses the limitations of the current investigation and outlines opportunities for future work.

# Chapter 11 — Limitations & Future Work

## 11.1 Overview

Although the analyses presented in this report provide meaningful insights into seller performance, delivery operations, and customer satisfaction, they should be interpreted within the scope and limitations of the available dataset.

Understanding these limitations is essential for appropriately interpreting the results and identifying opportunities for future investigation.

---

# 11.2 Dataset Limitations

The Olist dataset represents historical marketplace transactions collected between 2016 and 2018.

Consequently, the findings reflect the operational characteristics of the marketplace during that period and should not be assumed to represent current marketplace behavior.

Furthermore, the dataset captures transactional outcomes rather than the complete business environment in which those outcomes occurred.

Several potentially influential variables are unavailable.

These include:

* marketing expenditure,
* advertising campaigns,
* inventory levels,
* warehouse locations,
* carrier performance,
* shipment distance,
* weather conditions,
* promotional events,
* and customer demographics beyond basic location information.

The absence of these variables limits the ability to explain certain observed relationships.

---

# 11.3 Correlation Does Not Imply Causation

A central objective of this project was to identify relationships between business variables.

However, statistical association should not be interpreted as proof of causation.

For example:

* Sellers processing more orders generally generate higher revenue.
* Orders with higher freight values generally require longer delivery times.
* Late deliveries are associated with lower review scores.

These findings demonstrate meaningful relationships but do not establish direct causal mechanisms.

Unobserved variables may contribute to the observed patterns.

Consequently, conclusions throughout this report are framed in terms of association rather than causal inference.

---

# 11.4 Data Quality Considerations

Comprehensive profiling demonstrated that the dataset is generally of high quality.

Nevertheless, several anomalies were identified.

Examples include:

* timestamp ordering inconsistencies,
* incomplete product metadata,
* optional review fields with high missingness,
* and historical logging anomalies affecting a small number of delivered orders.

Rather than removing these observations indiscriminately, each anomaly was investigated and documented to preserve transparency.

This approach balances analytical rigor with reproducibility.

---

# 11.5 Scope Limitations

The project intentionally focuses on descriptive and diagnostic analytics.

Several important analytical domains fall outside its scope.

These include:

* customer segmentation,
* demand forecasting,
* recommendation systems,
* churn prediction,
* customer lifetime value estimation,
* and predictive machine learning models.

Restricting the project's scope enabled deeper investigation of the selected business questions while maintaining a coherent analytical narrative.

---

# 11.6 Methodological Limitations

Several methodological decisions should also be considered.

The project emphasizes relational data preparation, statistical analysis, and business intelligence rather than predictive modeling.

Consequently:

* analytical tables were optimized for reporting rather than model training,
* hypothesis testing focused on identifying statistically significant relationships,
* and the dashboard summarizes validated analytical findings instead of providing real-time operational monitoring.

Alternative methodologies may produce complementary insights beyond those presented in this report.

---

# 11.7 Opportunities for Future Work

The analytical foundation developed in this project supports numerous future extensions.

## Predictive Delivery Analytics

Machine learning models could be developed to estimate delivery duration or predict late deliveries before shipment.

Potential features include:

* freight characteristics,
* seller history,
* product category,
* purchase date,
* and customer location.

---

## Revenue Forecasting

Historical marketplace activity could be used to forecast seller revenue, identify seasonal demand patterns, and estimate future marketplace growth.

---

## Customer Segmentation

Behavioral segmentation could identify distinct customer groups based on purchasing patterns, spending behavior, or review activity.

Such segmentation would support targeted marketing and personalized customer experiences.

---

## Geographic Analysis

The geolocation dataset was not extensively explored within the current project.

Future work could investigate:

* regional delivery performance,
* geographic demand,
* seller distribution,
* and logistics efficiency across different areas.

---

## Interactive Analytics

The current dashboard presents validated analytical summaries.

Future versions could incorporate:

* dynamic filtering,
* drill-down capabilities,
* time-based trend analysis,
* and automated KPI monitoring.

---

# 11.8 Lessons Learned

Beyond the business insights themselves, this project reinforced several important principles of practical data analytics.

### Data Preparation Is Fundamental

A substantial proportion of the project was devoted to understanding and validating the dataset before performing business analysis.

Reliable conclusions depend on reliable data.

---

### Business Questions Should Drive Analysis

Every statistical test, visualization, and analytical table was developed to answer a specific business question rather than to demonstrate technical techniques in isolation.

---

### Visualization Complements Statistics

Visualizations communicate patterns effectively, while statistical tests provide quantitative evidence supporting those observations.

Combining both approaches results in stronger analytical conclusions.

---

### Database Design Improves Reproducibility

Separating the workflow into raw, clean, and analytics layers simplified maintenance, improved reproducibility, and enabled straightforward dashboard development.

---

# 11.9 Chapter Summary

This chapter discussed the primary limitations of the project and identified several opportunities for future work.

Although the available dataset imposes constraints on causal interpretation and predictive modeling, it provides a robust foundation for descriptive and diagnostic analytics.

The project's architecture also enables future extensions with minimal restructuring, making it suitable for incorporating additional data sources, predictive models, and more advanced business intelligence applications.

The final chapter summarizes the complete investigation and reflects on the project's overall contributions.

# Chapter 12 — Conclusion

## 12.1 Overview

The objective of this project was to develop an end-to-end analytical workflow capable of transforming raw transactional data into actionable business insights.

Rather than limiting the investigation to exploratory visualization, the project emphasized the complete analytics lifecycle, including data ingestion, profiling, normalization, statistical analysis, and business intelligence reporting.

Using the Olist Brazilian E-Commerce dataset, three major aspects of marketplace performance were investigated:

* Seller performance
* Delivery operations
* Customer satisfaction

Together, these analyses provide a comprehensive view of how commercial activity, logistics performance, and customer experience interact within an online marketplace.

---

# 12.2 Summary of Findings

## Seller Performance

The investigation demonstrated that marketplace revenue is highly concentrated among a relatively small proportion of sellers.

Lorenz Curve analysis and the calculated Gini Coefficient (**0.7915**) revealed substantial inequality in seller revenue distribution.

Further analysis showed that order volume exhibits a strong positive relationship with seller revenue (Pearson = **0.7997**, Spearman = **0.8526**), suggesting that transaction volume is one of the strongest observable indicators of commercial success.

These findings indicate that marketplace growth is driven disproportionately by a relatively small number of sellers.

---

## Delivery Performance

Operational analysis identified considerable variation in logistics performance across product categories.

Office furniture consistently exhibited slower delivery times and higher late-delivery rates than most other categories.

Further investigation demonstrated that freight value is more strongly associated with delivery performance than physical product dimensions, suggesting that logistical complexity extends beyond package size alone.

These results highlight the importance of considering operational characteristics when evaluating delivery performance.

---

## Customer Satisfaction

Customer satisfaction analysis demonstrated statistically significant relationships between delivery performance and review scores.

Although faster deliveries were generally associated with higher customer ratings, delivery reliability emerged as the stronger indicator of customer satisfaction.

Customers appeared more sensitive to missed delivery commitments than to modest differences in delivery duration.

This finding emphasizes the importance of reliable logistics alongside operational efficiency.

---

# 12.3 Technical Contributions

Beyond the business insights themselves, this project demonstrates a complete analytics workflow representative of modern data analytics practice.

Key technical contributions include:

* Construction of a PostgreSQL analytical database.
* Metadata-driven data profiling using PostgreSQL system catalogs.
* Validation of primary keys and foreign-key relationships.
* Database normalization into raw, clean, and analytics layers.
* Development of reusable analytical tables.
* Statistical hypothesis testing using non-parametric methods.
* Interactive dashboard development using Metabase.

By integrating database engineering, statistical analysis, and business intelligence within a single workflow, the project illustrates how reliable analytical systems can be built from raw operational data.

---

# 12.4 Business Contributions

From a business perspective, the project demonstrates how analytical techniques can support evidence-based decision making.

The analyses provide insights into:

* marketplace revenue distribution,
* seller performance,
* operational bottlenecks,
* logistics reliability,
* and customer satisfaction.

These findings may assist marketplace operators in prioritizing operational improvements, strengthening seller development initiatives, and enhancing customer experience through improved delivery performance.

---

# 12.5 Reflection

One of the principal lessons from this project is that meaningful business insights depend on rigorous data preparation.

A significant portion of the project focused on understanding the dataset before performing analysis.

Activities such as validating relational structure, profiling data quality, investigating anomalies, and constructing reusable analytical tables ensured that subsequent analyses were based on trustworthy data.

This workflow reflects an important principle of practical analytics:

> Reliable business conclusions begin with reliable data.

---

# 12.6 Final Remarks

This project demonstrates that effective data analytics extends beyond producing visualizations.

A successful analytical workflow requires:

* understanding the business problem,
* validating the underlying data,
* selecting appropriate statistical methods,
* communicating findings clearly,
* and translating results into practical business recommendations.

The completed workflow—from raw CSV files through PostgreSQL, data profiling, normalization, statistical analysis, and interactive dashboard development—illustrates a complete end-to-end analytics pipeline suitable for real-world business intelligence applications.

While the analyses presented in this report focus specifically on the Olist marketplace, the methodology is broadly applicable to other transactional datasets and can be extended to support predictive analytics, machine learning, and operational decision support systems.

---

# 12.7 Closing Statement

This project successfully transformed raw marketplace transaction data into validated analytical assets, statistically supported business insights, and interactive business intelligence dashboards.

In doing so, it demonstrates not only proficiency in data analysis, but also the importance of data engineering, analytical rigor, and effective communication in delivering reliable, actionable insights from complex relational datasets.
