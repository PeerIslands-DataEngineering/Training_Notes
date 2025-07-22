

## ⚠️ 1. Why Spark Performance Anti-Patterns Matter

Spark is a **distributed processing engine**, and performance issues arise when:

* You use it like local Python/SQL
* You ignore partitioning/shuffles
* You misuse joins or UDFs
* You underutilize built-in optimizations like **Catalyst**, **AQE**, **Tungsten**

---

<a name="section2"></a>

## ❌ 2. Data Ingestion Anti-Patterns

### ❌ Reading massive files without schema inference

```python
# BAD
df = spark.read.json("hdfs://logs/")
```

* Spark infers schema at runtime = slow

✅ **Fix**:

```python
from pyspark.sql.types import *
schema = StructType([...])
df = spark.read.schema(schema).json("hdfs://logs/")
```

---

### ❌ Small files problem ("many small files")

* Causes too many tasks, high scheduling overhead

✅ **Fix**:

* Use **`coalesce()`** to merge during write:

```python
df.write.option("maxRecordsPerFile", 100000).parquet("path/")
```

* Use **auto-merge** if available in Delta Lake

---

### ❌ Reading CSV without optimizations

```python
# BAD
df = spark.read.csv("data.csv", header=True, inferSchema=True)
```

✅ **Fix**:

```python
df = spark.read.option("header", True).schema(schema).csv("data.csv")
```

---

<a name="section3"></a>

## 🚫 3. Transformations and Actions Anti-Patterns

### ❌ Triggering multiple actions without caching

```python
df.count()
df.collect()
df.write.parquet("...")
```

Each action recomputes lineage!

✅ **Fix**:

```python
df.cache()
df.count()
df.write.parquet("...")
```

---

### ❌ Overuse of `withColumn`

```python
df = df.withColumn("c1", expr1)
df = df.withColumn("c2", expr2)
```

Each triggers a new logical plan.

✅ **Fix**:

```python
df.selectExpr("col1", "col2", "expr1 as c1", "expr2 as c2")
```

---

<a name="section4"></a>

## 🪓 4. Partitioning and Shuffling Anti-Patterns

### ❌ Not controlling shuffle partitions

```python
# BAD
df.groupBy("col").count()  # may use 200 partitions by default
```

✅ **Fix**:

```python
spark.conf.set("spark.sql.shuffle.partitions", "64")
```

---

### ❌ Using `repartition()` blindly

```python
df.repartition(1000)
```

This causes **full shuffle**.

✅ **Fix**:

* Use **`coalesce()`** to reduce partitions
* Repartition only before a wide operation like join/groupBy

---

### ❌ Not handling skewed keys

```python
df.groupBy("country").count()
```

* If 90% of data is from `US`, that partition is a hotspot.

✅ **Fix**:

* Add **salted keys**
* Enable **Adaptive Query Execution**:

```python
spark.conf.set("spark.sql.adaptive.enabled", "true")
```

---

<a name="section5"></a>

## 💾 5. Caching and Storage Anti-Patterns

### ❌ Caching everything

```python
df.cache()
```

* Large datasets may **evict useful data** or crash executor memory.

✅ **Fix**:

* Cache **only when reused** and fits in memory
* Use `.unpersist()` when done

---

### ❌ Not persisting long computations

```python
df = df.withColumn("x", my_udf(df["col"]))
df.show()
df.write.parquet("...")
```

UDF runs twice

✅ **Fix**:

```python
df = df.persist()
```

---

<a name="section6"></a>

## 🔥 6. Join Optimization Anti-Patterns

### ❌ Joining large tables without broadcast

```python
df1.join(df2, "id")  # both large
```

✅ **Fix**:

* Broadcast small table:

```python
from pyspark.sql.functions import broadcast
df1.join(broadcast(df2), "id")
```

---

### ❌ Cartesian Join (by mistake)

```python
df1.join(df2)
```

No condition = Cartesian explosion.

✅ **Fix**: Always use an explicit join condition.

---

### ❌ Not enabling AQE for better joins

✅ Enable this:

```python
spark.conf.set("spark.sql.adaptive.enabled", "true")
spark.conf.set("spark.sql.adaptive.shuffle.targetPostShuffleInputSize", "64MB")
```

> AQE switches join strategy (sort-merge ↔ broadcast) **dynamically** during execution.

---

<a name="section7"></a>

## 🐘 7. UDF and Serialization Anti-Patterns

### ❌ Using Python UDFs over SQL functions

```python
@udf
def custom_func(x): return x + 1
df = df.withColumn("new", custom_func("old"))
```

* Slow, no Catalyst optimization

✅ **Fix**:

* Use built-in SQL functions like `col + 1`, `when`, `regexp_extract`

---

### ❌ Not using Vectorized Pandas UDFs

If custom logic is needed:

✅ Use **Pandas UDF**:

```python
from pyspark.sql.functions import pandas_udf
@pandas_udf("int")
def double_udf(s: pd.Series) -> pd.Series:
    return s * 2

df = df.withColumn("new", double_udf("col"))
```

---

### ❌ Using complex Python objects in RDDs

```python
rdd = sc.parallelize([MyObject(), MyObject()])
```

* May cause **serialization errors** or **Kryo failures**

✅ Use simple types: `dict`, `tuple`, `list`, or use `DataFrame`

---

<a name="section8"></a>

## ⚙️ 8. Configuration Anti-Patterns

### ❌ Leaving default configurations

```python
# Default: shuffle partitions = 200, broadcast threshold = 10MB
```

✅ Optimize:

```python
spark.conf.set("spark.sql.shuffle.partitions", "64")
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", "50MB")
spark.conf.set("spark.sql.adaptive.enabled", "true")
```

---

### ❌ No executor memory tuning

Default memory often leads to OOMs.

✅ Tune based on workload:

* Set `--executor-memory`, `--executor-cores`, `--driver-memory`
* Avoid too many cores per executor (spark parallelism sweet spot: 5-6 cores)

---

<a name="section9"></a>

## ✅ Summary: Spark Anti-Patterns vs Best Practices

| Anti-Pattern                | Fix                          |
| --------------------------- | ---------------------------- |
| `collect()` on big datasets | Use `.show()`, `.limit()`    |
| `withColumn()` chained      | Use `selectExpr()`           |
| Over-caching                | Cache only reused DataFrames |
| Blind repartitioning        | Repartition only when needed |
| Python UDFs                 | Use built-in or pandas UDF   |
| Unused broadcast            | Use `broadcast()` explicitly |
| Skewed joins                | Enable AQE, use salting      |
| No schema on read           | Use `read.schema()`          |
| Small files                 | Merge files via `coalesce`   |

---
