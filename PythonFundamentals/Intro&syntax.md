## **📌 Introduction to Python**
Python is a **high-level, interpreted, dynamically typed, and object-oriented** programming language known for its simplicity and readability. It is widely used in web development, data science, artificial intelligence, automation, and more.

### **🌟 Key Features of Python**
✔️ **Easy to Learn & Read** – Uses English-like syntax.  
✔️ **Dynamically Typed** – No need to declare variable types explicitly.  
✔️ **Interpreted Language** – Executes code line by line.  
✔️ **Multi-paradigm** – Supports **procedural, object-oriented, and functional programming**.  
✔️ **Extensive Libraries** – Offers built-in and third-party libraries like NumPy, Pandas, Django, TensorFlow, etc.  

---

# **📝 Python Syntax Guide**
Let's dive into Python’s syntax with examples! 🚀  

## **1️⃣ Hello World (Basic Syntax)**
```python
print("Hello, World!")  
```
🔹 The `print()` function outputs text to the console.  
🔹 No semicolons (`;`) are required at the end of lines.

---

## **2️⃣ Variables & Data Types**
```python
name = "Alice"       # String
age = 25            # Integer
height = 5.7        # Float
is_student = True   # Boolean
```
🔹 No need to declare variable types; Python infers them automatically.  
🔹 **Boolean values** are `True` or `False` (capitalized).

---

## **3️⃣ Comments in Python**
```python
# This is a single-line comment

"""
This is a 
multi-line comment
"""
```
🔹 Use `#` for single-line comments.  
🔹 Use `""" """` or `''' '''` for multi-line comments.

---

## **4️⃣ Taking User Input**
```python
name = input("Enter your name: ")
print("Hello, " + name + "!")
```
🔹 The `input()` function takes user input as a string.

---

## **5️⃣ Type Casting**
```python
age = int(input("Enter your age: "))   # Convert input to an integer
height = float(input("Enter your height: "))  # Convert input to float
```
🔹 Use `int()`, `float()`, or `str()` to convert data types.

---

## **6️⃣ Conditional Statements (if-elif-else)**
```python
age = 18
if age >= 18:
    print("You are an adult.")
elif age == 17:
    print("You are almost an adult.")
else:
    print("You are a minor.")
```
🔹 Indentation (`4 spaces`) is required instead of curly brackets `{}`.

---

## **7️⃣ Loops in Python**
### **🔹 For Loop**
```python
for i in range(5):  # Loops from 0 to 4
    print("Iteration:", i)
```
### **🔹 While Loop**
```python
count = 0
while count < 5:
    print("Count:", count)
    count += 1
```
🔹 `range(n)` generates numbers from `0` to `n-1`.

---

## **8️⃣ Functions in Python**
```python
def greet(name):
    return "Hello, " + name + "!"

print(greet("Alice"))
```
🔹 Functions are defined using the `def` keyword.  
🔹 The `return` statement sends a value back.

---

## **9️⃣ Lists (Arrays in Python)**
```python
fruits = ["Apple", "Banana", "Cherry"]
print(fruits[0])      # Access first element
fruits.append("Mango")  # Add new element
print(fruits)
```
🔹 Lists are **mutable** and can store mixed data types.

---

## **🔟 Tuples (Immutable List)**
```python
colors = ("Red", "Green", "Blue")
print(colors[1])  # Output: Green
```
🔹 Tuples **cannot be modified** after creation.

---

## **1️⃣1️⃣ Dictionaries (Key-Value Pairs)**
```python
student = {"name": "Alice", "age": 20, "grade": "A"}
print(student["name"])  # Output: Alice
```
🔹 Dictionaries allow fast lookup using keys.

---

## **1️⃣2️⃣ Classes & Objects (OOP in Python)**
```python
class Person:
    def __init__(self, name, age):  # Constructor
        self.name = name
        self.age = age

    def greet(self):
        return f"Hello, my name is {self.name}."

p1 = Person("Alice", 25)
print(p1.greet())  # Output: Hello, my name is Alice.
```
🔹 `__init__` is the **constructor method**.  
🔹 `self` refers to the current instance of the class.

---

## **1️⃣3️⃣ Exception Handling**
```python
try:
    num = int(input("Enter a number: "))
    result = 10 / num
except ZeroDivisionError:
    print("Cannot divide by zero!")
except ValueError:
    print("Invalid input! Please enter a number.")
finally:
    print("Execution completed.")
```
🔹 Use `try-except` to handle runtime errors.

---

## **1️⃣4️⃣ File Handling**
```python
with open("sample.txt", "w") as file:
    file.write("Hello, Python!")

with open("sample.txt", "r") as file:
    content = file.read()
    print(content)
```
🔹 `with open()` ensures automatic file closure.  
🔹 `"w"` mode writes, `"r"` reads, `"a"` appends.

---

## **1️⃣5️⃣ Lambda Functions**
```python
square = lambda x: x * x
print(square(5))  # Output: 25
```
🔹 **Anonymous functions** using `lambda`.

---

# **🎯 Summary**
| Concept         | Example |
|----------------|---------|
| Print Statement | `print("Hello, World!")` |
| Variables | `x = 10`, `name = "Alice"` |
| Data Types | `int, float, str, bool, list, tuple, dict` |
| If-Else | `if age > 18: print("Adult")` |
| Loops | `for i in range(5): print(i)` |
| Functions | `def greet(): return "Hello"` |
| Lists | `fruits.append("Mango")` |
| Dictionaries | `student["name"]` |
| Classes | `class Car:` |
| Exception Handling | `try-except-finally` |
| File Handling | `open("file.txt", "r")` |
| Lambda Function | `lambda x: x * x` |

---
