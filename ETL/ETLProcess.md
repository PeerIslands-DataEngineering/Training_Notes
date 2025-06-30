# **Building a BigQuery ETL Process – Overview**  

## **1. What is BigQuery ETL?**  
ETL (**Extract, Transform, Load**) in **Google BigQuery** refers to the process of:  
- **Extracting** data from various sources (databases, APIs, files, streaming data).  
- **Transforming** data to clean, enrich, and structure it.  
- **Loading** the transformed data into **BigQuery tables** for analytics.

---

## **2. BigQuery ETL Architecture**  

### **1️⃣ Data Sources (Extract)**
- **Batch Sources**: CSV, JSON, Avro, Parquet, ORC files from Google Cloud Storage (GCS), relational databases (MySQL, PostgreSQL, SQL Server).  
- **Streaming Sources**: Kafka, Pub/Sub, real-time API feeds.  
- **Cloud Services**: Firebase, Google Analytics, Google Sheets, Ads data.  

### **2️⃣ Data Ingestion (Load)**
- **Batch Loading**:  
  - Load structured data from **GCS to BigQuery** using **bq commands** or **scheduled queries**.  
  - Use **Dataflow, Dataproc, or Apache Beam** for complex batch ETL.  
- **Streaming Loading**:  
  - Use **Google Pub/Sub** to stream data in real time.  
  - Process with **Dataflow (Apache Beam)** and push to BigQuery.  

### **3️⃣ Data Transformation (Transform)**
- Perform transformations inside BigQuery using **SQL**, **DBT**, or **Dataflow**:  
  - **Cleaning**: Remove duplicates, fix data types, handle NULL values.  
  - **Joining**: Merge multiple tables using **JOINs**.  
  - **Aggregations**: Perform **SUM, AVG, COUNT, GROUP BY** for analytics.  
  - **Partitioning & Clustering**: Optimize data storage.  

### **4️⃣ Data Storage (Load into BigQuery)**
- Store data in **raw tables** first.  
- Process data and load into **processed tables**.  
- Create **aggregated tables** for reporting.  

### **5️⃣ Data Access & Reporting**
- Use **BigQuery BI Engine** for fast dashboards.  
- Connect to **Looker Studio, Power BI, Tableau** for visualization.  
- Build **scheduled queries** to update tables periodically.  

---

## **3. Setting Up a BigQuery ETL Process**
Here’s a **step-by-step guide** to setting up an ETL pipeline in **BigQuery**.

### **Step 1: Create a BigQuery Dataset**
BigQuery organizes data into **datasets and tables**.

```sql
CREATE SCHEMA my_project.my_dataset;
```

---

### **Step 2: Load Raw Data into BigQuery**
- **Loading CSV from Google Cloud Storage (GCS)**
```sql
LOAD DATA INTO my_project.my_dataset.raw_table
FROM FILES (
  format = 'CSV',
  uris = ['gs://my-bucket/raw-data.csv']
);
```
- **Loading JSON Data**
```sql
LOAD DATA INTO my_project.my_dataset.raw_json_table
FROM FILES (
  format = 'JSON',
  uris = ['gs://my-bucket/raw-data.json']
);
```

---

### **Step 3: Transform Data Using SQL**
#### **Cleaning Data (Removing Nulls, Fixing Dates)**
```sql
CREATE OR REPLACE TABLE my_project.my_dataset.cleaned_table AS
SELECT 
  id,
  name,
  COALESCE(email, 'unknown@example.com') AS email,
  PARSE_DATE('%Y-%m-%d', event_date) AS formatted_date
FROM my_project.my_dataset.raw_table;
```

#### **Joining Data from Multiple Tables**
```sql
CREATE OR REPLACE TABLE my_project.my_dataset.final_table AS
SELECT 
  a.id, 
  a.name, 
  b.purchase_amount
FROM my_project.my_dataset.customers a
JOIN my_project.my_dataset.transactions b
ON a.id = b.customer_id;
```

#### **Creating an Aggregated Table**
```sql
CREATE OR REPLACE TABLE my_project.my_dataset.sales_summary AS
SELECT 
  category, 
  SUM(sales) AS total_sales, 
  COUNT(*) AS order_count
FROM my_project.my_dataset.orders
GROUP BY category;
```

