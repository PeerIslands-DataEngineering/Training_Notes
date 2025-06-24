## **Data Query Language (DQL) in MySQL**  

**DQL (Data Query Language)** is a subset of SQL used to query the database and retrieve data. The primary DQL command is `SELECT`, which is used to retrieve data from one or more tables in a database.

---

## **1. Key DQL Command**  

### **1.1 SELECT** – Used to retrieve records from a table or multiple tables.  

---

## **2. MySQL DQL Command in Detail**  

### **2.1 Basic SELECT Statement**  

The `SELECT` statement is the most common DQL command. It retrieves data from a table.  

#### **Example: Retrieving All Columns from a Table**
```sql
SELECT * FROM Employees;
```
- The `*` symbol represents all columns in the `Employees` table.  
- This query will return all records and columns from the `Employees` table.  

---

### **2.2 SELECT with Specific Columns**  

If you only need specific columns, you can specify them in the `SELECT` statement.  

#### **Example: Retrieving Specific Columns**
```sql
SELECT full_name, email FROM Employees;
```
- This retrieves only the `full_name` and `email` columns from the `Employees` table.  

---

### **2.3 SELECT with WHERE Clause**  

The `WHERE` clause allows you to filter records based on specified conditions.  

#### **Example: Filtering with WHERE**
```sql
SELECT * FROM Employees 
WHERE department = 'IT';
```
- This retrieves all employees in the `IT` department.  

#### **Example: Using Multiple Conditions in WHERE**
```sql
SELECT * FROM Employees 
WHERE department = 'IT' AND age > 30;
```
- This retrieves employees in the `IT` department who are older than 30 years.  

#### **Example: Using OR in WHERE**
```sql
SELECT * FROM Employees 
WHERE department = 'HR' OR department = 'IT';
```
- This retrieves employees who are either in the `HR` or `IT` department.  

#### **Example: Using LIKE for Pattern Matching**
```sql
SELECT * FROM Employees 
WHERE full_name LIKE 'John%';
```
- This retrieves all employees whose `full_name` starts with 'John'.  
- `%` is a wildcard representing any sequence of characters.  

---

### **2.4 SELECT with ORDER BY**  

The `ORDER BY` clause is used to sort the result set by one or more columns.  

#### **Example: Sorting in Ascending Order**
```sql
SELECT * FROM Employees 
ORDER BY age ASC;
```
- This retrieves all employees and sorts them by `age` in ascending order (default).  

#### **Example: Sorting in Descending Order**
```sql
SELECT * FROM Employees 
ORDER BY age DESC;
```
- This sorts the employees by `age` in descending order.  

#### **Example: Sorting by Multiple Columns**
```sql
SELECT * FROM Employees 
ORDER BY department ASC, age DESC;
```
- This retrieves employees sorted first by `department` in ascending order and then by `age` in descending order within each department.  

---

### **2.5 SELECT with LIMIT**  

The `LIMIT` clause is used to restrict the number of rows returned by a query.  

#### **Example: Limiting the Number of Results**
```sql
SELECT * FROM Employees 
LIMIT 5;
```
- This retrieves only the first 5 records from the `Employees` table.  

#### **Example: Limiting with OFFSET**
```sql
SELECT * FROM Employees 
LIMIT 5 OFFSET 5;
```
- This retrieves 5 records starting from the 6th record (skips the first 5).  

---

### **2.6 SELECT with DISTINCT**  

The `DISTINCT` keyword is used to remove duplicate records from the result set.  

#### **Example: Retrieving Unique Values**
```sql
SELECT DISTINCT department FROM Employees;
```
- This retrieves all unique departments from the `Employees` table.  

---

### **2.7 SELECT with Aggregate Functions**  

Aggregate functions perform calculations on multiple rows of data and return a single result. Common aggregate functions are `COUNT`, `SUM`, `AVG`, `MIN`, and `MAX`.  

#### **Example: COUNT**
```sql
SELECT COUNT(*) FROM Employees;
```
- This counts the total number of employees in the `Employees` table.  

#### **Example: SUM**
```sql
SELECT SUM(salary) FROM Employees;
```
- This calculates the total sum of salaries for all employees.  

#### **Example: AVG**
```sql
SELECT AVG(age) FROM Employees;
```
- This calculates the average age of all employees.  

#### **Example: MIN and MAX**
```sql
SELECT MIN(age), MAX(age) FROM Employees;
```
- This returns the minimum and maximum ages of employees.  

---

### **2.8 GROUP BY Clause**  

The `GROUP BY` clause is used to group rows that have the same values in specified columns. It is often used with aggregate functions to perform calculations on each group.  

#### **Example: Grouping by Department**
```sql
SELECT department, COUNT(*) 
FROM Employees 
GROUP BY department;
```
- This counts the number of employees in each department.  

#### **Example: Grouping with Aggregate Function**
```sql
SELECT department, AVG(age) 
FROM Employees 
GROUP BY department;
```
- This calculates the average age of employees in each department.  

---

### **2.9 HAVING Clause**  

The `HAVING` clause is used to filter groups created by the `GROUP BY` clause. It is similar to `WHERE` but operates on grouped records.  

#### **Example: Filtering Groups with HAVING**
```sql
SELECT department, AVG(age) 
FROM Employees 
GROUP BY department 
HAVING AVG(age) > 30;
```
- This retrieves departments where the average age of employees is greater than 30.  

---

### **2.10 JOINs**  

A **JOIN** is used to combine rows from two or more tables based on a related column between them.  

#### **Example: INNER JOIN**
```sql
SELECT Employees.full_name, Departments.department_name
FROM Employees
INNER JOIN Departments ON Employees.department = Departments.department_id;
```
- This retrieves employee names and their corresponding department names by joining `Employees` with `Departments` on the matching `department` and `department_id` columns.  

#### **Example: LEFT JOIN**
```sql
SELECT Employees.full_name, Departments.department_name
FROM Employees
LEFT JOIN Departments ON Employees.department = Departments.department_id;
```
- This retrieves all employees and their departments, even if some employees do not have a matching department.  

---

## **3. Summary of MySQL DQL Commands**  

| **Command**         | **Description** |
|---------------------|-----------------|
| `SELECT`            | Retrieves data from one or more tables. |
| `WHERE`             | Filters rows based on conditions. |
| `ORDER BY`          | Sorts the result set by one or more columns. |
| `LIMIT`             | Restricts the number of rows returned. |
| `DISTINCT`          | Removes duplicate rows from the result. |
| `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` | Aggregate functions to perform calculations. |
| `GROUP BY`          | Groups rows based on one or more columns. |
| `HAVING`            | Filters groups created by `GROUP BY`. |
| `JOIN`              | Combines rows from two or more tables based on related columns. |

---
