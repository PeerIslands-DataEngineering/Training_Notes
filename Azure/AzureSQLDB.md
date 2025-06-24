## 📘 What is Azure SQL Database?

**Azure SQL Database** is a fully managed **Platform as a Service (PaaS)** offering by Microsoft for hosting **relational SQL Server databases** in the cloud. It automates patching, backups, replication, and monitoring, so you can focus on application development.

It is **built on SQL Server** engine but includes enhancements like **scalability**, **high availability**, and **intelligent performance** tuning.

---

## 🔍 Key Features of Azure SQL Database

| Feature                   | Description                                                     |
| ------------------------- | --------------------------------------------------------------- |
| **Fully Managed**         | No manual patching, backups, or maintenance required.           |
| **High Availability**     | 99.99% SLA with geo-redundancy and failover.                    |
| **Built-in Intelligence** | Index tuning, query optimization.                               |
| **Security**              | Encryption (TDE), AAD integration, firewall rules.              |
| **Scalability**           | Scale vertically (compute) and elastically (serverless, pools). |
| **Geo-replication**       | Automatic or manual replicas in different regions.              |

---

## 📂 Deployment Models

| Model                | Description                                                              |
| -------------------- | ------------------------------------------------------------------------ |
| **Single Database**  | Isolated DB with dedicated resources.                                    |
| **Elastic Pool**     | Share resources across multiple DBs.                                     |
| **Managed Instance** | Lift-and-shift of full SQL Server instance (more control, VNet support). |

For this tutorial, we’ll use the **Single Database** model.

---

## 🧭 Step-by-Step: Create Azure SQL Database (Portal)

### ✅ Prerequisites

* Azure Subscription
* Basic understanding of SQL

---

### 🧱 Step 1: Create a SQL Server (logical container)

1. Go to [Azure Portal](https://portal.azure.com)
2. Search for **“SQL servers”** → Click **“+ Create”**
3. Fill the form:

   * **Server name**: e.g., `sqlserverdemo1234`
   * **Region**: Choose closest to your users
   * **Admin login**: `sqladmin`
   * **Password**: Choose a strong one
4. Click **Review + Create** → **Create**

---

### 🛢️ Step 2: Create Azure SQL Database

1. Search for **“SQL databases”** → Click **“+ Create”**
2. Fill the form:

   * **Database name**: `mydemoDB`
   * **Subscription + Resource Group**
   * **Select existing SQL Server** from Step 1
   * **Compute + Storage**: Click **Configure database**

     * Start with **Basic**, **General Purpose**, or **Serverless** (choose based on use case)
   * Click **Apply**
3. Click **Review + Create** → **Create**

---

### 🌐 Step 3: Configure Firewall Access

1. After deployment → Click on your **SQL server**
2. Go to **Networking**
3. Under **Firewall rules**, add your client IP (or a range) to allow access
4. Save changes

---

### 🛠️ Step 4: Connect to Database

**From SQL Server Management Studio (SSMS) / Azure Data Studio**:

1. Server name: `<your_server_name>.database.windows.net`
2. Authentication: SQL Auth
3. Username: `sqladmin`, Password: your password
4. Select DB: `mydemoDB`

---

### 🧪 Step 5: Create a Table & Insert Data

```sql
-- Create a table
CREATE TABLE Employees (
    Id INT PRIMARY KEY,
    Name NVARCHAR(50),
    Department NVARCHAR(50)
);

-- Insert data
INSERT INTO Employees VALUES (1, 'John Doe', 'HR'), (2, 'Jane Smith', 'IT');

-- Query
SELECT * FROM Employees;
```

---

## 🧾 Step-by-Step via Azure CLI

```bash
# Login to Azure
az login

# Create resource group
az group create --name rg-sqldemo --location eastus

# Create SQL server
az sql server create \
  --name sqlserverdemo1234 \
  --resource-group rg-sqldemo \
  --location eastus \
  --admin-user sqladmin \
  --admin-password YourP@ssword123

# Create SQL database
az sql db create \
  --resource-group rg-sqldemo \
  --server sqlserverdemo1234 \
  --name mydemoDB \
  --service-objective S0

# Allow your IP to access SQL
az sql server firewall-rule create \
  --resource-group rg-sqldemo \
  --server sqlserverdemo1234 \
  --name AllowMyIP \
  --start-ip-address <YOUR_IP> \
  --end-ip-address <YOUR_IP>
```

---

## 💡 Azure SQL Pricing Tiers (simplified)

| Tier                     | Use Case                         | DTUs / vCores          |
| ------------------------ | -------------------------------- | ---------------------- |
| **Basic**                | Small dev/test apps              | 5 DTUs                 |
| **Standard (S1, S2, …)** | Web & Business apps              | 10-100 DTUs            |
| **Premium**              | High performance                 | 125+ DTUs              |
| **vCore-based**          | More control & predictable costs | Choose compute/storage |
| **Serverless**           | Intermittent workloads           | Auto-pause/resume      |

📝 Use the [Azure Pricing Calculator](https://azure.microsoft.com/en-in/pricing/calculator/) to estimate.

---

## 🔐 Security Features

* **TDE (Transparent Data Encryption)** – Enabled by default.
* **SQL Authentication** – Username/password.
* **AAD Authentication** – Use Entra ID for central identity management.
* **Firewall Rules** – Restrict IP access.
* **Auditing** – Track access, changes.
* **Threat Detection** – Alerts for suspicious activities.

---

## 📊 Monitoring & Alerts

* Go to SQL database → **Monitoring**

  * **Query Performance Insight**
  * **Auditing logs**
  * **Metrics & Alerts**
* Integrate with **Azure Monitor**, **Log Analytics**, or **App Insights**.

---

## 🧠 Best Practices

| Area               | Recommendation                                          |
| ------------------ | ------------------------------------------------------- |
| **Access Control** | Use least privilege + AAD                               |
| **Backups**        | Rely on automated backups (up to 35 days)               |
| **DR**             | Use geo-replication if needed                           |
| **Performance**    | Use Query Store + Performance recommendations           |
| **Cost**           | Choose right pricing tier and auto-pause serverless DBs |

---

## ✅ Summary

| Aspect     | Info                                     |
| ---------- | ---------------------------------------- |
| Type       | Relational, fully managed                |
| Engine     | SQL Server                               |
| Deployment | Single, Elastic Pool, Managed Instance   |
| Ideal for  | Web apps, business apps, reporting tools |
| Management | Portal, CLI, PowerShell, IaC (ARM/Bicep) |