---

### **Step 4: Automate ETL with Scheduled Queries**
BigQuery allows scheduling queries to run automatically.

```sql
CREATE SCHEDULED QUERY my_project.my_dataset.daily_sales_update
OPTIONS (schedule="every 24 hours") 
AS
SELECT 
  category, 
  SUM(sales) AS total_sales
FROM my_project.my_dataset.orders
GROUP BY category;
```

---

### **Step 5: Optimize Data Storage in BigQuery**
1. **Partitioning Data** (Improve Query Performance)
```sql
CREATE OR REPLACE TABLE my_project.my_dataset.sales_partitioned
PARTITION BY DATE(transaction_date) AS
SELECT * FROM my_project.my_dataset.sales;
```
2. **Clustering Data** (Faster Lookups)
```sql
CREATE OR REPLACE TABLE my_project.my_dataset.sales_clustered
CLUSTER BY customer_id AS
SELECT * FROM my_project.my_dataset.sales;
```

---

### **Step 6: Real-Time Streaming into BigQuery**
- **Stream data using Pub/Sub + Dataflow**
- **Enable real-time BigQuery inserts**
```python
from google.cloud import bigquery

client = bigquery.Client()
table_id = "my_project.my_dataset.real_time_table"

rows_to_insert = [
    {"id": 1, "name": "Alice", "timestamp": "2025-03-20 12:00:00"},
]

errors = client.insert_rows_json(table_id, rows_to_insert)
if errors:
    print("Errors:", errors)
else:
    print("Successfully inserted")
```

---

## **4. BigQuery ETL Best Practices**
✅ **Use Partitioning & Clustering** for large datasets to improve performance.  
✅ **Avoid SELECT *** (explicitly select only required columns).  
✅ **Use Scheduled Queries** for automating periodic transformations.  
✅ **Use Streaming Inserts for real-time data processing** instead of batch loads.  
✅ **Monitor Query Costs** using BigQuery **Query Execution Plans**.  
✅ **Store Aggregated Data** instead of raw data to reduce query complexity.  

---

## **5. Example End-to-End BigQuery ETL Pipeline**
### **Use Case: Process E-commerce Sales Data**
🔹 **Source**: JSON files in Google Cloud Storage (GCS).  
🔹 **ETL Steps**:  
  1. Extract data from GCS → Load into BigQuery (raw table).  
  2. Transform data (clean dates, remove duplicates, join with customer data).  
  3. Load processed data into final table.  
  4. Automate with scheduled queries.  
  5. Use **Looker Studio/Tableau** for visualization.  

### **Code Implementation**
#### **Step 1: Load Raw Data**
```sql
LOAD DATA INTO my_project.my_dataset.raw_sales
FROM FILES (
  format = 'JSON',
  uris = ['gs://ecommerce-bucket/sales-data/*.json']
);
```

#### **Step 2: Transform Data**
```sql
CREATE OR REPLACE TABLE my_project.my_dataset.cleaned_sales AS
SELECT 
  order_id, 
  customer_id, 
  product_id, 
  SAFE_CAST(order_date AS DATE) AS order_date,
  SAFE_CAST(price AS FLOAT64) AS price
FROM my_project.my_dataset.raw_sales
WHERE order_date IS NOT NULL;
```

#### **Step 3: Create Aggregated Sales Report**
```sql
CREATE OR REPLACE TABLE my_project.my_dataset.sales_summary AS
SELECT 
  product_id, 
  SUM(price) AS total_revenue,
  COUNT(order_id) AS total_orders
FROM my_project.my_dataset.cleaned_sales
GROUP BY product_id;
```

#### **Step 4: Automate with Scheduled Queries**
```sql
CREATE SCHEDULED QUERY my_project.my_dataset.daily_sales_update
OPTIONS (schedule="every 24 hours") 
AS
SELECT 
  product_id, 
  SUM(price) AS total_revenue
FROM my_project.my_dataset.cleaned_sales
GROUP BY product_id;
```

---

## **Conclusion**
- **BigQuery is highly scalable** for ETL processing.  
- Use **batch or streaming ingestion** based on real-time needs.  
- **Partition & Cluster** tables for performance optimization.  
- **Automate transformations** using scheduled queries.  
