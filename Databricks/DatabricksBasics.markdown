# Databricks Tutorial: Clusters, Notebooks, FileStore, Hive Metastore, File Types, and Security Fundamentals

This tutorial provides a step-by-step guide to creating and managing clusters, using notebooks, and understanding key Databricks concepts like FileStore, Hive Metastore, supported file types, and security fundamentals in Azure Databricks. The instructions assume you have an active Azure Databricks workspace (refer to Azure Databricks documentation for workspace setup).

## 1. Creating and Managing Databricks Clusters
A Databricks cluster is a set of computational resources (virtual machines) used to run data processing tasks, notebooks, and jobs. Clusters can be configured for interactive workloads (e.g., notebooks) or automated jobs.

### Steps to Create a Cluster
1. **Log in to Azure Databricks**:
   - Navigate to your Databricks workspace via the Azure Portal (`Launch Workspace` button).
   - Sign in with your Azure credentials.

2. **Access the Clusters Tab**:
   - In the Databricks workspace, click the **Clusters** icon in the left sidebar.

3. **Create a Cluster**:
   - Click **Create Cluster** in the Clusters page.
   - Configure the cluster:
     - **Cluster Name**: Enter a unique name (e.g., `MyFirstCluster`).
     - **Cluster Mode**:
       - **Standard**: For multi-node clusters, suitable for large-scale processing.
       - **Single Node**: For small workloads or testing (uses one node for both driver and workers).
     - **Databricks Runtime Version**: Select the latest stable version (e.g., 13.3 LTS) for optimal features, including Delta Lake and ML support.
     - **Auto-scaling**: Enable for dynamic scaling (adjusts nodes based on workload). Set min/max workers (e.g., 2–8 workers).
     - **Node Type**: Choose a VM type (e.g., `Standard_D3_v2` for general-purpose tasks). Use spot instances for cost savings.
     - **Driver Type**: Typically matches worker type unless specific requirements exist.
     - **Termination After Inactivity**: Set to 30–120 minutes to save costs (e.g., 60 minutes).
     - **Advanced Options** (optional):
       - **Spark Config**: Add custom Spark configurations (e.g., `spark.executor.memory 4g`).
       - **Tags**: Add metadata for cost tracking.
   - Click **Create Cluster**. The cluster takes 3–5 minutes to start.

4. **Manage the Cluster**:
   - **Start/Stop**: Use the **Start** or **Terminate** buttons to manage cluster state.
   - **Edit Configuration**: Modify node types, scaling, or runtime via the **Edit** button.
   - **Monitor Usage**: Check the **Event Log** for cluster events and the **Metrics** tab for resource usage (CPU, memory).

5. **Best Practices**:
   - Use **auto-scaling** to optimize costs and performance.
   - Terminate idle clusters to avoid unnecessary charges.
   - Choose **spot instances** for non-critical workloads to reduce VM costs.
   - Select the appropriate runtime version for compatibility with your libraries.

### Example Cluster Configuration
- Name: `DataAnalyticsCluster`
- Mode: Standard
- Runtime: 13.3 LTS
- Node Type: `Standard_D3_v2` (4 cores, 14 GB RAM)
- Workers: 2–4 (auto-scaling enabled)
- Termination: 60 minutes

## 2. Using Databricks Notebooks
Databricks notebooks are interactive environments for writing and executing code in Python, Scala, SQL, or R. They support collaboration, visualization, and integration with Spark clusters.

### Steps to Create and Use a Notebook
1. **Create a Notebook**:
   - In the Databricks workspace, click the **Workspace** icon in the sidebar.
   - Navigate to your user folder (e.g., `/Users/your.email@domain.com`).
   - Click the dropdown arrow next to your folder and select **Create > Notebook**.
   - Configure:
     - **Name**: Enter a name (e.g., `MyAnalysisNotebook`).
     - **Default Language**: Choose Python, Scala, SQL, or R.
     - **Cluster**: Select an active cluster (e.g., `MyFirstCluster`).
   - Click **Create**.

