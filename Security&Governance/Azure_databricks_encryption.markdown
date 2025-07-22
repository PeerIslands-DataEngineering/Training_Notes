# Azure Databricks Data Encryption Tutorial: At Rest and In Transit

This tutorial provides a comprehensive guide to configuring data encryption at rest and in transit in Azure Databricks. It includes step-by-step instructions for setting up customer-managed keys (CMKs) for encryption at rest and enabling Transport Layer Security (TLS) for encryption in transit, ensuring compliance with security and privacy requirements.

## Prerequisites

Before starting, ensure you have the following:
- An **Azure account** with an active subscription.
- An **Azure Databricks workspace** (Premium plan or above for CMK support).
- **Azure Key Vault** configured to store customer-managed keys.
- **Databricks Admin permissions** or equivalent access to configure encryption settings.
- **Azure Active Directory (Azure AD)** integrated with Azure Databricks for identity management.
- Basic familiarity with Azure Portal, Azure Key Vault, and Databricks workspace navigation.

## Overview of Data Encryption in Azure Databricks

Azure Databricks provides robust mechanisms to secure data:
- **Encryption at Rest**: Protects data stored in Azure Databricks, such as in the Databricks File System (DBFS) root, managed disks, and SQL queries. By default, Microsoft-managed keys (MMKs) are used, but customer-managed keys (CMKs) stored in Azure Key Vault can be configured for greater control.
- **Encryption in Transit**: Secures data as it moves between clients, Databricks services, and external resources using TLS/SSL protocols. By default, user queries and transformations are sent over encrypted channels, but inter-node communication within clusters may require additional configuration.

This tutorial covers:
1. Configuring encryption at rest with customer-managed keys (CMKs).
2. Enabling encryption in transit for inter-node communication and external connections.
3. Best practices for key management and security.

## Section 1: Encryption at Rest with Customer-Managed Keys (CMKs)

Azure Databricks supports encryption at rest for:
- **Managed Services**: Data stored in the control plane, including notebook source files, notebook results, secrets, SQL queries, query history, and Git integration credentials.
- **DBFS Root**: Data stored in the workspace storage account, such as the DBFS root container.
- **Managed Disks**: Disks associated with Databricks clusters.

### Step 1: Set Up Azure Key Vault

