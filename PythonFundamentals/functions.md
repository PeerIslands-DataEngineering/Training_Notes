
## **1. What is a Function?**
A function is a reusable block of code that performs a specific task. Functions help break down large problems into smaller, manageable parts.

### **Types of Functions in Python**
1. **Built-in Functions** (e.g., `len()`, `print()`, `max()`)
2. **User-defined Functions** (functions defined by users)
3. **Lambda Functions** (anonymous, one-line functions)
4. **Recursive Functions** (functions that call themselves)
5. **Higher-order Functions** (functions that take other functions as arguments)

---

## **2. Defining and Calling Functions**
### **Syntax:**
```python
def function_name(parameters):
    """Optional Docstring"""
    # Function body
    return value  # Optional
```

### **Example 1: Simple Function**
```python
def greet():
    print("Hello, welcome to Python!")

greet()  # Output: Hello, welcome to Python!
```

---

## **3. Function Parameters and Arguments**
Parameters allow functions to accept values when called.

### **Example 2: Function with Parameters**
```python
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")  # Output: Hello, Alice!
```

### **Types of Arguments in Python**
1. **Positional Arguments**
2. **Keyword Arguments**
3. **Default Arguments**
4. **Variable-Length Arguments (`*args`, `**kwargs`)**

---

### **Example 3: Positional and Keyword Arguments**
```python
def person_info(name, age):
    print(f"Name: {name}, Age: {age}")

# Positional arguments
person_info("Alice", 25)  

# Keyword arguments
person_info(age=30, name="Bob")  
```
✔ **Positional arguments** must match the function parameter order.  
✔ **Keyword arguments** allow flexibility in passing values.

---

### **Example 4: Default Arguments**
```python
def greet(name="Guest"):
    print(f"Hello, {name}!")

greet()  # Output: Hello, Guest!
greet("Charlie")  # Output: Hello, Charlie!
```
✔ If no value is passed, the default value is used.

---

### **Example 5: Variable-Length Arguments (`*args`, `**kwargs`)**
```python
# *args: Accepts multiple positional arguments as a tuple
def sum_numbers(*args):
    return sum(args)

print(sum_numbers(10, 20, 30))  # Output: 60

# **kwargs: Accepts multiple keyword arguments as a dictionary
def person_details(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

person_details(name="Alice", age=25, city="New York")
```
✔ `*args` is used when we don't know the number of arguments.  
✔ `**kwargs` is used for handling multiple named arguments.

---

## **4. Return Statement**
Functions can return values using `return`.

### **Example 6: Function Returning a Value**
```python
def add(a, b):
    return a + b

result = add(5, 7)
print(result)  # Output: 12
```

---

## **5. Lambda Functions (Anonymous Functions)**
Lambda functions are one-liner functions without a `def` keyword.

### **Example 7: Lambda Function**
```python
add = lambda x, y: x + y
print(add(3, 4))  # Output: 7
```
✔ Best for short, simple operations.

---

## **6. Function Scope and Global vs. Local Variables**
### **Example 8: Local and Global Scope**
```python
x = 10  # Global variable

def my_function():
    x = 5  # Local variable
    print("Inside function:", x)

my_function()  # Output: Inside function: 5
print("Outside function:", x)  # Output: Outside function: 10
```
✔ **Local variables** exist inside functions.  
✔ **Global variables** exist throughout the program.

---

### **Example 9: Using `global` Keyword**
```python
x = 10

def modify_global():
    global x
    x = 20
    print("Inside function:", x)

modify_global()
print("Outside function:", x)  # Output: 20
```
✔ `global` allows modifying a global variable inside a function.

---

## **7. Recursive Functions**
A function that calls itself is called **recursion**.

### **Example 10: Recursive Function for Factorial**
```python
def factorial(n):
    if n == 1:
        return 1
    return n * factorial(n - 1)

print(factorial(5))  # Output: 120
```
✔ Recursive functions are useful for solving problems like factorial, Fibonacci series, and tree traversals.

---

## **8. Higher-Order Functions**
A function that **takes another function as an argument** or **returns a function** is called a **higher-order function**.

### **Example 11: Using `map()`**
```python
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x ** 2, numbers))
print(squared)  # Output: [1, 4, 9, 16, 25]
```

### **Example 12: Using `filter()`**
```python
numbers = [10, 15, 20, 25, 30]
filtered = list(filter(lambda x: x % 2 == 0, numbers))
print(filtered)  # Output: [10, 20, 30]
```
✔ `map()` applies a function to all elements.  
✔ `filter()` selects elements based on a condition.

---

## **9. Function Decorators**
Decorators modify the behavior of functions.

### **Example 13: Function Decorator**
```python
def decorator(func):
    def wrapper():
        print("Before function call")
        func()
        print("After function call")
    return wrapper

@decorator
def say_hello():
    print("Hello!")

say_hello()
```
✔ `@decorator` modifies the function behavior.

---

## **10. Function Annotations (Type Hints)**
Python allows type hints for better readability.

### **Example 14: Function Type Hints**
```python
def add(x: int, y: int) -> int:
    return x + y

print(add(3, 5))  # Output: 8
```
✔ Helps with code clarity but does **not enforce types**.

---

## **Summary**
| Feature | Description |
|---------|-------------|
| Function Definition | `def func_name():` |
| Parameters | Accepts values (positional, keyword, default, variable-length) |
| `return` | Returns values |
| Lambda Functions | One-line anonymous functions |
| Scope | Local vs. Global |
| Recursion | Function calls itself |
| Higher-Order Functions | Takes or returns another function (`map()`, `filter()`) |
| Decorators | Modify function behavior |
| Type Hints | Improves readability |

Python functions make code **modular**, **efficient**, and **reusable**.  
