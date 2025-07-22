

## 1. ❌ Why Anti-Patterns Matter

**Anti-patterns** are bad practices that reduce **performance**, **scalability**, and **maintainability**.

> PySpark is built on distributed computing. Using it like local Python or SQL can lead to slow jobs and cluster overload.

---

## 2. ⚠️ General PySpark Anti-Patterns

### ❌ Using Python lists or for-loops with large datasets

```python
# BAD: Collecting and looping on driver
ids = df.select("id").rdd.flatMap(lambda x: x).collect()
for id in ids:
    process(id)
```

✅ **Fix**: Use `DataFrame` APIs instead of collecting to driver.

---

### ❌ Using `.collect()` unnecessarily

```python
# BAD
data = df.collect()  # brings all data to driver
```

✅ **Fix**:

```python
# Only use collect() for small debugging datasets
df.show()  # better for preview
```

---

### ❌ Using Python-native functions instead of Spark APIs

```python
# BAD
df.rdd.map(lambda x: my_python_function(x.col)).collect()
```

✅ **Fix**: Use native Spark functions (from `pyspark.sql.functions`).

---

## 3. 🧱 DataFrame Operation Anti-Patterns

### ❌ Chain too many `withColumn()`

```python
df = df.withColumn("col1", ...)
df = df.withColumn("col2", ...)
df = df.withColumn("col3", ...)
```

Each `withColumn` creates a new plan stage.

✅ **Fix**:

```python
df = df.selectExpr("*", "expr1 as col1", "expr2 as col2", "expr3 as col3")
```

---

### ❌ Repartition unnecessarily

```python
# BAD: Over-repartitioning
df = df.repartition(1000)
```

✅ **Fix**:

* Use `.repartition(n)` **only when increasing parallelism** is needed
* Use `.coalesce(n)` when **shrinking partitions**

---

## 4. 🧠 Transformation vs Action Anti-Patterns

### ❌ Forgetting lazy evaluation and triggering multiple actions

```python
# BAD
df.count()
df.collect()
df.write.csv(...)
```

Each action recomputes the lineage.

✅ **Fix**:

```python
df.cache()
df.count()
df.collect()
```

---

## 5. 🪓 Shuffle and Partitioning Anti-Patterns

### ❌ Ignoring shuffle cost

Operations like `.groupBy()`, `.join()`, `.distinct()`, `.orderBy()` cause **shuffles**.

```python
df.groupBy("country").count()  # expensive if skewed
```

✅ **Fix**:

* Use **approximate aggregations** if possible
* Tune `spark.sql.shuffle.partitions`
* Use `salting` or `skew join optimization` if needed

---

### ❌ Not using proper partitioning before writing

```python
# BAD: Huge files in single partition
df.coalesce(1).write.csv("...")
```

✅ **Fix**:

* Use `.repartition()` for writing in parallel
* Partition by relevant columns:

```python
df.write.partitionBy("year", "month").parquet("...")
```

---

## 6. 💾 Storage and Caching Anti-Patterns

### ❌ Caching everything

```python
# BAD
df.cache()  # large dataset, not reused later
```

✅ **Fix**:
Only cache if:

* It is reused multiple times
* Fits into memory

---

### ❌ Not persisting complex computations

```python
df = df.withColumn("col", complex_udf("col"))
df.show()
df.write.parquet("...")
```

UDF is re-evaluated twice.

✅ **Fix**:

```python
df = df.persist()
df.show()
df.write.parquet("...")
```

---

## 7. 🔥 Join and Broadcast Anti-Patterns

### ❌ Blindly joining large tables

```python
df1.join(df2, "id")  # both are large
```

✅ **Fix**: Use `broadcast()` if one side is small.

```python
from pyspark.sql.functions import broadcast
df1.join(broadcast(df2), "id")
```

---

### ❌ Not handling skewed joins

If a column has high cardinality (e.g., country = ‘US’ for 90% rows), shuffle will be skewed.

✅ **Fix**:

* Add salting keys
* Use adaptive query execution (AQE):

```python
spark.conf.set("spark.sql.adaptive.enabled", "true")
```

---

## 8. 🚫 UDF Anti-Patterns

### ❌ Overusing Python UDFs

```python
from pyspark.sql.functions import udf

@udf
def my_func(x): return x + 1

df = df.withColumn("new", my_func("old"))
```

✅ **Fix**:

* Use built-in SQL functions (from `pyspark.sql.functions`)
* Use **Pandas UDFs** if necessary (vectorized)

---

## 9. 📉 Performance Bottlenecks and Fixes

| Problem           | Anti-pattern            | Solution                   |
| ----------------- | ----------------------- | -------------------------- |
| Driver OOM        | `collect()` on big data | Use `.show()`, `.limit()`  |
| Task Skew         | groupBy on skewed key   | Add salt key or use AQE    |
| Memory issues     | Over-caching            | Unpersist when not needed  |
| Long job duration | Too many shuffles       | Reduce groupBy/orderBy     |
| Large files       | coalesce(1) on write    | Use `repartition()` wisely |

---

## 10. ✅ Best Practices Summary

| ✅ Do This                                | ❌ Avoid This                  |
| ---------------------------------------- | ----------------------------- |
| Use DataFrame API                        | Using RDDs unless needed      |
| Use built-in functions                   | Using Python UDFs             |
| Use broadcast joins wisely               | Joining large tables directly |
| Use `repartition()` before wide shuffles | Too many default partitions   |
| Cache only when reused                   | Blind caching                 |
| Use AQE and dynamic partition pruning    | Static join strategies        |

---

## Optional: Anti-pattern Visualization Example

### ❌ Bad

```python
data = df.collect()
for row in data:
    print(row['id'])
```

### ✅ Good

```python
df.select("id").show()
```

---
