# **BigQuery Connection for ETL using Python**  


## **1. Introduction to BigQuery & ETL**  
### **1.1 What is Google BigQuery?**  
Google BigQuery is a **serverless, highly scalable, and cost-effective data warehouse** provided by Google Cloud Platform (GCP). It is optimized for **running SQL queries on large datasets** efficiently.  

### **1.2 What is ETL in BigQuery?**  
ETL (Extract, Transform, Load) is the process of:  
✅ **Extracting** data from sources (CSV, databases, APIs, etc.).  
✅ **Transforming** data (cleaning, filtering, aggregating).  
✅ **Loading** data into BigQuery for analysis.  

### **1.3 Why Use Python for BigQuery ETL?**  
- **Automate ETL workflows** using Python scripts.  
- **Handle large datasets** efficiently.  
- **Integrate with cloud services** like Google Cloud Storage (GCS), Dataflow, and AI/ML tools.  

---

## **2. Setup BigQuery and Python Environment**  
### **2.1 Prerequisites**  
✅ **Google Cloud Project** – Create one in [Google Cloud Console](https://console.cloud.google.com/).  
✅ **Enable BigQuery API** – Go to "APIs & Services" and enable **BigQuery API**.  
✅ **Service Account & Key** –  
   1. Navigate to **IAM & Admin → Service Accounts**.  
   2. Create a new service account and grant the role **BigQuery Admin**.  
   3. Download the JSON key file.  
✅ **Install Required Python Packages**  
```bash
pip install google-cloud-bigquery pandas
```

---

## **3. Connect to BigQuery in Python**  
### **3.1 Import Required Libraries**  
```python
from google.cloud import bigquery
import pandas as pd
import os
```

### **3.2 Authenticate with BigQuery**  
```python
# Set Google Cloud Credentials (Replace with your downloaded JSON key file)
os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "path/to/your-service-account.json"

# Initialize BigQuery Client
client = bigquery.Client()
```
✅ **This establishes a secure connection with BigQuery**.

---

## **4. Extract Data from a CSV File**  
### **4.1 Read CSV into Pandas DataFrame**  
Assume we have a CSV file (`sales_data.csv`) with **sales transactions**.
```python
df = pd.read_csv("sales_data.csv")
print(df.head())  # Display first few rows
```
✅ **Example Sales Data**:  
| OrderID | CustomerName | Product | Price | Quantity | OrderDate  |
|---------|-------------|---------|-------|----------|------------|
| 1001    | John Doe    | Laptop  | 800   | 1        | 2025-03-15 |
| 1002    | Jane Smith  | Phone   | 500   | 2        | 2025-03-16 |

---

## **5. Transform Data**  
### **5.1 Clean & Prepare Data**  
Let’s ensure the data types are correct before loading into BigQuery.  
```python
# Convert OrderDate to Date Format
df["OrderDate"] = pd.to_datetime(df["OrderDate"])

# Ensure Price and Quantity are integers
df["Price"] = df["Price"].astype(float)
df["Quantity"] = df["Quantity"].astype(int)

# Print Data Types
print(df.dtypes)
```
✅ **This ensures correct data types for BigQuery schema compatibility**.

---

## **6. Load Data into BigQuery Table**  
### **6.1 Define BigQuery Dataset & Table**  
```python
project_id = "your-gcp-project-id"
dataset_id = "sales_dataset"
table_id = "sales_data"
table_ref = f"{project_id}.{dataset_id}.{table_id}"
```

### **6.2 Define Schema for BigQuery Table**  
```python
schema = [
    bigquery.SchemaField("OrderID", "INTEGER"),
    bigquery.SchemaField("CustomerName", "STRING"),
    bigquery.SchemaField("Product", "STRING"),
    bigquery.SchemaField("Price", "FLOAT"),
    bigquery.SchemaField("Quantity", "INTEGER"),
    bigquery.SchemaField("OrderDate", "DATE"),
]
```

### **6.3 Create the BigQuery Table (If Not Exists)**  
```python
dataset_ref = client.dataset(dataset_id)
table_ref = dataset_ref.table(table_id)

# Check if Table Exists
try:
    client.get_table(table_ref)
    print("Table already exists.")
except Exception:
    table = bigquery.Table(table_ref, schema=schema)
    client.create_table(table)
    print("Table created successfully.")
```

---

## **7. Insert Data into BigQuery**  
### **7.1 Load Pandas DataFrame into BigQuery**  
```python
job = client.load_table_from_dataframe(df, table_ref, job_config=bigquery.LoadJobConfig(write_disposition="WRITE_APPEND"))

job.result()  # Wait for job to complete
print("Data loaded successfully into BigQuery!")
```
✅ **The data is now available in BigQuery for analysis!**  

---

## **8. Query Data from BigQuery using Python**  
You can run SQL queries on BigQuery data using Python.  

```python
query = f"""
    SELECT CustomerName, SUM(Price * Quantity) AS TotalSales
    FROM `{project_id}.{dataset_id}.{table_id}`
    GROUP BY CustomerName
    ORDER BY TotalSales DESC
"""

query_job = client.query(query)  # Execute Query
df_results = query_job.to_dataframe()  # Convert to Pandas DataFrame

print(df_results)
```
✅ **This will return total sales per customer**.

---

## **9. Automate the ETL Process**  
To automate ETL, you can:  
🔹 **Use Google Cloud Functions** to run the script periodically.  
🔹 **Schedule with Apache Airflow (Cloud Composer in GCP)**.  
🔹 **Use Cloud Scheduler** to trigger the script.  

---

## **10. Full ETL Code**  
```python
from google.cloud import bigquery
import pandas as pd
import os

# Set Credentials
os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "path/to/your-service-account.json"

# Initialize BigQuery Client
client = bigquery.Client()

# Read Data
df = pd.read_csv("sales_data.csv")

# Transform Data
df["OrderDate"] = pd.to_datetime(df["OrderDate"])
df["Price"] = df["Price"].astype(float)
df["Quantity"] = df["Quantity"].astype(int)

# Define BigQuery Table
project_id = "your-gcp-project-id"
dataset_id = "sales_dataset"
table_id = "sales_data"
table_ref = f"{project_id}.{dataset_id}.{table_id}"

# Define Schema
schema = [
    bigquery.SchemaField("OrderID", "INTEGER"),
    bigquery.SchemaField("CustomerName", "STRING"),
    bigquery.SchemaField("Product", "STRING"),
    bigquery.SchemaField("Price", "FLOAT"),
    bigquery.SchemaField("Quantity", "INTEGER"),
    bigquery.SchemaField("OrderDate", "DATE"),
]

# Check if Table Exists
dataset_ref = client.dataset(dataset_id)
table_ref = dataset_ref.table(table_id)
try:
    client.get_table(table_ref)
except Exception:
    table = bigquery.Table(table_ref, schema=schema)
    client.create_table(table)

# Load Data into BigQuery
job = client.load_table_from_dataframe(df, table_ref, job_config=bigquery.LoadJobConfig(write_disposition="WRITE_APPEND"))
job.result()
print("Data loaded successfully!")

# Query Data
query = f"""
    SELECT CustomerName, SUM(Price * Quantity) AS TotalSales
    FROM `{project_id}.{dataset_id}.{table_id}`
    GROUP BY CustomerName
    ORDER BY TotalSales DESC
"""
query_job = client.query(query)
df_results = query_job.to_dataframe()
print(df_results)
```

---

## **11. Conclusion**  
🎯 **What we learned:**  
✅ Set up **BigQuery API** and **authenticate using Python**.  
✅ **Extract** data from CSV using **Pandas**.  
✅ **Transform** data (data cleaning, type conversion).  
✅ **Load** data into BigQuery using **Python and BigQuery API**.  
✅ **Query BigQuery tables** from Python.  
