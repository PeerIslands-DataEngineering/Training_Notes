## **Normalization**

**Normalization** is the process of organizing data in a database to reduce redundancy and dependency by dividing large tables into smaller, related tables. The goal is to ensure data integrity and optimize storage, making the database more efficient.

Normalization is accomplished through the application of **normal forms**. Each normal form has a set of rules that the database must adhere to.

---

### **What are Normal Forms?**

The standard normal forms are as follows:

1. **First Normal Form (1NF)**
2. **Second Normal Form (2NF)**
3. **Third Normal Form (3NF)**
4. **Boyce-Codd Normal Form (BCNF)**
5. **Fourth Normal Form (4NF)**
6. **Fifth Normal Form (5NF)**

Each normal form builds upon the previous one, with additional rules and requirements to further eliminate redundancy and improve data integrity.

---

### **1. First Normal Form (1NF)**

A table is in **1NF** if:

- All columns contain atomic (indivisible) values.
- Each column contains only one value per row.
- Each record in a table must be unique.

In other words, **1NF** eliminates repeating groups and ensures that each field contains only a single value.

#### **Example:**

Consider the following table where a `Student` has multiple courses:

| Student_ID | Student_Name | Courses              |
|------------|--------------|----------------------|
| 101        | Alice        | Math, Physics        |
| 102        | Bob          | Chemistry, Biology   |

This table is **not in 1NF** because the `Courses` column contains multiple values (i.e., a list of courses). To bring it to **1NF**, we need to ensure that each field contains atomic values:

#### **Table in 1NF:**

| Student_ID | Student_Name | Course       |
|------------|--------------|--------------|
| 101        | Alice        | Math         |
| 101        | Alice        | Physics      |
| 102        | Bob          | Chemistry    |
| 102        | Bob          | Biology      |

Now, each row contains only a single value in the `Course` column, making the table compliant with **1NF**.

---

### **2. Second Normal Form (2NF)**

A table is in **2NF** if:

- It is in **1NF**.
- It has **no partial dependency** — every non-key attribute must be fully functionally dependent on the primary key. In other words, there should be no dependency on only a part of the primary key (which is an issue in composite keys).

If a table has a composite primary key, the non-key attributes must depend on the **whole** key, not just a part of it.

#### **Example:**

Consider a table where a student is assigned to a class:

| Student_ID | Class_ID | Student_Name | Class_Name     |
|------------|----------|--------------|----------------|
| 101        | 001      | Alice        | Math           |
| 101        | 002      | Alice        | Physics        |
| 102        | 003      | Bob          | Chemistry      |

In this table, the composite key is `(Student_ID, Class_ID)` because together they uniquely identify each row. However, `Student_Name` depends only on `Student_ID`, and `Class_Name` depends only on `Class_ID`. This violates **2NF**, because these columns should not depend on only part of the primary key.

#### **Table in 2NF:**

To bring this table into **2NF**, we need to split it into two tables to remove the partial dependency:

**Students Table:**

| Student_ID | Student_Name |
|------------|--------------|
| 101        | Alice        |
| 102        | Bob          |

**Classes Table:**

| Class_ID | Class_Name   |
|----------|--------------|
| 001      | Math         |
| 002      | Physics      |
| 003      | Chemistry    |

**Enrollments Table:**

| Student_ID | Class_ID |
|------------|----------|
| 101        | 001      |
| 101        | 002      |
| 102        | 003      |

Now, each non-key attribute (`Student_Name`, `Class_Name`) is fully dependent on the primary key in each respective table, making the tables compliant with **2NF**.

---

### **3. Third Normal Form (3NF)**

A table is in **3NF** if:

- It is in **2NF**.
- It has **no transitive dependency** — non-key attributes must not depend on other non-key attributes.

In other words, each non-key attribute should depend only on the primary key and not on other non-key attributes.

#### **Example:**

Consider the following table:

| Student_ID | Student_Name | Advisor | Advisor_Phone   |
|------------|--------------|---------|-----------------|
| 101        | Alice        | Dr. Lee | 123-456-7890    |
| 102        | Bob          | Dr. Kim | 987-654-3210    |

