# 🔷 Azure Storage Services 

---

## ✅ What is Azure Storage?

Azure Storage is a **Microsoft-managed cloud storage solution** for modern data storage scenarios. It offers **high availability, durability, scalability**, and secure data access.

Azure Storage supports:

* Structured, semi-structured, and unstructured data
* Data for analytics, backup, archiving, media, and more

---

## 📦 Main Types of Azure Storage Services

| Storage Type                     | Description                                              | Best For                                        |
| -------------------------------- | -------------------------------------------------------- | ----------------------------------------------- |
| **Blob Storage**                 | Object storage for unstructured data                     | Images, videos, documents, backups              |
| **File Storage (File Share)**    | Managed file shares using SMB protocol                   | Lift-and-shift apps, file server replacement    |
| **Queue Storage**                | Messaging store for async communication between services | Decoupled app components, message queues        |
| **Table Storage**                | NoSQL key-value store for structured data                | Lightweight, schema-less data (e.g., logs)      |
| **Disk Storage**                 | Managed persistent disks for VMs                         | OS & data disks for Azure VMs                   |
| **Azure Data Lake Storage Gen2** | Hierarchical namespace over blob, optimized for big data | Analytics, big data workloads with Hadoop/Spark |

---

## 🔹 1. Azure Blob Storage (Binary Large Object)

### 🔸 Features:

* **Object storage** (unstructured data)
* Store images, audio, video, documents, backups
* **Hot, Cool, and Archive tiers**
* **Append**, **Block**, and **Page** blobs
* **Lifecycle management** policies
* Native support for **Azure Data Lake Gen2**

### 🔸 Blob Tiers:

| Tier        | Use Case                         | Cost     | Access Speed         |
| ----------- | -------------------------------- | -------- | -------------------- |
| **Hot**     | Frequently accessed data         | High     | Fast                 |
| **Cool**    | Infrequently accessed (30+ days) | Lower    | Slight delay         |
| **Archive** | Rarely accessed (180+ days)      | Very low | Requires rehydration |

### 🔸 Common Uses:

* Static file hosting (e.g., website images)
* Data lake storage
* Backup and restore
* Media storage for apps

---

## 🔹 2. Azure File Storage

### 🔸 Features:

* Fully managed **file share** in the cloud
* Access via **SMB/NFS protocols**
* **Mountable** on Windows, Linux, macOS
* Supports **Azure File Sync** (hybrid file share)
* **Snapshot support** for backups

### 🔸 Common Uses:

* Lift-and-shift legacy applications
* Shared configuration files across VMs
* User profile and home directories

---

## 🔹 3. Azure Queue Storage

### 🔸 Features:

* Message-based communication system
* Lightweight, async communication
* Stores millions of messages
* Message size limit: **64 KB**
* Message retention: **up to 7 days**

### 🔸 Common Uses:

* Decoupling microservices
* Event-driven architecture
* Background job processing

---

## 🔹 4. Azure Table Storage

### 🔸 Features:

* NoSQL key-value store
* Highly scalable and cost-effective
* Supports **OData REST API**
* Fast lookups using PartitionKey + RowKey

### 🔸 Common Uses:

* User data, metadata, device logs
* Configuration data
* Lightweight structured storage

---

## 🔹 5. Azure Disk Storage

### 🔸 Features:

* Persistent block storage for Azure VMs
* **Ultra**, **Premium SSD**, **Standard SSD**, **HDD** tiers
* Supports disk snapshots, encryption, and backup
* Highly available and durable

### 🔸 Common Uses:

* Operating system and data disks
* High IOPS workloads (databases, SAP)
* Backup and recovery with Azure Backup

---

## 🔹 6. Azure Data Lake Storage Gen2 (ADLS)

### 🔸 Features:

* Combines hierarchical file system + Blob storage
* Optimized for big data analytics
* Supports Hadoop-compatible APIs (WebHDFS)
* Integrated with **Azure Synapse**, **Databricks**, **HDInsight**
* Fine-grained **access control via ACLs**

### 🔸 Common Uses:

* Big data pipelines
* Storing raw and processed datasets
* ETL/ELT with Spark, Synapse, Data Factory

---

## 🔐 Security Features (All Azure Storage Types)

* **Encryption at rest** using Microsoft-managed or customer-managed keys
* **RBAC** and **Azure AD** integration
* **Private endpoints** for VNet isolation
* **Shared Access Signatures (SAS)** for granular access
* **Soft delete** for blobs, files, and containers

---

## 🔄 Redundancy & Replication Options

| Redundancy Type             | Description                                 | Use When...                       |
| --------------------------- | ------------------------------------------- | --------------------------------- |
| **LRS** (Locally redundant) | 3 copies in one data center region          | Cost-effective, not geo-resilient |
| **GRS** (Geo-redundant)     | LRS + async replication to secondary region | Disaster recovery                 |
| **ZRS** (Zone-redundant)    | 3 copies across availability zones          | High availability in region       |
| **RA-GRS**                  | GRS + read access to secondary region       | Read-only DR and geo-access       |

---

## 📊 Pricing Overview (High-Level)

| Type         | Pricing Basis                                      |
| ------------ | -------------------------------------------------- |
| Blob Storage | Size, access tier, operations, data egress         |
| File Storage | Provisioned share size, transactions, snapshots    |
| Queue/Table  | Number of operations, data stored                  |
| Disk Storage | Disk size, performance tier                        |
| ADLS Gen2    | Same as blob with extra features (namespace, ACLs) |

Use the [Azure Pricing Calculator](https://azure.microsoft.com/en-us/pricing/calculator/) to estimate.

---

## ⚙️ Tools & SDKs

* **Azure Storage Explorer** – GUI tool to manage all storage types
* **az CLI / PowerShell** – For scripting and automation
* **.NET, Python, Java, Node.js SDKs** – Native support in all major languages
* **REST API** – For direct programmatic access

---

## 📐 Architecture Use Case Examples

### 1. Web App Storing Images

* Use: **Blob Storage (Hot tier)**
* Access via: HTTPS URLs + SAS Tokens

### 2. Shared File System Between VMs

* Use: **Azure File Storage + SMB**
* Access via: Mount file share

### 3. Event Queue for Microservices

* Use: **Queue Storage**
* Access via: SDK or REST API

### 4. IoT Device Logs

* Use: **Table Storage** or **ADLS**
* Access via: Key-based/REST/OData queries

### 5. Big Data Analytics Pipeline

* Use: **Data Lake Gen2 + Synapse/Databricks**
* Store raw → bronze → silver → gold data layers

---

## ✅ Best Practices

* **Choose appropriate redundancy** (LRS for dev/test, GRS/ZRS for prod)
* **Set up lifecycle policies** for Blob tier transitions
* **Use Managed Identities and Key Vault** for secure access
* **Monitor using Azure Monitor + Storage Metrics**
* **Enable versioning and soft delete** for critical data

---
