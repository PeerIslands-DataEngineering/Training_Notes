## **MySQL Joins**

In MySQL, a **JOIN** is used to combine rows from two or more tables based on a related column between them. Joins are essential when you need to fetch data from multiple tables that are connected in some way, such as using a foreign key. There are several types of joins in MySQL, each serving a different purpose. Let's look at each type in detail with examples.

---

### **Types of Joins in MySQL**

1. **INNER JOIN**
2. **LEFT JOIN (or LEFT OUTER JOIN)**
3. **RIGHT JOIN (or RIGHT OUTER JOIN)**
4. **FULL JOIN (or FULL OUTER JOIN)**
5. **CROSS JOIN**
6. **SELF JOIN**

---

### **1. INNER JOIN**

An **INNER JOIN** returns only the rows that have matching values in both tables. If there is no match, the row is not included in the result.

#### **Syntax:**

```sql
SELECT columns
FROM table1
INNER JOIN table2
ON table1.column = table2.column;
```

#### **Example:**

Consider two tables:

**Employees Table:**
```sql
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    full_name VARCHAR(100),
    department_id INT
);
```

**Departments Table:**
```sql
CREATE TABLE Departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100)
);
```

To find the list of employees along with their department names, you can use an **INNER JOIN**.

```sql
SELECT Employees.full_name, Departments.department_name
FROM Employees
INNER JOIN Departments
ON Employees.department_id = Departments.department_id;
```

- This query will return the `full_name` of employees along with their `department_name` where there is a matching `department_id` in both the `Employees` and `Departments` tables.

#### **INNER JOIN Example Output:**
| full_name        | department_name |
|------------------|-----------------|
| John Doe         | HR              |
| Jane Smith       | IT              |
| Alice Brown      | Sales           |

If an employee does not belong to any department (i.e., no match in the `Departments` table), they will not appear in the result.

---

### **2. LEFT JOIN (or LEFT OUTER JOIN)**

A **LEFT JOIN** (or **LEFT OUTER JOIN**) returns all rows from the left table (table1), along with matching rows from the right table (table2). If there is no match in the right table, NULL values will be returned for columns from the right table.

#### **Syntax:**

```sql
SELECT columns
FROM table1
LEFT JOIN table2
ON table1.column = table2.column;
```

#### **Example:**

To get the list of employees and their departments, but this time including employees who are not assigned to any department (NULL if no match):

```sql
SELECT Employees.full_name, Departments.department_name
FROM Employees
LEFT JOIN Departments
ON Employees.department_id = Departments.department_id;
```

#### **LEFT JOIN Example Output:**
| full_name        | department_name |
|------------------|-----------------|
| John Doe         | HR              |
| Jane Smith       | IT              |
| Alice Brown      | Sales           |
| Bob Green        | NULL            |

- `Bob Green` does not belong to any department, so the `department_name` is returned as `NULL`.

---

### **3. RIGHT JOIN (or RIGHT OUTER JOIN)**

A **RIGHT JOIN** (or **RIGHT OUTER JOIN**) returns all rows from the right table (table2), along with matching rows from the left table (table1). If there is no match in the left table, NULL values will be returned for columns from the left table.

#### **Syntax:**

```sql
SELECT columns
FROM table1
RIGHT JOIN table2
ON table1.column = table2.column;
```

#### **Example:**

To get all departments, including those with no employees:

```sql
SELECT Employees.full_name, Departments.department_name
FROM Employees
RIGHT JOIN Departments
ON Employees.department_id = Departments.department_id;
```

#### **RIGHT JOIN Example Output:**
| full_name        | department_name |
|------------------|-----------------|
| John Doe         | HR              |
| Jane Smith       | IT              |
| Alice Brown      | Sales           |
| NULL             | Marketing       |

- The `Marketing` department has no employees, so the `full_name` is `NULL`.

---

### **4. FULL JOIN (or FULL OUTER JOIN)**

A **FULL JOIN** (or **FULL OUTER JOIN**) returns all rows from both tables. If there is no match, NULL values are returned for columns from the table without a match.

**Note:** MySQL does not support `FULL OUTER JOIN` directly. However, you can simulate it using a combination of **LEFT JOIN** and **RIGHT JOIN** with a `UNION`.

