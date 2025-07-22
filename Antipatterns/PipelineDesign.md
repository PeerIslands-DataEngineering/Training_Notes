

# 🚨 Pipeline Design Anti-Patterns 

---

## 🧱 1. **Monolithic Pipeline Design**

### ❌ Anti-Pattern:

All data extraction, transformation, and loading are done in **one large pipeline or script**.

### 🔍 Why it's bad:

* Hard to debug
* Difficult to scale
* Long reruns on failures
* No modularity

### ✅ Better Pattern:

Break into **modular stages**:

* Extract (raw ingestion)
* Transform (staging)
* Load (curated layer)
* Semantic (marts)

**Use tools like**:

* DBT for modeling
* Airflow/DLT for orchestration

---

## ⏱️ 2. **Tight Coupling of Data Sources and Targets**

### ❌ Anti-Pattern:

Pipeline tightly binds specific source schema to destination logic.

```python
df = spark.read.csv("s3://prod/raw/sales.csv")
df.select("col1", "col2", "col3")...
```

### 🔍 Why it's bad:

* Fails if schema changes
* Reusable logic impossible
* Vendor lock-in

### ✅ Better Pattern:

* Abstract ingestion via configs/metadata
* Use schema evolution tools (e.g., schema registry or Delta auto-schema)

---

## 🔁 3. **Lack of Idempotency**

### ❌ Anti-Pattern:

Pipeline **duplicates records** on every run due to append-based ingestion or missing deduplication logic.

### 🔍 Why it's bad:

* Inflated data
* Hard to trust results
* Troubleshooting is difficult

### ✅ Better Pattern:

* Use **merge/upserts** (e.g., Delta Lake MERGE INTO)
* Use **SCD Type 1 or 2** techniques
* Add **run tracking & job markers**

```sql
MERGE INTO target t
USING updates u
ON t.key = u.key
WHEN MATCHED THEN UPDATE ...
WHEN NOT MATCHED THEN INSERT ...
```

---

## ❌ 4. **No Data Quality Gates**

### Anti-Pattern:

* Pipelines don't validate nulls, constraints, duplicates, or anomalies.

### 🔍 Consequence:

* Garbage in, garbage out
* Silent corruption in downstream models

### ✅ Better Pattern:

* Use **expectations** (DLT, Great Expectations)
* Use **assertions** in DBT models
* Build custom validation layers in Airflow

```sql
-- dbt test
SELECT * FROM sales WHERE order_id IS NULL
```

---

## 📆 5. **Hardcoded Date/Time Filtering**

### ❌ Anti-Pattern:

```python
df.filter("event_date = '2024-06-01'")
```

### 🔍 Why it's bad:

* Non-dynamic, manual reruns
* Breaks backfills and automation

### ✅ Better Pattern:

* Parameterize the date:

```python
process_date = dbutils.widgets.get("process_date")
df.filter(f"event_date = '{process_date}'")
```

* In Airflow/DBT: Use Jinja templating with `ds`, `execution_date`, etc.

---

## 📦 6. **Batching Everything (No Incremental Processing)**

### ❌ Anti-Pattern:

Always truncate and reload entire dataset.

### 🔍 Why it's bad:

* Expensive
* Not scalable
* More prone to errors

### ✅ Better Pattern:

Use **incremental models**:

* DBT `is_incremental()`
* Delta time-travel for incremental pulls

```sql
-- dbt
WHERE updated_at > (SELECT MAX(updated_at) FROM {{ this }})
```

---

## ⚙️ 7. **Poor Orchestration Design**

### ❌ Anti-Pattern:

* All jobs triggered via cron or UI
* No dependency tracking
* No retries, no alerting

### 🔍 Why it's bad:

* Hard to automate
* Silent failures
* Race conditions

### ✅ Better Pattern:

Use orchestrators like:

* **Airflow**
* **Databricks Workflows**
* **DLT pipelines**
  With:
* Task dependencies
* Retry policies
* SLAs and alerting

```python
@task(retries=3)
def extract():
    ...
```

---

## 🔄 8. **Reprocessing Already Processed Data Without Checkpoints**

### ❌ Anti-Pattern:

Every run reloads same data from source without versioning/checks.

### 🔍 Why it's bad:

* Wastes compute
* Creates duplicates
* Time travel useless if overwritten

### ✅ Better Pattern:

* Use **bookmarks**, **watermarks**, or **audit columns**
* For streaming: use structured streaming + checkpointLocation
* Use pipeline state to track processed offsets

---

## 🧪 9. **No Testing / CI for Data Pipelines**

### ❌ Anti-Pattern:

* No dev/prod separation
* No tests for logic correctness
* No code reviews or CI/CD

### 🔍 Why it's bad:

* Risky deployments
* Silent logic failures

### ✅ Better Pattern:

* Use **dbt tests**, **Airflow unit tests**
* Use **GitOps** to version pipeline logic
* Validate changes in staging

---

## 🔥 10. **Too Much Logic in Orchestrator**

### ❌ Anti-Pattern:

Business logic embedded directly in Airflow DAG or Databricks Workflow

```python
def run():
    df = spark.read...
    df = df.filter(...)
    df.write...
```

### 🔍 Why it's bad:

* Orchestrator becomes logic owner
* Difficult to reuse
* No separation of concerns

### ✅ Better Pattern:

* Keep DAG minimal: only trigger tasks
* Push business logic into reusable modules, scripts, or DBT models

---

## 🔗 11. **Inconsistent Data Contracts Between Producers and Consumers**

### ❌ Anti-Pattern:

* Upstream data changes unexpectedly (columns renamed, dropped)
* Downstream breaks silently

### 🔍 Why it's bad:

* Fragility
* Long resolution times

### ✅ Better Pattern:

* Enforce **data contracts**
* Monitor schema drift
* Use tools like:

  * Schema Registry
  * dbt contracts (`@model.contract(enforced=True)`)

---

## 🧰 Tools to Enforce Best Practices

| Tool                          | Use                                          |
| ----------------------------- | -------------------------------------------- |
| **DBT**                       | Modular transformations, testing, versioning |
| **Airflow / Workflows**       | Scheduling, dependency tracking              |
| **Delta Lake**                | ACID, schema enforcement, time travel        |
| **Great Expectations / Soda** | Data validation                              |
| **MLflow / Wandb**            | Metadata and experiment tracking             |
| **Git + CI/CD**               | Deployment hygiene                           |

---

## ✅ Final Pipeline Design Checklist

| Check               | Description                         |
| ------------------- | ----------------------------------- |
| ✅ Modular design    | Split into extract, transform, load |
| ✅ Incremental loads | Avoid full refreshes                |
| ✅ Partitioning used | Filterable, efficient reads         |
| ✅ DQ tests          | Nulls, constraints, duplicates      |
| ✅ Schema control    | Contracts, versioning               |
| ✅ Retry/alerting    | Orchestration best practices        |
| ✅ Monitoring        | Logs, metrics, lineage              |
| ✅ GitOps            | Versioned, tested code              |
| ✅ Parameterization  | Dynamic, rerunnable                 |
| ✅ Clean outputs     | No dangling temp files or partials  |

---
