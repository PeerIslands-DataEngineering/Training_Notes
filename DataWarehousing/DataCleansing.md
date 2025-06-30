# **Data Cleansing in Data Warehousing**  

## **🔹 Introduction to Data Cleansing**  
Data cleansing (also called **data scrubbing or data cleaning**) is the process of **identifying, correcting, or removing inaccurate, incomplete, duplicate, or inconsistent data** in a data warehouse.  

Since **data warehouses** integrate data from multiple sources (ERP, CRM, IoT devices, etc.), raw data often contains **errors, missing values, duplicates, and inconsistencies**. Data cleansing ensures **high-quality, reliable, and accurate data** for business intelligence (BI) and analytics.  

---

## **🔹 Why is Data Cleansing Important?**
🚀 **Improves data accuracy** → More reliable business decisions.  
🚀 **Eliminates duplicates & inconsistencies** → Reduces storage & processing costs.  
🚀 **Enhances query performance** → Faster insights & reporting.  
🚀 **Ensures compliance** → Avoids regulatory risks (GDPR, HIPAA).  

---

## **🔹 Steps in Data Cleansing Process**
Data cleansing involves **six key steps** to improve data quality before it enters the data warehouse.

### **1️⃣ Data Auditing & Profiling** (Identify Issues)  
Before cleaning data, it's important to **analyze its quality** using **data profiling** techniques.  
✅ **Detect errors**: Missing values, duplicates, incorrect formats.  
✅ **Understand data patterns**: Identify outliers & inconsistencies.  
✅ **Tools**: Apache Griffin, Talend Data Quality, Informatica Data Quality.  

📌 **Example (SQL Query to Check Missing Values)**  
```sql
SELECT column_name, COUNT(*) 
FROM data_table 
WHERE column_name IS NULL 
GROUP BY column_name;
```

---

### **2️⃣ Handling Missing Data**
Missing values can occur due to **data entry errors, system failures, or incomplete records**. There are different ways to handle them:  

| **Strategy** | **Description** | **Use Case** |
|-------------|--------------|------------|
| **Remove Rows** | Delete records with too many missing fields | If <5% data is missing |
| **Fill with Default Values** | Replace NULLs with default values (e.g., ‘Unknown’, 0) | Categorical data (e.g., Gender, Country) |
| **Use Statistical Imputation** | Fill missing values with mean, median, or mode | Numerical data (e.g., Sales, Temperature) |
| **Use Previous/Next Values** | Fill missing time-series data with previous or next values | IoT sensor readings, financial data |

📌 **Example (Filling NULL Values in SQL)**  
```sql
UPDATE customers 
SET country = 'Unknown' 
WHERE country IS NULL;
```

📌 **Example (Pandas in Python - Filling Missing Values with Mean)**  
```python
import pandas as pd
df = pd.read_csv("data.csv")
df["salary"].fillna(df["salary"].mean(), inplace=True)
```

---

### **3️⃣ Removing Duplicates**
Duplicate records can **waste storage and distort analysis**. They must be identified and removed.  

📌 **Example (Finding Duplicate Records in SQL)**  
```sql
SELECT customer_id, COUNT(*) 
FROM customers 
GROUP BY customer_id 
HAVING COUNT(*) > 1;
```

📌 **Example (Removing Duplicates in Pandas - Python)**  
```python
df = df.drop_duplicates(subset=['customer_id'], keep='first')
```

---

### **4️⃣ Standardizing Data Formats**
Data from different sources may have **inconsistent formats** (e.g., different date formats, inconsistent case in text fields). Standardization ensures uniformity.  

#### **Common Fixes:**
✅ Convert dates to a single format (**YYYY-MM-DD**).  
✅ Standardize text case (**Uppercase/Lowercase**).  
✅ Normalize phone numbers (**Remove dashes, spaces**).  

📌 **Example (Standardizing Date Formats in SQL)**  
```sql
UPDATE orders 
SET order_date = STR_TO_DATE(order_date, '%d/%m/%Y');
```

📌 **Example (Python – Standardizing Text Case in Pandas)**  
```python
df["name"] = df["name"].str.title()  # Converts to Proper Case
```

---

### **5️⃣ Correcting Data Inconsistencies**
Inconsistencies occur when different values represent the **same entity**.  

| **Inconsistent Data** | **Corrected Data** |
|----------------------|------------------|
| **NY, N.Y., New York** | **New York** |
| **male, Male, M** | **Male** |
| **US, U.S.A, United States** | **United States** |

📌 **Example (Fixing Inconsistencies in SQL)**  
```sql
UPDATE customers 
SET country = 'United States' 
WHERE country IN ('US', 'U.S.A');
```

📌 **Example (Replacing Values in Pandas - Python)**  
```python
df["gender"] = df["gender"].replace({"M": "Male", "male": "Male"})
```

---

### **6️⃣ Validating & Verifying Data**
After cleaning data, it must be validated to ensure **integrity and correctness**.  
✅ **Check Data Integrity** → Foreign key constraints must match.  
✅ **Cross-Check Business Rules** → Ensure logical accuracy.  

📌 **Example (SQL Query for Data Validation - Checking Email Format)**  
```sql
SELECT email 
FROM users 
WHERE email NOT LIKE '%@%.%';
```

📌 **Example (Python - Regex to Validate Email Format)**  
```python
import re
def is_valid_email(email):
    return re.match(r"[^@]+@[^@]+\.[^@]+", email) is not None
```

---

## **🔹 Automating Data Cleansing with ETL Tools**
To handle large datasets, **ETL (Extract, Transform, Load) tools** automate the data cleansing process.  

| **ETL Tool** | **Features** |
|-------------|------------|
| **Talend** | Open-source, data quality rules, profiling |
| **Informatica Data Quality** | Advanced data validation, cleansing workflows |
| **Apache NiFi** | Real-time data transformation |
| **AWS Glue** | Cloud-native ETL automation |

📌 **Example: Talend Data Cleansing Steps**
1. **Extract data** from various sources (CSV, DB, API).  
2. **Transform data** (handle missing values, remove duplicates).  
3. **Load into Data Warehouse** (AWS Redshift, Snowflake, BigQuery).  

---

## **🔹 Best Practices for Data Cleansing**
✔️ **Regular Data Audits** – Monitor data quality proactively.  
✔️ **Automate Cleansing** – Use ETL pipelines for large-scale cleansing.  
✔️ **Define Data Quality Metrics** – Set benchmarks for accuracy, completeness.  
✔️ **Use Machine Learning for Anomaly Detection** – Identify patterns of bad data.  

---

## **🔹 Final Thoughts**
Data cleansing is **essential** in data warehousing to ensure **high-quality, reliable, and accurate data**. Using **ETL tools, SQL scripts, and Python automation**, organizations can **clean, standardize, and validate** data efficiently.  
