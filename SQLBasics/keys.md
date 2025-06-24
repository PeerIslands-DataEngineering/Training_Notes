## **Keys in MySQL**

In MySQL, keys are used to uniquely identify records and establish relationships between tables. There are several types of keys, each with its specific purpose. Below is a detailed explanation of the most common types of keys in MySQL: **Primary Key**, **Foreign Key**, **Unique Key**, **Composite Key**, **Alternate Key**, and **Secondary Key**.

---

### **1. Primary Key**  

A **Primary Key** is used to uniquely identify each record in a table. A table can have only one primary key. The primary key column(s) must contain unique values, and it cannot contain `NULL` values.

- **Characteristics**:  
  - Uniquely identifies each row in the table.  
  - Cannot contain `NULL` values.  
  - Automatically creates a unique index.

#### **Syntax to Define a Primary Key**
```sql
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    full_name VARCHAR(100),
    age INT
);
```
- In this example, `employee_id` is the **primary key** for the `Employees` table. Each `employee_id` value must be unique and not `NULL`.

#### **Example: Inserting Data with Primary Key**
```sql
INSERT INTO Employees (employee_id, full_name, age) 
VALUES (1, 'John Doe', 30);

-- This will fail if the same employee_id is inserted again
INSERT INTO Employees (employee_id, full_name, age) 
VALUES (1, 'Jane Doe', 28);  -- This will cause a duplicate key error
```

---

### **2. Foreign Key**  

A **Foreign Key** is a key used to establish and enforce a link between the data in two tables. It is a column or a set of columns in one table that refers to the **primary key** in another table.

- **Characteristics**:  
  - Ensures data integrity by enforcing the relationship between tables.  
  - A foreign key can have `NULL` values.  
  - Helps maintain referential integrity between parent and child tables.

#### **Syntax to Define a Foreign Key**
```sql
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    employee_id INT,
    order_date DATE,
    FOREIGN KEY (employee_id) REFERENCES Employees(employee_id)
);
```
- In this example, `employee_id` in the `Orders` table is a **foreign key** that references the `employee_id` column in the `Employees` table.  

#### **Example: Inserting Data with Foreign Key**
```sql
-- First, insert an employee
INSERT INTO Employees (employee_id, full_name, age) 
VALUES (1, 'John Doe', 30);

-- Then insert an order that references the employee
INSERT INTO Orders (order_id, employee_id, order_date) 
VALUES (101, 1, '2025-03-14');
```

#### **Example: Foreign Key Constraint Violation**
```sql
-- This will fail because employee_id 2 does not exist in the Employees table
INSERT INTO Orders (order_id, employee_id, order_date) 
VALUES (102, 2, '2025-03-15');  -- Error: Foreign key constraint fails
```

---

### **3. Unique Key**  

A **Unique Key** ensures that all values in a column or a set of columns are distinct across rows. Unlike a **Primary Key**, a table can have multiple unique keys, and they can allow `NULL` values.

- **Characteristics**:  
  - Ensures all values are unique.  
  - Allows `NULL` values, but only one `NULL` value per column.
  - Can be defined on one or more columns.

#### **Syntax to Define a Unique Key**
```sql
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    full_name VARCHAR(100),
    age INT
);
```
- In this example, the `email` column has a **unique key** constraint, ensuring that no two employees can have the same email.

#### **Example: Inserting Data with Unique Key**
```sql
INSERT INTO Employees (employee_id, email, full_name, age) 
VALUES (1, 'john.doe@example.com', 'John Doe', 30);

-- This will fail if you try to insert a duplicate email
INSERT INTO Employees (employee_id, email, full_name, age) 
VALUES (2, 'john.doe@example.com', 'Jane Doe', 28);  -- Error: Duplicate entry
```

---

### **4. Composite Key**  

A **Composite Key** (also known as a **Composite Primary Key**) is a primary key that consists of more than one column. It is used when a single column is not sufficient to uniquely identify records in the table.

- **Characteristics**:  
  - Composed of two or more columns.  
  - The combination of the columns must be unique across rows.

#### **Syntax to Define a Composite Key**
```sql
CREATE TABLE EmployeeProjects (
    employee_id INT,
    project_id INT,
    start_date DATE,
    PRIMARY KEY (employee_id, project_id)
);
```
- In this example, the combination of `employee_id` and `project_id` forms the **composite primary key** for the `EmployeeProjects` table. This means that an employee can work on multiple projects, but the same employee can't be assigned to the same project more than once.

#### **Example: Inserting Data with Composite Key**
```sql
INSERT INTO EmployeeProjects (employee_id, project_id, start_date) 
VALUES (1, 101, '2025-03-01');

-- This will fail if you try to insert the same employee_id and project_id combination
INSERT INTO EmployeeProjects (employee_id, project_id, start_date) 
VALUES (1, 101, '2025-03-05');  -- Error: Duplicate entry
```

---

### **5. Alternate Key**  

An **Alternate Key** is a column (or combination of columns) that could have been chosen as the primary key but was not. It is a unique key that is not the primary key.

- **Characteristics**:  
  - Alternate keys are unique and can be used to identify records.  
  - A table can have multiple alternate keys, but only one primary key.

#### **Example: Defining an Alternate Key**
```sql
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    phone_number VARCHAR(20) UNIQUE,
    full_name VARCHAR(100),
    age INT
);
```
- In this example, `email` and `phone_number` are both alternate keys because they are unique, but only `employee_id` is the **primary key**.

#### **Alternate Key Usage**
- You can think of alternate keys as other possible unique identifiers for the table.
  
---

### **6. Secondary Key**  

A **Secondary Key** is another name for a non-primary key index. It allows faster searching and retrieval of records based on columns that are not part of the primary key.

- **Characteristics**:  
  - Used for performance optimization.  
  - May be unique or non-unique.

#### **Example: Defining a Secondary Key**
```sql
CREATE INDEX idx_employee_name 
ON Employees (full_name);
```
- In this example, `idx_employee_name` is a **secondary key** that helps speed up lookups by the `full_name` column. This is an index on `full_name`, but it is not part of the primary key.

#### **Example: Using a Secondary Key for Searching**
```sql
-- This will use the secondary key to search faster
SELECT * FROM Employees WHERE full_name = 'John Doe';
```

---

## **Summary of Keys in MySQL**

| **Key Type**         | **Description**                                                         | **Example**                          |
|----------------------|-------------------------------------------------------------------------|--------------------------------------|
| **Primary Key**       | Uniquely identifies a record in a table. Cannot contain `NULL`.          | `PRIMARY KEY (employee_id)`          |
| **Foreign Key**       | Establishes a relationship between two tables. Refers to a primary key. | `FOREIGN KEY (employee_id) REFERENCES Employees(employee_id)` |
| **Unique Key**        | Ensures that all values in a column are distinct. Allows `NULL`.        | `UNIQUE (email)`                    |
| **Composite Key**     | A primary key that consists of two or more columns.                     | `PRIMARY KEY (employee_id, project_id)` |
| **Alternate Key**     | A unique key that could be the primary key but was not chosen as such.  | `UNIQUE (email)`, `UNIQUE (phone_number)` |
| **Secondary Key**     | An index that speeds up queries. Not part of the primary key.           | `CREATE INDEX idx_employee_name ON Employees (full_name)` |

---
