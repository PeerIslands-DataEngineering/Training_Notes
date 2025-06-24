
## 🌐 What is Microsoft Azure?

**Azure** is Microsoft’s **cloud computing platform** that provides a wide range of **services** like computing, networking, storage, databases, analytics, machine learning, DevOps, and more. It allows organizations to **build, deploy, and manage** applications through Microsoft-managed data centers.

---

## 🧱 Azure Core Concepts

### 1. **Cloud Models Supported**

* **IaaS (Infrastructure as a Service)**: Provision virtual machines, networking, storage, etc.
* **PaaS (Platform as a Service)**: Deploy web apps, APIs, containers without managing the underlying infrastructure.
* **SaaS (Software as a Service)**: Access software like Office 365 through the cloud.
* **Serverless**: Run code on-demand (e.g., Azure Functions) without managing servers.

### 2. **Deployment Models**

* **Public Cloud**: Services hosted on Microsoft’s data centers and shared across tenants.
* **Private Cloud**: Dedicated infrastructure for one organization (via Azure Stack).
* **Hybrid Cloud**: Combine on-premises infrastructure with Azure using services like Azure Arc and VPNs.

---

## 🧩 Azure Global Infrastructure

| Component              | Description                                                                            |
| ---------------------- | -------------------------------------------------------------------------------------- |
| **Regions**            | Geographical areas (e.g., East US, West Europe) where Azure resources can be deployed. |
| **Availability Zones** | Physically separate datacenters within a region to provide redundancy.                 |
| **Resource Groups**    | Logical containers to group and manage related Azure resources.                        |
| **Subscriptions**      | Billed entities associated with Azure usage; can host multiple resource groups.        |
| **Management Groups**  | Hierarchical grouping of subscriptions for governance and policy enforcement.          |

---

## 🔧 Core Azure Services (Grouped by Category)

### 1. **Compute**

| Service                            | Description                                   |
| ---------------------------------- | --------------------------------------------- |
| **Azure Virtual Machines**         | Create Linux/Windows VMs in the cloud.        |
| **App Services**                   | Host web apps, REST APIs, or mobile backends. |
| **Azure Functions**                | Serverless compute for small pieces of code.  |
| **Azure Container Instances**      | Lightweight containers without orchestration. |
| **AKS (Azure Kubernetes Service)** | Fully managed Kubernetes cluster.             |

### 2. **Storage**

| Service           | Description                                            |
| ----------------- | ------------------------------------------------------ |
| **Blob Storage**  | Store unstructured data (images, video, backups).      |
| **File Storage**  | Fully managed SMB file shares.                         |
| **Queue Storage** | Store and retrieve message queues.                     |
| **Table Storage** | NoSQL key-value store for large-scale structured data. |

### 3. **Networking**

| Service                    | Description                                 |
| -------------------------- | ------------------------------------------- |
| **Virtual Network (VNet)** | Private, isolated networks.                 |
| **VPN Gateway**            | Connect on-premises networks to Azure.      |
| **Azure Load Balancer**    | Distribute traffic across resources.        |
| **Application Gateway**    | Load balancing with SSL offloading and WAF. |
| **Azure Front Door**       | Global web traffic management with CDN.     |

### 4. **Databases**

| Service                                 | Description                                     |
| --------------------------------------- | ----------------------------------------------- |
| **Azure SQL Database**                  | PaaS SQL database (high availability, backups). |
| **Cosmos DB**                           | Globally distributed NoSQL database.            |
| **Azure Database for MySQL/PostgreSQL** | Fully managed relational DBs.                   |
| **Azure Synapse Analytics**             | Data warehouse + big data analytics.            |

### 5. **AI & Machine Learning**

| Service                    | Description                                    |
| -------------------------- | ---------------------------------------------- |
| **Azure Machine Learning** | End-to-end MLOps platform.                     |
| **Cognitive Services**     | Prebuilt AI models (vision, speech, language). |
| **Bot Service**            | Build conversational AI (chatbots).            |

### 6. **DevOps and Monitoring**

| Service                  | Description                          |
| ------------------------ | ------------------------------------ |
| **Azure DevOps**         | CI/CD, repos, pipelines, test plans. |
| **GitHub Actions**       | CI/CD workflows using GitHub.        |
| **Azure Monitor**        | Logs, metrics, alerts.               |
| **Application Insights** | APM for apps.                        |

### 7. **Security & Identity**

| Service                                           | Description                                  |
| ------------------------------------------------- | -------------------------------------------- |
| **Azure Active Directory (AAD)**                  | Identity and access management.              |
| **Azure Key Vault**                               | Secure storage for keys, secrets, and certs. |
| **Azure Defender / Microsoft Defender for Cloud** | Threat protection and security posture.      |

---

## 🧠 Key Azure Concepts

| Concept                          | Description                                                 |
| -------------------------------- | ----------------------------------------------------------- |
| **ARM (Azure Resource Manager)** | Infrastructure-as-code and deployment engine.               |
| **Azure Marketplace**            | Third-party and Microsoft apps/services.                    |
| **Tags**                         | Metadata for categorizing resources.                        |
| **Policies**                     | Enforce governance (e.g., allowed SKUs, locations).         |
| **Blueprints**                   | Repeatable environments with policies, RBAC, ARM templates. |

---

## 💼 Common Use Cases

1. **Hosting web apps and APIs**
2. **Lift-and-shift on-premises workloads to VMs**
3. **Running containers and Kubernetes**
4. **Big data analytics using Synapse + Data Lake**
5. **CI/CD pipelines for DevOps**
6. **Disaster recovery and backup**
7. **Hybrid cloud and on-prem integration**
8. **Serverless event-driven automation**

---

## ✅ Advantages of Azure

| Feature               | Benefit                                                         |
| --------------------- | --------------------------------------------------------------- |
| **Global Reach**      | Operates in 60+ regions worldwide.                              |
| **Security**          | Compliance with 90+ certifications (GDPR, HIPAA, etc).          |
| **Scalability**       | Automatic and manual scaling options.                           |
| **Cost Control**      | Pay-as-you-go, spot pricing, and reserved instances.            |
| **Integration**       | Works well with Microsoft ecosystem (Windows, Office 365, etc). |
| **High Availability** | SLA-backed services with redundancy options.                    |

---

## 🔐 Azure Management Tools

| Tool                      | Purpose                                           |
| ------------------------- | ------------------------------------------------- |
| **Azure Portal**          | Web interface for all Azure services.             |
| **Azure CLI**             | Command-line interface for scripting deployments. |
| **Azure PowerShell**      | PowerShell-based resource management.             |
| **ARM Templates / Bicep** | IaC to define and deploy infrastructure.          |
| **Terraform**             | Open-source IaC tool (cross-cloud).               |

---

## 🔄 Lifecycle of Azure Resource Deployment

1. **Design**: Select region, service, sizing.
2. **Provision**: Use portal/CLI/ARM/Bicep to deploy.
3. **Configure**: Network, monitoring, authentication.
4. **Monitor & Scale**: Autoscale rules, alerts.
5. **Secure**: Role-based access control, firewalls.
6. **Decommission**: Remove unused resources to save cost.

---
