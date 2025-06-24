## **MySQL Functions: Aggregate, Scalar, and Built-in Functions **

In MySQL, functions are used to manipulate data and perform various operations. These functions are categorized into three main types: **Aggregate Functions**, **Scalar Functions**, and **Built-in Functions**. This tutorial will cover each type in detail with examples.

---

### **1. Aggregate Functions in MySQL**

Aggregate functions are used to perform calculations on a set of values and return a single value. They are typically used in conjunction with the `GROUP BY` clause to group rows that have the same values in specified columns.

#### **Common Aggregate Functions in MySQL**

1. **COUNT()**  
   Returns the number of rows that match a specified condition.

   - **Syntax**:  
     ```sql
     COUNT(expression)
     ```
   - **Example**:  
     ```sql
     SELECT COUNT(*) FROM Employees;
     ```
     This will return the total number of rows in the `Employees` table.

2. **SUM()**  
   Returns the sum of values in a specified column.

   - **Syntax**:  
     ```sql
     SUM(expression)
     ```
   - **Example**:  
     ```sql
     SELECT SUM(salary) FROM Employees;
     ```
     This will return the sum of all `salary` values in the `Employees` table.

3. **AVG()**  
   Returns the average value of a specified column.

   - **Syntax**:  
     ```sql
     AVG(expression)
     ```
   - **Example**:  
     ```sql
     SELECT AVG(age) FROM Employees;
     ```
     This will return the average `age` of all employees in the `Employees` table.

4. **MIN()**  
   Returns the minimum value from a specified column.

   - **Syntax**:  
     ```sql
     MIN(expression)
     ```
   - **Example**:  
     ```sql
     SELECT MIN(age) FROM Employees;
     ```
     This will return the minimum `age` of all employees.

5. **MAX()**  
   Returns the maximum value from a specified column.

   - **Syntax**:  
     ```sql
     MAX(expression)
     ```
   - **Example**:  
     ```sql
     SELECT MAX(salary) FROM Employees;
     ```
     This will return the maximum `salary` from the `Employees` table.

6. **GROUP_CONCAT()**  
   Returns a concatenated string of values from a group.

   - **Syntax**:  
     ```sql
     GROUP_CONCAT(expression)
     ```
   - **Example**:  
     ```sql
     SELECT GROUP_CONCAT(full_name) FROM Employees;
     ```
     This will return a comma-separated string of all employee names.

#### **Example Using `GROUP BY` and Aggregate Functions**

```sql
SELECT department, COUNT(*), AVG(salary)
FROM Employees
GROUP BY department;
```
- This query will return the total number of employees and the average salary in each department.

---

### **2. Scalar Functions in MySQL**

Scalar functions perform operations on individual values and return a single value. They operate on a single row at a time and return results based on the input expression.

#### **Common Scalar Functions in MySQL**

1. **LENGTH()**  
   Returns the length of a string.

   - **Syntax**:  
     ```sql
     LENGTH(string)
     ```
   - **Example**:  
     ```sql
     SELECT LENGTH(full_name) FROM Employees;
     ```
     This will return the length of each employee's name.

2. **UPPER()**  
   Converts a string to uppercase.

   - **Syntax**:  
     ```sql
     UPPER(string)
     ```
   - **Example**:  
     ```sql
     SELECT UPPER(full_name) FROM Employees;
     ```
     This will return the employee names in uppercase.

3. **LOWER()**  
   Converts a string to lowercase.

   - **Syntax**:  
     ```sql
     LOWER(string)
     ```
   - **Example**:  
     ```sql
     SELECT LOWER(full_name) FROM Employees;
     ```
     This will return the employee names in lowercase.

4. **CONCAT()**  
   Concatenates two or more strings.

   - **Syntax**:  
     ```sql
     CONCAT(string1, string2, ...)
     ```
   - **Example**:  
     ```sql
     SELECT CONCAT(first_name, ' ', last_name) FROM Employees;
     ```
     This will return the full name by combining `first_name` and `last_name` of each employee.

5. **NOW()**  
   Returns the current date and time.

   - **Syntax**:  
     ```sql
     NOW()
     ```
   - **Example**:  
     ```sql
     SELECT NOW();
     ```
     This will return the current date and time.

