# **MySQL Triggers**  

## **1. What is a Trigger in MySQL?**  
A **trigger** in MySQL is a **stored program** that automatically executes **before** or **after** an INSERT, UPDATE, or DELETE operation on a table.  

Triggers help enforce **business rules**, **automatic calculations**, **logging changes**, and **audit trails**.

---

## **2. When to Use Triggers?**  
✅ **Automatic validation** – Enforce business logic before inserting or updating records.  
✅ **Audit logs** – Record changes to important tables (who made changes and when).  
✅ **Complex integrity checks** – Enforce complex constraints that cannot be done with just `FOREIGN KEY`.  
✅ **Automatic updates** – Update related tables automatically when data changes.  

---

## **3. Types of Triggers in MySQL**  

| Trigger Type  | When it Executes |
|--------------|----------------|
| **BEFORE INSERT** | Before inserting a new row. |
| **AFTER INSERT** | After a row is inserted. |
| **BEFORE UPDATE** | Before updating a row. |
| **AFTER UPDATE** | After a row is updated. |
| **BEFORE DELETE** | Before deleting a row. |
| **AFTER DELETE** | After a row is deleted. |

---

## **4. Creating Triggers in MySQL**  

### **Syntax**
```sql
CREATE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON table_name
FOR EACH ROW
BEGIN
    -- SQL statements
END;
```

---

## **5. Trigger Examples in MySQL**

### **Example 1: BEFORE INSERT Trigger (Auto-fill a column before inserting data)**
Let’s assume we have a `students` table, and we want to automatically capitalize names before inserting a new row.

#### **Create Table**
```sql
CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE
);
```

#### **Create Trigger**
```sql
DELIMITER $$

CREATE TRIGGER before_student_insert
BEFORE INSERT ON students
FOR EACH ROW
BEGIN
    SET NEW.name = UPPER(NEW.name);
END$$

DELIMITER ;
```
#### **Test the Trigger**
```sql
INSERT INTO students (name, email) VALUES ('john doe', 'john@example.com');
SELECT * FROM students;
```
🔹 The name **"john doe"** will be automatically stored as **"JOHN DOE"**.

---

### **Example 2: AFTER INSERT Trigger (Audit Log for New Records)**
Let’s log every new student entry into an `audit_log` table.

#### **Create Audit Log Table**
```sql
CREATE TABLE audit_log (
    log_id INT AUTO_INCREMENT PRIMARY KEY,
    action VARCHAR(255),
    log_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### **Create AFTER INSERT Trigger**
```sql
DELIMITER $$

CREATE TRIGGER after_student_insert
AFTER INSERT ON students
FOR EACH ROW
BEGIN
    INSERT INTO audit_log (action) 
    VALUES (CONCAT('New student added: ', NEW.name, ' - ', NEW.email));
END$$

DELIMITER ;
```

#### **Test the Trigger**
```sql
INSERT INTO students (name, email) VALUES ('Alice Smith', 'alice@example.com');
SELECT * FROM audit_log;
```
🔹 The `audit_log` will contain an entry like:  
**"New student added: ALICE SMITH - alice@example.com"**  

---

### **Example 3: BEFORE UPDATE Trigger (Prevent Salary Reduction)**
Let’s ensure that salaries in the `employees` table **cannot be reduced**.

#### **Create Table**
```sql
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    salary DECIMAL(10,2)
);
```

#### **Create Trigger**
```sql
DELIMITER $$

CREATE TRIGGER before_salary_update
BEFORE UPDATE ON employees
FOR EACH ROW
BEGIN
    IF NEW.salary < OLD.salary THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'Salary reduction is not allowed';
    END IF;
END$$

DELIMITER ;
```

#### **Test the Trigger**
```sql
INSERT INTO employees (name, salary) VALUES ('Bob', 50000);
UPDATE employees SET salary = 45000 WHERE name = 'Bob';
```
🔹 **Error: Salary reduction is not allowed** ❌  

---

### **Example 4: AFTER DELETE Trigger (Record Deleted Rows in a Backup Table)**
Let’s back up deleted rows into an `employees_backup` table.

#### **Create Backup Table**
```sql
CREATE TABLE employees_backup (
    id INT,
    name VARCHAR(100),
    salary DECIMAL(10,2),
    deleted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### **Create AFTER DELETE Trigger**
```sql
DELIMITER $$

CREATE TRIGGER after_employee_delete
AFTER DELETE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employees_backup (id, name, salary) 
    VALUES (OLD.id, OLD.name, OLD.salary);
END$$

DELIMITER ;
```

#### **Test the Trigger**
```sql
DELETE FROM employees WHERE id = 1;
SELECT * FROM employees_backup;
```
🔹 **Deleted records are saved in the `employees_backup` table** ✅  

---

## **6. How to View, Drop, and Manage Triggers**

### **View Triggers in MySQL**
```sql
SHOW TRIGGERS;
```

### **Drop a Trigger**
```sql
DROP TRIGGER before_salary_update;
```

### **Modify a Trigger (Drop and Recreate)**
- MySQL **does not support ALTER TRIGGER**.  
- You must **drop and recreate** it.  

---

## **7. Best Practices for Using Triggers**
✅ **Use BEFORE triggers for data validation** (e.g., formatting, constraints).  
✅ **Use AFTER triggers for logging and auditing** (e.g., audit logs, backups).  
✅ **Avoid using too many triggers on the same table**, as they can slow performance.  
✅ **Do not use triggers for business logic** – Keep logic in application code when possible.  
✅ **Be careful with recursive triggers** (Trigger calls another trigger, causing infinite loops).  

---

## **8. Conclusion**
MySQL triggers are powerful for **automating data management, enforcing rules, and maintaining logs**. However, they should be used wisely to **avoid performance issues**.  