2. **Write and Execute Code**:
   - The notebook opens with an empty cell. Enter code in the default language (e.g., Python):
     ```python
     # Sample Python code to read a CSV file
     df = spark.read.csv("dbfs:/FileStore/sample_data.csv", header=True)
     df.show()
     ```
   - Press **Shift + Enter** to execute the cell.
   - Add new cells using the **+** button or `Ctrl + Enter` for additional commands.
   - Mix languages in a single notebook by using magic commands (e.g., `%sql`, `%scala`, `%r`):
     ```sql
     %sql
     SELECT * FROM delta.`dbfs:/FileStore/my_table`
     ```

3. **Visualize Data**:
   - Use the **Display** function to visualize DataFrames:
     ```python
     display(df)  # Renders table or chart
     ```
   - Click the chart icon below the output to create visualizations (e.g., bar, line, scatter).
   - Customize visualizations using the UI (e.g., set X/Y axes, group by columns).

4. **Collaborate and Share**:
   - Share notebooks via the **Share** button, granting permissions to team members (view, edit, or run).
   - Use **version control** to track changes (click **Revision history**).
   - Export notebooks as HTML, IPython, or DBC files for sharing externally.

5. **Schedule Jobs**:
   - Click **Schedule** to automate notebook execution as a job.
   - Configure frequency (e.g., daily at 2 AM) and select a cluster or use a job cluster for cost efficiency.

### Example Notebook Workflow
- Create a notebook named `SalesAnalysis`.
- Attach it to `DataAnalyticsCluster`.
- Write code to load, process, and visualize sales data:
  ```python
  # Load data
  sales_df = spark.read.format("delta").load("dbfs:/FileStore/sales_data")
  # Aggregate by region
  summary_df = sales_df.groupBy("region").sum("revenue")
  # Visualize
  display(summary_df)
  ```
- Save and share with your team.

## 3. Databricks FileStore
The Databricks FileStore is a managed file storage system within the Databricks File System (DBFS), designed for storing and accessing files like CSVs, JSONs, or models in a Databricks workspace.

### Overview
- **Location**: Files are stored in `dbfs:/FileStore/` and backed by cloud storage (e.g., Azure Blob Storage).
- **Purpose**: Store temporary files, datasets, libraries, or ML models accessible to notebooks and jobs.
- **Access**: Use the DBFS path (`dbfs:/FileStore/`) in code or the FileStore UI.

### Using FileStore
1. **Upload Files**:
   - Navigate to the **Data** tab in the sidebar.
   - Select **FileStore** and click **Upload File**.
   - Drag and drop or browse to upload files (e.g., `sample_data.csv`).
   - The file is stored at `dbfs:/FileStore/sample_data.csv`.

2. **Access Files in Notebooks**:
   - Read files using Spark or Python APIs:
     ```python
     # Spark API
     df = spark.read.csv("dbfs:/FileStore/sample_data.csv", header=True)
     # Python API (for small files)
     with open("/dbfs/FileStore/sample_data.csv", "r") as f:
         data = f.read()
     ```
   - Write files to FileStore:
     ```python
     df.write.csv("dbfs:/FileStore/output_data.csv")
     ```

3. **Download Files**:
   - In the **Data** tab, navigate to `FileStore`, select a file, and click **Download**.

4. **Best Practices**:
   - Use FileStore for temporary or small files; prefer cloud storage (e.g., Azure Data Lake) for large datasets.
   - Organize files in subdirectories (e.g., `dbfs:/FileStore/datasets/`).
   - Avoid storing sensitive data without encryption.

## 4. Hive Metastore
The Hive Metastore is a centralized metadata repository used by Databricks to manage table definitions, schemas, and partitions for Spark SQL queries.

- **Purpose**: Stores metadata (e.g., table names, column types, locations) for tables accessed via Spark SQL or Delta Lake.
- **Types**:
  - **Default Hive Metastore**: Managed by Databricks, stored in the workspace’s DBFS.
  - **External Hive Metastore**: User-managed (e.g., in Azure SQL Database) for custom configurations or cross-workspace sharing.
- **Access**: Tables registered in the metastore are accessible via SQL queries or Spark DataFrames.

