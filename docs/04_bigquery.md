# Data Warehousing — BigQuery 🏛️

A data warehouse is a system *optimised for analysing large volumes of data* — not for writing individual records, but for running complex queries across millions of rows fast.

**BigQuery is Google's fully managed, serverless data warehouse. No servers to set up, no indexes to tune — just SQL at scale.**

---

## Index 👩🏽‍💻

1. [OLTP vs OLAP](#oltp-vs-olap)
2. [What is BigQuery](#what-is-bigquery)
3. [Core Concepts](#core-concepts)
4. [Partitioning and Clustering](#partitioning-and-clustering)
5. [External Tables](#external-tables)
6. [ML in BigQuery](#ml-in-bigquery)
7. [Why it Matters in Data Engineering](#why-it-matters-in-data-engineering)

---

## OLTP vs OLAP

| | OLTP | OLAP |
|---|---|---|
| Stands for | Online Transaction Processing | Online Analytical Processing |
| Use case | Day-to-day operations (inserts, updates) | Analytics and reporting |
| Examples | Postgres, MySQL | BigQuery, Snowflake, Redshift |
| Query type | Many small, fast queries | Few large, complex queries |
| Optimised for | Write speed | Read speed |

Postgres is great for your app. BigQuery is great for answering "how many users did X last quarter?"

A common real-world setup: a company has Oracle (or any OLTP system) running operations for years. They don't replace it — they build a pipeline that extracts data from it periodically, loads it into BigQuery, and run all analysis and ML there. The operational system keeps doing what it does well, without being affected by heavy analytical queries.

---

## What is BigQuery

- **Serverless** — no infrastructure to manage. Google handles everything.
- **Columnar storage** — data is stored by column, not by row. This makes aggregations (SUM, AVG, COUNT) much faster.
- **Massively parallel** — queries run across many machines at once.
- **Pay per query** — you're charged by the amount of data scanned, not by uptime.

---

## Core Concepts

- **Project** — the top-level container in GCP.
- **Dataset** — a container for tables, like a schema or database.
- **Table** — where data lives. Can be native (stored in BQ) or external (stored elsewhere).
- **Job** — any action in BQ (query, load, export) runs as a job.

---

## Partitioning and Clustering

These are the two main ways to make queries cheaper and faster.

### Partitioning
Splits a table into segments based on a column (usually a date).

- When you query with a date filter, BQ only scans the relevant partitions — not the whole table.
- Huge cost savings on large tables.

```sql
-- Only scans data from 2024, not the whole table
SELECT * FROM trips WHERE date = '2024-01-01'
```

### Clustering
Sorts data within partitions by one or more columns.

- Useful for columns you frequently filter or group by (e.g. `vendor_id`, `payment_type`).
- Works on top of partitioning for additional performance gains.

> Rule of thumb: partition by date, cluster by the columns you filter most.

---

## External Tables

A table that points to data stored outside BigQuery (e.g. in a GCS bucket), without loading it in.

- Useful for raw/landing data you don't want to duplicate.
- Slower to query than native tables (data isn't optimised for BQ).
- Good for exploration before deciding what to load.

---

## ML in BigQuery

BigQuery ML lets you train and run machine learning models directly inside the warehouse using SQL — no need to export data or set up a separate environment.

```sql
-- Train a model
CREATE MODEL my_dataset.fare_model
OPTIONS (model_type = 'linear_reg', input_label_cols = ['fare_amount'])
AS SELECT * FROM my_dataset.trips;

-- Run predictions
SELECT * FROM ML.PREDICT(MODEL my_dataset.fare_model,
  (SELECT * FROM my_dataset.new_trips));
```

### Choosing the right model type

The type of output you want determines the model to use:

| You want to predict | Use |
|---|---|
| A number (price, duration) | Linear regression |
| Yes / No (wins, churns, clicks) | Logistic regression |
| One of several categories (win/draw/loss) | Multiclass logistic regression |
| Natural groups in data | K-means clustering |

### When it's useful
- Data is already in BigQuery — no need to move it
- Quick validation of a hypothesis before building something more complex
- Pricing validation (e.g. flag trips that cost much more than the model expects)
- Churn prediction, customer segmentation, demand forecasting

### When it's not enough
BigQuery ML is not a replacement for PyTorch or TensorFlow. For complex deep learning models or custom architectures, you'd use Vertex AI or train externally and import the model.

---

## Why it Matters in Data Engineering

BigQuery is typically the destination of the pipeline — where cleaned, transformed data lives and gets queried by analysts and dashboards. Understanding how to structure data here affects:
- **Cost** — bad partitioning = scanning terabytes unnecessarily
- **Performance** — good clustering = queries that return in seconds instead of minutes
- **Reliability** — knowing the difference between native and external tables avoids surprises