#### **Syntax (Simulated Full Outer Join):**

```sql
SELECT columns
FROM table1
LEFT JOIN table2
ON table1.column = table2.column
UNION
SELECT columns
FROM table1
RIGHT JOIN table2
ON table1.column = table2.column;
```

#### **Example:**

To simulate a full outer join of employees and departments:

```sql
SELECT Employees.full_name, Departments.department_name
FROM Employees
LEFT JOIN Departments
ON Employees.department_id = Departments.department_id
UNION
SELECT Employees.full_name, Departments.department_name
FROM Employees
RIGHT JOIN Departments
ON Employees.department_id = Departments.department_id;
```

#### **FULL JOIN Example Output:**
| full_name        | department_name |
|------------------|-----------------|
| John Doe         | HR              |
| Jane Smith       | IT              |
| Alice Brown      | Sales           |
| Bob Green        | NULL            |
| NULL             | Marketing       |

- The result contains all employees and all departments, with `NULL` for the missing values in either table.

---

### **5. CROSS JOIN**

A **CROSS JOIN** returns the Cartesian product of both tables. This means it returns every combination of rows from both tables. It is rarely used because it can result in large result sets.

#### **Syntax:**

```sql
SELECT columns
FROM table1
CROSS JOIN table2;
```

#### **Example:**

If you have a `Colors` table and a `Sizes` table, and you want to get all possible combinations of colors and sizes:

```sql
CREATE TABLE Colors (
    color_name VARCHAR(50)
);

CREATE TABLE Sizes (
    size_name VARCHAR(50)
);

INSERT INTO Colors VALUES ('Red'), ('Blue');
INSERT INTO Sizes VALUES ('Small'), ('Medium');

SELECT Colors.color_name, Sizes.size_name
FROM Colors
CROSS JOIN Sizes;
```

#### **CROSS JOIN Example Output:**
| color_name  | size_name |
|-------------|-----------|
| Red         | Small     |
| Red         | Medium    |
| Blue        | Small     |
| Blue        | Medium    |

- Every color is paired with every size.

---

### **6. SELF JOIN**

A **SELF JOIN** is a join where a table is joined with itself. This is useful when you have hierarchical data (e.g., employees and managers) and you need to relate rows within the same table.

#### **Syntax:**

```sql
SELECT columns
FROM table1 AS alias1
JOIN table1 AS alias2
ON alias1.column = alias2.column;
```

#### **Example:**

Consider an **Employees** table where employees have a `manager_id` pointing to the `employee_id` of their manager.

```sql
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    full_name VARCHAR(100),
    manager_id INT
);

INSERT INTO Employees VALUES (1, 'John Doe', NULL), (2, 'Jane Smith', 1), (3, 'Alice Brown', 1);

SELECT e1.full_name AS Employee, e2.full_name AS Manager
FROM Employees e1
LEFT JOIN Employees e2
ON e1.manager_id = e2.employee_id;
```

#### **SELF JOIN Example Output:**
| Employee       | Manager     |
|----------------|-------------|
| John Doe       | NULL        |
| Jane Smith     | John Doe    |
| Alice Brown    | John Doe    |

- This query returns the `full_name` of each employee and their corresponding manager (if any).

---

### **Summary of Joins in MySQL**

| **Type of Join**           | **Description**                                                 | **When to Use**                                      |
|----------------------------|-----------------------------------------------------------------|------------------------------------------------------|
| **INNER JOIN**              | Returns only the rows with matching values in both tables.      | When you need to combine data from both tables where there is a match. |
| **LEFT JOIN (OUTER)**       | Returns all rows from the left table and matching rows from the right table. | When you need all rows from the left table, even if there is no match in the right table. |
| **RIGHT JOIN (OUTER)**      | Returns all rows from the right table and matching rows from the left table. | When you need all rows from the right table, even if there is no match in the left table. |
| **FULL JOIN (OUTER)**       | Returns all rows from both tables, with NULLs where no match.   | When you need all rows from both tables, even if there is no match (simulated in MySQL). |
| **CROSS JOIN**              | Returns the Cartesian product of both tables.                   | When you need to combine every row from the first table with every row from the second table. |
| **SELF JOIN**               | Joins a table with itself.                                      | When you need to relate rows within the same table, such as in hierarchical structures. |

---

