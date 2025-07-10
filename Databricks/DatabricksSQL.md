

## 🔰 Part 1: Basic Setup in Databricks

### Create SparkSession (Automatically done in Databricks notebooks):

```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("SparkSQLDatabricks").getOrCreate()
```

---

## 📁 Part 2: Loading Data and Inferring Schema

Let’s assume you have a CSV file:

📂 Path: `/Workspace/Users/arulanandha.guru@databricks.com/data.csv`

```python
df = spark.read.option("header", True).option("inferSchema", True).csv("/Workspace/Users/arulanandha.guru@databricks.com/data.csv")
df.show(5)
df.printSchema()
```

### Sample Output Schema:

```
root
 |-- company: string
 |-- founded: integer
 |-- hq: string
 |-- industry: string
 |-- total_funding: string
 |-- arr: string
 |-- valuation: string
 |-- employees: integer
 |-- top_investors: string
 |-- g2_rating: double
```

---

## 🔁 Part 3: Changing Column Data Types

### Convert funding and valuation columns from strings like "\$1.2B" or "\$300M" to numbers:

```python
from pyspark.sql.functions import regexp_replace, col, when

def convert_to_number(col_name):
    return when(col(col_name).contains("B"),
                regexp_replace(col(col_name), "[$B]", "").cast("double") * 1e9) \
           .when(col(col_name).contains("M"),
                regexp_replace(col(col_name), "[$M]", "").cast("double") * 1e6) \
           .otherwise(regexp_replace(col(col_name), "[$]", "").cast("double"))

df = df.withColumn("total_funding_num", convert_to_number("total_funding")) \
       .withColumn("valuation_num", convert_to_number("valuation"))

df.select("total_funding", "total_funding_num", "valuation", "valuation_num").show(5)
```

---

## 🔍 Part 4: Filtering Data

### Examples of filtering rows

```python
# Companies with ARR > $100M
df.filter(col("arr") > "$100M").show()

# Convert ARR first to number, then filter
df = df.withColumn("arr_num", convert_to_number("arr"))
df.filter(col("arr_num") > 100_000_000).select("company", "arr", "arr_num").show()

# Filter companies in the "Fintech" industry
df.filter(col("industry") == "Fintech").show()
```

---

## 🔄 Part 5: Sorting and Ordering

```python
# Top 10 companies by valuation
df.orderBy(col("valuation_num").desc()).select("company", "valuation_num").show(10)

# Sort by number of employees (ascending)
df.orderBy("employees").select("company", "employees").show(10)
```

---

## 🔗 Part 6: Joins

Let’s say we have another table `hq_country_df` with columns `hq` and `country`.

```python
hq_country = [
    ("San Francisco", "USA"),
    ("London", "UK"),
    ("Bangalore", "India")
]
hq_country_df = spark.createDataFrame(hq_country, ["hq", "country"])
```

### Perform Joins:

```python
# INNER JOIN
joined_df = df.join(hq_country_df, on="hq", how="inner")
joined_df.select("company", "hq", "country").show()

# LEFT JOIN
df.join(hq_country_df, on="hq", how="left").select("company", "hq", "country").show()

# RIGHT JOIN
df.join(hq_country_df, on="hq", how="right").select("company", "hq", "country").show()

# FULL OUTER JOIN
df.join(hq_country_df, on="hq", how="outer").select("company", "hq", "country").show()
```

---

## 🔀 Part 7: UNION and UNION ALL

Let’s duplicate some rows to demonstrate:

```python
df_sample = df.select("company", "industry", "employees").limit(5)
df_union = df_sample.union(df_sample)  # UNION ALL behavior
df_union.show()
```

> Spark uses `union()` like UNION ALL by default. For exact SQL-like `UNION` (distinct), you can do `.distinct()` after the union.

---

## 🔁 Part 8: Transformations and Aggregations

```python
from pyspark.sql.functions import avg, max, count

# Average ARR by industry
df.groupBy("industry").agg(avg("arr_num").alias("avg_arr")).orderBy("avg_arr", ascending=False).show()

# Count companies by HQ
df.groupBy("hq").agg(count("*").alias("num_companies")).orderBy("num_companies", ascending=False).show()
```

---

## 🧠 Part 9: User Defined Functions (UDF)

### Use-case: Tag company as "Enterprise", "SMB", or "Startup" based on employee count

```python
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType

def classify_company(emp_count):
    if emp_count >= 1000:
        return "Enterprise"
    elif emp_count >= 100:
        return "SMB"
    else:
        return "Startup"

company_type_udf = udf(classify_company, StringType())
df = df.withColumn("company_type", company_type_udf(col("employees")))

df.select("company", "employees", "company_type").show(10)
```

---

## 💾 Part 10: Creating and Using Spark SQL Tables

### Create Table from DataFrame

```python
df.write.format("delta").mode("overwrite").saveAsTable("company_data")
```

### Use SQL to Query

```sql
-- In a Databricks SQL cell
SELECT company, industry, employees FROM company_data WHERE employees > 500 ORDER BY employees DESC;
```

---

## 🛠 Extra: Use Temporary Views for SQL

```python
df.createOrReplaceTempView("company_view")
```

Now run:

```sql
SELECT industry, AVG(employees) as avg_emp FROM company_view GROUP BY industry ORDER BY avg_emp DESC;
```

---

## 🧪 Part 11: Caching and Optimization

```python
df.cache()
df.count()  # First time triggers cache
```

```sql
-- SQL cache
CACHE TABLE company_view;
```

---

## 📊 Final Result: Use in Dashboards

The Gold layer (from Medallion) created with Spark SQL can directly feed BI tools:

* **Power BI**
* **Tableau**
* **Databricks SQL Dashboards**

---

## ✅ Summary Table of Operations

| Feature     | PySpark Method                               |
| ----------- | -------------------------------------------- |
| Load CSV    | `spark.read.csv()`                           |
| Change type | `withColumn(...cast(...))`                   |
| Filter      | `filter()`                                   |
| Sort        | `orderBy()`                                  |
| Join        | `join()`                                     |
| GroupBy     | `groupBy().agg()`                            |
| Union       | `union()`                                    |
| UDF         | `udf(...), withColumn()`                     |
| SQL Table   | `saveAsTable()`, `createOrReplaceTempView()` |
| SQL Query   | `spark.sql("SELECT ...")`                    |
| Cache       | `cache()`, `CACHE TABLE`                     |

---
