# **Google BigQuery Queries**  

Google **BigQuery** is a fully managed, serverless, and highly scalable cloud data warehouse designed for fast SQL-based analytics on large datasets. It supports **standard SQL**, and its architecture allows processing terabytes or even petabytes of data efficiently.

---

## **1. BigQuery Basics**
BigQuery uses **Google Standard SQL**, which is similar to traditional SQL but optimized for massive data analytics.

### **Basic Query Syntax**
```sql
SELECT column_name
FROM dataset_name.table_name
WHERE condition
ORDER BY column_name
LIMIT number;
```

---

## **2. Querying Data in BigQuery**
### **a. Selecting Data**
#### *Example: Fetching all columns from a table*
```sql
SELECT * FROM `project_id.dataset_name.table_name`
LIMIT 10;
```
- `project_id` → Google Cloud Project where the dataset exists.
- `dataset_name` → Logical grouping of tables.
- `table_name` → Name of the table.

#### *Example: Selecting specific columns*
```sql
SELECT user_id, user_name, email
FROM `my_project.my_dataset.users`
WHERE country = 'USA'
ORDER BY created_at DESC
LIMIT 100;
```

### **b. Filtering Data**
Filters use `WHERE` conditions to retrieve specific data.

#### *Example: Filtering by conditions*
```sql
SELECT name, age, country
FROM `my_project.my_dataset.customers`
WHERE age > 30
AND country = 'India';
```

#### *Example: Filtering with `IN` clause*
```sql
SELECT name, salary
FROM `my_project.hr_data.employees`
WHERE department IN ('Engineering', 'Marketing');
```

#### *Example: Filtering with `BETWEEN` clause*
```sql
SELECT order_id, total_amount
FROM `my_project.sales_data.orders`
WHERE total_amount BETWEEN 500 AND 1000;
```

#### *Example: Filtering with `LIKE` (Pattern Matching)*
```sql
SELECT product_name
FROM `my_project.store.products`
WHERE product_name LIKE 'Samsung%';  -- Finds names starting with "Samsung"
```

---

## **3. Aggregation Queries**
BigQuery supports powerful aggregation functions like `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`.

#### *Example: Counting rows*
```sql
SELECT COUNT(*) AS total_customers
FROM `my_project.my_dataset.customers`;
```

#### *Example: Summing values*
```sql
SELECT SUM(total_amount) AS total_sales
FROM `my_project.sales_data.orders`
WHERE status = 'Completed';
```

#### *Example: Grouping data*
```sql
SELECT country, COUNT(user_id) AS total_users
FROM `my_project.my_dataset.users`
GROUP BY country
ORDER BY total_users DESC;
```

#### *Example: Average value calculation*
```sql
SELECT department, AVG(salary) AS avg_salary
FROM `my_project.hr_data.employees`
GROUP BY department;
```

---

## **4. Using Joins in BigQuery**
BigQuery supports different types of joins for combining tables.

### **a. INNER JOIN (Only Matching Rows)**
```sql
SELECT o.order_id, c.name, o.total_amount
FROM `my_project.sales_data.orders` o
JOIN `my_project.sales_data.customers` c
ON o.customer_id = c.customer_id;
```

### **b. LEFT JOIN (All Rows from Left Table + Matches from Right Table)**
```sql
SELECT c.name, o.order_id, o.total_amount
FROM `my_project.sales_data.customers` c
LEFT JOIN `my_project.sales_data.orders` o
ON c.customer_id = o.customer_id;
```

### **c. RIGHT JOIN (All Rows from Right Table + Matches from Left Table)**
```sql
SELECT c.name, o.order_id, o.total_amount
FROM `my_project.sales_data.customers` c
RIGHT JOIN `my_project.sales_data.orders` o
ON c.customer_id = o.customer_id;
```

### **d. FULL OUTER JOIN (All Rows from Both Tables)**
```sql
SELECT c.name, o.order_id, o.total_amount
FROM `my_project.sales_data.customers` c
FULL JOIN `my_project.sales_data.orders` o
ON c.customer_id = o.customer_id;
```

---

## **5. Working with Dates and Times**
BigQuery provides several functions for handling **date and timestamp values**.

### **a. Getting the Current Date & Time**
```sql
SELECT CURRENT_DATE() AS today_date, CURRENT_TIMESTAMP() AS now;
```

### **b. Extracting Date Parts**
```sql
SELECT 
  order_id, 
  EXTRACT(YEAR FROM order_date) AS order_year,
  EXTRACT(MONTH FROM order_date) AS order_month
FROM `my_project.sales_data.orders`;
```

### **c. Date Difference Calculation**
```sql
SELECT 
  user_id, 
  registration_date, 
  CURRENT_DATE() AS today,
  DATE_DIFF(CURRENT_DATE(), registration_date, DAY) AS days_since_signup
FROM `my_project.my_dataset.users`;
```

---

## **6. Window Functions**
Window functions allow calculations across a subset of rows related to the current row.

### **a. Ranking Data with `ROW_NUMBER()`**
```sql
SELECT 
  user_id, 
  name, 
  country,
  ROW_NUMBER() OVER (PARTITION BY country ORDER BY created_at DESC) AS rank
FROM `my_project.my_dataset.users`;
```

### **b. Running Total with `SUM()`**
```sql
SELECT 
  order_id, 
  customer_id, 
  total_amount, 
  SUM(total_amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS running_total
FROM `my_project.sales_data.orders`;
```

---

## **7. Using Nested & Repeated Data in BigQuery**
BigQuery supports **nested and repeated fields**, which are useful for handling JSON-like structures.

### **a. Querying Nested Data**
```sql
SELECT 
  user_id, 
  info.name, 
  info.email
FROM `my_project.my_dataset.users`
```
- Here, `info` is a **nested field** inside the `users` table.

### **b. Flattening Arrays Using `UNNEST()`**
```sql
SELECT 
  user_id, 
  event_name
FROM `my_project.analytics.events`,
UNNEST(event_list) AS event_name;
```
- `event_list` is an **array column** in the table.

---

## **8. Query Optimization in BigQuery**
### **a. Partitioning and Clustering**
BigQuery allows **partitioned** and **clustered tables** for performance optimization.

#### *Example: Using a Partitioned Table*
```sql
SELECT * 
FROM `my_project.sales_data.orders`
WHERE order_date >= '2024-01-01'; -- Optimized due to partitioning
```

#### *Example: Using a Clustered Table*
```sql
SELECT * 
FROM `my_project.sales_data.orders`
WHERE customer_id = '12345'; -- Optimized due to clustering on customer_id
```

### **b. Avoid `SELECT *` in Large Tables**
Instead of querying all columns, **select only the required columns**.

✅ **Better approach:**
```sql
SELECT order_id, total_amount FROM `my_project.sales_data.orders`;
```

🚫 **Avoid this (wastes resources):**
```sql
SELECT * FROM `my_project.sales_data.orders`;
```

### **c. Use `LIMIT` for Testing Queries**
Before running queries on large datasets, use `LIMIT` to preview results.

```sql
SELECT * FROM `my_project.sales_data.orders` LIMIT 10;
```

---

## **Conclusion**
BigQuery provides a powerful SQL-based interface for querying large datasets efficiently. By understanding **joins, aggregations, window functions, date handling, nested data, and optimizations**, you can maximize performance and cost efficiency.
