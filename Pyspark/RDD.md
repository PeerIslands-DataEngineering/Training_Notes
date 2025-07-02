# PySpark RDD 

Apache Spark's RDD (Resilient Distributed Dataset) is a foundational abstraction that enables distributed and fault-tolerant data processing. This document presents a deep-dive into PySpark RDDs with explanations, real-world use cases, and code examples.

---

## 1. What is an RDD?

An RDD, or **Resilient Distributed Dataset**, is the most fundamental data structure in Apache Spark. It represents a **read-only**, **distributed collection** of objects that are partitioned across nodes in a cluster and processed in parallel.

### Key Concepts:

* **Resilient**: If a partition of an RDD is lost, it can be **recomputed** using the original transformations (lineage).
* **Distributed**: RDDs are **automatically distributed** across nodes for scalability and fault tolerance.
* **Immutable**: Once an RDD is created, it **cannot be changed**. Transformations return new RDDs.
* **Lazy Evaluation**: Spark builds a **lineage graph** of transformations and only executes them when an action is called.

### Example:

```python
rdd = spark.sparkContext.parallelize([1, 2, 3, 4, 5])
```

This creates an RDD with 5 elements distributed across the Spark cluster.

---

## 2. Creating RDDs

### 2.1 From Python Collections

You can create RDDs from Python lists using `parallelize()`:

```python
rdd = spark.sparkContext.parallelize([10, 20, 30, 40])
```

This splits the list across the cluster nodes.

### 2.2 From External Files

You can create RDDs from text files (local or HDFS):

```python
rdd = spark.sparkContext.textFile("/path/to/log.txt")
```

Each line in the file becomes an element in the RDD.

---

## 3. RDD Transformations

Transformations are **lazy operations** that return a new RDD and define a computation lineage.

### Common Transformations:

* `map(func)`: Applies `func` to each element.

```python
rdd.map(lambda x: x * 2)
```

* `filter(func)`: Filters elements based on `func`.

```python
rdd.filter(lambda x: x > 10)
```

* `flatMap(func)`: Similar to map but flattens results.

```python
rdd.flatMap(lambda line: line.split(" "))
```

* `distinct()`: Returns unique elements.

```python
rdd.distinct()
```

* `union(otherRDD)`: Combines two RDDs.

```python
rdd1.union(rdd2)
```

* `intersection(otherRDD)`: Returns common elements.

```python
rdd1.intersection(rdd2)
```

Each of these operations builds the lineage of computation but doesn’t actually trigger execution.

---

## 4. RDD Actions

Actions are operations that trigger **execution of transformations** and return a result to the driver or write data externally.

### Common Actions:

* `collect()`: Returns all elements to the driver.

```python
rdd.collect()
```

* `count()`: Returns the number of elements.

```python
rdd.count()
```

* `first()`: Returns the first element.

```python
rdd.first()
```

* `take(n)`: Returns first n elements.

```python
rdd.take(3)
```

* `reduce(func)`: Combines elements using `func`.

```python
rdd.reduce(lambda a, b: a + b)
```

* `saveAsTextFile(path)`: Saves the RDD to external storage.

```python
rdd.saveAsTextFile("/output/path")
```

Actions are the only way to materialize RDD computation.

---

## 5. Key-Value RDDs (Pair RDDs)

Key-Value RDDs are a specialized form where each element is a tuple of (key, value). These are foundational for operations like grouping and aggregations.

### Creating a Pair RDD:

```python
data = [("apple", 1), ("banana", 2), ("apple", 3)]
rdd = spark.sparkContext.parallelize(data)
```

### Common Pair RDD Operations:

* `reduceByKey(func)`: Aggregates values for each key.

```python
rdd.reduceByKey(lambda x, y: x + y)
```

* `groupByKey()`: Groups values by key (less efficient than `reduceByKey`).

```python
rdd.groupByKey()
```

* `mapValues(func)`: Applies `func` only to values.

```python
rdd.mapValues(lambda v: v * 10)
```

* `join()`: Joins two pair RDDs on keys.

```python
rdd1.join(rdd2)
```

These operations are heavily used in data aggregation and transformation pipelines.

---

## 6. Shared Variables: Broadcast and Accumulator

### 6.1 Broadcast Variables

Broadcast variables are read-only variables that are **cached on each machine** rather than shipped with tasks. This avoids redundant copies.

#### Example:

```python
broadcast_dict = spark.sparkContext.broadcast({"a": 1, "b": 2})
rdd.map(lambda x: broadcast_dict.value.get(x, 0)).collect()
```

Use case: Lookups, configuration values, static reference data.

### 6.2 Accumulators

Accumulators are **write-only shared variables** used for **counters and metrics**.

#### Example:

```python
acc = spark.sparkContext.accumulator(0)

def count_nulls(x):
    if x is None:
        acc.add(1)
    return x

rdd.map(count_nulls).collect()
print(acc.value)  # Number of nulls
```

Use case: Logging, counting bad records.

---

## 7. Real-World Example: Word Count Using RDDs

This is the canonical example of using RDDs:

```python
text = spark.sparkContext.textFile("/path/to/file.txt")
words = text.flatMap(lambda line: line.split(" "))
pairs = words.map(lambda word: (word, 1))
counts = pairs.reduceByKey(lambda a, b: a + b)
counts.collect()
```

Steps:

* Split lines into words
* Map each word to (word, 1)
* Reduce by key to count occurrences

This pattern is widely used in log analysis, analytics, and pre-processing tasks.

---

## 8. Comparison: RDD vs DataFrame

While RDD is low-level and flexible, DataFrames offer optimization and simplicity.

### Feature Comparison:

| Feature       | RDD                      | DataFrame                  |
| ------------- | ------------------------ | -------------------------- |
| API Level     | Low-level functional API | High-level SQL-like API    |
| Schema        | No schema                | Schema defined             |
| Optimization  | Manual                   | Catalyst Optimizer         |
| Performance   | Slower                   | Faster due to optimization |
| Ease of Use   | Verbose                  | Concise and readable       |
| Serialization | Java serialization       | Tungsten binary format     |

### When to Use RDD:

* You need **fine-grained control** over your data and computations
* You work with **unstructured data** or **complex types**
* You need **custom partitioning** or **manual tuning**

---
