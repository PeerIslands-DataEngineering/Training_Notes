## **Data Definition Language (DDL) in MySQL**  

**DDL (Data Definition Language)** consists of SQL commands that define, modify, and manage database structures like tables, schemas, indexes, and constraints. DDL operations do not manipulate data but rather define the database schema.  

---

## **1. Key DDL Commands**  

### **1.1 CREATE** – Used to create databases, tables, indexes, and views.  
### **1.2 ALTER** – Used to modify existing database structures.  
### **1.3 DROP** – Used to delete databases, tables, or other objects.  
### **1.4 TRUNCATE** – Used to remove all records from a table without removing its structure.  
### **1.5 RENAME** – Used to rename database objects like tables.  

---

## **2. MySQL DDL Commands in Detail**  

### **2.1 CREATE Statements**  

#### **Creating a Database**  
```sql
CREATE DATABASE CompanyDB;
```
- Creates a new database named `CompanyDB`.  
- Use `SHOW DATABASES;` to verify its creation.  

#### **Creating a Table**  
```sql
USE CompanyDB;  -- Select the database

CREATE TABLE Employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    age INT CHECK (age > 18),
    department VARCHAR(50) DEFAULT 'General'
);
```
- Defines an `Employees` table with constraints:  
  - `PRIMARY KEY` ensures `emp_id` is unique.  
  - `NOT NULL` ensures `name` cannot be empty.  
  - `UNIQUE` ensures `email` is unique.  
  - `CHECK` ensures `age` is greater than 18.  
  - `DEFAULT` assigns a default value to `department`.  

#### **Creating an Index**  
```sql
CREATE INDEX idx_email ON Employees(email);
```
- Creates an index on the `email` column to improve search performance.  

---

### **2.2 ALTER Statements**  

#### **Adding a New Column**  
```sql
ALTER TABLE Employees ADD COLUMN salary DECIMAL(10,2);
```
- Adds a new column `salary` to the `Employees` table.  

#### **Modifying a Column**  
```sql
ALTER TABLE Employees MODIFY COLUMN age TINYINT UNSIGNED;
```
- Modifies the `age` column to `TINYINT UNSIGNED`, which does not allow negative values.  

#### **Renaming a Column**  
```sql
ALTER TABLE Employees CHANGE COLUMN name full_name VARCHAR(100);
```
- Renames the `name` column to `full_name` with an updated data type.  

#### **Dropping a Column**  
```sql
ALTER TABLE Employees DROP COLUMN salary;
```
- Removes the `salary` column from the `Employees` table.  

---

### **2.3 DROP Statements**  

#### **Dropping a Table**  
```sql
DROP TABLE Employees;
```
- Deletes the `Employees` table and all its data.  

#### **Dropping a Database**  
```sql
DROP DATABASE CompanyDB;
```
- Deletes the `CompanyDB` database along with all its tables.  

---

### **2.4 TRUNCATE Statement**  

```sql
TRUNCATE TABLE Employees;
```
- Removes all rows from the `Employees` table but keeps the table structure intact.  
- Faster than `DELETE` since it does not log individual row deletions.  

---

### **2.5 RENAME Statement**  

```sql
RENAME TABLE Employees TO Staff;
```
- Renames the `Employees` table to `Staff`.  

---

## **3. Summary of MySQL DDL Commands**  

| **Command**  | **Description** |
|-------------|----------------|
| `CREATE` | Creates a database, table, or index. |
| `ALTER` | Modifies an existing table (add, modify, delete columns). |
| `DROP` | Deletes a database, table, or column permanently. |
| `TRUNCATE` | Removes all data from a table but retains structure. |
| `RENAME` | Renames a table. |

---