## 5. Supported File Types
Databricks supports various file formats for reading and writing data, optimized for different use cases.

### Common File Types
- **CSV**:
  - Use: Simple, human-readable format for tabular data.
  - Example:
    ```python
    df = spark.read.csv("dbfs:/FileStore/data.csv", header=True, inferSchema=True)
    df.write.csv("dbfs:/FileStore/output.csv")
    ```
- **Parquet**:
  - Use: Columnar storage, optimized for Spark and big data processing.
  - Benefits: Compression, efficient columnar queries.
  - Example:
    ```python
    df.write.parquet("dbfs:/FileStore/output.parquet")
    ```
- **JSON**:
  - Use: Semi-structured data, common for APIs and logs.
  - Example:
    ```python
    df = spark.read.json("dbfs:/FileStore/data.json")
    ```
- **Delta**:
  - Use: Databricks’ optimized format with ACID transactions, versioning, and time travel.
  - Example:
    ```python
    df.write.format("delta").save("dbfs:/FileStore/my_table")
    ```
- **Avro**:
  - Use: Compact, schema-evolving format for streaming data.
  - Example:
    ```python
    df.write.format("avro").save("dbfs:/FileStore/output.avro")
    ```
- **ORC**:
  - Use: Optimized Row Columnar format for Hive compatibility.
  - Example:
    ```python
    df.write.orc("dbfs:/FileStore/output.orc")
    ```

### Best Practices
- Prefer **Delta** for production workloads due to reliability and performance.
- Use **Parquet** for analytics requiring columnar access.
- Compress large datasets to reduce storage costs (e.g., `spark.sql.parquet.compression.codec gzip`).

## 6. Security Fundamentals
Databricks provides robust security features to protect data, clusters, and workspaces, especially in the Premium tier.

### Key Security Features
- **Authentication**:
  - Azure Active Directory (AAD) integration for single sign-on (SSO).
  - Configure in **Admin Settings > SSO** with AAD credentials.
- **Authorization**:
  - **Role-Based Access Control (RBAC)**:
    - Assign permissions at workspace, cluster, notebook, or table levels.
    - Roles: Workspace Admin, Contributor, Viewer.
    - Example: Grant a user `SELECT` on a table:
      ```sql
      %sql
      GRANT SELECT ON TABLE my_table TO `user@domain.com`;
      ```
  - **Table Access Control**: Enable in cluster settings (`spark.databricks.acl.enabled true`) to restrict table access.
- **Data Encryption**:
  - Data is encrypted at rest (using Azure Blob Storage encryption) and in transit (TLS).
  - Use customer-managed keys for additional control (Premium tier).
- **Network Security**:
  - Deploy Databricks in a Virtual Network (VNet) for network isolation.
  - Configure IP access lists to restrict workspace access to specific IPs.
  - Example: In **Admin Settings > IP Access List**, add allowed IP ranges (e.g., `192.168.1.0/24`).
- **Audit Logging**:
  - Track user actions (e.g., cluster creation, notebook execution) in audit logs (Premium tier).
  - Store logs in Azure Blob Storage for analysis.
- **Secret Management**:
  - Use Databricks Secrets to store sensitive information (e.g., API keys).
  - Example:
    ```python
    from pyspark.dbutils import DBUtils
    dbutils = DBUtils(spark)
    api_key = dbutils.secrets.get(scope="my-scope", key="my-api-key")
    ```
  - Create a secret scope in **Admin Settings > Secrets**.

### Security Best Practices
- Enable **Premium tier** for advanced security features like RBAC and audit logs.
- Use **VNet injection** to isolate Databricks resources.
- Restrict cluster access to specific users or groups.
- Regularly review audit logs for suspicious activity.
- Store sensitive data in Delta tables with access controls.

## Conclusion
This tutorial covered the essentials of working with Azure Databricks, including cluster creation, notebook usage, FileStore, Hive Metastore, supported file types, and security fundamentals. By leveraging these components, you can build scalable, secure, and collaborative data workflows. For further learning, explore Databricks’ official documentation or the Community Edition for hands-on practice.