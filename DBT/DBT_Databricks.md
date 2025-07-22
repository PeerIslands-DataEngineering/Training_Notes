
# ✅ Part 1: Setting Up DBT with Databricks

## 🛠️ 1. Pre-requisites

* Databricks workspace (with access to SQL Warehouse or cluster)
* DBT Core (`pip install dbt-databricks`)
* Access token from Databricks (for authentication)
* Git repository (optional but recommended)

---

## ⚙️ 2. Install DBT for Databricks

```bash
pip install dbt-databricks
```

---

## 🔐 3. Configure Profiles

Create a file at: `~/.dbt/profiles.yml`

```yaml
databricks_project:
  target: dev
  outputs:
    dev:
      type: databricks
      catalog: hive_metastore
      schema: analytics     # your target schema
      host: https://<your-databricks-instance>
      http_path: /sql/1.0/warehouses/<warehouse-id>
      token: <your-access-token>
      threads: 4
```

> You get `http_path` and `token` from **Databricks > SQL > Compute > Warehouse > Connection Details**.

---

## 📁 4. Create DBT Project

```bash
dbt init databricks_project
cd databricks_project
```

---

## 🧪 5. Test Connection

```bash
dbt debug
```

---

# ✅ Part 2: DBT Transformations in Databricks

Let’s assume you have a raw table `raw.sales_orders` in Databricks.

---

## 🔁 1. Create a Staging Model

📄 File: `models/staging/stg_sales_orders.sql`

```sql
SELECT
    order_id,
    customer_id,
    UPPER(product_name) AS product_name,
    order_date,
    amount
FROM {{ source('raw', 'sales_orders') }}
```

📄 `models/staging/schema.yml`

```yaml
version: 2

sources:
  - name: raw
    tables:
      - name: sales_orders

models:
  - name: stg_sales_orders
    columns:
      - name: order_id
        tests:
          - not_null
          - unique
```

📦 Run this:

```bash
dbt run
dbt test
```

---

## 🧱 2. Build a Dimensional or Fact Model

📄 File: `models/marts/fct_orders.sql`

```sql
SELECT
    s.order_id,
    s.customer_id,
    s.product_name,
    s.order_date,
    s.amount,
    DATE(order_date) as order_day
FROM {{ ref('stg_sales_orders') }} s
WHERE s.order_date >= '2022-01-01'
```

📄 `models/marts/schema.yml`

```yaml
version: 2

models:
  - name: fct_orders
    description: "Fact table for all orders"
    columns:
      - name: order_id
        tests:
          - unique
```

📦 Run this:

```bash
dbt run --select fct_orders
```

---

## 💡 Incremental Model in Databricks

📄 File: `models/marts/fct_orders_incremental.sql`

```sql
{{ config(materialized='incremental', unique_key='order_id') }}

SELECT
    s.order_id,
    s.customer_id,
    s.product_name,
    s.order_date,
    s.amount
FROM {{ ref('stg_sales_orders') }} s

{% if is_incremental() %}
WHERE s.order_date > (SELECT MAX(order_date) FROM {{ this }})
{% endif %}
```

---

# ✅ Part 3: Snapshots in DBT for Slowly Changing Dimensions (SCD Type 2)

Let’s track changes in the `customer` table over time.

---

## 🧊 1. Create a Snapshot

📄 File: `snapshots/snap_customers.sql`

```sql
{% snapshot snap_customers %}
{
  "target_schema": "snapshots",
  "unique_key": "customer_id",
  "strategy": "timestamp",
  "updated_at": "updated_at"
}

SELECT
    customer_id,
    name,
    email,
    address,
    updated_at
FROM {{ source('raw', 'customers') }}

{% endsnapshot %}
```

> DBT will auto-track inserts and updates over time.

---

## 📄 Add to `dbt_project.yml`

```yaml
snapshot-paths: ["snapshots"]
```

---

## 📦 Run Snapshot

```bash
dbt snapshot
```

It creates a table like:

| customer\_id | name | email | address | dbt\_valid\_from    | dbt\_valid\_to      |
| ------------ | ---- | ----- | ------- | ------------------- | ------------------- |
| 1            | A    | a\@x  | Loc1    | 2023-01-01 00:00:00 | 2023-06-01 00:00:00 |
| 1            | A    | a\@x  | Loc2    | 2023-06-01 00:00:00 | NULL                |

---

## 🔍 Optional Tests on Snapshots

📄 `snapshots/schema.yml`

```yaml
version: 2

snapshots:
  - name: snap_customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null
```

---

# ✅ Part 4: Running on Databricks with SQL Warehouse

* Always make sure `http_path` is a **SQL Warehouse**
* Materializations (`table`, `view`, `incremental`) are fully supported
* `ref()` and `source()` generate Delta Lake compatible SQL under the hood

---

# ✅ Part 5: Good Practices with DBT + Databricks

| Best Practice                                  | Why                                |
| ---------------------------------------------- | ---------------------------------- |
| Use `staging`, `intermediate`, `marts` folders | Clean model layering               |
| Use Delta Lake capabilities (MERGE, UPSERTs)   | Efficient transformations          |
| Use `incremental` config for large fact tables | Faster builds                      |
| Store snapshots in a separate schema           | Isolate slowly changing dimensions |
| Add tests for all key columns                  | Prevent silent data issues         |
| Use a Git + CI tool (like GitHub Actions)      | Ensure reliable deployment         |

---

# ✅ Final: Useful DBT Commands for Databricks

```bash
dbt debug                           # Test connection
dbt run                             # Run all models
dbt run --select stg_*              # Run staging only
dbt test                            # Run all tests
dbt snapshot                        # Capture SCD history
dbt docs generate && dbt docs serve # Generate and host docs
```

---
