# **Slowly Changing Dimensions (SCD) – Detailed Tutorial with Types**  

## **1. What is Slowly Changing Dimensions (SCD)?**  
In **Data Warehousing (DWH)**, **Slowly Changing Dimensions (SCD)** refer to **dimension tables** that change **slowly over time** rather than on a regular basis.  

For example, consider a **customer table** where a customer’s address may change occasionally. The system must decide:  
- **Should we keep a history of old addresses?**  
- **Should we update the existing record?**  

SCD **helps manage these changes efficiently** while maintaining accurate historical data.  

---

## **2. Types of SCD**
There are **six main types of SCD**, but the most commonly used are **Type 0, Type 1, Type 2, and Type 3**.

| **SCD Type** | **Description** |
|-------------|---------------|
| **SCD Type 0** | No changes allowed (static data). |
| **SCD Type 1** | Overwrites old data (no history maintained). |
| **SCD Type 2** | Creates a new record for each change (history maintained). |
| **SCD Type 3** | Keeps a limited history using additional columns. |
| **SCD Type 4** | Maintains history in a separate table. |
| **SCD Type 6** | A hybrid of SCD Types 1, 2, and 3. |

---

## **3. SCD Type 0 – Fixed Dimension (No Change Allowed)**
- **Data never changes** once inserted.  
- Used for **static reference data** (e.g., country codes, product categories).  

### **Example**: Country Table (SCD Type 0)
| country_id | country_name |
|------------|-------------|
| 1          | USA         |
| 2          | Canada      |

Even if **Canada changes its name**, the data in this table **remains unchanged**.

---

## **4. SCD Type 1 – Overwrite the Old Data**
- The **existing record is updated** when a change occurs.  
- **No history is maintained** (only the latest data is available).  
- Suitable for **correcting mistakes** (e.g., a customer’s typo in the name).  

### **Example**: Customer Table (Before Change)
| customer_id | name   | city   |
|------------|--------|--------|
| 101        | Alice  | London |

### **After Change (SCD Type 1)**
| customer_id | name   | city    |
|------------|--------|---------|
| 101        | Alice  | New York |

- The **"London" value is lost** because we overwrite it with "New York".

### **SQL Query (SCD Type 1 - Update)**
```sql
UPDATE customer_dim
SET city = 'New York'
WHERE customer_id = 101;
```
---

## **5. SCD Type 2 – Maintain Full History (New Record for Each Change)**
- **Each change creates a new row**.  
- **A "validity" column** is added to track changes.  
- Used when **historical tracking** is essential (e.g., address changes over time).  

### **Example**: Customer Table (SCD Type 2)
| customer_id | name  | city      | start_date | end_date  | is_active |
|------------|-------|-----------|------------|----------|----------|
| 101        | Alice | London    | 2022-01-01 | 2023-01-01 | No  |
| 102        | Alice | New York  | 2023-01-02 | NULL     | Yes |

- **New record created** when Alice moves to New York.  
- The **end_date is updated for old data**, and the latest row remains **active**.  

### **SQL Query (SCD Type 2 - Insert New Row)**
```sql
-- Mark old record as inactive
UPDATE customer_dim
SET end_date = '2023-01-01', is_active = 'No'
WHERE customer_id = 101 AND is_active = 'Yes';

-- Insert new record
INSERT INTO customer_dim (customer_id, name, city, start_date, end_date, is_active)
VALUES (101, 'Alice', 'New York', '2023-01-02', NULL, 'Yes');
```

---

## **6. SCD Type 3 – Maintain Limited History (Add New Column)**
- Instead of adding **new rows**, it **adds columns** for the old value.  
- Good for cases where **only recent history is needed** (e.g., last two addresses).  

### **Example**: Customer Table (SCD Type 3)
| customer_id | name  | current_city | previous_city |
|------------|------|-------------|---------------|
| 101        | Alice | New York    | London        |

- **Only one previous value is stored**.  

### **SQL Query (SCD Type 3 - Update)**
```sql
UPDATE customer_dim
SET previous_city = current_city,
    current_city = 'New York'
WHERE customer_id = 101;
```
---

## **7. SCD Type 4 – Maintain History in a Separate Table**
- The **main table stores current data**.  
- A **history table stores past changes**.  

### **Example**:
**Current Table**
| customer_id | name  | city     |
|------------|------|---------|
| 101        | Alice | New York |

**History Table**
| history_id | customer_id | name  | city   | change_date |
|-----------|------------|------|-------|-------------|
| 1         | 101        | Alice | London | 2022-01-01 |

### **SQL Query (SCD Type 4 - Move Old Data to History Table)**
```sql
-- Move old data to history table
INSERT INTO customer_history (customer_id, name, city, change_date)
SELECT customer_id, name, city, NOW() FROM customer_dim WHERE customer_id = 101;

-- Update current record
UPDATE customer_dim
SET city = 'New York'
WHERE customer_id = 101;
```
---

## **8. SCD Type 6 – Hybrid of SCD Types 1, 2, and 3**
- Combines **Type 1, Type 2, and Type 3** features.
- **Tracks full history** but also stores **previous values** for easier access.

### **Example: Customer Table**
| customer_id | name  | current_city | previous_city | start_date | end_date  | is_active |
|------------|------|-------------|---------------|------------|----------|----------|
| 101        | Alice | New York    | London        | 2023-01-02 | NULL     | Yes |
| 102        | Alice | London      | NULL          | 2022-01-01 | 2023-01-01 | No  |

- **Combines elements of SCD Type 1 (overwrite), SCD Type 2 (historical records), and SCD Type 3 (previous value stored in column).**

### **SQL Query (SCD Type 6 - Update and Insert)**
```sql
UPDATE customer_dim
SET previous_city = current_city,
    end_date = '2023-01-01',
    is_active = 'No'
WHERE customer_id = 101 AND is_active = 'Yes';

INSERT INTO customer_dim (customer_id, name, current_city, previous_city, start_date, end_date, is_active)
VALUES (101, 'Alice', 'New York', 'London', '2023-01-02', NULL, 'Yes');
```
---

## **9. Choosing the Right SCD Type**
| Use Case | Recommended SCD Type |
|----------|----------------------|
| Data never changes | **SCD Type 0** |
| Minor updates (no history needed) | **SCD Type 1** |
| Full historical tracking | **SCD Type 2** |
| Limited history (track recent changes) | **SCD Type 3** |
| Keep current and full history separate | **SCD Type 4** |
| Combination of Type 1, 2, and 3 | **SCD Type 6** |

---

## **Conclusion**
- **SCDs are critical for managing changes in dimension tables**.
- **Type 1 (overwrite) is simplest**, while **Type 2 (historical tracking) is most commonly used**.
- **Choosing the right SCD type depends on business requirements**.