Here, `Advisor_Phone` depends on `Advisor`, not directly on the `Student_ID`. This is a **transitive dependency** and violates **3NF**.

#### **Table in 3NF:**

To convert this to **3NF**, we need to split the data into two tables:

**Students Table:**

| Student_ID | Student_Name | Advisor |
|------------|--------------|---------|
| 101        | Alice        | Dr. Lee |
| 102        | Bob          | Dr. Kim |

**Advisors Table:**

| Advisor    | Advisor_Phone   |
|------------|-----------------|
| Dr. Lee    | 123-456-7890    |
| Dr. Kim    | 987-654-3210    |

Now, `Advisor_Phone` is only stored in the `Advisors` table and is directly dependent on `Advisor`. This eliminates the transitive dependency, making the tables compliant with **3NF**.

---

### **4. Boyce-Codd Normal Form (BCNF)**

A table is in **BCNF** if:

- It is in **3NF**.
- Every determinant is a candidate key.

In simpler terms, if a column (or set of columns) determines another column, that column (or set) must be a **candidate key**.

#### **Example:**

Consider the following table:

| Course_ID | Instructor | Room    |
|-----------|------------|---------|
| 101       | Dr. Lee    | Room A  |
| 102       | Dr. Kim    | Room B  |
| 101       | Dr. Lee    | Room C  |

In this table, `Course_ID` determines `Instructor`, and `Instructor` determines `Room`. However, `Instructor` is not a candidate key, which violates **BCNF**.

#### **Table in BCNF:**

To bring this to **BCNF**, we need to split it into two tables:

**Courses Table:**

| Course_ID | Instructor |
|-----------|------------|
| 101       | Dr. Lee    |
| 102       | Dr. Kim    |

**Rooms Table:**

| Instructor | Room    |
|------------|---------|
| Dr. Lee    | Room A  |
| Dr. Kim    | Room B  |

This ensures that every determinant is a candidate key, making the tables compliant with **BCNF**.

---

### **5. Fourth Normal Form (4NF)**

A table is in **4NF** if:

- It is in **BCNF**.
- It has **no multi-valued dependencies** — each column must store atomic values, and no column should contain sets of values that depend on other columns.

#### **Example:**

Consider the following table where a student has multiple hobbies and skills:

| Student_ID | Student_Name | Hobby         | Skill         |
|------------|--------------|---------------|---------------|
| 101        | Alice        | Painting      | Java          |
| 101        | Alice        | Swimming      | Python        |

Here, the student has multiple hobbies and skills. This results in a **multi-valued dependency** and violates **4NF**.

#### **Table in 4NF:**

To bring this into **4NF**, we split the data into two tables:

**Students_Hobbies Table:**

| Student_ID | Student_Name | Hobby   |
|------------|--------------|---------|
| 101        | Alice        | Painting|
| 101        | Alice        | Swimming|

**Students_Skills Table:**

| Student_ID | Skill   |
|------------|---------|
| 101        | Java    |
| 101        | Python  |

---

### **6. Fifth Normal Form (5NF)**

A table is in **5NF** if:

- It is in **4NF**.
- It has **no join dependency** and cannot be decomposed into smaller tables without losing data.

---

### **Summary of Normal Forms**

| **Normal Form** | **Requirements**                                           | **Objective**                            |
|-----------------|------------------------------------------------------------|------------------------------------------|
| **1NF**         | Eliminate repeating groups; all columns contain atomic values. | Ensure each field contains a single value. |
| **2NF**         | Be in 1NF; remove partial dependency on composite keys.     | Ensure full dependency on the primary key. |
| **3NF**         | Be in 2NF; eliminate transitive dependency between non-key attributes. | Ensure no dependency between non-key attributes. |
| **BCNF**        | Be in 3NF; ensure all determinants are candidate keys.      | Eliminate non-candidate key dependencies. |
| **4NF**         | Be in BCNF; remove multi-valued dependencies.               | Ensure columns contain atomic values only. |
| **5NF**         | Be in 4NF; ensure no join dependencies.                     | Ensure that tables cannot be further decomposed without loss of information. |

---

Normalization is an important process for maintaining data integrity and ensuring efficient database design. 