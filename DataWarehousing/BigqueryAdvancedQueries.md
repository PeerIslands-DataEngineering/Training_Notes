# **BigQuery Advanced Queries**  

Google **BigQuery** is a **serverless, highly scalable, and cost-effective cloud data warehouse** that allows users to run **SQL-like queries on massive datasets**. This guide covers **advanced query techniques** for efficient data processing, analysis, and optimization.

---

## **1️⃣ Advanced Query Features in BigQuery**
BigQuery supports **advanced SQL features**, including:  
✅ **Window Functions** → Running totals, rankings, moving averages.  
✅ **ARRAYs & STRUCTs** → Handling semi-structured data.  
✅ **Partitions & Clustering** → Performance optimization.  
✅ **WITH (CTE) Queries** → Breaking down complex queries.  
✅ **Nested Queries & Subqueries** → Advanced aggregations.  
✅ **Geospatial Analysis** → Using GEOGRAPHY data types.  
✅ **Machine Learning Queries** → Using BigQuery ML.  

---

## **2️⃣ Using Common Table Expressions (CTEs)**
CTEs (**WITH clauses**) help break down **complex queries into smaller, manageable parts**.

📌 **Example: Using CTEs to Find Total Sales per Customer**  
```sql
WITH customer_sales AS (
    SELECT customer_id, SUM(order_amount) AS total_spent
    FROM orders
    GROUP BY customer_id
)
SELECT customer_id, total_spent
FROM customer_sales
WHERE total_spent > 1000; -- Filtering high-spending customers
```
✔️ **Easier to read & debug**  
✔️ **Improves query performance by avoiding repeated subqueries**  

---

## **3️⃣ Window Functions (Analytic Functions)**
BigQuery supports **window functions**, which allow computations **over a set of rows related to the current row**.

📌 **Example: Running Total of Sales by Date**
```sql
SELECT order_date, customer_id, order_amount,
       SUM(order_amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS running_total
FROM orders;
```
✔️ **SUM() OVER()** computes a **cumulative total** for each customer.  
✔️ **PARTITION BY** groups data by customer.  

📌 **Example: Ranking Customers by Total Purchases**
```sql
SELECT customer_id, SUM(order_amount) AS total_spent,
       RANK() OVER (ORDER BY SUM(order_amount) DESC) AS rank
FROM orders
GROUP BY customer_id;
```
✔️ **RANK() OVER()** assigns a ranking based on total spending.  
✔️ Useful for **leaderboards, analytics, and trend analysis**.  

---

## **4️⃣ Working with Arrays & Structs in BigQuery**
BigQuery **natively supports** semi-structured data with **ARRAYs and STRUCTs**.

📌 **Example: Creating and Querying an ARRAY**
```sql
SELECT [10, 20, 30, 40] AS my_array;
```
📌 **Example: Unnesting an ARRAY (Expanding into Rows)**
```sql
WITH data AS (
  SELECT 1 AS id, ['apple', 'banana', 'cherry'] AS fruits
)
SELECT id, fruit
FROM data, UNNEST(fruits) AS fruit;
```
✔️ **UNNEST()** converts an array into individual rows.  

📌 **Example: Using STRUCTs**
```sql
SELECT STRUCT('John Doe' AS name, 30 AS age) AS person;
```
✔️ **STRUCTs** allow storing multiple fields in a single column.  

---

## **5️⃣ Partitioning & Clustering for Query Optimization**
BigQuery offers **partitioning and clustering** to improve query performance and reduce costs.

📌 **Creating a Partitioned Table (By Date)**
```sql
CREATE OR REPLACE TABLE sales_partitioned
PARTITION BY DATE(transaction_date) AS
SELECT * FROM raw_sales;
```
✔️ **Partitioning** divides data into **smaller segments** based on a column (e.g., date).  
✔️ **Queries scan only relevant partitions**, reducing cost.  

📌 **Creating a Clustered Table (By Category)**
```sql
CREATE OR REPLACE TABLE sales_clustered
CLUSTER BY product_category AS
SELECT * FROM raw_sales;
```
✔️ **Clustering** organizes similar data **together in storage**, speeding up queries.  

📌 **Querying a Partitioned Table Efficiently**
```sql
SELECT * FROM sales_partitioned
WHERE transaction_date BETWEEN '2024-01-01' AND '2024-01-31';
```
✔️ Avoids **scanning the entire dataset**.  

---

## **6️⃣ Nested Queries & Subqueries**
📌 **Example: Finding Customers with Purchases Above the Average**
```sql
SELECT customer_id, SUM(order_amount) AS total_spent
FROM orders
GROUP BY customer_id
HAVING total_spent > (SELECT AVG(order_amount) FROM orders);
```
✔️ **HAVING** filters based on a subquery result.  

📌 **Example: Subquery in FROM Clause**
```sql
SELECT customer_id, avg_spent
FROM (
    SELECT customer_id, AVG(order_amount) AS avg_spent
    FROM orders
    GROUP BY customer_id
) AS avg_orders
WHERE avg_spent > 500;
```
✔️ **Useful when a derived table is needed for filtering**.  

---

## **7️⃣ Geospatial Queries in BigQuery**
BigQuery supports **GEOGRAPHY** data types for location-based queries.

📌 **Example: Finding Distance Between Two Points**
```sql
SELECT ST_DISTANCE(
    ST_GEOGPOINT(-122.4194, 37.7749), -- San Francisco
    ST_GEOGPOINT(-118.2437, 34.0522)  -- Los Angeles
) AS distance_meters;
```
✔️ **ST_DISTANCE()** calculates distance between two locations.  

📌 **Example: Finding Points Within a 10km Radius**
```sql
SELECT name, location
FROM stores
WHERE ST_DWITHIN(location, ST_GEOGPOINT(-122.4194, 37.7749), 10000);
```
✔️ **ST_DWITHIN()** finds nearby locations within 10,000 meters (10 km).  

---

## **8️⃣ Using BigQuery ML for Machine Learning**
BigQuery allows running **machine learning models directly inside SQL**.

📌 **Example: Training a Linear Regression Model**
```sql
CREATE OR REPLACE MODEL my_dataset.sales_forecast
OPTIONS (model_type='linear_reg') AS
SELECT 
    total_spent, 
    num_purchases, 
    future_sales
FROM sales_data;
```
✔️ **BigQuery ML** allows **training ML models without moving data**.  

📌 **Example: Making Predictions**
```sql
SELECT * FROM ML.PREDICT(MODEL my_dataset.sales_forecast,  
    (SELECT total_spent, num_purchases FROM new_sales_data));
```
✔️ **ML.PREDICT()** generates predictions based on trained models.  

---

## **9️⃣ Performance Optimization Tips**
✅ **Use SELECT Instead of SELECT \*** → Avoid scanning unnecessary columns.  
✅ **Leverage Partitioning & Clustering** → Reduces scan cost.  
✅ **Filter Data Early (Use WHERE & LIMIT)** → Avoid scanning the full table.  
✅ **Use APPROXIMATE Aggregations** → Faster, less expensive.  
```sql
SELECT APPROX_COUNT_DISTINCT(customer_id) FROM orders;
```
✅ **Use Materialized Views** → Precompute frequently queried data.  

---

## **🔟 Final Thoughts**
BigQuery is a powerful **serverless data warehouse** designed for **fast, large-scale data processing**. By leveraging **advanced SQL queries, optimizations, and ML capabilities**, you can **analyze massive datasets efficiently**.
