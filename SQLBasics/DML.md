## **Data Manipulation Language (DML) in MySQL**  

**DML (Data Manipulation Language)** is a subset of SQL that deals with modifying and retrieving data stored in relational databases. Unlike DDL, which manages database structure, DML focuses on handling the actual data.  

---

## **1. Key DML Commands**  

### **1.1 INSERT** – Adds new records into a table.  
### **1.2 UPDATE** – Modifies existing records in a table.  
### **1.3 DELETE** – Removes specific records from a table.  
### **1.4 SELECT** – Retrieves records from a table (often considered part of DQL but used in data manipulation).  

---

## **2. MySQL DML Commands in Detail**  

### **2.1 INSERT Statement**  

The `INSERT` statement is used to add new rows into a table.  

#### **Example: Inserting a Single Row**
```sql
INSERT INTO Employees (emp_id, full_name, email, age, department) 
VALUES (1, 'John Doe', 'john@example.com', 30, 'IT');
```
- Adds a new employee with `emp_id = 1` into the `Employees` table.  

#### **Example: Inserting Multiple Rows**
```sql
INSERT INTO Employees (emp_id, full_name, email, age, department) 
VALUES 
(2, 'Jane Smith', 'jane@example.com', 28, 'HR'),
(3, 'Alice Brown', 'alice@example.com', 35, 'Finance');
```
- Adds multiple records in a single query.  

#### **Example: Inserting Data with Default Values**
```sql
INSERT INTO Employees (emp_id, full_name, email, age) 
VALUES (4, 'Robert White', 'robert@example.com', 40);
```
- The `department` column will take the default value ('General' as defined earlier).  

---

### **2.2 UPDATE Statement**  

The `UPDATE` statement is used to modify existing records in a table.  

#### **Example: Updating a Single Record**
```sql
UPDATE Employees 
SET department = 'Engineering' 
WHERE emp_id = 1;
```
- Updates the `department` of the employee with `emp_id = 1`.  

#### **Example: Updating Multiple Columns**
```sql
UPDATE Employees 
SET age = 29, department = 'Marketing' 
WHERE emp_id = 2;
```
- Updates both `age` and `department` for the employee with `emp_id = 2`.  

#### **Example: Updating All Records in a Table**
```sql
UPDATE Employees 
SET department = 'Operations';
```
- Changes the `department` for all employees (use with caution).  

---

### **2.3 DELETE Statement**  

The `DELETE` statement is used to remove specific rows from a table.  

#### **Example: Deleting a Single Record**
```sql
DELETE FROM Employees 
WHERE emp_id = 3;
```
- Removes the employee with `emp_id = 3`.  

#### **Example: Deleting Multiple Records**
```sql
DELETE FROM Employees 
WHERE department = 'HR';
```
- Removes all employees in the `HR` department.  

#### **Example: Deleting All Records from a Table**
```sql
DELETE FROM Employees;
```
- Deletes all rows in the `Employees` table but keeps the structure intact.  
- **Difference from `TRUNCATE`**: `DELETE` logs individual deletions and can be rolled back if within a transaction, while `TRUNCATE` cannot.  

---

### **2.4 SELECT Statement**  

The `SELECT` statement is used to retrieve data from a table.  

#### **Example: Retrieving All Data**
```sql
SELECT * FROM Employees;
```
- Fetches all columns for all employees.  

#### **Example: Retrieving Specific Columns**
```sql
SELECT full_name, email FROM Employees;
```
- Fetches only `full_name` and `email` columns.  

#### **Example: Filtering Data**
```sql
SELECT * FROM Employees 
WHERE department = 'IT';
```
- Fetches all employees in the IT department.  

#### **Example: Using ORDER BY**
```sql
SELECT * FROM Employees 
ORDER BY age DESC;
```
- Retrieves all employees sorted by age in descending order.  

#### **Example: Using LIMIT**
```sql
SELECT * FROM Employees 
LIMIT 2;
```
- Retrieves only the first two records.  

---

## **3. Transactions in DML (COMMIT, ROLLBACK, SAVEPOINT)**  

DML operations can be used with transactions to ensure data consistency.  

### **3.1 Using COMMIT and ROLLBACK**  

#### **Example: Transaction with COMMIT**
```sql
START TRANSACTION;

INSERT INTO Employees (emp_id, full_name, email, age, department) 
VALUES (5, 'Emma Wilson', 'emma@example.com', 27, 'Sales');

COMMIT;
```
- `COMMIT` saves the changes permanently.  

#### **Example: Transaction with ROLLBACK**
```sql
START TRANSACTION;

UPDATE Employees 
SET department = 'Admin' 
WHERE emp_id = 1;

ROLLBACK;
```
- `ROLLBACK` undoes the `UPDATE` operation, restoring the previous state.  

### **3.2 Using SAVEPOINT**  

#### **Example: Creating and Rolling Back to a SAVEPOINT**
```sql
START TRANSACTION;

UPDATE Employees SET age = 32 WHERE emp_id = 1;
SAVEPOINT sp1;

UPDATE Employees SET department = 'HR' WHERE emp_id = 1;
ROLLBACK TO sp1;  -- Rolls back only the second update

COMMIT;
```
- `SAVEPOINT` creates a checkpoint within a transaction.  
- `ROLLBACK TO sp1;` undoes changes made after the savepoint but keeps earlier changes.  

---

## **4. Summary of MySQL DML Commands**  

| **Command** | **Description** |
|------------|----------------|
| `INSERT` | Adds new records into a table. |
| `UPDATE` | Modifies existing records. |
| `DELETE` | Removes specific records. |
| `SELECT` | Retrieves data from a table. |
| `COMMIT` | Saves transaction changes permanently. |
| `ROLLBACK` | Undoes changes in the current transaction. |
| `SAVEPOINT` | Creates a checkpoint within a transaction. |

---
