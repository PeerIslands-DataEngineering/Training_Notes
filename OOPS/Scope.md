# **Python Scope**

## **What is Scope in Python?**
Scope in Python refers to the **visibility** and **lifetime** of variables. It determines where a variable can be accessed and modified within a program.

Python has **four types of variable scopes**, often remembered using the **LEGB Rule**:
1. **Local Scope** – Variables defined inside a function.
2. **Enclosing Scope** – Variables in nested functions (non-local).
3. **Global Scope** – Variables declared at the top level of a script/module.
4. **Built-in Scope** – Predefined names from Python's built-in modules.

---

# **1. Local Scope (Function Scope)**
Variables declared inside a function belong to the **local scope** and are only accessible within that function.

### **Example: Local Scope**
```python
def my_function():
    x = 10  # Local variable
    print("Inside function:", x)

my_function()
# print(x)  # This would raise a NameError: x is not defined
```
**Output:**
```
Inside function: 10
```
**Explanation:**
- `x` is a **local variable** and only exists inside `my_function()`.
- Accessing `x` outside the function results in an error.

---

# **2. Enclosing Scope (Non-Local Scope)**
If a function is **nested inside another function**, it can access variables from the **enclosing function**.

### **Example: Enclosing Scope (Nested Functions)**
```python
def outer_function():
    outer_var = "I am outer"
    
    def inner_function():
        print(outer_var)  # Accessing the outer function's variable

    inner_function()

outer_function()
```
**Output:**
```
I am outer
```
**Explanation:**
- `inner_function()` **can access** `outer_var` because it's defined in the enclosing function.
- The variable follows **Enclosing (Non-Local) Scope**.

### **Modifying an Enclosing Variable Using `nonlocal`**
If you want to modify an **enclosing (non-local) variable**, use the `nonlocal` keyword.

```python
def outer():
    count = 0  # Enclosing variable
    
    def inner():
        nonlocal count  # Use the enclosing variable
        count += 1
        print("Inner Count:", count)

    inner()
    inner()

outer()
```
**Output:**
```
Inner Count: 1
Inner Count: 2
```
**Explanation:**
- Without `nonlocal`, modifying `count` inside `inner()` would create a new local variable.
- Using `nonlocal`, it **modifies the enclosing variable**.

---

# **3. Global Scope**
A **global variable** is declared outside of any function and can be accessed throughout the program.

### **Example: Global Scope**
```python
x = 100  # Global variable

def my_function():
    print("Inside function:", x)

my_function()
print("Outside function:", x)
```
**Output:**
```
Inside function: 100
Outside function: 100
```
**Explanation:**
- `x` is defined **outside the function**, so it is **globally accessible**.

### **Modifying a Global Variable Using `global`**
If you need to modify a **global variable** inside a function, use the `global` keyword.

```python
count = 0  # Global variable

def increment():
    global count  # Modify the global variable
    count += 1
    print("Inside function:", count)

increment()
print("Outside function:", count)
```
**Output:**
```
Inside function: 1
Outside function: 1
```
**Without `global`, modifying `count` inside the function would create a local variable instead.**

---

# **4. Built-in Scope**
Python has many **built-in functions and variables** that are available by default.

### **Example: Built-in Scope**
```python
print(len("Hello"))  # Built-in 'len' function
print(abs(-5))       # Built-in 'abs' function
```
**Output:**
```
5
5
```
### **Checking Built-in Functions**
To see all built-in names in Python:
```python
print(dir(__builtins__))
```

---

# **LEGB Rule (Scope Resolution Order)**
When Python encounters a variable, it searches in the following order:

1. **Local Scope** – Inside the current function.
2. **Enclosing Scope** – In the enclosing function (if any).
3. **Global Scope** – In the global (top-level) scope.
4. **Built-in Scope** – In Python’s built-in names.

### **Example of LEGB Rule**
```python
x = "global"  # Global variable

def outer():
    x = "enclosing"  # Enclosing variable

    def inner():
        x = "local"  # Local variable
        print(x)  # Local scope is used first

    inner()

outer()
```
**Output:**
```
local
```
**Explanation:**
- Python first looks for `x` in the **local scope** (inside `inner()`).
- If `x` wasn’t found in `inner()`, it would check the **enclosing scope** (`outer()`).
- If still not found, it would check the **global scope**.
- Finally, it would check the **built-in scope**.

---

# **5. Best Practices for Scope in Python**
### ✅ **Use Local Variables When Possible**
- Reduces unintended side effects.
- Improves code readability and maintainability.

### ✅ **Use `global` and `nonlocal` Sparingly**
- Modifying global variables inside functions can **lead to bugs**.
- Instead, **return values** from functions and assign them to global variables.

### ✅ **Avoid Using the Same Name for Global and Local Variables**
```python
x = 10  # Global variable

def example():
    x = 20  # Local variable (shadows global x)
    print(x)  # Output: 20

example()
print(x)  # Output: 10
```
This can be confusing! Always try to use distinct names for different scopes.

---

## **6. Advanced: Using `globals()` and `locals()`**
Python provides `globals()` and `locals()` functions to inspect and modify variables dynamically.

### **Example: Using `globals()`**
```python
x = 10

def change_global():
    globals()['x'] = 20  # Modify the global variable dynamically

change_global()
print(x)  # Output: 20
```

### **Example: Using `locals()`**
```python
def my_function():
    x = 5
    print(locals())  # Shows all local variables in the function

my_function()
```
**Output:**
```
{'x': 5}
```

---

# **Summary Table**
| **Scope**      | **Where Defined?** | **Where Accessible?** | **Keyword to Modify** |
|---------------|------------------|---------------------|-------------------|
| **Local**     | Inside function  | Only inside that function | N/A |
| **Enclosing** | Inside outer function | Inner (nested) functions | `nonlocal` |
| **Global**    | Outside functions | Anywhere in the program | `global` |
| **Built-in**  | Python's built-in module | Everywhere | N/A |

---

# **Conclusion**
- Python follows the **LEGB rule** for resolving variable names.
- **Local variables** are preferred to avoid unintended modifications.
- **Use `global` and `nonlocal` carefully** to avoid scope-related bugs.
- **Built-in functions** are always available, but avoid shadowing them with your own variables.
