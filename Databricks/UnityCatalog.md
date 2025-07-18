Unity Catalog is **Databricks’ unified governance solution** for all data and AI assets in the **Databricks Lakehouse Platform**. It helps manage **data access, lineage, auditing, and metadata** across **workspaces and clouds**, supporting structured, semi-structured, and unstructured data.

---

### 🔑 Key Features of Unity Catalog

| Feature                           | Description                                                       |
| --------------------------------- | ----------------------------------------------------------------- |
| **Centralized Metadata**          | Unified metadata layer across multiple workspaces.                |
| **Fine-Grained Access Control**   | Row-, column-, and privilege-level controls using ANSI SQL.       |
| **Data Lineage**                  | Automatically captures lineage for tables, notebooks, jobs.       |
| **Audit Logging**                 | Native integration with audit logs for compliance and monitoring. |
| **Multi-Cloud & Cross-Workspace** | Works across AWS, Azure, and GCP and across workspaces.           |
| **Support for Files & ML Assets** | Governs not just tables but also files, models, functions, etc.   |

---

### 🏗️ Core Components

| Component              | Description                                                    |
| ---------------------- | -------------------------------------------------------------- |
| **Catalog**            | Top-level container; e.g., `main`, `catalog1`.                 |
| **Schema**             | Also known as "database"; exists within a catalog.             |
| **Table / View**       | Tables are within schemas.                                     |
| **Volume**             | Stores unstructured data like images, PDFs, etc.               |
| **External Location**  | Path in cloud storage registered with Unity Catalog.           |
| **Storage Credential** | Defines how to access external storage (with IAM role or key). |

---

### 🧱 Unity Catalog Hierarchy

```
Catalog
 └── Schema (Database)
      └── Tables / Views / Functions
```

Example:

```sql
SELECT * FROM main.sales.orders;
```

---

### 🔒 Access Control (DAC)

* Unity Catalog uses **attribute-based access control** (ABAC) and **role-based access control** (RBAC).
* You can use SQL to **GRANT** permissions:

```sql
GRANT SELECT ON TABLE main.sales.orders TO `data_analyst_group`;
```

* Supports **row-level** and **column-level** security using dynamic views or Delta Sharing.

---

### 🔁 Data Lineage

* Unity Catalog automatically tracks:

  * **Input/output tables** used in notebooks, jobs, and dashboards.
  * **Upstream/downstream** dependencies.
* Viewable via Databricks UI for audit and debugging.

---

### 🔄 Integration with Delta Lake

* All Delta Lake tables in Unity Catalog:

  * Have **governed schema**.
  * Can enforce **data quality** and **audit** policies.

---

### 📁 Volumes (Unstructured Data)

```sql
CREATE VOLUME my_volume
COMMENT 'volume to store images';
```

* Allows managing files like CSVs, images, PDFs inside Unity Catalog governance.

---

### 📦 External Locations

To access data in cloud storage (ADLS, S3, GCS), you register external locations:

```sql
CREATE EXTERNAL LOCATION my_ext_location
URL 's3://my-bucket/datalake/'
WITH STORAGE CREDENTIAL my_aws_role;
```

---

### 🧪 Lineage & Discovery

* Unity Catalog offers **built-in lineage UI** in the Databricks workspace.
* Catalog Explorer helps users **browse and discover** tables, schemas, views, and volumes.

---

### 🛡️ Compliance & Governance

* Enables:

  * **SOC 2, HIPAA, GDPR, ISO** compliance.
  * **Audit logging** and **monitoring**.
* Centralizes policies for multiple teams and projects.

---

### 🧭 Unity Catalog vs Hive Metastore

| Feature                  | Hive Metastore | Unity Catalog |
| ------------------------ | -------------- | ------------- |
| Multi-workspace          | ❌              | ✅             |
| Fine-grained ACL         | ❌              | ✅             |
| Lineage                  | ❌              | ✅             |
| Files, models, notebooks | ❌              | ✅             |
| Cloud-agnostic           | ❌              | ✅             |

---

### ⚙️ Enabling Unity Catalog (Steps Summary)

1. **Create metastore** in Databricks Admin Console.
2. **Assign it** to your workspace.
3. Create **storage credentials** and **external locations**.
4. **Migrate** existing Hive tables if needed.
5. Start creating **catalogs, schemas, and tables**.

---