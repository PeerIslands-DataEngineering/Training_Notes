
## 🔷 1. Bucketing in PySpark

### ✅ Purpose:

Reduces shuffle during joins by pre-sorting and distributing data into fixed number of buckets.

### 🔍 When to Use:

* Large joins on same key repeatedly
* Improves query performance in **Hive-compatible table formats**

### 🧠 Concept:

```text
Partition = divides data into different files/directories
Bucketing = groups rows by hash of column into fixed number of buckets
```

### 🧪 Example:

```python
df.write \
  .bucketBy(8, "user_id") \
  .sortBy("user_id") \
  .mode("overwrite") \
  .saveAsTable("bucketed_users")
```

> ⚠️ Bucketing works only when used with `.saveAsTable()` and **Hive support** enabled.

### 📌 Enable Hive Support:

```python
spark = SparkSession.builder \
    .appName("BucketingExample") \
    .enableHiveSupport() \
    .getOrCreate()
```

---

## 🔷 2. Sorting in PySpark

### ✅ Purpose:

Improves performance of **merge joins**, **window functions**, **range partitioning**, etc.

### 🔍 When to Use:

* **Before writing** large datasets (minimize shuffle later)
* Optimize joins with sorted data
* Improve **orderBy**, **sort**, **rangeBetween** operations

### 🧪 Example:

```python
df_sorted = df.sort("user_id")
```

### ➕ Optimized write:

```python
df_sorted.write \
  .partitionBy("country") \
  .sortBy("user_id") \
  .parquet("/output/sorted/")
```

> Sorting helps reduce the cost of downstream shuffle-heavy operations.

---

## 🔷 3. Caching / Persisting

### ✅ Purpose:

Speeds up repeated access to the same data by **storing it in memory** or **disk+memory**.

### 🧠 Cache vs Persist:

* `cache()` = shorthand for `persist(StorageLevel.MEMORY_AND_DISK)`
* `persist()` = allows choosing storage level explicitly

### 🔍 When to Use:

* Reuse the same DataFrame across multiple actions or stages
* Avoid recomputation in iterative algorithms (e.g. ML)

### 🧪 Example:

```python
df.cache()
df.count()  # triggers cache
```

OR

```python
from pyspark.storagelevel import StorageLevel

df.persist(StorageLevel.MEMORY_ONLY)
```

### ⚠️ Tips:

* Use `df.unpersist()` to free memory
* Don't cache small one-time-use datasets
* Monitor via Spark UI (Storage tab)

---

## 🔷 4. Broadcast Joins

### ✅ Purpose:

Avoids shuffling large data by **sending smaller table to all executors**

### 🔍 When to Use:

* One table is **very small** (\~<10MB or <1 million rows)
* Joins between large and small DataFrames

### 🧠 Concept:

Spark performs join without moving large table’s partitions

### 🧪 Example:

```python
from pyspark.sql.functions import broadcast

# df_small is the smaller DataFrame
joined_df = df_large.join(broadcast(df_small), "id")
```

### 🔧 Config:

Auto broadcast enabled by default:

```python
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 10 * 1024 * 1024)  # 10 MB
```

Set to `-1` to disable.

---

## 🔷 5. Combined Optimization Example

Suppose you're joining user transactions and user metadata, filtering top spenders.

### ✅ Optimized Flow:

```python
# Enable Hive support for bucketing if needed
spark = SparkSession.builder.enableHiveSupport().getOrCreate()

# Cache reusable dimension table
user_meta = spark.read.parquet("/user/meta").cache()

# Bucketed write for future optimization
user_txn.write.bucketBy(16, "user_id").sortBy("user_id").saveAsTable("bucketed_txn")

# Join using broadcast
from pyspark.sql.functions import broadcast
result = user_txn.join(broadcast(user_meta), "user_id") \
                 .filter("total_spend > 10000") \
                 .select("user_id", "country", "total_spend")
result.show()
```

---

## 🔷 6. Additional Performance Tips

| Technique                      | Benefit                                            |
| ------------------------------ | -------------------------------------------------- |
| `spark.sql.shuffle.partitions` | Reduce from default 200 to 50/100 for faster joins |
| `coalesce()`                   | Reduce partitions before write                     |
| `repartition()`                | Increase parallelism before wide transformations   |
| `.explain(True)`               | Analyze physical plan for optimizations            |
| Catalyst Optimizer             | Ensures logical plan is rewritten for efficiency   |

---

## 🔷 7. Summary Table

| Feature            | When to Use                   | Benefit              | Example                            |
| ------------------ | ----------------------------- | -------------------- | ---------------------------------- |
| **Bucketing**      | Repeated joins on same key    | Minimize shuffle     | `.bucketBy(8, "id").saveAsTable()` |
| **Sorting**        | Before window or range ops    | Optimized processing | `.sort("user_id")`                 |
| **Caching**        | Reuse across multiple actions | Faster access        | `.cache()`                         |
| **Broadcast Join** | Join with small table         | Avoid shuffling      | `join(broadcast(df))`              |
