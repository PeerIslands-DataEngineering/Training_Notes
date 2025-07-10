Delta Live Tables (DLT) is a **Databricks-native framework for building reliable, maintainable, and declarative data pipelines**. It builds on top of **Delta Lake** and **Apache Spark**, providing enhanced functionality for managing data transformations, especially in **ETL/ELT pipelines**.

---

### 🔍 What is Delta Live Tables (DLT)?

Delta Live Tables is a framework that allows **declarative pipeline development** using SQL or Python. It handles:

* Table creation and schema enforcement
* Job orchestration
* Data quality checks
* Error handling and retries
* Efficient incremental updates (Change Data Capture)
* Lineage tracking and logging

---

### ✅ Key Features

| Feature                          | Description                                                                         |
| -------------------------------- | ----------------------------------------------------------------------------------- |
| **Declarative Pipelines**        | Use SQL or Python decorators (`@dlt.table`) to declare what should happen, not how. |
| **Automatic Table Management**   | No need to define target table schema manually. DLT handles it.                     |
| **CDC (Change Data Capture)**    | Built-in support for slowly changing data and streaming updates.                    |
| **Data Quality Enforcement**     | Use `@dlt.expect()` to define expectations and handle bad records.                  |
| **Lineage Tracking**             | Built-in UI to visualize dependencies and data flow.                                |
| **Batch and Streaming Modes**    | Supports both batch and streaming jobs transparently.                               |
| **Orchestration and Monitoring** | Pipeline scheduling, retries, versioning, logs, alerts.                             |

---

### 🧠 Key Concepts

1. **DLT Pipeline**: A workflow made up of multiple stages/tables.
2. **DLT Tables**: Tables declared using SQL or Python decorated with `@dlt.table`.
3. **Views**: Intermediate transformations that are not materialized as permanent tables.
4. **Expectations**: Declarative constraints and checks for data validation.
5. **Materialization Modes**:

   * `LIVE TABLE`: Persisted table
   * `LIVE VIEW`: Temporary view for intermediate logic

---

## 🧪 Example 1: SQL-Based Delta Live Tables

### Pipeline Goal: Ingest CSV from cloud storage, clean data, and aggregate by category.

### 📁 Input File:

`/mnt/data/products.csv`:

```csv
id,name,category,price
1,Keyboard,Electronics,29.99
2,Mouse,Electronics,19.99
3,Shampoo,Personal Care,5.99
4,Conditioner,Personal Care,6.49
```

### 🧾 Step-by-step SQL DLT

```sql
-- Bronze Layer: Raw ingestion
CREATE OR REFRESH LIVE TABLE raw_products
AS SELECT * FROM cloud_files(
  "/mnt/data/products.csv", 
  "csv", 
  map("header", "true", "inferSchema", "true")
);

-- Silver Layer: Cleaned Data
CREATE OR REFRESH LIVE TABLE clean_products
AS SELECT
  id,
  name,
  category,
  price
FROM LIVE.raw_products
WHERE price > 0;

-- Gold Layer: Aggregation
CREATE OR REFRESH LIVE TABLE category_price_summary
AS SELECT
  category,
  COUNT(*) AS total_products,
  ROUND(AVG(price), 2) AS avg_price
FROM LIVE.clean_products
GROUP BY category;
```

---

## 🧪 Example 2: Python-based Delta Live Tables with Expectations

```python
import dlt
from pyspark.sql.functions import col

# Bronze table
@dlt.table(
  name="raw_products"
)
def load_raw_data():
    return (
        spark.read.format("csv")
        .option("header", "true")
        .option("inferSchema", "true")
        .load("/mnt/data/products.csv")
    )

# Silver table with expectations
@dlt.table
@dlt.expect("valid_price", "price > 0")
@dlt.expect_or_drop("valid_category", "category IS NOT NULL")
def clean_data():
    df = dlt.read("raw_products")
    return df.select("id", "name", "category", "price")

# Gold aggregation
@dlt.table
def summary_by_category():
    df = dlt.read("clean_data")
    return df.groupBy("category").agg({"price": "avg", "*": "count"})
```

---

## 🔁 Incremental Data Processing

DLT automatically tracks and processes **only new or changed data** using:

* **Auto Loader** + **Streaming Reads**
* **Merge/Update/Delete** on Delta Lake
* **CDC for slowly changing dimensions (SCD)**

---

## 🧱 Medallion Architecture with Delta Live Tables

| Layer  | DLT Table           | Description                                       |
| ------ | ------------------- | ------------------------------------------------- |
| Bronze | `raw_events`        | Ingests raw data from external source             |
| Silver | `cleaned_events`    | Applies schema, filters bad data                  |
| Gold   | `aggregated_events` | Business-level aggregations, ready for dashboards |

---

## 📈 Monitoring in UI

* Go to **Databricks Workspace → Workflows → Delta Live Tables**
* View:

  * Pipeline status
  * Data lineage
  * Throughput and latency
  * Failures and retries
  * Logs and checkpoints

---

## 🧩 Data Quality with `@dlt.expect`

| Function                               | Behavior                                    |
| -------------------------------------- | ------------------------------------------- |
| `@dlt.expect(name, condition)`         | Logs failures but processes all rows        |
| `@dlt.expect_or_drop(name, condition)` | Drops rows that fail the condition          |
| `@dlt.expect_or_fail(name, condition)` | Fails the pipeline if condition is violated |

---

## 💡 When to Use Delta Live Tables?

| Scenario                                       | Use DLT?            |
| ---------------------------------------------- | ------------------- |
| Building real-time + batch ETL pipelines       | ✅ Yes               |
| Need data lineage and monitoring               | ✅ Yes               |
| Want built-in data quality checks              | ✅ Yes               |
| Running ad-hoc analysis                        | ❌ No                |
| Only batch ingestion with no automation needed | ⚠️ Maybe not needed |

---
