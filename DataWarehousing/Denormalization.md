# **Denormalization **  

## **🔹 Introduction to Denormalization**  
**Denormalization** is the process of **reducing the complexity of database queries by adding redundant data** to optimize read performance. It involves **combining tables, precomputing aggregates, and storing derived data** to improve efficiency at the cost of some redundancy.  

🔹 **Normalization vs. Denormalization**  
| **Normalization** | **Denormalization** |
|-------------------|--------------------|
| Reduces redundancy by dividing data into multiple tables | Introduces redundancy to optimize query performance |
| Uses **JOINs** to retrieve data | Reduces the need for **JOINs** |
| Best for transactional systems (OLTP) | Best for analytical systems (OLAP, Data Warehouses) |
| Slower queries (due to JOINs) | Faster queries (due to precomputed data) |

---

## **🔹 When to Use Denormalization?**  
✅ **Read-heavy Applications** → If data retrieval speed is critical (e.g., reporting, dashboards).  
✅ **Big Data & Data Warehousing** → When handling large datasets in OLAP systems.  
✅ **Frequent JOIN Queries** → To avoid expensive JOIN operations.  
✅ **Precomputed Aggregations Needed** → To speed up analytics and reporting.  

🚫 **When NOT to Use Denormalization?**  
❌ **Write-heavy Applications** → Increases redundancy, making updates more complex.  
❌ **High Data Integrity Needs** → Increased risk of inconsistency.  

---

## **🔹 Techniques of Denormalization**
Denormalization can be implemented in multiple ways:

### **1️⃣ Merging Tables (Combining Related Entities)**
Instead of keeping data in multiple related tables, merge them into a single table to **avoid JOIN operations**.

📌 **Example: Normalized Database (Before Denormalization)**  
| **orders** (OrderID, CustomerID, OrderDate) | **customers** (CustomerID, Name, Email) |
|------------------------------------|-------------------------|
| 101, 1, 2024-03-20 | 1, John Doe, john@example.com |
| 102, 2, 2024-03-21 | 2, Jane Smith, jane@example.com |

📌 **Denormalized Table (After Merging)**  
| OrderID | CustomerID | Name | Email | OrderDate |
|---------|-----------|------|-------|------------|
| 101 | 1 | John Doe | john@example.com | 2024-03-20 |
| 102 | 2 | Jane Smith | jane@example.com | 2024-03-21 |

✔️ **Benefit** → Queries like `SELECT * FROM orders WHERE CustomerID = 1;` become faster.  
❌ **Drawback** → If a customer updates their email, multiple records must be updated.  

---

### **2️⃣ Storing Derived Data (Precomputed Aggregations)**
Instead of computing aggregates on-the-fly, store them in a table to speed up queries.

📌 **Example: Normalized Sales Data**  
| OrderID | CustomerID | OrderAmount |
|---------|-----------|-------------|
| 101 | 1 | 100 |
| 102 | 1 | 150 |
| 103 | 2 | 200 |

📌 **Denormalized Table with Precomputed Totals**  
| CustomerID | TotalSpent |
|-----------|------------|
| 1 | 250 |
| 2 | 200 |

✔️ **Benefit** → Faster queries like `SELECT TotalSpent FROM sales_summary WHERE CustomerID = 1;`  
❌ **Drawback** → Must update the total whenever new orders are added or changed.  

---

### **3️⃣ Using Redundant Data (Duplicating Columns for Faster Reads)**
Duplicate commonly used columns to avoid excessive JOINs.

📌 **Example: Normalized Tables (Before Denormalization)**
| **employees** (EmpID, Name, DeptID) | **departments** (DeptID, DeptName) |
|------------------------------------|------------------------------|
| 1, Alice, 10 | 10, HR |
| 2, Bob, 20 | 20, IT |

📌 **Denormalized Table with Redundant Department Name**  
| EmpID | Name | DeptID | DeptName |
|-------|------|--------|-----------|
| 1 | Alice | 10 | HR |
| 2 | Bob | 20 | IT |

✔️ **Benefit** → Querying employee departments is now faster (`SELECT Name, DeptName FROM employees;`).  
❌ **Drawback** → If `DeptName` changes, all records must be updated.  

---

### **4️⃣ Creating Read-Optimized Tables (Materialized Views)**
Instead of querying large tables, create a **read-optimized summary table**.

📌 **Example: Querying Sales by Region**  
```sql
SELECT region, SUM(sales) FROM sales_data GROUP BY region;
```
📌 **Denormalized Approach (Precomputed Table)**
| Region | TotalSales |
|--------|------------|
| North | 50000 |
| South | 70000 |

✔️ **Benefit** → Faster query performance.  
❌ **Drawback** → Must update periodically.  

---

### **5️⃣ Using JSON or Arrays for Complex Data Structures**
Instead of creating multiple tables for related data, store it as JSON/ARRAY.

📌 **Example: Orders with Multiple Items (Normalized Schema)**
| OrderID | ItemID | ItemName | Quantity |
|---------|--------|---------|---------|
| 101 | 1 | Laptop | 1 |
| 101 | 2 | Mouse | 2 |

📌 **Denormalized Table with JSON**
| OrderID | Items |
|---------|------------------------|
| 101 | `[{"ItemID": 1, "ItemName": "Laptop", "Quantity": 1}, {"ItemID": 2, "ItemName": "Mouse", "Quantity": 2}]` |

✔️ **Benefit** → Fewer joins, better query performance in NoSQL & BigQuery.  
❌ **Drawback** → Harder to manipulate using standard SQL.  

---

## **🔹 Denormalization in Data Warehousing**
Denormalization is widely used in **OLAP (Online Analytical Processing)** for reporting & dashboards.

📌 **Example: Star Schema for Data Warehousing**
Instead of a highly normalized schema, a **star schema** is used for faster analytics.

| **Fact Table (sales_fact)** | **Dimension Tables** |
|-----------------------------|---------------------|
| OrderID, ProductID, CustomerID, Amount | Products (ProductID, ProductName) |
| | Customers (CustomerID, Name, Region) |

✔️ **Fact Table** → Contains sales transactions.  
✔️ **Dimension Tables** → Contain descriptive data (Products, Customers).  

This structure **reduces JOIN complexity** and improves **query performance in BI tools like Tableau & Power BI**.  

---

## **🔹 Pros & Cons of Denormalization**
| **Pros** | **Cons** |
|---------|---------|
| Faster queries (fewer joins) | Increased redundancy |
| Simplifies query logic | Harder to maintain consistency |
| Optimized for read-heavy workloads | Larger storage requirements |
| Improves reporting & analytics performance | Updates are complex & expensive |

---

🚀 **Denormalization is a tradeoff** between **performance and redundancy**. It’s useful in **data warehouses, reporting systems, and read-heavy applications** but should be avoided in **high-write transactional databases**.  
