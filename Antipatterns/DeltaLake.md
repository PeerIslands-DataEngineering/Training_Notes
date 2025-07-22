
# 🧱 Delta Lake Anti-Patterns 

This guide focuses on **common mistakes in Delta Lake usage**, especially in a **Databricks** environment, and how to avoid them for better **performance, cost, and reliability**.

---

## ❗ Why Anti-Patterns Matter

Delta Lake offers:

* ACID transactions
* Time travel
* Scalable storage
* Schema enforcement & evolution

But poor practices can cause:

* Expensive queries
* Small file issues
* Metadata bloat
* Version history issues
* Merge conflicts

---

## 🗂️ Table Design Anti-Patterns

### ❌ No Partitioning

```python
df.write.format("delta").save("/mnt/sales")  # No partitioning
```

**Problem**:

* Entire table is scanned on queries
* Poor performance

**Fix**:
Partition by low-cardinality, query-driven columns:

```python
df.write.partitionBy("event_date").format("delta").save("/mnt/sales")
```

---

### ❌ Over-Partitioning

```python
df.write.partitionBy("user_id").format("delta").save("/mnt/sales")
```

**Problem**:

* Creates millions of directories
* Metadata overhead
* Spark job slowdowns

**Fix**:
Partition by `event_date`, `region`, etc.

---

## 💾 Write Anti-Patterns

### ❌ Small Files Explosion

```python
df.write.mode("append").format("delta").save("/mnt/sales")
```

**Problem**:

* Too many files
* Expensive metadata handling

**Fix**:
Use `coalesce()` or `repartition()`:

```python
df.coalesce(10).write.format("delta").save("/mnt/sales")
```

---

### ❌ Overwrites on Large Tables

```python
df.write.mode("overwrite").format("delta").save("/mnt/sales")
```

**Problem**:

* Deletes entire partition
* Poor performance

**Fix**:
Use **dynamic partition overwrite**:

```python
spark.conf.set("spark.sql.sources.partitionOverwriteMode", "dynamic")
df.write.mode("overwrite").partitionBy("event_date").format("delta").save("/mnt/sales")
```

---

## 🧮 Query / Read Anti-Patterns

### ❌ Full Table Scans

```sql
SELECT * FROM sales
```

**Problem**:

* Loads all partitions and files

**Fix**:
Filter by partition:

```sql
SELECT * FROM sales WHERE event_date = '2024-01-01'
```

---

### ❌ No OPTIMIZE or ZORDER

**Problem**:

* No data skipping
* Read slowness

**Fix**:
Use `OPTIMIZE`:

```sql
OPTIMIZE sales ZORDER BY (customer_id)
```

---

## 🔄 MERGE / UPSERT Anti-Patterns

### ❌ Merge Without Partition Filter

```sql
MERGE INTO sales AS t
USING updates AS u
ON t.order_id = u.order_id
```

**Problem**:

* Full scan of both tables

**Fix**:
Filter by partition:

```sql
MERGE INTO sales AS t
USING (SELECT * FROM updates WHERE event_date = '2024-01-01') u
ON t.order_id = u.order_id AND t.event_date = u.event_date
```

---

### ❌ Merge Repeatedly with Same Keys

**Problem**:

* Delta logs grow rapidly
* Conflicts, slow writes

**Fix**:

* Use deduplicated keys before merge
* Or use merge condition with timestamp filter

---

## 🧹 VACUUM / OPTIMIZE / ZORDER Anti-Patterns

### ❌ Not Running VACUUM

**Problem**:

* Unused data accumulates
* Cost and storage spike

**Fix**:

```sql
VACUUM sales RETAIN 168 HOURS
```

**Never do this** in automated pipelines:

```sql
VACUUM sales RETAIN 0 HOURS
```

---

### ❌ OPTIMIZE Without ZORDER

```sql
OPTIMIZE sales
```

**Better**:

```sql
OPTIMIZE sales ZORDER BY (product_id, customer_id)
```

ZORDER helps data skipping when queries filter on those columns.

---

## ⚙️ Schema Evolution Anti-Patterns

### ❌ Overuse of mergeSchema

```python
df.write.option("mergeSchema", "true")...
```

**Problem**:

* Hidden schema changes
* Future compatibility issues

**Fix**:
Use version-controlled schemas or `StructType`.

---

### ❌ Inconsistent Schemas Between Systems

Example:

* Adding a column manually in SQL
* Spark job unaware of schema change

**Fix**:
Maintain schema through controlled pipelines and contracts.

---

## 👁️ Time Travel Anti-Patterns

### ❌ Blind Use of Time Travel

```sql
SELECT * FROM sales VERSION AS OF 3
```

**Problem**:

* Version might have been removed by `VACUUM`
* Job fails

**Fix**:

* Set retention period properly:

```sql
ALTER TABLE sales SET TBLPROPERTIES ('delta.logRetentionDuration' = '7 days')
```

---

## ✅ Best Practices Summary

| Area             | Anti-Pattern             | Fix                                        |
| ---------------- | ------------------------ | ------------------------------------------ |
| Partitioning     | None / Over-partitioning | Use meaningful, low-cardinality columns    |
| Writes           | Many small files         | Use `coalesce` / `repartition`             |
| Overwrites       | Whole table overwrites   | Use merge or dynamic partition overwrite   |
| Reads            | No filters               | Filter by partition columns                |
| MERGE            | Full scan                | Add partition filters in merge             |
| OPTIMIZE         | Not using ZORDER         | Use ZORDER for frequently filtered columns |
| Schema Evolution | mergeSchema overuse      | Use explicitly managed schema changes      |
| Time Travel      | Ignoring vacuum policy   | Set and respect retention policies         |

---