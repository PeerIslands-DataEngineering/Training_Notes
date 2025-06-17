Control flow in Python determines the **execution order** of statements in a program. It includes:  
1️⃣ **Conditional Statements** (`if`, `elif`, `else`)  
2️⃣ **Looping Statements** (`for`, `while`)  
3️⃣ **Loop Control Statements** (`break`, `continue`, `pass`)  
4️⃣ **Exception Handling** (`try`, `except`, `finally`)  

Let’s explore each in **detail** with **examples**! 🚀  

---


# **📌 1. Conditional Statements**
Python uses `if`, `elif`, and `else` to **make decisions** based on conditions.  

### **🔹 `if` Statement**
Executes a block of code **only if** the condition is `True`.

```python
x = 10

if x > 5:
    print("x is greater than 5")  # Output: x is greater than 5
```

---

### **🔹 `if-else` Statement**
Executes **one block** if the condition is `True`, otherwise executes another block.

```python
x = 3

if x > 5:
    print("x is greater than 5")
else:
    print("x is less than or equal to 5")  # Output: x is less than or equal to 5
```

---

### **🔹 `if-elif-else` (Multiple Conditions)**
Allows multiple **conditions** to be checked in sequence.

```python
x = 7

if x > 10:
    print("x is greater than 10")
elif x > 5:
    print("x is between 6 and 10")  # Output: x is between 6 and 10
else:
    print("x is 5 or less")
```

---

### **🔹 Short-Hand `if` (Ternary Operator)**
Python allows single-line `if` statements.

```python
x = 10
print("x is positive") if x > 0 else print("x is non-positive")
```

---

### **🔹 Nested `if` Statements**
You can **nest `if` conditions** inside each other.

```python
x = 20

if x > 10:
    print("x is greater than 10")
    if x > 15:
        print("x is also greater than 15")  # Output: Both statements will execute
```

---

# **📌 2. Loops in Python**
Loops **repeat** a block of code multiple times.

### **🔹 `for` Loop (Iterating Over a Sequence)**
The `for` loop is used when you know **how many times** to iterate.

```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)
```

💡 **Using `range()` in `for` Loop**
```python
for i in range(1, 6):  # Loops from 1 to 5
    print(i)
```

💡 **Looping with Index (`enumerate`)**
```python
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(index, fruit)
```

---

### **🔹 `while` Loop (Condition-Based Looping)**
A `while` loop **runs as long as the condition is `True`**.

```python
x = 0

while x < 5:
    print(x)
    x += 1  # Increment x to avoid infinite loop
```

---

# **📌 3. Loop Control Statements**
Python provides `break`, `continue`, and `pass` for **controlling loop execution**.

### **🔹 `break` (Exits Loop Immediately)**
```python
for num in range(1, 10):
    if num == 5:
        break  # Stops the loop when num is 5
    print(num)  # Output: 1, 2, 3, 4
```

---

### **🔹 `continue` (Skips Current Iteration)**
```python
for num in range(1, 6):
    if num == 3:
        continue  # Skips 3 and continues to next iteration
    print(num)  # Output: 1, 2, 4, 5
```

---

### **🔹 `pass` (Placeholder Statement)**
Used as a **dummy statement** where syntax requires a block but no action is needed.

```python
for i in range(5):
    if i == 3:
        pass  # Does nothing, just a placeholder
    print(i)
```

---

# **📌 4. Exception Handling**
Python uses `try`, `except`, and `finally` to handle **errors gracefully**.

### **🔹 `try-except` Block**
Prevents the program from crashing due to an error.

```python
try:
    x = 10 / 0  # Division by zero error
except ZeroDivisionError:
    print("Error: Cannot divide by zero!")  # Output: Error: Cannot divide by zero!
```

---

### **🔹 Handling Multiple Exceptions**
```python
try:
    num = int(input("Enter a number: "))
    result = 10 / num
except ZeroDivisionError:
    print("Cannot divide by zero!")
except ValueError:
    print("Invalid input! Please enter a number.")
```

---

### **🔹 `finally` (Always Executes)**
```python
try:
    f = open("test.txt", "r")
except FileNotFoundError:
    print("File not found!")
finally:
    print("This will always execute.")  # Cleanup code
```

---

# **🚀 Summary**
Python's control flow structures make programs **dynamic** and **efficient**.  
✔ `if-elif-else` for **decision making**  
✔ `for` and `while` for **looping**  
✔ `break`, `continue`, `pass` for **loop control**  
✔ `try-except-finally` for **error handling**  
