## 📘 What is Azure Synapse Analytics?

**Azure Synapse Analytics** (formerly Azure SQL Data Warehouse) is a **limitless analytics service** that brings together:

* **Big data processing** (Spark, Data Lake),
* **Data warehousing** (T-SQL),
* **ETL/ELT pipelines** (Data Flows, Mapping),
* **Real-time analytics** (Streaming),
* and **BI Integration** (Power BI).

All in a single **unified platform**.

---

## 🧠 Core Concepts

| Concept               | Description                                                                       |
| --------------------- | --------------------------------------------------------------------------------- |
| **Workspace**         | Central environment for Synapse development                                       |
| **Synapse SQL**       | Query engine: Dedicated SQL pool (provisioned) or Serverless SQL pool (on-demand) |
| **Apache Spark**      | Built-in engine for big data and machine learning                                 |
| **Pipelines**         | Orchestration and data integration (like Azure Data Factory)                      |
| **Data Lake Storage** | Integrated with ADLS Gen2 for massive data scale                                  |
| **Notebooks**         | For Python, Spark, or SQL-based data exploration                                  |
| **Linked Services**   | Connections to external data sources (SQL DB, Blob, etc.)                         |

---

## 🔁 Comparison: Serverless SQL vs Dedicated SQL Pool

| Feature     | Serverless SQL             | Dedicated SQL Pool                  |
| ----------- | -------------------------- | ----------------------------------- |
| Billing     | Per TB of data queried     | Per hour (compute reserved)         |
| Use Case    | Ad hoc querying over files | Structured warehousing              |
| Performance | Limited tuning             | Indexing, partitions, distributions |
| Scaling     | Auto                       | Manual (DWUs)                       |

---

## 🎯 Use Cases

* Modern **data warehousing**
* Interactive **data exploration**
* Real-time or batch **ETL pipelines**
* Building **dashboards and reports**
* Big data & ML integration via Spark

---

## 🚀 Step-by-Step: Azure Synapse Setup (Portal)

### ✅ Prerequisites:

* Azure subscription
* Azure Storage account with **ADLS Gen2** enabled

---

### 🧱 Step 1: Create a Synapse Workspace

1. Go to **Azure Portal**
2. Search **“Synapse Analytics”** → Click **Create**
3. Fill in the basics:

   * **Workspace Name**: `synapsewsdemo`
   * **Region**: Closest to you
   * **Account Admin**: yourself
   * **Storage Account**: Select or create one (must support ADLS Gen2)
   * **File System Name**: `synapse` (container in ADLS)
4. Click **Next** for other settings (leave defaults)
5. Click **Review + Create** → **Create**

📌 This will provision:

* Synapse workspace
* ADLS integration
* Serverless SQL pool (by default)

---

### 🔐 Step 2: Networking & Access

* After creation:

  * Go to the **Workspace**
  * Under **Access control (IAM)**, give Synapse Admin/Contributor roles to yourself/team
  * Under **Networking**, allow access from your IP if necessary

---

### 🖥️ Step 3: Launch Synapse Studio

1. In the workspace overview, click **“Open Synapse Studio”**
2. This opens a web-based development environment

Inside Synapse Studio:

* **Data** tab: Browse ADLS files, linked sources
* **Develop** tab: Notebooks, SQL Scripts, Pipelines
* **Monitor** tab: Pipeline runs, triggers

---

### 🗃️ Step 4: Connect to Data Lake or SQL

#### To connect to a CSV file in ADLS:

1. Upload a `.csv` file to your storage container using **Storage Explorer** or the **portal**
2. In **Synapse Studio → Data → Linked → Azure Data Lake Storage Gen2**
3. Right-click your file → **New SQL Script → Select TOP 100 rows**

You’ll see auto-generated SQL using **`OPENROWSET`** (serverless SQL pool):

```sql
SELECT TOP 100 *
FROM OPENROWSET(
    BULK 'https://<yourstorage>.dfs.core.windows.net/synapse/sales.csv',
    FORMAT='CSV',
    HEADER_ROW = TRUE
) AS rows;
```

---

### 🛢️ Step 5: Use Serverless SQL Pool

* Use `Built-in` pool (no cost until you run queries)
* Sample query:

```sql
SELECT COUNT(*) 
FROM OPENROWSET(
    BULK 'https://<yourstorage>.dfs.core.windows.net/synapse/sales.csv',
    FORMAT='CSV',
    HEADER_ROW = TRUE
) AS data;
```

🧾 Billing is based on **data scanned** (pay-per-TB), so be mindful of file size.

---

### 🔄 Step 6: Create a Pipeline (ETL-like)

1. Go to **Integrate** tab → **+ Pipeline**
2. Add **Source**: e.g., Blob Storage dataset
3. Add **Sink**: e.g., Azure SQL Database
4. Connect via Linked Services
5. Click **Debug** or **Trigger Now**

🧩 This is like **ADF (Azure Data Factory)** — built into Synapse.

---

### 🧑‍🔬 Step 7: Create a Notebook (Optional)

1. In **Develop** tab → **+ Notebook**
2. Use Spark (PySpark/Scala) to explore data:

```python
df = spark.read.csv('abfss://synapse@<storage>.dfs.core.windows.net/sales.csv', header=True)
df.show()
```

3. Attach to Spark pool (create one if needed)
4. Run the cell

---

## 🔐 Security Features

| Feature                               | Description                                     |
| ------------------------------------- | ----------------------------------------------- |
| **Managed Identity**                  | Secure access to storage, SQL DBs               |
| **Role-Based Access Control**         | Control who can access workspace                |
| **Firewalls & VNet Integration**      | Limit access                                    |
| **Column-level & Row-level Security** | Available in SQL pools                          |
| **Auditing and Logs**                 | Integrated with Azure Monitor and Log Analytics |

---

## 💰 Pricing Overview

| Resource               | Pricing Basis                  |
| ---------------------- | ------------------------------ |
| **Serverless SQL**     | ₹ / TB data scanned            |
| **Dedicated SQL Pool** | ₹ / hour (based on DWU)        |
| **Pipelines**          | Per activity run (same as ADF) |
| **Spark Pools**        | Per node/hour                  |
| **Storage**            | ADLS Gen2 pricing              |

Use the [Azure Pricing Calculator](https://azure.microsoft.com/en-in/pricing/calculator/) for estimates.

---

## 🧠 Best Practices

| Area                  | Practice                                      |
| --------------------- | --------------------------------------------- |
| Data Lake Structuring | Use folder partitioning, naming conventions   |
| Serverless SQL        | Use filters early to reduce scanned data      |
| Notebooks             | Use for exploration, not production jobs      |
| Pipelines             | Parameterize and monitor for retries/failures |
| Security              | Use managed identities and RBAC always        |

---

## ✅ Summary

| Feature               | Purpose                                                        |
| --------------------- | -------------------------------------------------------------- |
| **Synapse Studio**    | All-in-one interface                                           |
| **SQL Pools**         | Structured querying (serverless/dedicated)                     |
| **Pipelines**         | Data movement and transformation                               |
| **Notebooks + Spark** | Big data and ML                                                |
| **Data Integration**  | Works with ADLS, SQL DB, Cosmos DB, Blob, and external sources |

