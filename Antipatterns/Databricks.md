

# 🛑 Databricks Anti-Patterns 
---

## ⚠️ 1. Not Using Delta Lake (Still Using CSV/Parquet as Sink)

### ❌ Anti-Pattern:

Storing transformed output as **CSV/Parquet** in S3/ADLS.

```python
df.write.parquet("abfss://bucket/output/")
```

### 🔍 Why it fails:

* No ACID transactions
* No schema evolution
* Difficult to handle updates/deletes
* No time travel

### ✅ Better Pattern:

Always write **Delta format** for reliable and queryable data.

```python
df.write.format("delta").mode("overwrite").save("/mnt/output")
```

---

## 🧹 2. No VACUUM or OPTIMIZE Calls on Delta Tables

### ❌ Anti-Pattern:

Letting Delta tables accumulate small files and stale data.

### 🔍 Issues:

* Read performance drops
* Storage costs increase
* Metadata gets bloated

### ✅ Better Pattern:

Schedule regular **OPTIMIZE** and **VACUUM** on large and frequently used tables.

```sql
OPTIMIZE sales_data ZORDER BY (customer_id);
VACUUM sales_data RETAIN 168 HOURS;
```

---

## 🪓 3. Overusing `.repartition()` or `.coalesce()` Without Reason

### ❌ Anti-Pattern:

Blindly using:

```python
df.repartition(200)
```

Or aggressively shrinking partitions with `.coalesce(1)`

### 🔍 Why it's bad:

* Overloads the shuffle stage
* Can lead to out-of-memory errors
* Reduces parallelism

### ✅ Better Pattern:

Let Spark **auto-tune** partitioning or use:

```python
df.repartition("customer_id")  # Based on key, not number
```

---

## 📜 4. Using Display() and Collect() in Production Notebooks

### ❌ Anti-Pattern:

```python
display(df)
print(df.collect())
```

### 🔍 Why it's bad:

* `collect()` brings full data to driver → crashes on large datasets
* `display()` is for exploration only

### ✅ Better Pattern:

* Use `.show(truncate=False)` for preview
* Use `.write()` or `.count()` for production operations

---

## 🧩 5. Missing Schema Enforcement and Evolution

### ❌ Anti-Pattern:

Writing data without specifying schema:

```python
spark.read.json("...")  # infers schema
```

### 🔍 Problems:

* Inconsistent schemas across files
* Breaks queries in Unity Catalog

### ✅ Better Pattern:

Use **explicit schema definition** or enforce schema-on-write.

```python
schema = StructType([...])
df = spark.read.schema(schema).json("...")
```

And for Delta:

```python
.write.option("mergeSchema", "true")
```

---

## 🏗️ 6. Flat Architecture: No Medallion Layers

### ❌ Anti-Pattern:

Putting all datasets in one path/table: `db.raw_data_combined`

### 🔍 Risks:

* Hard to debug lineage
* No separation of trusted vs untrusted data

### ✅ Better Pattern:

Use **Medallion Architecture**:

| Layer  | Use                          |
| ------ | ---------------------------- |
| Bronze | Raw ingestion (landing zone) |
| Silver | Cleaned and validated        |
| Gold   | Aggregated, curated for BI   |

Organize paths as:

```bash
/mnt/bronze/events/
/mnt/silver/customers/
/mnt/gold/aggregates/
```

---

## 🔄 7. Overwriting Delta Tables Without Time Travel Versioning

### ❌ Anti-Pattern:

Using `.mode("overwrite")` every run with same location.

### 🔍 Risks:

* Loses history
* Time travel becomes useless

### ✅ Better Pattern:

* Use `append` + business keys + merge/upsert logic
* Retain previous versions for auditing

---

## 🔧 8. Running Workloads in All-Purpose Clusters

### ❌ Anti-Pattern:

Using **interactive (all-purpose)** clusters for scheduled workflows.

### 🔍 Problems:

* High cost (clusters always on)
* Resource waste

### ✅ Better Pattern:

Use **job clusters** or **serverless compute** for jobs.

```json
"new_cluster": {
  "node_type_id": "Standard_DS3_v2",
  "spark_version": "13.x-scala2.12",
  "autoscale": { "min_workers": 1, "max_workers": 5 }
}
```

---

## 🚨 9. Improper Join Strategies and No Broadcast Hints

### ❌ Anti-Pattern:

Large join without optimization:

```python
df1.join(df2, "id")
```

### 🔍 Problem:

* Full shuffle join → slow

### ✅ Better Pattern:

Use **broadcast joins** when one dataset is small:

```python
df1.join(broadcast(df2), "id")
```

Or tune join strategy via `spark.sql.autoBroadcastJoinThreshold`.

---

## 🛠️ 10. Using Python UDFs Instead of Built-in Spark Functions

### ❌ Anti-Pattern:

```python
@udf
def custom_func(x): ...
df.withColumn("new", custom_func(col("old")))
```

### 🔍 Problem:

* Slower (no codegen, JVM-Python overhead)
* Not optimized by Catalyst

### ✅ Better Pattern:

Use built-in Spark functions wherever possible (`regexp_replace`, `when`, `date_format`, etc.)

---

## 📂 11. Unmanaged Tables Without Metadata Catalog

### ❌ Anti-Pattern:

Using unmanaged tables (pure file-based):

```python
df.write.format("delta").save("/mnt/silver/data/")
```

No table metadata, no access control.

### ✅ Better Pattern:

Use Unity Catalog + managed tables:

```sql
CREATE TABLE my_catalog.my_schema.sales_data
USING DELTA
LOCATION '/mnt/silver/sales_data'
```

Or via PySpark:

```python
df.write.saveAsTable("my_catalog.my_schema.sales_data")
```

---

## ⚙️ 12. Manual Job Dependencies Without Workflow Pipelines

### ❌ Anti-Pattern:

Chaining notebooks manually or with external scripts

### 🔍 Risks:

* No retry/alerting
* Fragile and non-modular

### ✅ Better Pattern:

Use:

* **Databricks Workflows**
* **Airflow with DatabricksSubmitRunOperator**
* **DLT pipelines**

---

## 📉 13. No Monitoring or Logging for Job Runs

### ❌ Anti-Pattern:

No logs or metrics for jobs, just visual notebook outputs

### 🔍 Risks:

* No visibility during failures
* Hard to debug performance bottlenecks

### ✅ Better Pattern:

* Use MLflow logging for metrics
* Set up Job Alerts and Webhook notifications
* Export logs to external monitoring (e.g., Azure Monitor, CloudWatch)

---

## ✅ Summary Checklist

| ✅ Best Practice                     | ❌ Anti-Pattern               |
| ----------------------------------- | ---------------------------- |
| Use Delta format                    | CSV/Parquet sinks            |
| Modular layers (Bronze/Silver/Gold) | Flat design                  |
| Managed tables in Unity Catalog     | File-based tables            |
| OPTIMIZE + ZORDER + VACUUM          | Letting tables bloat         |
| Job clusters or serverless          | All-purpose clusters         |
| Broadcast small tables              | Shuffle joins without tuning |
| Use Spark functions                 | Python UDFs                  |
| Use DLT/Workflows                   | Manual chaining              |
| Schema-on-write                     | Infer schema blindly         |
| Monitor jobs                        | No observability             |

---
