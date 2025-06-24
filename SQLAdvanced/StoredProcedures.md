# **MySQL Stored Procedures**  

A **stored procedure** in MySQL is a set of SQL statements stored in the database that can be executed repeatedly. Stored procedures improve performance, encapsulate business logic, and reduce code duplication.

---

## **1. Creating a Stored Procedure**  
A stored procedure is created using the `CREATE PROCEDURE` statement.

### **Syntax:**  
```sql
DELIMITER //
CREATE PROCEDURE procedure_name (parameters)
BEGIN
    -- SQL Statements
END //
DELIMITER ;
```
- **`DELIMITER //`** prevents MySQL from terminating the procedure early.  
- **Parameters** can be:
  - `IN` – Input parameter
  - `OUT` – Output parameter
  - `INOUT` – Both input and output  

---

## **2. Simple Stored Procedure**  

### **Example 1: Procedure Without Parameters**
Let's create a procedure to fetch all students from a `students` table.

```sql
DELIMITER //
CREATE PROCEDURE GetAllStudents()
BEGIN
    SELECT * FROM students;
END //
DELIMITER ;
```

### **Calling the Procedure**  
```sql
CALL GetAllStudents();
```

---

## **3. Stored Procedure with Input Parameters**  

### **Example 2: Fetching Students by Grade**
```sql
DELIMITER //
CREATE PROCEDURE GetStudentsByGrade(IN grade_param VARCHAR(10))
BEGIN
    SELECT * FROM students WHERE grade = grade_param;
END //
DELIMITER ;
```

### **Calling the Procedure**
```sql
CALL GetStudentsByGrade('A');
```

---

## **4. Stored Procedure with Output Parameters**  

### **Example 3: Counting Students in a Grade**
```sql
DELIMITER //
CREATE PROCEDURE CountStudentsByGrade(IN grade_param VARCHAR(10), OUT student_count INT)
BEGIN
    SELECT COUNT(*) INTO student_count FROM students WHERE grade = grade_param;
END //
DELIMITER ;
```

### **Calling the Procedure and Getting Output**
```sql
CALL CountStudentsByGrade('A', @total);
SELECT @total AS StudentCount;
```
**Output:**
```
+--------------+
| StudentCount |
+--------------+
|            2 |
+--------------+
```

---

## **5. Stored Procedure with INOUT Parameters**  

### **Example 4: Incrementing Age of a Student**
```sql
DELIMITER //
CREATE PROCEDURE IncreaseAge(INOUT student_age INT)
BEGIN
    SET student_age = student_age + 1;
END //
DELIMITER ;
```

### **Calling the Procedure**
```sql
SET @age = 20;
CALL IncreaseAge(@age);
SELECT @age AS NewAge;
```
**Output:**
```
+--------+
| NewAge |
+--------+
|     21 |
+--------+
```

---

## **6. Using Variables in Stored Procedures**  

### **Example 5: Using a Variable to Store Data**
```sql
DELIMITER //
CREATE PROCEDURE GetTopStudent()
BEGIN
    DECLARE top_student VARCHAR(50);
    SELECT name INTO top_student FROM students WHERE grade = 'A' ORDER BY age LIMIT 1;
    SELECT top_student AS TopStudent;
END //
DELIMITER ;
```

### **Calling the Procedure**
```sql
CALL GetTopStudent();
```
**Output:**
```
+-----------+
| TopStudent |
+-----------+
| Alice     |
+-----------+
```

---

## **7. Stored Procedure with Loops (WHILE, REPEAT, LOOP)**  

### **Example 6: Using WHILE Loop**
```sql
DELIMITER //
CREATE PROCEDURE PrintNumbers(IN max_num INT)
BEGIN
    DECLARE counter INT DEFAULT 1;
    WHILE counter <= max_num DO
        SELECT counter;
        SET counter = counter + 1;
    END WHILE;
END //
DELIMITER ;
```

### **Calling the Procedure**
```sql
CALL PrintNumbers(5);
```
**Output:**
```
+----------+
| counter  |
+----------+
|        1 |
|        2 |
|        3 |
|        4 |
|        5 |
+----------+
```

---

## **8. Stored Procedure with Conditional Statements**  

### **Example 7: Using IF Statement**
```sql
DELIMITER //
CREATE PROCEDURE CheckPassOrFail(IN student_id INT)
BEGIN
    DECLARE student_grade CHAR(1);
    
    SELECT grade INTO student_grade FROM students WHERE id = student_id;
    
    IF student_grade IN ('A', 'B') THEN
        SELECT 'Pass' AS Result;
    ELSE
        SELECT 'Fail' AS Result;
    END IF;
END //
DELIMITER ;
```

### **Calling the Procedure**
```sql
CALL CheckPassOrFail(2);
```
**Output:**
```
+--------+
| Result |
+--------+
| Pass   |
+--------+
```

---

## **9. Deleting a Stored Procedure**  
To remove a stored procedure, use `DROP PROCEDURE`.

### **Example:**
```sql
DROP PROCEDURE IF EXISTS GetAllStudents;
```

---

## **10. Advantages of Stored Procedures**  
✅ **Performance Improvement:** Stored procedures execute faster due to precompiled execution plans.  
✅ **Encapsulation:** Business logic is centralized in the database.  
✅ **Security:** Users can be given access to stored procedures without direct table access.  
✅ **Reduced Network Traffic:** Instead of sending multiple queries, a single procedure call is sent.  

---

## **11. Limitations of Stored Procedures**  
❌ **Difficult to Debug:** No built-in debugging tools in MySQL.  
❌ **Increased Complexity:** Large procedures can become hard to manage.  
❌ **Not Always Portable:** Stored procedures are database-specific and may not work across different database systems.  

---

## **Conclusion**  
Stored procedures in MySQL are powerful tools for handling repetitive database operations efficiently. They improve security, performance, and maintainability. However, they should be used wisely to avoid complexity.