6. **DATE()**  
   Extracts the date part of a datetime or timestamp.

   - **Syntax**:  
     ```sql
     DATE(expression)
     ```
   - **Example**:  
     ```sql
     SELECT DATE(join_date) FROM Employees;
     ```
     This will return the date part of the `join_date` column.

7. **ROUND()**  
   Rounds a numeric value to a specified number of decimal places.

   - **Syntax**:  
     ```sql
     ROUND(number, decimals)
     ```
   - **Example**:  
     ```sql
     SELECT ROUND(salary, 2) FROM Employees;
     ```
     This will round the `salary` to 2 decimal places.

8. **IF()**  
   A conditional function that returns one value if the condition is true and another if false.

   - **Syntax**:  
     ```sql
     IF(condition, value_if_true, value_if_false)
     ```
   - **Example**:  
     ```sql
     SELECT IF(age > 30, 'Senior', 'Junior') FROM Employees;
     ```
     This will return 'Senior' if the `age` is greater than 30, otherwise, it will return 'Junior'.

---

### **3. Built-in Functions in MySQL**

MySQL has a rich set of built-in functions that handle many different types of data, including strings, numbers, dates, and more. These functions can be classified into several categories such as string functions, date functions, and numeric functions.

#### **String Functions**

1. **SUBSTRING()**  
   Extracts a substring from a string.

   - **Syntax**:  
     ```sql
     SUBSTRING(string, start, length)
     ```
   - **Example**:  
     ```sql
     SELECT SUBSTRING(full_name, 1, 4) FROM Employees;
     ```
     This will return the first 4 characters of the `full_name` of each employee.

2. **REPLACE()**  
   Replaces all occurrences of a substring with another substring.

   - **Syntax**:  
     ```sql
     REPLACE(string, old_substring, new_substring)
     ```
   - **Example**:  
     ```sql
     SELECT REPLACE(full_name, 'John', 'Jonathan') FROM Employees;
     ```
     This will replace 'John' with 'Jonathan' in the `full_name`.

#### **Date Functions**

1. **CURDATE()**  
   Returns the current date.

   - **Syntax**:  
     ```sql
     CURDATE()
     ```
   - **Example**:  
     ```sql
     SELECT CURDATE();
     ```
     This will return the current date.

2. **DATE_ADD()**  
   Adds a time interval to a date.

   - **Syntax**:  
     ```sql
     DATE_ADD(date, INTERVAL value unit)
     ```
   - **Example**:  
     ```sql
     SELECT DATE_ADD(NOW(), INTERVAL 1 MONTH);
     ```
     This will return the date 1 month from today.

#### **Numeric Functions**

1. **ABS()**  
   Returns the absolute value of a number.

   - **Syntax**:  
     ```sql
     ABS(number)
     ```
   - **Example**:  
     ```sql
     SELECT ABS(-100) FROM Employees;
     ```
     This will return 100, the absolute value of -100.

2. **CEIL()**  
   Returns the smallest integer value greater than or equal to the specified number.

   - **Syntax**:  
     ```sql
     CEIL(number)
     ```
   - **Example**:  
     ```sql
     SELECT CEIL(15.3) FROM Employees;
     ```
     This will return 16.

3. **FLOOR()**  
   Returns the largest integer value less than or equal to the specified number.

   - **Syntax**:  
     ```sql
     FLOOR(number)
     ```
   - **Example**:  
     ```sql
     SELECT FLOOR(15.3) FROM Employees;
     ```
     This will return 15.

---

### **Summary of MySQL Functions**

| **Function Type**       | **Function**        | **Description**                                      |
|-------------------------|---------------------|------------------------------------------------------|
| **Aggregate Functions**  | COUNT(), SUM(), AVG(), MIN(), MAX(), GROUP_CONCAT() | Perform calculations on a set of rows.                |
| **Scalar Functions**     | LENGTH(), UPPER(), LOWER(), CONCAT(), NOW(), IF(), ROUND(), DATE() | Operate on individual values and return a result.     |
| **Built-in Functions**   | SUBSTRING(), REPLACE(), CURDATE(), DATE_ADD(), ABS(), CEIL(), FLOOR() | Perform specific operations like string manipulation, date calculations, and numeric functions. |

---