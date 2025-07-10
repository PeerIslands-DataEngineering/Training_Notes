
## 🏛️ Medallion Architecture?

**Medallion Architecture** is a **data lake design pattern** that organizes data into **progressive layers** of refinement:

| Layer  | Purpose                                      | Example Data                       |
| ------ | -------------------------------------------- | ---------------------------------- |
| Bronze | Raw ingestion (semi-structured/unstructured) | JSON from Kafka, CSV from blob     |
| Silver | Cleaned & transformed data                   | Joins, filtered, standardized data |
| Gold   | Aggregated and business-level data           | KPIs, dashboards, ML features      |

This helps in **data quality management**, **modularity**, **scalability**, and **auditing**.

---

## 🔧 Tools Used

* **Azure Databricks** (for compute)
* **Azure Data Lake Storage (ADLS)** (for data storage)
* **Delta Lake** (for ACID-compliant transactions)
* **PySpark** (for transformation logic)

---

## 📁 Sample Data

Let's assume we have raw JSON files of **e-commerce order data**:

```json
{
  "order_id": "O123",
  "user_id": "U456",
  "order_amount": "45.67",
  "order_date": "2025-06-15",
  "status": "shipped"
}
```

---

## 🔰 1. Bronze Layer – Raw Ingestion

**Goal**: Ingest raw data from source and store as-is.

### Code:

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import input_file_name

spark = SparkSession.builder.appName("BronzeLayer").getOrCreate()

# Ingest raw JSON from ADLS
raw_df = spark.read.option("multiline", True).json("abfss://raw@<storage_account>.dfs.core.windows.net/orders/")

# Add audit columns
bronze_df = raw_df.withColumn("source_file", input_file_name())

# Write to Bronze Delta table
bronze_df.write.format("delta") \
    .mode("overwrite") \
    .save("/mnt/datalake/bronze/orders")

# Register table
spark.sql("CREATE TABLE IF NOT EXISTS bronze_orders USING DELTA LOCATION '/mnt/datalake/bronze/orders'")
```

**Bronze Layer Summary:**

* No heavy transformations.
* Ideal for auditing and replaying.
* Uses Delta format for ACID guarantees.

---

## ⚙️ 2. Silver Layer – Cleaned & Enriched Data

**Goal**: Clean, normalize, and enrich data.

### Tasks:

* Convert data types.
* Filter invalid records.
* Add new derived columns.

### Code:

```python
from pyspark.sql.functions import col, to_date

bronze_df = spark.read.format("delta").load("/mnt/datalake/bronze/orders")

# Basic cleaning
silver_df = bronze_df.filter(col("order_amount").isNotNull()) \
    .withColumn("order_amount", col("order_amount").cast("double")) \
    .withColumn("order_date", to_date("order_date", "yyyy-MM-dd"))

# Write to Silver layer
silver_df.write.format("delta") \
    .mode("overwrite") \
    .save("/mnt/datalake/silver/orders")

spark.sql("CREATE TABLE IF NOT EXISTS silver_orders USING DELTA LOCATION '/mnt/datalake/silver/orders'")
```

**Silver Layer Summary:**

* Structured and standardized.
* Easier to join with reference data.
* Supports analytics and machine learning.

---

## 👑 3. Gold Layer – Aggregated Business-Level Data

**Goal**: Provide business-specific outputs for reporting or ML.

### Use Case:

Let's say the business wants to see **daily total revenue**.

### Code:

```python
silver_df = spark.read.format("delta").load("/mnt/datalake/silver/orders")

gold_df = silver_df.groupBy("order_date").sum("order_amount") \
    .withColumnRenamed("sum(order_amount)", "daily_revenue")

# Write to Gold layer
gold_df.write.format("delta") \
    .mode("overwrite") \
    .save("/mnt/datalake/gold/daily_revenue")

spark.sql("CREATE TABLE IF NOT EXISTS gold_daily_revenue USING DELTA LOCATION '/mnt/datalake/gold/daily_revenue'")
```

**Gold Layer Summary:**

* Aggregations and metrics.
* Used by BI tools (e.g., Power BI, Tableau).
* Can power real-time dashboards or ML features.

---

## 📊 Architecture Diagram

```
         ┌────────────┐
         │  Raw Data  │ (Kafka, CSV, JSON)
         └─────┬──────┘
               ▼
        ┌────────────┐
        │  Bronze    │ (Raw ingested)
        └─────┬──────┘
              ▼
        ┌────────────┐
        │  Silver    │ (Cleaned & enriched)
        └─────┬──────┘
              ▼
        ┌────────────┐
        │   Gold     │ (Aggregated/Business-ready)
        └────────────┘
```

---

## ✅ Benefits of Medallion Architecture

| Benefit                  | Description                                           |
| ------------------------ | ----------------------------------------------------- |
| Data Lineage             | Easy to trace data from gold → silver → bronze        |
| Reprocessing Flexibility | Re-run transformations if downstream data is corrupt  |
| Cost-Efficient           | Avoids reloading from external sources                |
| Modularity               | Different teams can own different layers              |
| Time Travel              | Delta Lake provides versioning for debugging/auditing |
| Scalability              | Can easily plug into CI/CD, Airflow, ML pipelines     |

---

## 🧪 Example: Time Travel & Auditing

Delta Lake lets you roll back or query data as it was before:

```sql
-- Query Gold table as it was yesterday
SELECT * FROM gold_daily_revenue VERSION AS OF 3
```

---

## 🛠️ Partitioning & Performance Tips

* Partition Bronze/Silver by ingestion date.
* Partition Gold by business dimensions (e.g., `order_date`, `region`).
* Use ZORDER for optimizing read-heavy Gold tables.

```python
gold_df.write.format("delta") \
    .mode("overwrite") \
    .option("overwriteSchema", "true") \
    .partitionBy("order_date") \
    .save("/mnt/datalake/gold/daily_revenue")
```

---

## 🔄 Integration with Airflow / ADF

You can schedule these workflows using:

* **Apache Airflow** via Databricks Operator
* **Azure Data Factory** pipelines with Databricks activities

---

## 🔚 Conclusion

The Medallion Architecture provides a **structured, scalable** approach to **data lake design** using **Delta Lake on Databricks**. It enables:

* Layered data quality
* Easier debugging and reprocessing
* Simplified data governance

---
