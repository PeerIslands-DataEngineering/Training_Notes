# **MySQL Index**  

## **1. Introduction to Indexes in MySQL**  
An **index** in MySQL is a **data structure** that improves the speed of data retrieval operations on a table. It is used to **quickly locate data** without scanning the entire table.  

Indexes work similarly to a book's **table of contents** or **index page**, allowing MySQL to find specific rows efficiently.

---

## **2. Why Use Indexes?**
### **Advantages of Indexes**
✅ **Faster SELECT queries** – Searching for rows is much quicker.  
✅ **Optimized WHERE conditions** – MySQL can filter data efficiently.  
✅ **Efficient JOIN operations** – Indexes speed up joins between tables.  
✅ **Speeds up ORDER BY and GROUP BY** – Sorting and grouping work better with indexed columns.  

### **Disadvantages of Indexes**
❌ **Slower INSERT, UPDATE, DELETE operations** – MySQL must update indexes along with table data.  
❌ **Consumes more disk space** – Large indexes require storage.  
❌ **Not useful for small tables** – Full table scans might be faster.  

---

## **3. Types of Indexes in MySQL**
### **1. Primary Index**
- Automatically created on the **PRIMARY KEY** column.  
- Unique and cannot contain NULL values.  
- Only one **PRIMARY KEY** index per table.  

**Example:**
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY, 
    name VARCHAR(100), 
    salary DECIMAL(10,2)
);
```

OR

```sql
ALTER TABLE employees ADD PRIMARY KEY (id);
```
---

### **2. Unique Index**
- Ensures values in the indexed column(s) are unique.  
- Allows **one NULL value** in MySQL.  
- Useful for enforcing constraints.  

**Example:**
```sql
CREATE UNIQUE INDEX idx_unique_email ON employees(email);
```

OR

```sql
ALTER TABLE employees ADD UNIQUE (email);
```
---

### **3. Composite Index (Multi-Column Index)**
- Created on **multiple columns** to speed up queries using those columns together.  
- **Order matters** in composite indexes.  
- Helps with queries that filter using **both columns** in the WHERE clause.  

**Example:**
```sql
CREATE INDEX idx_name_salary ON employees(name, salary);
```
This index will be useful for:
```sql
SELECT * FROM employees WHERE name = 'John' AND salary > 50000;
```
---

### **4. Full-Text Index**
- Used for **fast text searching** in large datasets.  
- Works with **CHAR, VARCHAR, and TEXT** columns.  
- Supports **MATCH() AGAINST()** for searching.  

**Example:**
```sql
CREATE TABLE articles (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255),
    content TEXT,
    FULLTEXT (title, content)
);
```
**Query using full-text search:**
```sql
SELECT * FROM articles WHERE MATCH(title, content) AGAINST('database');
```
---

### **5. Index on Foreign Keys**
- Automatically created when a **FOREIGN KEY** is added to a table.  
- Helps with **JOIN operations** for relational integrity.  

**Example:**
```sql
CREATE TABLE orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```
Here, MySQL creates an **index on `customer_id`** to speed up lookups.

---

### **6. Clustered vs. Non-Clustered Index**
- **Clustered Index** – The data is physically sorted in the order of the index. MySQL’s **PRIMARY KEY** is automatically a clustered index.  
- **Non-Clustered Index** – Index stores pointers to actual table rows, rather than sorting them physically.  

---

## **4. How to Check and Manage Indexes in MySQL**
### **View Indexes on a Table**
```sql
SHOW INDEXES FROM employees;
```
---

### **Remove an Index**
```sql
DROP INDEX idx_name_salary ON employees;
```

OR  

```sql
ALTER TABLE employees DROP INDEX idx_name_salary;
```
---

## **5. Best Practices for Indexing**
✅ **Use indexes on frequently searched columns** (WHERE, JOIN, ORDER BY).  
✅ **Prefer single-column indexes over multi-column unless necessary.**  
✅ **Avoid indexing columns with a low cardinality** (few unique values, like "gender").  
✅ **Index foreign keys** for faster joins.  
✅ **Regularly analyze and optimize indexes** (`ANALYZE TABLE employees;`).  

---

## **6. Example Queries and Index Performance**
### **Without Index (Slow Query)**
```sql
SELECT * FROM employees WHERE name = 'Alice';
```
- If `name` is not indexed, MySQL **scans all rows** (Full Table Scan).

### **With Index (Faster Query)**
```sql
CREATE INDEX idx_name ON employees(name);
SELECT * FROM employees WHERE name = 'Alice';
```
- Now MySQL **uses the index** to find "Alice" quickly.

---

## **7. How to Analyze Query Performance (EXPLAIN)**
To check if MySQL is using an index:
```sql
EXPLAIN SELECT * FROM employees WHERE name = 'Alice';
```
- Look for `Using index` or `ref` in the output.

---

## **8. Conclusion**
Indexes are powerful for optimizing MySQL queries but should be used wisely to avoid performance degradation on **INSERT, UPDATE, DELETE** operations.  
