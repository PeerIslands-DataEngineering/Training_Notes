

# 🛡️ Azure Compliance and Audit Trails – Detailed Tutorial

---

## ✅ 1. **What Is Azure Compliance?**

Azure compliance ensures that your cloud resources meet **legal, regulatory, and internal governance standards** such as:

* **GDPR, ISO 27001, HIPAA, SOC 2, PCI-DSS**, etc.
* Regional data residency laws
* Internal company policies (naming conventions, tag usage, RBAC policies)

🔍 **Why It Matters:**

* Reduces risk of data breaches
* Proves alignment with legal standards
* Enables secure cloud governance

---

## 🧭 2. **Tools for Compliance and Auditing in Azure**

| Tool                                     | Purpose                                                    |
| ---------------------------------------- | ---------------------------------------------------------- |
| **Microsoft Purview Compliance Manager** | Governance and compliance reports                          |
| **Azure Policy**                         | Enforce rules (e.g., allowed VM SKUs, region restrictions) |
| **Azure Activity Logs**                  | Who did what and when (audit trail)                        |
| **Azure Monitor & Log Analytics**        | Operational and security logs                              |
| **Microsoft Defender for Cloud**         | Compliance posture monitoring                              |
| **Azure Blueprints**                     | Deploy and audit environment governance                    |
| **Azure Resource Graph**                 | Query resources for compliance analysis                    |
| **Azure Lighthouse**                     | Cross-tenant compliance for MSSPs                          |

---

## 📜 3. **Microsoft Purview Compliance Manager**

### 🧩 Purpose:

Tracks compliance with over **300+ regulations** and provides **compliance scores**.

### 🔧 How to Use:

1. Go to [Microsoft Purview Compliance Portal](https://compliance.microsoft.com)
2. Navigate to **Compliance Manager**
3. Choose an assessment (e.g., GDPR, NIST, ISO)
4. Review:

   * **Control Implementation Status**
   * **Improvement actions**
   * **Assigned users**

> 📝 Tracks controls across Microsoft 365, Azure, and Power Platform.

---

## 🛑 4. **Azure Policy – Enforcing Governance**

### 🧩 Purpose:

Prevent misconfigurations **before they happen** by applying rules to subscriptions or resource groups.

### 📘 Example Scenarios:

* Only deploy in Southeast Asia region
* Only allow Standard\_B-series VMs
* Ensure all resources have a “costCenter” tag

### 🛠 Example Policy Definition (Allowed Locations):

```json
{
  "if": {
    "not": {
      "field": "location",
      "in": ["southeastasia"]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### ✅ Steps:

1. Go to **Azure Policy**
2. Assign built-in or custom policy to a scope (subscription/RG)
3. Evaluate compliance
4. Auto-remediate non-compliant resources (for some policies)

---

## 📊 5. **Azure Activity Logs – Audit Trail for Control Plane**

### 🔍 Captures:

* Who did what at the **Azure Resource Manager (ARM)** level
* Operations like create, update, delete

### 📘 Example Log Entry:

* `Delete Virtual Machine` by user `arun@revature.com` on `2025-07-22T10:30:00Z`

### 📍 Where to Find:

* Azure Portal > Monitor > Activity Log
* Export to Log Analytics for advanced queries
* Set alerts for sensitive actions (e.g., delete, modify identity)

### 🔎 Sample Kusto Query:

```kusto
AzureActivity
| where OperationNameValue == "Microsoft.Compute/virtualMachines/delete"
| project TimeGenerated, Caller, ResourceGroup, ActivityStatusValue
```

---

## 📄 6. **Azure Diagnostic Settings + Monitor Logs**

Enable diagnostic logs for **compute, storage, networking, Key Vault**, etc.

### ✅ Use Cases:

* Log Key Vault access
* Track RDP/SSH attempts
* Monitor NSG rule hits

### 🔧 Steps:

1. Go to resource > **Diagnostic Settings**
2. Send logs to:

   * Log Analytics
   * Storage account (for archival)
   * Event Hub (for external SIEM)

### 💡 Example: Key Vault Logging

```bash
az monitor diagnostic-settings create \
  --name "kv-audit" \
  --resource "/subscriptions/.../resourceGroups/.../providers/Microsoft.KeyVault/vaults/mykv" \
  --logs '[{"category": "AuditEvent", "enabled": true}]' \
  --workspace "/subscriptions/.../resourceGroups/.../providers/Microsoft.OperationalInsights/workspaces/loganalytics"
```

---

## 🛡️ 7. **Microsoft Defender for Cloud – Compliance & Security Posture**

### ✅ Features:

* Regulatory compliance dashboard
* Recommendations for fixing security & compliance gaps
* Coverage: Azure + hybrid (on-prem + AWS + GCP)

### 📘 Built-in Frameworks:

* Azure CIS 1.3.0 Benchmark
* NIST SP 800-53
* PCI DSS 3.2.1
* ISO 27001

### 📍 Steps:

1. Enable Defender for Cloud
2. Go to **Regulatory Compliance Blade**
3. Track passed/failed controls and remediate

---

## 🧵 8. **Azure Blueprints – Governance-as-Code**

### ✅ Purpose:

Deploy reusable, compliant environments at scale.

### 👇 Example:

Define and deploy a blueprint that includes:

* ARM templates for infrastructure
* RBAC assignments
* Azure Policies
* Resource locks

### Use Case:

Ensure every new project has:

* Diagnostics enabled
* Naming convention enforced
* Only specific SKUs allowed

---

## 🔍 9. **Audit Identity and Access**

### 👤 Tools:

* **Azure AD Sign-In Logs**: Tracks all login attempts
* **Conditional Access Logs**
* **PIM (Privileged Identity Management)**: Tracks elevated roles

### 🔎 Kusto Query (AAD sign-in log):

```kusto
SigninLogs
| where ResultType != 0
| project UserPrincipalName, IPAddress, Status, AppDisplayName
```

---

## 🗂️ 10. **Export and Retention of Audit Logs**

### 📦 Export Logs To:

* Azure Storage (long-term retention)
* Event Hub (streaming to SIEMs)
* Log Analytics (search and dashboarding)

### ✅ Best Practice:

* Retain logs for **at least 1 year** (for compliance)
* Set up alerts for unauthorized access attempts or unusual activity

---

## ✅ Summary Table

| Area                  | Service                       | Purpose                                |
| --------------------- | ----------------------------- | -------------------------------------- |
| Governance            | Azure Policy                  | Enforce compliance proactively         |
| Monitoring            | Azure Monitor & Log Analytics | Deep visibility into resource behavior |
| Auditing              | Azure Activity Logs           | Audit trail of operations              |
| Security Posture      | Defender for Cloud            | Compliance dashboards + security fixes |
| Compliance Management | Purview Compliance Manager    | Track regulatory compliance status     |
| Identity Logs         | Azure AD                      | Sign-in and access logs                |
| Deployment Governance | Azure Blueprints              | Reusable compliant environments        |

---

## 📘 Example Scenario

**Goal**: Enforce and audit GDPR compliance across Azure environment.

### 🔧 Steps:

1. Use **Purview Compliance Manager** to assess GDPR controls
2. Define **Azure Policies** for:

   * Disallowed regions
   * Required tagging
   * Approved SKUs only
3. Enable **Defender for Cloud** and activate GDPR compliance view
4. Stream **Activity Logs + AAD Logs** to **Log Analytics**
5. Use **Alerts** for:

   * Role assignments
   * Resource deletion
   * Key Vault access

---
