

## 🧠 1. What is DBT?

**DBT (Data Build Tool)** is a **transformation-only** tool in the **Modern Data Stack** that lets data analysts and engineers **transform raw data into clean, tested, documented datasets** using **SQL** and **software engineering best practices** like:

* Modular SQL code
* Version control (Git)
* CI/CD
* Testing
* Documentation

> ✅ **It does not extract or load data** – it works **after the data is loaded into a data warehouse** (ELT).

---

## 🚀 2. Why Use DBT – Key Features & Benefits

| Feature                           | Benefit                                         |
| --------------------------------- | ----------------------------------------------- |
| SQL-based                         | Easy for analysts & engineers to use            |
| Modularity                        | Reusable models & easier debugging              |
| Auto dependency management        | Builds DAG of models                            |
| Built-in Testing                  | Data validation at build time                   |
| Documentation                     | Auto-docs with lineage                          |
| CI/CD Support                     | Automated testing and deployment                |
| Compatible with modern warehouses | Snowflake, BigQuery, Redshift, Databricks, etc. |

---

## 🔑 3. Core Concepts in DBT

| Concept       | Explanation                                                     |
| ------------- | --------------------------------------------------------------- |
| **Models**    | SQL files that define transformations (each is a view or table) |
| **Seeds**     | CSV files versioned in Git, loaded into the warehouse           |
| **Snapshots** | Capture slowly changing dimensions (SCD Type 2)                 |
| **Sources**   | Definitions for raw tables in the warehouse                     |
| **Tests**     | Assertions to validate your data (e.g., uniqueness, non-null)   |
| **Docs**      | Auto-generated documentation                                    |
| **Packages**  | Reusable DBT projects shared across teams                       |
| **Macros**    | Jinja-templated reusable logic in SQL (like functions)          |

---

## 📚 4. Use Cases of DBT

### ✅ a) Data Transformation in ELT Pipelines

Transform raw data after loading into the warehouse.

**Example:**

```sql
-- models/customers.sql
SELECT
    id,
    LOWER(email) as email,
    created_at::DATE as signup_date
FROM {{ source('raw', 'users') }}
```

### ✅ b) Data Cleaning and Normalization

Standardize formats, handle missing values, enforce data types.

### ✅ c) Building Dimensional Models (Star Schema)

Create fact and dimension tables for BI tools.

### ✅ d) Data Quality Testing

Ensure uniqueness, non-null constraints, referential integrity.

```yaml
version: 2

models:
  - name: orders
    tests:
      - unique:
          column_name: id
      - not_null:
          column_name: customer_id
```

### ✅ e) Snapshotting for Slowly Changing Dimensions

Track history of changes in tables like `users`, `products`.

### ✅ f) Documenting the Data Warehouse

Auto-generated data dictionary with lineage graphs.

### ✅ g) Version-controlled Data Workflows

Code stored in Git → promotes team collaboration and CI/CD.

---

## 🔧 5. End-to-End DBT Workflow (With Examples)

### 📁 Folder Structure:

```
my_project/
│
├── models/
│   ├── staging/
│   ├── marts/
│
├── snapshots/
├── seeds/
├── tests/
├── dbt_project.yml
├── packages.yml
```

---

### Step-by-Step:

### ✅ 1. **Define Sources**

```yaml
# models/src_sources.yml
version: 2
sources:
  - name: raw
    tables:
      - name: customers_raw
```

### ✅ 2. **Create Staging Models**

```sql
-- models/staging/stg_customers.sql
SELECT
    id,
    LOWER(email) as email,
    signup_date::DATE as signup_date
FROM {{ source('raw', 'customers_raw') }}
```

### ✅ 3. **Create Fact/Dimensional Models**

```sql
-- models/marts/fct_orders.sql
SELECT
    o.id AS order_id,
    o.amount,
    c.email
FROM {{ ref('stg_orders') }} o
JOIN {{ ref('stg_customers') }} c
  ON o.customer_id = c.id
```

