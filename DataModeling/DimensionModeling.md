# **Dimensional Modeling**  

## **1. Introduction to Dimensional Modeling**  
Dimensional Modeling is a **data modeling technique used for designing data warehouses and analytical systems**. It focuses on making data easy to understand and retrieve for reporting and analysis, primarily using **facts and dimensions**.  

### **1.1 Why Use Dimensional Modeling?**  
✅ **Optimized for Query Performance** – Queries run faster due to simplified table structures.  
✅ **Easy for Business Users** – Data is structured in an intuitive way.  
✅ **Reduces Complexity** – Uses a **denormalized** approach for better performance.  
✅ **Supports Aggregation and Drill-downs** – Allows analysis at different levels.  
✅ **Widely Used in Business Intelligence (BI) and OLAP Systems** – Essential for reporting and dashboards.  

### **1.2 Key Components of Dimensional Modeling**  
- **Fact Table** – Stores measurable business data (e.g., sales, revenue).  
- **Dimension Table** – Stores descriptive attributes (e.g., customer details, product details).  
- **Schema Types** – Star Schema, Snowflake Schema, and Galaxy Schema.  

---

## **2. Fact and Dimension Tables**  

### **2.1 Fact Table**  
📌 A Fact Table contains **measurable, quantitative data** related to business processes. It usually consists of:  
- **Foreign Keys (FKs) to Dimension Tables**  
- **Facts (numeric values)** that can be aggregated (SUM, AVG, COUNT).  

🔹 **Example: Sales Fact Table**  
| **DateKey** | **CustomerKey** | **ProductKey** | **SalesAmount** | **QuantitySold** |
|------------|---------------|---------------|----------------|----------------|
| 20250301 | 101 | 201 | 500.00 | 2 |
| 20250302 | 102 | 202 | 300.00 | 1 |

✅ **DateKey, CustomerKey, ProductKey** → Foreign Keys to Dimension Tables  
✅ **SalesAmount, QuantitySold** → Measures (Facts)  

### **2.2 Dimension Table**  
📌 A Dimension Table stores **descriptive attributes** that provide context to facts.  

🔹 **Example: Customer Dimension Table**  
| **CustomerKey (PK)** | **CustomerName** | **City** | **Region** | **Segment** |
|------------------|----------------|---------|---------|----------|
| 101 | John Doe | New York | East | Retail |
| 102 | Jane Smith | San Francisco | West | Corporate |

🔹 **Example: Product Dimension Table**  
| **ProductKey (PK)** | **ProductName** | **Category** | **Brand** |
|------------------|-------------|----------|------|
| 201 | Laptop | Electronics | Dell |
| 202 | Mobile | Electronics | Apple |

✅ **Dimension tables store descriptive data that helps analyze facts.**  

---

## **3. Schema Types in Dimensional Modeling**  

### **3.1 Star Schema**  
**Structure:**  
- A **single fact table** is linked to **multiple dimension tables**.  
- Dimension tables **are denormalized** (flattened structure).  
- **Best for performance** (fast queries).  

📌 **Example: Star Schema for Sales Analysis**  
```
              +------------------+
              |  Date Dimension  |
              +------------------+
                      |
                      |
 +-----------------+    +---------------+   +------------------+
 | Customer Dim   |----| Sales Fact Table |----| Product Dim   |
 +-----------------+    +---------------+   +------------------+
```
✅ **Advantages of Star Schema**:  
✔ **Fast query performance** (fewer joins).  
✔ **Easier to understand** for business users.  
✔ **Simpler to maintain and scale.**  

🚀 **Example SQL Query (Total Sales by Customer)**  
```sql
SELECT c.CustomerName, SUM(f.SalesAmount) AS TotalSales
FROM SalesFact f
JOIN CustomerDim c ON f.CustomerKey = c.CustomerKey
GROUP BY c.CustomerName;
```

---

### **3.2 Snowflake Schema**  
**Structure:**  
- A **normalized version** of the star schema.  
- Some dimension tables are split into **sub-dimensions** to remove redundancy.  
- **More storage-efficient but slower performance** due to additional joins.  

📌 **Example: Snowflake Schema for Sales Analysis**  
```
             +------------------+
             |  Date Dimension  |
             +------------------+
                     |
                     |
+-----------------+   +---------------+   +------------------+
| Customer Dim   |---| Sales Fact Table |---| Product Dim   |
+-----------------+   +---------------+   +------------------+
                     |
                     |
              +------------------+
              | Category Dim     |
              +------------------+
```
✅ **Advantages of Snowflake Schema**:  
✔ **Reduces data redundancy** (normalized dimensions).  
✔ **Uses less storage space**.  
❌ **More complex queries** due to additional joins.  

🚀 **Example SQL Query (Total Sales by Product Category)**  
```sql
SELECT cat.CategoryName, SUM(f.SalesAmount) AS TotalSales
FROM SalesFact f
JOIN ProductDim p ON f.ProductKey = p.ProductKey
JOIN CategoryDim cat ON p.CategoryKey = cat.CategoryKey
GROUP BY cat.CategoryName;
```

---

### **3.3 Galaxy Schema (Fact Constellation Schema)**  
- **Multiple fact tables share common dimension tables**.  
- Used for complex analytical applications.  

📌 **Example: Galaxy Schema with Multiple Fact Tables**  
```
 +-----------------+   +---------------+   +------------------+
 | Customer Dim   |---| Sales Fact Table |---| Product Dim   |
 +-----------------+   +---------------+   +------------------+
                     |
                     |
             +------------------+
             | Inventory Fact Table |
             +------------------+
```
✅ **Advantages of Galaxy Schema**:  
✔ **Supports multiple business processes** (e.g., sales and inventory).  
✔ **More flexible for analytical queries**.  
❌ **More complex maintenance** due to multiple fact tables.  

---

## **4. Fact Table Types**  
### **4.1 Transactional Fact Table**  
- Stores **event-level data** (e.g., each sale, each purchase).  
- Example: `Sales Fact Table`.  

### **4.2 Periodic Snapshot Fact Table**  
- Stores data at **regular intervals** (daily, weekly, monthly).  
- Example: **Daily Sales Summary**.  

### **4.3 Accumulating Snapshot Fact Table**  
- Stores **lifecycle data** (tracks processes over time).  
- Example: **Order Processing** (Order Received → Shipped → Delivered).  

---

## **5. Best Practices for Dimensional Modeling**  
✔ **Use Surrogate Keys** – Instead of natural keys for better data integrity.  
✔ **Optimize Queries with Indexing** – Index fact table foreign keys.  
✔ **Use Aggregate Tables** – For faster reporting on high-volume data.  
✔ **Maintain Slowly Changing Dimensions (SCD)** – Track historical changes in dimension data.  
✔ **Choose Schema Based on Needs** – Use **Star Schema** for performance, **Snowflake Schema** for normalization.  

---

## **6. Conclusion**  
Dimensional modeling is a crucial technique for **data warehouses** and **business intelligence** applications. The **Star Schema** is commonly used for its **query performance** and **simplicity**, while the **Snowflake Schema** offers **space efficiency**. The **Galaxy Schema** supports **multiple fact tables**.  