1. **Sign in to the Azure Portal**:
   - Navigate to [portal.azure.com](https://portal.azure.com) and sign in with an account that has Databricks Admin or Global Admin permissions.

2. **Create an Azure Key Vault**:
   - In the Azure Portal, click **Create a resource** and search for **Key Vault**.
   - Click **Create** and provide the following details:
     - **Subscription**: Select your Azure subscription.
     - **Resource Group**: Choose or create a resource group.
     - **Key Vault Name**: Enter a unique name (e.g., `my-databricks-keyvault`).
     - **Region**: Select the same region as your Databricks workspace.
     - **Pricing Tier**: Choose **Standard** or **Premium** based on your needs.
   - Click **Review + Create**, then **Create** to deploy the Key Vault.

3. **Create a Customer-Managed Key**:
   - Navigate to your Key Vault in the Azure Portal.
   - Under **Keys**, click **Generate/Import**.
   - Provide a key name (e.g., `databricks-cmk`) and select **RSA** as the key type with a size of **2048** or higher.
   - Click **Create** to generate the key.
   - Note the **Key Identifier** (URI) for later use (e.g., `https://my-databricks-keyvault.vault.azure.net/keys/databricks-cmk`).

4. **Configure Access Policies**:
   - In the Key Vault, go to **Access policies** and click **Add Access Policy**.
   - Select permissions: **Get**, **Wrap Key**, and **Unwrap Key**.
   - For **Select principal**, choose the user-assigned managed identity associated with your Databricks workspace (or create one if not already set up).
   - Click **Add** and **Save** to apply the policy.

### Step 2: Configure CMK for Managed Services

1. **Access the Databricks Workspace**:
   - In the Azure Portal, navigate to your Azure Databricks workspace.
   - Click on the workspace name to open the resource.

2. **Enable CMK for Managed Services**:
   - In the workspace navigation panel, under **Settings**, select **Encryption**.
   - In the **Customer-managed keys** section, check the box for **Managed Services**.
   - Select your Azure subscription from the **Subscription** dropdown.
   - Paste the **Key Identifier** from the Azure Key Vault (from Step 1.3).
   - Click **Save** to apply the changes.

   This configuration ensures that notebook source files, results, secrets, SQL queries, and query history in the control plane are encrypted using your CMK.

3. **Verify Configuration**:
   - Confirm that the **Managed Services** setting is enabled and the correct Key Identifier is displayed.
   - Note: Data stored before enabling CMK or prior to May 20, 2021, may not be encrypted with the CMK.[](https://learn.microsoft.com/en-us/azure/databricks/security/keys/sql-encryption)

### Step 3: Configure CMK for DBFS Root

1. **Enable CMK for DBFS Root**:
   - In the same **Encryption** settings page, check the box for **DBFS Root**.
   - Select your Azure subscription and paste the same or a different **Key Identifier** from Azure Key Vault.
   - Click **Save** to apply the changes.

   This encrypts all data in the workspace storage account, including the DBFS root container, using your CMK.

2. **Verify Configuration**:
   - Confirm that the **DBFS Root** setting is enabled and the correct Key Identifier is displayed.

### Step 4: Configure CMK for Managed Disks

1. **Enable CMK for Managed Disks**:
   - In the **Encryption** settings page, check the box for **Managed Disks**.
   - Select your Azure subscription and paste the **Key Identifier**.
   - Click **Save** to apply the changes.

2. **Verify Configuration**:
   - Confirm that the **Managed Disks** setting is enabled.

### Step 5: Set Up Key Rotation Policies

1. **Configure Automatic Key Rotation**:
   - In the Azure Key Vault, navigate to the key created in Step 1.3.
   - Under **Rotation policy**, enable automatic rotation (e.g., every 90 days).
   - Set notifications to alert administrators before rotation occurs.
   - Click **Save** to apply the policy.

2. **Test Key Rotation**:
   - Manually rotate the key by generating a new version in Azure Key Vault.
   - Verify that Databricks continues to function without errors, as it should automatically use the new key version.

## Section 2: Encryption in Transit

Azure Databricks secures data in transit using TLS/SSL by default for communication between clients, the control plane, and the data plane. However, inter-node communication within clusters is not encrypted by default and requires additional configuration.

### Step 1: Enable TLS for External Connections

1. **Verify Default TLS Settings**:
   - All API calls and user queries to Databricks are transmitted over HTTPS with TLS, ensuring encryption in transit. No additional configuration is required for external connections.[](https://stackoverflow.com/questions/60672248/is-data-encrypted-during-in-transit-in-azure-data-factory-and-databricks-runtime)

2. **Enhance Security with Azure Private Link**:
   - To ensure private connectivity, configure **Azure Private Link** for your Databricks workspace:
     - In the Azure Portal, navigate to your Databricks workspace.
     - Under **Networking**, enable **Private Endpoint Connections**.
     - Create a private endpoint in your Virtual Network (VNet) to connect to Azure Databricks, ensuring traffic stays within your private network.[](https://sutejakanuri.medium.com/how-to-achieve-network-and-storage-isolation-in-azure-databricks-for-sensitive-data-19be9e901dbf)

### Step 2: Encrypt Inter-Node Communication

By default, data exchanged between worker nodes in a Databricks cluster is not encrypted. To enable encryption:

1. **Create an Init Script**:
   - Write an init script to configure AES 256-bit encryption over TLS 1.3 for inter-node communication. Below is an example script:

   ```bash
   #!/bin/bash
   # File: /dbfs/init_scripts/encrypt_inter_node.sh
   # Purpose: Enable AES 256-bit encryption over TLS 1.3 for inter-node communication

   # Generate a shared encryption secret
   KEYSTORE_PATH="/dbfs/keystore/encryption_keystore"
   SHARED_SECRET=$(openssl dgst -sha256 $KEYSTORE_PATH | awk '{print $2}')

   # Configure Spark to use TLS for inter-node communication
   echo "spark.network.crypto.enabled true" >> /databricks/spark/conf/spark-env.sh
   echo "spark.network.crypto.keyFactoryAlgorithm PBKDF2WithHmacSHA1" >> /databricks/spark/conf/spark-env.sh
   echo "spark.network.crypto.keyLength 256" >> /databricks/spark/conf/spark-env.sh
   echo "spark.network.crypto.key $SHARED_SECRET" >> /databricks/spark/conf/spark-env.sh
   echo "spark.network.crypto.saslEnabled true" >> /databricks/spark/conf/spark-env.sh
   ```

   - Upload the script to DBFS (e.g., `/dbfs/init_scripts/encrypt_inter_node.sh`).

2. **Configure Cluster to Use Init Script**:
   - In the Databricks workspace, navigate to **Compute** and select or create a cluster.
   - Under **Advanced Options**, go to the **Init Scripts** tab.
   - Add the path to the init script (e.g., `dbfs:/init_scripts/encrypt_inter_node.sh`).
   - Save and restart the cluster.

3. **Important Notes**:
   - If the shared secret (keystore) is updated, all running clusters must be restarted to avoid authentication failures.[](https://learn.microsoft.com/en-us/azure/databricks/security/keys/encrypt-otw)
   - Ensure that only authorized users have access to the DBFS path where the keystore is stored, as it contains the shared secret.

4. **Verify Inter-Node Encryption**:
   - Run a sample Spark job and monitor network traffic (using tools like Wireshark in a test environment) to confirm that data between worker nodes is encrypted.

### Step 3: Configure TLS for External Services

1. **Secure Connections to Azure Data Lake Storage (ADLS)**:
   - Ensure that ADLS connections use HTTPS, which is enabled by default.[](https://learn.microsoft.com/en-us/azure/security/fundamentals/encryption-overview)
   - Configure **Azure Private Link** for ADLS to keep traffic within your VNet.

2. **Manage TLS Certificates**:
   - In Azure Key Vault, store and manage TLS certificates for secure communication.
   - Configure Databricks to use these certificates for connections to external services (e.g., Azure SQL Database) by updating cluster configurations or notebook code.

## Section 3: Best Practices for Security and Compliance

1. **Key Management**:
   - Use a key hierarchy with a **Key Encryption Key (KEK)** in Azure Key Vault to encrypt **Data Encryption Keys (DEKs)** for better performance and security.[](https://learn.microsoft.com/en-us/security/benchmark/azure/baselines/azure-databricks-security-baseline)
   - Regularly rotate keys and audit key usage via Azure Key Vault logs.
   - Enable **soft delete** and **purge protection** in Azure Key Vault to recover deleted keys.[](https://learn.microsoft.com/en-us/azure/security/fundamentals/data-encryption-best-practices)

2. **Access Control**:
   - Use **Azure Role-Based Access Control (RBAC)** to restrict access to Key Vault and Databricks resources.
   - Implement **Unity Catalog** for fine-grained access control at the catalog, schema, and table levels.[](https://sutejakanuri.medium.com/how-to-achieve-network-and-storage-isolation-in-azure-databricks-for-sensitive-data-19be9e901dbf)
   - Store credentials in **Databricks Secrets** backed by Azure Key Vault instead of embedding them in notebooks.[](https://learn.microsoft.com/en-us/azure/databricks/security/)

3. **Network Security**:
   - Deploy Databricks workspaces in a **VNet** using VNet injection to ensure network isolation.[](https://sutejakanuri.medium.com/how-to-achieve-network-and-storage-isolation-in-azure-databricks-for-sensitive-data-19be9e901dbf)
   - Use **Network Security Groups (NSGs)** to restrict access to trusted IP addresses or VNets.
   - Enable **IP access lists** to secure access to the Databricks web application and REST API.[](https://learn.microsoft.com/en-us/security/benchmark/azure/baselines/azure-databricks-security-baseline)

4. **Monitoring and Auditing**:
   - Use the **Security Analysis Tool (SAT)** to analyze workspace security configurations and follow best practices.[](https://learn.microsoft.com/en-us/azure/databricks/security/)
   - Enable **Azure Databricks Diagnostic Logs** to monitor platform activity and detect anomalies.

5. **Compliance**:
   - Ensure configurations meet regulatory requirements (e.g., GDPR, HIPAA, PCI-DSS) by using CMKs and confidential computing.[](https://www.aciinfotech.com/blogs/maximizing-data-security-in-azure-databricks)
   - Leverage **Azure Confidential Computing (ACC)** to protect data in use during processing.[](https://community.databricks.com/t5/technical-blog/how-to-enhance-data-protection-in-azure-databricks-with/ba-p/66272)

## Section 4: Testing and Validation

1. **Test Encryption at Rest**:
   - Store a sample dataset in DBFS and run a SQL query in Databricks SQL.
   - Verify that the data and query history are encrypted by checking the Key Vault logs for key usage.

2. **Test Encryption in Transit**:
   - Run a Spark job that processes data across cluster nodes.
   - Confirm that inter-node traffic is encrypted by monitoring network packets (in a test environment).

3. **Simulate Key Rotation**:
   - Rotate the CMK in Azure Key Vault and verify that Databricks continues to access encrypted data without issues.

## Conclusion

By configuring customer-managed keys for encryption at rest and enabling TLS for encryption in transit, you can ensure that your Azure Databricks environment meets stringent security and compliance requirements. Regularly audit and update your configurations to maintain a secure data analytics platform.

## References

- Microsoft Learn: [Data security and encryption - Azure Databricks](https://learn.microsoft.com/en-us/azure/databricks/security/encryption)[](https://learn.microsoft.com/en-us/azure/databricks/security/keys/)
- Microsoft Learn: [Encrypt traffic between cluster worker nodes](https://learn.microsoft.com/en-us/azure/databricks/security/encryption/encrypt-worker-traffic)[](https://learn.microsoft.com/en-us/azure/databricks/security/keys/encrypt-otw)
- Microsoft Learn: [Azure encryption overview](https://learn.microsoft.com/en-us/azure/security/fundamentals/encryption-overview)[](https://learn.microsoft.com/en-us/azure/security/fundamentals/encryption-overview)
- Databricks Documentation: [Data security and encryption](https://docs.databricks.com/security/encryption.html)[](https://docs.databricks.com/aws/en/security/keys)
- Medium: [How to Achieve Network and Storage Isolation in Azure Databricks](https://sutejakanuri.medium.com/how-to-achieve-network-and-storage-isolation-in-azure-databricks-for-sensitive-data-1c0b0f1b0c2b)[](https://sutejakanuri.medium.com/how-to-achieve-network-and-storage-isolation-in-azure-databricks-for-sensitive-data-19be9e901dbf)