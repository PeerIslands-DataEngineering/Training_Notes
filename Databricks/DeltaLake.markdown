# Delta Lake in Databricks: Tutorial

This tutorial demonstrates **Delta Lake** features in **Databricks Community Edition**, including **ACID transactions**, **time travel**, **schema evolution**, and **data validation**. It uses `hive_metastore` since Unity Catalog is limited in the free edition.


## Step 1: Set Up a Delta Table
Create a sample Delta table named `employee`.

**Python**:
```python
data = [
    (1, "Alice", 25, "2025-07-10 10:00:00"),
    (2, "Bob", 30, "2025-07-10 10:01:00")
]
columns = ["id", "name", "age", "timestamp"]
df = spark.createDataFrame(data, columns)
df.write.format("delta").mode("overwrite").saveAsTable("hive_metastore.default.employee")
```

**SQL**:
```sql
CREATE OR REPLACE TABLE hive_metastore.default.employee
USING DELTA
AS SELECT 1 AS id, 'Alice' AS name, 25 AS age, '2025-07-10 10:00:00' AS timestamp
UNION ALL
SELECT 2, 'Bob', 30, '2025-07-10 10:01:00';
```

**Verify**:
```sql
SELECT * FROM hive_metastore.default.employee;
```

## Step 2: ACID Transactions
Delta Lake ensures **ACID** properties for reliable data operations.

**Python**:
```python
# Append new data
new_data = [(3, "Charlie", 28, "2025-07-10 10:02:00")]
spark.createDataFrame(new_data, columns).write.format("delta").mode("append").saveAsTable("hive_metastore.default.employee")

# Update data
from delta.tables import DeltaTable
delta_table = DeltaTable.forName(spark, "hive_metastore.default.employee")
delta_table.update(condition="id = 1", set={"age": "age + 1"})
```

**SQL**:
```sql
INSERT INTO hive_metastore.default.employee
VALUES (3, 'Charlie', 28, '2025-07-10 10:02:00');

UPDATE hive_metastore.default.employee
SET age = age + 1
WHERE id = 1;
```

**Verify**:
```sql
SELECT * FROM hive_metastore.default.employee;
```

## Step 3: Time Travel
Query historical versions of the table.

**Check History**:
```sql
DESCRIBE HISTORY hive_metastore.default.employee;
```

**Query by Version**:
```sql
SELECT * FROM hive_metastore.default.employee VERSION AS OF 0;
```

**Query by Timestamp**:
```sql
SELECT * FROM hive_metastore.default.employee TIMESTAMP AS OF '2025-07-10 10:31:00';
```

**Restore**:
```python
delta_table.restoreToVersion(0)
```
**SQL**:
```sql
RESTORE TABLE hive_metastore.default.employee TO VERSION AS OF 0;
```

## Step 4: Schema Evolution
Add a new column (`department`) with schema evolution.

**Python**:
```python
new_data = [(4、出"David", 35, "2025-07-10 10:03:00", "Engineering")]
new_columns = ["id", "name", "age", "timestamp", "department"]
df_new = spark.createDataFrame(new_data, new_columns)
df_new.write.format("delta").mode("append").option("mergeSchema", "true").saveAsTable("hive_metastore.default.employee")
```

**SQL**:
```sql
INSERT INTO hive_metastore.default.employee
SELECT 4 AS id, 'David' AS name, 35 AS age, '2025-07-10 10:03:00' AS timestamp, 'Engineering' AS department;
```

**Verify**:
```sql
DESCRIBE hive_metastore.default.employee;
SELECT * FROM hive_metastore.default.employee;
```

## Step 5: Data Validation
Add constraints to ensure data quality.

**Add Constraints**:
```sql
ALTER TABLE hive_metastore.default.employee
ADD CONSTRAINT name_not_null CHECK (name IS NOT NULL);

ALTER TABLE hive_metastore.default.employee
ADD CONSTRAINT age_positive CHECK (age > 0);
```

**Test Invalid Data** (will fail):
```python
invalid_data = [(5, None, 40, "2025-07-10 10:04:00", "Marketing")]
spark.createDataFrame(invalid_data, new_columns).write.format("delta").mode("append").saveAsTable("hive_metastore.default.employee")
```

**Test Valid Data**:
```python
valid_data = [(5, "Eve", 40, "2025-07-10 10:04:00", "Marketing")]
spark.createDataFrame(valid_data, new_columns).write.format("delta").mode("append").saveAsTable("hive_metastore.default.employee")
```

**Verify**:
```sql
SELECT * FROM hive_metastore.default.employee;
```

## Step 6: Optimize and Clean Up
**Optimize**:
```sql
OPTIMIZE hive_metastore.default.employee;
```

**Vacuum**:
```sql
VACUUM hive_metastore.default.employee RETAIN 168 HOURS;
```

## Best Practices
- Name constraints clearly for debugging.
- Check table history regularly for auditing.
- Use `mergeSchema` cautiously to avoid unintended changes.
- Run `OPTIMIZE` and `VACUUM` periodically.
- Use small datasets in Community Edition to avoid timeouts.

## Troubleshooting
- **Constraint Violation**: Check error messages for specific constraints.
- **Time Travel Fails**: Verify version/timestamp in `DESCRIBE HISTORY`.
- **Schema Error**: Ensure `mergeSchema` is enabled.
- **Quota Limits**: Reduce data size or wait for quota reset.