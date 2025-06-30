# **Google BigQuery: Introduction**  

## **1. Introduction to BigQuery**  
Google BigQuery is a **serverless, highly scalable, and cost-effective** cloud data warehouse designed for fast SQL-based analytics on large datasets. It is part of **Google Cloud Platform (GCP)** and is optimized for running analytical queries over petabytes of data.  

### **1.1 Key Features of BigQuery**  
✅ **Serverless**: No infrastructure management required.  
✅ **Scalable**: Handles petabytes of data with high performance.  
✅ **Columnar Storage**: Uses a **columnar format** for efficient querying.  
✅ **Built-in Machine Learning (BigQuery ML)**: Perform ML modeling directly in SQL.  
✅ **Standard SQL Support**: Uses familiar **SQL syntax**.  
✅ **Real-Time Streaming**: Supports **real-time data ingestion**.  
✅ **Seamless Integration**: Works with **Google Sheets, Looker, Data Studio, and more**.  

---

## **2. Getting Started with BigQuery**  
Before we create datasets and tables, let’s set up BigQuery in Google Cloud.

### **2.1 Prerequisites**  
🔹 **Google Cloud Account** – If you don’t have one, sign up at [Google Cloud Console](https://console.cloud.google.com/).  
🔹 **Enable BigQuery API** – In Google Cloud Console, navigate to **BigQuery** and enable the API if not already activated.  
🔹 **Billing Account** – BigQuery offers a **free tier (1TB of free queries per month)**, but for larger queries, you need a billing account.  

### **2.2 Accessing BigQuery**  
You can interact with BigQuery using:  
1. **Google Cloud Console** – Web-based UI for running SQL queries.  
2. **bq Command-Line Tool** – CLI for managing datasets, tables, and queries.  
3. **BigQuery REST API** – Programmatic access via Python, Java, etc.  
4. **Google Cloud SDK** – For automating BigQuery tasks.  

---

## **3. Creating a Dataset in BigQuery**  
A **dataset** is a top-level container in BigQuery, which holds tables and views.

### **3.1 Creating a Dataset via Google Cloud Console**
1. Go to [BigQuery Console](https://console.cloud.google.com/bigquery).
2. In the **Explorer Panel**, click on your **Project**.
3. Click on **Create Dataset**.
4. Enter the following details:  
   - **Dataset ID**: Unique name for your dataset (e.g., `sales_data`).  
   - **Data Location**: Select a region (e.g., `US` or `EU`).  
   - **Default Table Expiration**: (Optional) Set how long tables should be retained.  
   - **Encryption**: (Optional) Choose **Google-managed key** or **Customer-managed key**.  
5. Click **Create Dataset**.

---

## **4. Creating a Table in BigQuery**  
A **table** stores data in **rows and columns**, similar to relational databases.

### **4.1 Creating a Table via Cloud Console**
1. In BigQuery **Explorer**, select your **dataset** (`sales_data`).
2. Click **Create Table**.
3. Choose the **source** of your table data:
   - **Upload**: Upload a CSV, JSON, Avro, Parquet, or ORC file.
   - **Google Cloud Storage**: Load data from GCS (`gs://bucket-name/file.csv`).
   - **Google Sheets**: Connect a Google Sheet.
   - **BigQuery Table**: Create from another existing table.
4. Set **Table ID** (e.g., `customer_orders`).
5. Select the **Schema** (define columns manually or auto-detect if using a file):
   - Example Schema:
     | Column Name | Data Type | Mode |
     |------------|----------|------|
     | order_id   | STRING   | REQUIRED |
     | customer_name | STRING | NULLABLE |
     | order_date | DATE | NULLABLE |
     | amount     | FLOAT   | NULLABLE |
6. Click **Create Table**.

---

## **5. Loading Data into a BigQuery Table**  
Once the table is created, you can insert data using various methods.

### **5.1 Uploading CSV Data (Cloud Console)**
1. Go to **BigQuery Console**.
2. Select your **dataset** (`sales_data`).
3. Click on the table **customer_orders**.
4. Go to the **Details** tab and click **Upload Data**.
5. Choose a **CSV file**, select schema mode (**auto-detect or manual**).
6. Click **Start Upload**.

### **5.2 Loading Data via SQL Query**
```sql
INSERT INTO `your_project.sales_data.customer_orders`
(order_id, customer_name, order_date, amount)
VALUES
('ORD001', 'John Doe', '2025-03-20', 150.75),
('ORD002', 'Jane Smith', '2025-03-18', 200.50);
```

### **5.3 Loading Data from Google Cloud Storage**
```sql
LOAD DATA INTO `your_project.sales_data.customer_orders`
FROM FILES (
  format = 'CSV',
  uris = ['gs://your-bucket/orders.csv']
);
```

---

## **6. Querying Data in BigQuery**  
Once the data is loaded, you can **run SQL queries** to analyze it.

### **6.1 Basic Query (Retrieve All Records)**
```sql
SELECT * FROM `your_project.sales_data.customer_orders` LIMIT 10;
```

### **6.2 Filtering Data**
```sql
SELECT * FROM `your_project.sales_data.customer_orders`
WHERE amount > 100;
```

### **6.3 Aggregating Data**
```sql
SELECT order_date, SUM(amount) AS total_sales
FROM `your_project.sales_data.customer_orders`
GROUP BY order_date
ORDER BY order_date DESC;
```

### **6.4 Joining Tables**
```sql
SELECT o.order_id, o.customer_name, p.product_name, p.price
FROM `your_project.sales_data.customer_orders` AS o
JOIN `your_project.sales_data.products` AS p
ON o.order_id = p.order_id;
```

---

## **7. Exporting Data from BigQuery**
You can export data for further analysis.

### **7.1 Export to Google Cloud Storage (CSV)**
```sql
EXPORT DATA OPTIONS (
  uri = 'gs://your-bucket/customer_orders_*.csv',
  format = 'CSV',
  overwrite = true
) AS
SELECT * FROM `your_project.sales_data.customer_orders`;
```

### **7.2 Export to Google Sheets**
1. Open **BigQuery Console**.
2. Run a **SELECT query**.
3. Click **Save Results → Google Sheets**.

---

## **8. Automating BigQuery Tasks**
### **8.1 Using the bq Command-Line Tool**
```bash
bq query --use_legacy_sql=false 'SELECT COUNT(*) FROM `your_project.sales_data.customer_orders`'
```

### **8.2 Using Python with BigQuery API**
```python
from google.cloud import bigquery

client = bigquery.Client()
query = "SELECT * FROM `your_project.sales_data.customer_orders` LIMIT 10"
query_job = client.query(query)

for row in query_job:
    print(row)
```

---

## **9. Best Practices for Using BigQuery**
✔️ **Use Partitioning and Clustering** – Optimize queries by partitioning large tables.  
✔️ **Avoid SELECT *** – Query only the required columns to reduce costs.  
✔️ **Use Streaming for Real-Time Data** – Instead of batch inserts.  
✔️ **Optimize Joins** – Filter data before performing joins to reduce processing time.  
✔️ **Monitor Costs** – Use **BigQuery Cost Estimator** before running large queries.  

---

## **10. Conclusion**
Google BigQuery is a **powerful, serverless data warehouse** that enables fast analytics over large datasets. This tutorial covered **creating datasets, tables, loading data, querying, and best practices**. 