### ✅ 4. **Add Tests**

```yaml
# models/marts/schema.yml
version: 2

models:
  - name: fct_orders
    tests:
      - not_null:
          column_name: order_id
      - relationships:
          to: ref('stg_customers')
          field: customer_id
```

### ✅ 5. **Build Models**

```bash
dbt run
```

### ✅ 6. **Run Tests**

```bash
dbt test
```

### ✅ 7. **Generate Documentation**

```bash
dbt docs generate
dbt docs serve
```

---

## 🌍 6. Real-world DBT Examples

### 📌 a) E-commerce

* Raw tables: `orders_raw`, `customers_raw`, `products_raw`
* Stage and clean them using staging models
* Create:

  * `dim_customers`
  * `dim_products`
  * `fct_orders`
* Test for nulls, duplicates
* Document the data lineage

### 📌 b) Marketing Analytics

* Use DBT to create:

  * `fct_ad_spend`
  * `fct_user_signups`
* Attribute spend to signups/conversions
* Snapshots to track campaign effectiveness over time

### 📌 c) Finance

* Reconcile transaction data across sources
* Create monthly aggregates
* Track historical balance changes with snapshots

---

## 🧩 7. Advanced Features

### 🔁 a) Jinja Macros

```sql
-- macros/currency_convert.sql
{% macro convert_to_usd(amount, currency) %}
    CASE
      WHEN {{ currency }} = 'EUR' THEN {{ amount }} * 1.1
      WHEN {{ currency }} = 'INR' THEN {{ amount }} * 0.012
      ELSE {{ amount }}
    END
{% endmacro %}
```

### 👩‍💻 b) Incremental Models

```sql
-- models/fct_orders_incremental.sql
{{ config(materialized='incremental', unique_key='order_id') }}

SELECT * FROM {{ ref('stg_orders') }}
WHERE updated_at > (SELECT MAX(updated_at) FROM {{ this }})
```

### 🔁 c) Snapshots

```yaml
-- snapshots/snap_customers.sql
{% snapshot snap_customers %}
{
  "target_schema": "snapshots",
  "unique_key": "id",
  "strategy": "timestamp",
  "updated_at": "updated_at"
}
SELECT * FROM {{ source('raw', 'customers') }}
{% endsnapshot %}
```

### 🔗 d) DBT Cloud / CI Integration

* Automatically run DBT when changes are pushed
* Use GitHub Actions, GitLab CI, or dbt Cloud CI/CD

---

## ✅ 8. Best Practices

* Organize folders as:

  * `staging/`, `intermediate/`, `marts/`
* Use naming conventions like `stg_`, `dim_`, `fct_`
* Use incremental models for large datasets
* Always test and document models
* Version control all code
* Use macros for reusable logic
* Run dbt deps regularly (for packages)

---

## ❌ 9. Limitations of DBT

| Limitation                      | Workaround                                |
| ------------------------------- | ----------------------------------------- |
| No extract/load                 | Use Airbyte/Fivetran                      |
| SQL-only                        | For Python, use dbt-python (experimental) |
| Limited to supported warehouses | Ensure warehouse compatibility            |
| Not great for orchestration     | Use Airflow/Prefect to trigger dbt        |

---

## Summary

| Feature   | Description                                               |
| --------- | --------------------------------------------------------- |
| Tool Type | ELT Transform Tool                                        |
| Language  | SQL + Jinja                                               |
| Targets   | Snowflake, BigQuery, Redshift, Databricks, Postgres, etc. |
| Strength  | Transformations, Testing, Documentation                   |
| Weakness  | No EL, limited orchestration                              |

---

Would you like me to provide:

* A sample **DBT project template**?
* **CI/CD integration tutorial** for GitHub Actions + DBT?
* **Advanced use cases** (e.g., snapshot-based Type 2 slowly changing dimensions)?
