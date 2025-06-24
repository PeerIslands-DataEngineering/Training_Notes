# **User-Defined Functions (UDF)**  

## **1. What is a User-Defined Function (UDF) in MySQL?**  
A **User-Defined Function (UDF)** in MySQL is a **custom function** created by users to perform specific operations and return a value. Unlike stored procedures, **UDFs return a single value** and can be used in SQL statements like built-in MySQL functions.  

📌 **Key Differences Between UDF and Stored Procedure:**  
| Feature | User-Defined Function (UDF) | Stored Procedure |
|---------|-----------------------------|------------------|
| Returns | A single value | Can return multiple values |
| Use in Queries | Yes, can be used in SELECT, WHERE, etc. | No, called separately |
| Supports Transactions | No | Yes |
| Use in Indexes | Yes | No |

---

## **2. Types of UDFs in MySQL**
MySQL supports **three types** of user-defined functions:  

1. **Scalar Functions** – Return a single value (e.g., calculating a discount, getting employee age).  
2. **Aggregate Functions** – Process multiple rows and return a single result (e.g., custom `SUM` or `AVG`). *(Not natively supported in MySQL, but can be implemented in C-based plugins)*  
3. **Table-Valued Functions** – Not directly supported in MySQL (use **Stored Procedures + Temporary Tables** instead).  

---

## **3. Syntax for Creating UDFs**
```sql
DELIMITER $$

CREATE FUNCTION function_name(parameters)
RETURNS return_type
DETERMINISTIC
BEGIN
    -- Function logic
    RETURN value;
END $$

DELIMITER ;
```

🔹 **Explanation:**  
- **DELIMITER $$** – Avoids conflicts with semicolons inside function body.  
- **CREATE FUNCTION function_name(parameters)** – Defines the function and parameters.  
- **RETURNS return_type** – Specifies the data type of the return value.  
- **DETERMINISTIC | NOT DETERMINISTIC** – Defines whether the function always returns the same result for the same input.  
- **RETURN value** – Returns the result.  

---

## **4. Examples of User-Defined Functions (UDFs)**

### **Example 1: Simple Function to Calculate Square of a Number**
```sql
DELIMITER $$

CREATE FUNCTION square(n INT) 
RETURNS INT 
DETERMINISTIC 
BEGIN
    RETURN n * n;
END $$

DELIMITER ;
```

#### **Test the Function**
```sql
SELECT square(5); 
-- Output: 25
```

---

### **Example 2: Function to Calculate Employee Age from Date of Birth**
```sql
DELIMITER $$

CREATE FUNCTION get_employee_age(dob DATE) 
RETURNS INT 
DETERMINISTIC 
BEGIN
    RETURN TIMESTAMPDIFF(YEAR, dob, CURDATE());
END $$

DELIMITER ;
```

#### **Test the Function**
```sql
SELECT get_employee_age('1990-05-15');
-- Output: 34 (if the current year is 2024)
```

---

### **Example 3: Function to Convert Salary into Annual Salary**
```sql
DELIMITER $$

CREATE FUNCTION annual_salary(monthly_salary DECIMAL(10,2)) 
RETURNS DECIMAL(10,2) 
DETERMINISTIC 
BEGIN
    RETURN monthly_salary * 12;
END $$

DELIMITER ;
```

#### **Test the Function**
```sql
SELECT annual_salary(5000);
-- Output: 60000.00
```

---

### **Example 4: Function to Generate Employee Full Name**
```sql
DELIMITER $$

CREATE FUNCTION get_full_name(first_name VARCHAR(50), last_name VARCHAR(50)) 
RETURNS VARCHAR(100) 
DETERMINISTIC 
BEGIN
    RETURN CONCAT(first_name, ' ', last_name);
END $$

DELIMITER ;
```

#### **Test the Function**
```sql
SELECT get_full_name('John', 'Doe');
-- Output: John Doe
```

---

### **Example 5: Function to Get Discounted Price**
```sql
DELIMITER $$

CREATE FUNCTION get_discounted_price(original_price DECIMAL(10,2), discount_percentage INT) 
RETURNS DECIMAL(10,2) 
DETERMINISTIC 
BEGIN
    RETURN original_price - (original_price * discount_percentage / 100);
END $$

DELIMITER ;
```

#### **Test the Function**
```sql
SELECT get_discounted_price(1000, 10);
-- Output: 900.00
```

---

## **5. Using User-Defined Functions in Queries**
You can use **UDFs in SELECT statements, WHERE conditions, and JOINs**, just like built-in MySQL functions.

#### **Example: Using UDF in a SELECT Query**
```sql
SELECT name, monthly_salary, annual_salary(monthly_salary) AS yearly_salary FROM employees;
```

#### **Example: Using UDF in a WHERE Clause**
```sql
SELECT * FROM employees WHERE annual_salary(monthly_salary) > 50000;
```

---

## **6. Managing User-Defined Functions**
### **View All Functions**
```sql
SHOW FUNCTION STATUS WHERE Db = 'your_database_name';
```

### **Drop a Function**
```sql
DROP FUNCTION get_employee_age;
```

---

## **7. Best Practices for UDFs in MySQL**
✅ **Use DETERMINISTIC where possible** – Helps MySQL optimize queries.  
✅ **Keep functions simple and lightweight** – Avoid complex logic.  
✅ **Use appropriate return types** – Match expected data types.  
✅ **Avoid using UDFs inside large SELECT statements** – Can slow down performance.  

---

## **8. Conclusion**
MySQL **User-Defined Functions (UDFs)** are **powerful tools** that allow developers to create **custom logic** for data processing. They improve code reusability and readability but should be **used wisely** to avoid performance bottlenecks.  
