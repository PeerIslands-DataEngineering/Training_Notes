## **📌 Overview**  
Python provides a variety of **data types** to store different kinds of values and **operators** to perform computations. Understanding these is **fundamental** to mastering Python.  

---

# **🔷 1. Python Data Types**  

Python's data types can be categorized into:  
1️⃣ **Numeric Types**: `int`, `float`, `complex`  
2️⃣ **Sequence Types**: `list`, `tuple`, `range`, `str`  
3️⃣ **Set Types**: `set`, `frozenset`  
4️⃣ **Mapping Types**: `dict` (Dictionaries)  
5️⃣ **Boolean Type**: `bool` (True/False)  
6️⃣ **Binary Types**: `bytes`, `bytearray`, `memoryview`  
7️⃣ **None Type**: `NoneType` (Represents null values)  

---

## **📌 1.1 Numeric Data Types**  
### **🔹 Integer (`int`)**
- Used for **whole numbers** (positive or negative).  
- **No size limit** (unlike C or Java).  

```python
x = 100
y = -42
z = 999999999999999999999  # Large number (No overflow issues)
print(type(x))  # Output: <class 'int'>
```

---

### **🔹 Float (`float`)**
- Stores **decimal numbers** (floating-point values).  
- Precision is **up to 15 decimal places**.  

```python
pi = 3.14159
temperature = -10.5
print(type(pi))  # Output: <class 'float'>
```

---

### **🔹 Complex Numbers (`complex`)**
- Numbers with **real and imaginary** parts.  
- Written as `a + bj`, where `j` is the imaginary unit.  

```python
z = 3 + 4j
print(z.real)   # Output: 3.0
print(z.imag)   # Output: 4.0
print(type(z))  # Output: <class 'complex'>
```

---

## **📌 1.2 Sequence Data Types**  

### **🔹 Strings (`str`)**  
- A sequence of characters enclosed in quotes (`""` or `''`).  

```python
name = "Alice"
greeting = 'Hello, World!'
print(type(name))  # Output: <class 'str'>
```

- **String Slicing:**
```python
text = "Python"
print(text[0:3])  # Output: Pyt (substring)
```

---

### **🔹 Lists (`list`)**  
- **Ordered**, **mutable** (modifiable), and can store **mixed data types**.  

```python
fruits = ["apple", "banana", "cherry"]
fruits.append("mango")  # Add new item
print(fruits[1])  # Output: banana
```

---

### **🔹 Tuples (`tuple`)**  
- **Ordered**, **immutable** (cannot change after creation).  

```python
coordinates = (10, 20)
print(coordinates[0])  # Output: 10
```

---

### **🔹 Ranges (`range`)**  
- Used for generating sequences of numbers.  

```python
numbers = range(1, 10, 2)  # Start from 1, step by 2
print(list(numbers))  # Output: [1, 3, 5, 7, 9]
```

---

## **📌 1.3 Set Data Types**  

### **🔹 Sets (`set`)**  
- **Unordered**, **unique values only**.  

```python
unique_numbers = {1, 2, 3, 4, 4, 2}
print(unique_numbers)  # Output: {1, 2, 3, 4} (Duplicates removed)
```

---

### **🔹 Frozensets (`frozenset`)**  
- **Immutable version of a set** (cannot be modified).  

```python
immutable_set = frozenset([1, 2, 3, 4])
```

---

## **📌 1.4 Dictionary (`dict`)**  
- Stores **key-value pairs** (like a HashMap).  

```python
student = {"name": "Alice", "age": 25, "grade": "A"}
print(student["name"])  # Output: Alice
student["age"] = 26  # Modifying a value
```

---

## **📌 1.5 Boolean (`bool`)**  
- **True (`1`) and False (`0`)**  

```python
is_raining = True
print(type(is_raining))  # Output: <class 'bool'>
```

---

## **📌 1.6 None Type (`NoneType`)**  
- Represents **null or empty** values.  

```python
result = None
print(type(result))  # Output: <class 'NoneType'>
```

---

# **🔷 2. Python Operators**  

Python has various **operators** for performing operations on variables and values.  

## **📌 2.1 Arithmetic Operators**  
Perform **basic mathematical operations**.  

| Operator | Description | Example |
|----------|------------|---------|
| `+` | Addition | `x + y` |
| `-` | Subtraction | `x - y` |
| `*` | Multiplication | `x * y` |
| `/` | Division | `x / y` (float result) |
| `//` | Floor Division | `x // y` (int result) |
| `%` | Modulus (Remainder) | `x % y` |
| `**` | Exponentiation | `x ** y` (power) |

```python
a = 10
b = 3
print(a / b)  # Output: 3.333
print(a // b) # Output: 3
print(a ** b) # Output: 1000
```

---

## **📌 2.2 Comparison Operators**  
Used for **value comparisons**, return `True` or `False`.

| Operator | Meaning |
|----------|---------|
| `==` | Equal to |
| `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

```python
x = 5
y = 10
print(x < y)  # Output: True
```

---

## **📌 2.3 Logical Operators**  
Used to **combine conditions**.

| Operator | Meaning |
|----------|---------|
| `and` | Returns `True` if both conditions are `True` |
| `or` | Returns `True` if at least one condition is `True` |
| `not` | Reverses a boolean value |

```python
a = True
b = False
print(a and b)  # Output: False
print(a or b)   # Output: True
print(not a)    # Output: False
```

---

## **📌 2.4 Bitwise Operators**  
Operate at **binary level** (bit manipulation).

| Operator | Meaning |
|----------|---------|
| `&` | AND |
| `|` | OR |
| `^` | XOR |
| `~` | NOT |
| `<<` | Left shift |
| `>>` | Right shift |

```python
x = 5  # 0101
y = 3  # 0011
print(x & y)  # Output: 1 (0001)
```

---

## **📌 2.5 Assignment Operators**  
Used to **assign values** to variables.

```python
x = 10
x += 5  # Equivalent to x = x + 5
```

---

# **🔹 Summary**
| Data Type | Example |
|-----------|---------|
| Integer | `x = 42` |
| Float | `pi = 3.14` |
| String | `name = "Alice"` |
| List | `fruits = ["apple", "banana"]` |
| Tuple | `coordinates = (10, 20)` |
| Dictionary | `student = {"name": "Alice", "age": 20}` |
| Set | `unique_nums = {1, 2, 3}` |
