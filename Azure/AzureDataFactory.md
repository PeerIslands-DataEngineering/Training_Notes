# 💠 Azure Data Factory 

---

## 🔷 What is Azure Data Factory?

**Azure Data Factory (ADF)** is a **cloud-based ETL (Extract, Transform, Load) and data integration service** that allows you to:

* Move data between diverse sources and destinations
* Transform data on the fly or after movement
* Automate and orchestrate data workflows

✅ Think of ADF as a **data movement + transformation + scheduling tool** that connects databases, files, APIs, and analytics tools.

---

## 🧠 Key Terminologies

| Term                         | Explanation                                          |
| ---------------------------- | ---------------------------------------------------- |
| **Pipeline**                 | A logical grouping of activities (like a workflow)   |
| **Activity**                 | An action in a pipeline (e.g., Copy Data, Data Flow) |
| **Dataset**                  | Represents data structure pointing to source/sink    |
| **Linked Service**           | A connection string to data sources/destinations     |
| **Trigger**                  | Runs pipelines on schedule or event                  |
| **Integration Runtime (IR)** | The compute engine behind ADF                        |

---

## 🧭 High-Level Workflow in ADF

1. **Connect** to data sources using **Linked Services**
2. Define **Datasets** (source + target)
3. Build a **Pipeline** with activities like Copy or Data Flow
4. Use **Triggers** to schedule
5. **Monitor** and optimize

---

## 📦 Supported Data Sources

ADF supports 90+ connectors, including:

* Azure Blob, Azure Data Lake, Azure SQL DB
* Amazon S3, Google Cloud Storage
* On-prem SQL Server, Oracle
* SAP, Salesforce, REST APIs, FTP, etc.

---

## ✅ Step-by-Step Tutorial: ADF in Action

Let’s copy data from an **Azure Blob CSV file to Azure SQL Database**.

---

### 🔹 Step 1: Create Azure Data Factory

1. Go to **Azure Portal**
2. Search **Data Factory** → Click **Create**
3. Fill in:

   * Subscription
   * Resource Group
   * Region
   * Data Factory Name (must be globally unique)
4. Click **Review + Create** → **Create**

---

### 🔹 Step 2: Open Azure Data Factory Studio

1. Once created, open your ADF instance
2. Click **"Open Azure Data Factory Studio"**

ADF Studio has 5 main sections:

| Tab         | Purpose                        |
| ----------- | ------------------------------ |
| **Home**    | Quick actions                  |
| **Author**  | Build pipelines                |
| **Monitor** | Track executions               |
| **Manage**  | Configure Linked Services, IRs |
| **Data**    | Explore Datasets               |

---

### 🔹 Step 3: Create Linked Services (Connections)

1. Go to **Manage > Linked Services > + New**

2. Choose **Azure Blob Storage**

3. Fill connection details:

   * Choose Auth type (Account key / SAS / Managed Identity)
   * Select your Blob Storage account
   * Test connection → Create

4. Repeat the process for **Azure SQL Database**

---

### 🔹 Step 4: Create Datasets

#### 📌 Source Dataset (Blob Storage)

1. Go to **Author > + > Dataset**
2. Choose **Azure Blob Storage → CSV**
3. Select Linked Service (Blob)
4. Browse and select the **CSV file**
5. Name the dataset (e.g., `BlobInputDataset`)

#### 📌 Sink Dataset (SQL Table)

1. Create new Dataset → Azure SQL DB → Table
2. Choose Linked Service (SQL)
3. Point to the destination table or create a new one

---

### 🔹 Step 5: Build the Pipeline

1. Go to **Author > + > Pipeline**
2. Drag **Copy Data** activity from toolbox
3. Name the activity (e.g., `CopyBlobToSQL`)
4. Click the activity → configure:

#### 🔹 Source Tab:

* Select source dataset (BlobInputDataset)

#### 🔹 Sink Tab:

* Select sink dataset (SQLOutputDataset)

#### 🔹 Mapping (optional):

* Auto-map or manually map source → target columns

---

### 🔹 Step 6: Test and Publish

1. Click **Debug** (runs pipeline without publishing)
2. If it succeeds, click **Publish All**
3. Now your pipeline is saved and ready to run anytime

---

### 🔹 Step 7: Trigger the Pipeline

1. In the pipeline editor → Click **Add Trigger**
2. Choose:

   * **Trigger Now** (manual run)
   * **New Trigger** (schedule)

     * Example: Every day at 12AM
3. Monitor the run from the **Monitor tab**

---

## 🧪 Advanced Features of ADF

| Feature                             | Description                                                           |
| ----------------------------------- | --------------------------------------------------------------------- |
| **Mapping Data Flows**              | Visual drag-and-drop UI for data transformation (like SSIS)           |
| **Parameters**                      | Dynamically pass values into pipelines, datasets, and linked services |
| **Self-Hosted Integration Runtime** | Required for on-premise to cloud data movement                        |
| **Data Flow Debug**                 | Run and preview transformations                                       |
| **Azure Key Vault**                 | Secure connection secrets management                                  |
| **Git Integration**                 | Connect to Azure DevOps / GitHub for source control                   |

---

## 🧠 Real-World Use Cases

| Use Case                             | How ADF Helps                            |
| ------------------------------------ | ---------------------------------------- |
| Data ingestion from on-prem to cloud | Use Self-Hosted IR                       |
| Daily batch job                      | Use Time Trigger                         |
| ETL for analytics                    | Use Copy + Data Flow                     |
| Merge JSON logs to SQL               | Use Mapping Data Flow                    |
| Orchestrate multiple steps           | Use sequential activities, failure paths |

---

## 🔐 Security & Access

* Use **Managed Identity** to avoid hardcoded credentials
* **SAS tokens** or **Azure Key Vault** for secrets
* RBAC for user-level access control
* **Private endpoints** for secure network isolation

---

## 🛠 Tools for ADF Development

| Tool                            | Usage                            |
| ------------------------------- | -------------------------------- |
| **ADF Studio**                  | Main UI for pipeline development |
| **Azure CLI / PowerShell**      | Automation and scripting         |
| **ARM Templates / Terraform**   | Infrastructure-as-Code for CI/CD |
| **Data Flow Debugging Console** | Inspect data step-by-step        |

---

## 🔄 Monitoring & Logging

Go to the **Monitor** tab:

* View active and historical pipeline runs
* Drill into activity run details
* Retry failed runs
* Set up alerts using **Azure Monitor**

---

## 📚 Summary Table

| Feature        | ADF Capability                  |
| -------------- | ------------------------------- |
| Orchestration  | ✅ Pipelines & Triggers          |
| Transformation | ✅ Data Flows, SQL, Stored Procs |
| Connectors     | ✅ 90+ Built-in                  |
| Monitoring     | ✅ Logs, Alerts, Retry           |
| Scheduling     | ✅ Cron, Event, Manual           |
| On-Prem Access | ✅ Self-hosted IR                |
| DevOps         | ✅ Git Integration, ARM          |

