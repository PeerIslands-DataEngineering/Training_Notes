## Introduction to SQL  

### **1. What is SQL?**  
SQL (Structured Query Language) is a standard language used to interact with relational databases. It is used for querying, updating, inserting, and deleting data in databases, as well as for database structure management.  

---

### **2. Relational Database Management System (RDBMS)**  
An RDBMS is software that allows users to create, manage, and interact with relational databases. Examples include MySQL, PostgreSQL, SQL Server, and Oracle. Key characteristics of an RDBMS:  

- **Data is stored in tables** consisting of rows and columns.  
- **Enforces relationships** between tables using keys (Primary Key, Foreign Key).  
- **Supports ACID properties** (Atomicity, Consistency, Isolation, Durability) for data integrity.  

---

### **3. Table Structure in RDBMS**  
A **table** in SQL consists of:  

| Column Name | Data Type | Constraints | Example Value |
|------------|----------|------------|---------------|
| `id`       | INT      | PRIMARY KEY | 1             |
| `name`     | VARCHAR(50) | NOT NULL | 'John Doe'    |
| `email`    | VARCHAR(100) | UNIQUE | 'john@example.com' |
| `age`      | INT      | CHECK (age > 0) | 25            |

- **Columns** define attributes (fields) of data.  
- **Rows (records)** store actual data.  
- **Constraints** ensure data integrity (e.g., `NOT NULL`, `UNIQUE`, `PRIMARY KEY`).  

---

### **4. Database & Schema**  
#### **Database**  
A **database** is a collection of organized data stored in an RDBMS. It consists of multiple tables and other database objects like views, indexes, and stored procedures.

#### **Schema**  
A **schema** is the structure that defines how data is organized in a database. It includes:  
- Tables  
- Relationships between tables  
- Data types and constraints  

Example SQL to create a database and a table within a schema:  
```sql
CREATE DATABASE CompanyDB;

USE CompanyDB;

CREATE TABLE Employees (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    age INT CHECK (age > 0)
);
```
---