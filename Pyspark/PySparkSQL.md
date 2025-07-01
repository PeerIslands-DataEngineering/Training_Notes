
## 🔥 What is PySpark SQL?

**PySpark SQL** is a module in **Apache Spark** for structured data processing using **SQL queries** or **DataFrame API** in Python.

PySpark SQL provides:

* SQL syntax for querying data
* Integration with Hive
* Reading and writing various file formats (CSV, JSON, Parquet, Avro, etc.)
* Performance optimizations via **Catalyst Optimizer** and **Tungsten engine**

---

## ✅ Why Use PySpark SQL?

* You can **write SQL queries** on large-scale distributed data
* Interact with data using familiar SQL **without worrying about distributed computing**
* Easily switch between SQL and DataFrame API
* Handle **structured** and **semi-structured** data (like JSON, Parquet)
* Optimized execution through Catalyst engine

---

## 🧱 Key Concepts

### 1. **SparkSession**

This is the entry point to using PySpark SQL. It replaces the older `SQLContext` and `HiveContext`.

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("PySpark SQL Intro") \
    .getOrCreate()
```

---

### 2. **DataFrame**

Think of a DataFrame as a distributed table in memory — it’s similar to Pandas DataFrame but distributed across a cluster.

```python
data = [("Arun", 28), ("Guru", 30)]
columns = ["name", "age"]

df = spark.createDataFrame(data, columns)
df.show()
```

Output:

```
+-----+---+
| name|age|
+-----+---+
| Arun| 28|
| Guru| 30|
+-----+---+
```

---

### 3. **Creating Temporary View / Table**

You can convert a DataFrame to a **temporary SQL table** and query using SQL:

```python
df.createOrReplaceTempView("people")

result = spark.sql("SELECT name FROM people WHERE age > 28")
result.show()
```

---

### 4. **Reading External Files**

#### CSV:

```python
df = spark.read.option("header", True).csv("people.csv")
df.show()
```

#### JSON:

```python
df = spark.read.json("people.json")
df.show()
```

#### Parquet:

```python
df = spark.read.parquet("people.parquet")
df.show()
```

---

## 🔄 SQL vs DataFrame API Example

| SQL Query                                | DataFrame API                          |
| ---------------------------------------- | -------------------------------------- |
| `SELECT name FROM people WHERE age > 28` | `df.filter("age > 28").select("name")` |

---

## 🧠 Under the Hood – Catalyst Optimizer

Spark SQL uses an intelligent query optimizer called **Catalyst**, which:

* Analyzes and rewrites SQL queries or DataFrame transformations
* Generates optimized execution plans
* Applies rule-based and cost-based optimizations

---

## 📂 File Formats Support in PySpark SQL

| Format  | Read Function               | Write Function            |
| ------- | --------------------------- | ------------------------- |
| CSV     | `spark.read.csv()`          | `df.write.csv()`          |
| JSON    | `spark.read.json()`         | `df.write.json()`         |
| Parquet | `spark.read.parquet()`      | `df.write.parquet()`      |
| ORC     | `spark.read.orc()`          | `df.write.orc()`          |
| Avro    | `spark.read.format("avro")` | `df.write.format("avro")` |


---

## 🧪 Example: SQL on JSON Data

```python
df = spark.read.json("students.json")
df.createOrReplaceTempView("students")

spark.sql("SELECT name, marks FROM students WHERE marks > 70").show()
```

---


## 🔷PySpark DataFrame?

A **DataFrame** in PySpark is a **distributed collection of data** organized into **named columns**, similar to a table in a relational database or a Pandas DataFrame — but **optimized for big data and parallel processing**.

---


## 1️⃣ Create a SparkSession

This is your entry point to using DataFrames.

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("PySpark DataFrame Tutorial") \
    .getOrCreate()
```

---

## 2️⃣ Creating DataFrames

### a. From Python Lists

```python
data = [("Arun", 28), ("Guru", 30), ("Kumar", 25)]
columns = ["name", "age"]

df = spark.createDataFrame(data, columns)
df.show()
```

**Output**:

```
+-----+---+
| name|age|
+-----+---+
| Arun| 28|
| Guru| 30|
|Kumar| 25|
+-----+---+
```

### b. From a CSV File

```python
df = spark.read.option("header", True).csv("people.csv")
df.printSchema()
```

---

## 3️⃣ Viewing & Inspecting Data

```python
df.show()               # Show top 20 rows
df.show(5, truncate=False)  # Show full data without cutting

df.printSchema()        # Show data types
df.columns              # List column names
df.dtypes               # List of (column, type)
df.describe().show()    # Summary statistics
```

---

## 4️⃣ DataFrame Operations

### a. Selecting Columns

```python
df.select("name").show()
df.select("name", "age").show()
```

### b. Adding a New Column

```python
from pyspark.sql.functions import col

df = df.withColumn("age_plus_1", col("age") + 1)
df.show()
```

### c. Renaming Columns

```python
df = df.withColumnRenamed("age", "years_old")
```

### d. Dropping Columns

```python
df = df.drop("age_plus_1")
```

---

## 5️⃣ Filtering & Querying

### a. Using filter / where

```python
df.filter(col("years_old") > 25).show()
df.where(col("years_old") < 30).show()
```

### b. SQL Syntax via Temp View

```python
df.createOrReplaceTempView("people")
spark.sql("SELECT name FROM people WHERE years_old > 25").show()
```

---

## 6️⃣ Aggregations (groupBy, agg)

```python
df.groupBy("years_old").count().show()

from pyspark.sql.functions import avg, max, min

df.groupBy("name").agg(avg("years_old"), max("years_old")).show()
```

---

## 7️⃣ Working with Null Values

```python
df.na.drop().show()  # Drop rows with nulls

df.na.fill({"name": "Unknown", "years_old": 0}).show()  # Replace nulls
```

---

## 8️⃣ Joins

Suppose you have two DataFrames:

```python
data1 = [("Arun", "Math"), ("Guru", "Physics")]
df1 = spark.createDataFrame(data1, ["name", "subject"])

data2 = [("Arun", 90), ("Guru", 85), ("Kumar", 75)]
df2 = spark.createDataFrame(data2, ["name", "marks"])
```

### a. Inner Join

```python
df1.join(df2, on="name", how="inner").show()
```

### b. Left Join

```python
df1.join(df2, on="name", how="left").show()
```

---

## 9️⃣ Writing DataFrames to Files

### a. Write to CSV

```python
df.write.mode("overwrite").option("header", True).csv("output/people.csv")
```

### b. Write to Parquet (more efficient)

```python
df.write.mode("overwrite").parquet("output/people.parquet")
```

---

## 🔟 Performance Tips

| Tip          | Description                                            |
| ------------ | ------------------------------------------------------ |
| `cache()`    | Use when reusing same DataFrame multiple times         |
| Partitioning | Use `.repartition()` to optimize joins                 |
| `select()`   | Avoid `*` — select only required columns               |
| File Format  | Use Parquet for large datasets (columnar & compressed) |

---

## 📚 Bonus: Useful PySpark Functions

```python
from pyspark.sql.functions import when, upper, lit

df = df.withColumn("is_adult", when(col("years_old") >= 18, "Yes").otherwise("No"))

df = df.withColumn("name_upper", upper(col("name")))

df = df.withColumn("country", lit("India"))
```

