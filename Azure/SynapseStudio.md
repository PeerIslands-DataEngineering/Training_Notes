
# 💠 Azure Synapse Studio 

---

## 🔷 What is Azure Synapse Studio?

**Azure Synapse Studio** is a **unified workspace** for:

* Data ingestion and integration (ETL/ELT)
* Big data and warehouse queries
* Building analytics solutions
* Managing Spark and SQL compute

It combines features of SQL Server Management Studio (SSMS), Azure Data Factory, Power BI, and Jupyter Notebooks into **one browser-based IDE**.

---

## 🔹 How to Open Synapse Studio

1. Go to the Azure Portal.
2. Open your **Synapse Workspace**.
3. Click **"Open Synapse Studio"** — it opens in a new tab.

---

## 🧭 Interface Overview

The left sidebar contains **five main hubs**:

| Hub           | Purpose                                             |
| ------------- | --------------------------------------------------- |
| **Home**      | Quick links, recent resources                       |
| **Data**      | View and manage linked & workspace data             |
| **Develop**   | Create and manage scripts, notebooks, pipelines     |
| **Integrate** | Build data pipelines (like ADF)                     |
| **Monitor**   | Track pipeline and query executions                 |
| **Manage**    | Configure linked services, credentials, Spark pools |

---

## 🔹 Step 1: Create a SQL Script and Run Query

1. Go to **Develop** tab → Click **+** → **SQL script**
2. Choose a **built-in or serverless SQL pool**
3. Enter your query:

```sql
SELECT TOP 10 * FROM OPENROWSET(
    BULK 'https://<storageaccount>.blob.core.windows.net/sample-data/sales.csv',
    FORMAT = 'CSV',
    HEADER_ROW = TRUE
) AS rows;
```

4. Click **Run** — view results in grid or chart
5. Save the script to your workspace or Git

🔸 Use case: Query files from Azure Data Lake using **Serverless SQL pool**

---

## 🔹 Step 2: Create a Spark Notebook

1. Go to **Develop > + > Notebook**
2. Select **Spark pool** (must be provisioned first)
3. Choose **language**: PySpark, Scala, or C#
4. Write code:

```python
df = spark.read.option("header", True).csv("abfss://sample@datalake.dfs.core.windows.net/data/sales.csv")
df.show()
```

5. Run each cell individually or the full notebook
6. You can visualize with `display(df)`

🔸 Use case: Perform **big data analysis** with Spark directly in the browser.

---

## 🔹 Step 3: Ingest Data with Synapse Pipelines (like ADF)

1. Go to **Integrate > + > Pipeline**
2. Drag a **Copy data** activity
3. Configure:

   * **Source**: e.g., Azure Blob, CSV
   * **Sink**: e.g., Synapse SQL table
4. Add **datasets** and **linked services**
5. Publish → Debug (run immediately) → Add **trigger** (for scheduling)

🔸 Use case: Build **ETL workflows** within Synapse Studio.

---

## 🔹 Step 4: Visualize Data in Charts (SQL Results)

1. Run a query
2. Click the **chart icon** on result tab
3. Choose visualization: bar, pie, line, etc.
4. Customize axes, group by, filters
5. Export to **Power BI** if needed

🔸 Use case: Quick visual analysis from SQL query results.

---

## 🔹 Step 5: Manage Workspace and Linked Services

Go to **Manage tab** to handle:

| Section                  | Purpose                                        |
| ------------------------ | ---------------------------------------------- |
| **Linked Services**      | Connections to Blob, SQL, Cosmos DB, etc.      |
| **Integration Runtimes** | Compute environments for data movement         |
| **Apache Spark Pools**   | Configure Spark capacity                       |
| **SQL Pools**            | Serverless or Dedicated SQL                    |
| **Access Control**       | RBAC & firewall settings                       |
| **Credentials**          | Securely store secrets (can link to Key Vault) |

---

## 🔹 Step 6: Monitor Pipelines and Queries

1. Go to **Monitor tab**
2. View:

   * Pipeline runs (success/failure)
   * Spark job details
   * SQL query history
3. Click on a run → Get logs, duration, input/output details
4. Can retry failed runs or diagnose performance issues

---

## 🔹 Optional: Enable Git Integration

1. Go to **Manage > Git configuration**
2. Connect to **Azure DevOps or GitHub**
3. Create a **collaboration branch**
4. Changes to notebooks, pipelines, scripts are tracked
5. Supports **pull requests, versioning, and rollback**

---

## 🔹 Optional: Use Serverless SQL for Exploratory Queries

Query data directly from **Azure Data Lake** without provisioning SQL compute:

```sql
SELECT *
FROM OPENROWSET(
    BULK 'abfss://data@yourstorage.dfs.core.windows.net/sales.csv',
    FORMAT = 'CSV',
    HEADER_ROW = TRUE
) AS data;
```

Advantages:

* Pay-per-query
* No server management
* Ideal for data exploration

---

## 🧠 Summary of Key Capabilities in Synapse Studio

| Feature         | Tech Used                  | Purpose                                |
| --------------- | -------------------------- | -------------------------------------- |
| SQL script      | Serverless / Dedicated SQL | Query structured data or lake files    |
| Notebook        | Apache Spark               | Big data processing, machine learning  |
| Pipelines       | Synapse Pipelines (ADF)    | Orchestration (ETL/ELT) workflows      |
| Data Explorer   | ADLS, Synapse DB           | Browse lake files, database tables     |
| Git Integration | Azure DevOps / GitHub      | Version control & CI/CD                |
| Monitoring      | Built-in                   | Track all activity (queries/jobs/runs) |

---

## 🔐 Security & Access Control

* Use **Managed Identities** for secure storage access
* Fine-grained **RBAC** for developers, admins, analysts
* **Private endpoints** for secure network isolation
* **Auditing and logging** through Azure Monitor

---

## 🧪 Sample Use Case Workflow

🔁 **ETL Pipeline Example**:

1. Ingest raw CSV from Azure Blob
2. Clean and transform via Spark notebook
3. Load into Synapse SQL DW using a pipeline
4. Visualize in Power BI or publish to dashboard

