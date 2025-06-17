Python provides **many built-in functions** to perform operations efficiently without requiring external libraries. Let's explore them in **detail** with **examples**! 🚀  

---

# **📌 1. Python Built-in Functions Categories**
Python's built-in functions can be categorized into:  
1️⃣ **Type Conversion Functions** (`int()`, `float()`, `str()`, `bool()`, etc.)  
2️⃣ **Mathematical Functions** (`abs()`, `pow()`, `round()`, etc.)  
3️⃣ **Sequence & Collection Functions** (`len()`, `max()`, `min()`, `sum()`, `sorted()`, etc.)  
4️⃣ **String Functions** (`ord()`, `chr()`, `format()`, etc.)  
5️⃣ **Input & Output Functions** (`print()`, `input()`)  
6️⃣ **Iterators & Loop Functions** (`enumerate()`, `zip()`, `map()`, `filter()`, etc.)  
7️⃣ **Object & Variable Functions** (`id()`, `type()`, `dir()`, `isinstance()`, etc.)  

---

# **🔹 1. Type Conversion Functions**
Used to convert values from one type to another.  

| Function | Description | Example |
|----------|------------|---------|
| `int(x)` | Converts `x` to an integer | `int("10") → 10` |
| `float(x)` | Converts `x` to a float | `float("3.14") → 3.14` |
| `str(x)` | Converts `x` to a string | `str(100) → "100"` |
| `bool(x)` | Converts `x` to boolean | `bool(0) → False` |
| `list(x)` | Converts `x` to a list | `list("abc") → ['a', 'b', 'c']` |
| `tuple(x)` | Converts `x` to a tuple | `tuple([1,2,3]) → (1,2,3)` |
| `set(x)` | Converts `x` to a set | `set([1,2,2,3]) → {1,2,3}` |

```python
x = "123"
print(int(x))  # Output: 123
print(float(x))  # Output: 123.0
print(bool(""))  # Output: False
print(list("hello"))  # Output: ['h', 'e', 'l', 'l', 'o']
```

---

# **🔹 2. Mathematical Functions**
Perform mathematical operations.  

| Function | Description | Example |
|----------|------------|---------|
| `abs(x)` | Absolute value | `abs(-10) → 10` |
| `pow(x, y)` | `x` raised to power `y` | `pow(2, 3) → 8` |
| `round(x, n)` | Rounds `x` to `n` decimals | `round(3.14159, 2) → 3.14` |
| `min(iterable)` | Minimum value | `min([5, 1, 8]) → 1` |
| `max(iterable)` | Maximum value | `max([5, 1, 8]) → 8` |
| `sum(iterable)` | Sum of elements | `sum([1, 2, 3]) → 6` |

```python
print(abs(-7))  # Output: 7
print(pow(2, 5))  # Output: 32
print(round(3.14159, 3))  # Output: 3.142
print(min(10, 20, 5, 8))  # Output: 5
print(sum([1, 2, 3, 4, 5]))  # Output: 15
```

---

# **🔹 3. Sequence & Collection Functions**
Work with lists, tuples, dictionaries, and sets.  

| Function | Description | Example |
|----------|------------|---------|
| `len(iterable)` | Length of iterable | `len("hello") → 5` |
| `sorted(iterable)` | Sorts elements | `sorted([3, 1, 2]) → [1, 2, 3]` |
| `reversed(iterable)` | Reverses elements | `list(reversed([1,2,3])) → [3,2,1]` |
| `enumerate(iterable)` | Adds index to elements | `enumerate(["a", "b"]) → (0, "a")` |
| `zip(iter1, iter2)` | Combines iterables | `zip([1,2], ["a", "b"]) → [(1, "a"), (2, "b")]` |

```python
fruits = ["apple", "banana", "cherry"]
print(len(fruits))  # Output: 3
print(sorted([3, 1, 2]))  # Output: [1, 2, 3]
print(list(reversed(fruits)))  # Output: ['cherry', 'banana', 'apple']
```

---

# **🔹 4. String Functions**
Work with characters and Unicode values.  

| Function | Description | Example |
|----------|------------|---------|
| `ord(char)` | Unicode of character | `ord("A") → 65` |
| `chr(int)` | Character from Unicode | `chr(65) → "A"` |
| `format(value, spec)` | Formats value | `format(1000, ",") → "1,000"` |

```python
print(ord('A'))  # Output: 65
print(chr(97))  # Output: 'a'
print(format(10000, ","))  # Output: 10,000
```

---

# **🔹 5. Input & Output Functions**
Handle user input and output.  

| Function | Description | Example |
|----------|------------|---------|
| `print()` | Outputs text | `print("Hello")` |
| `input()` | Takes user input | `name = input("Enter name: ")` |

```python
name = input("Enter your name: ")
print("Hello,", name)
```

---

# **🔹 6. Iterators & Loop Functions**
Useful for loops and iteration.  

| Function | Description | Example |
|----------|------------|---------|
| `map(func, iterable)` | Applies function to elements | `map(str.upper, ["a", "b"]) → ["A", "B"]` |
| `filter(func, iterable)` | Filters elements based on function | `filter(lambda x: x>5, [1,6,8]) → [6,8]` |

```python
numbers = [1, 2, 3, 4]
squared = list(map(lambda x: x**2, numbers))
print(squared)  # Output: [1, 4, 9, 16]

filtered = list(filter(lambda x: x % 2 == 0, numbers))
print(filtered)  # Output: [2, 4]
```

---

# **🔹 7. Object & Variable Functions**
Used for working with variables and objects.  

| Function | Description | Example |
|----------|------------|---------|
| `type(x)` | Returns type of `x` | `type(10) → <class 'int'>` |
| `id(x)` | Returns memory ID of `x` | `id(10) → 140734342345344` |
| `dir(obj)` | Returns attributes of object | `dir(str)` |
| `isinstance(x, type)` | Checks if `x` is of type | `isinstance(10, int) → True` |

```python
x = 100
print(type(x))  # Output: <class 'int'>
print(id(x))  # Output: Memory location of x
print(isinstance(x, int))  # Output: True
```

---

# **🚀 Summary**
Python's built-in functions make programming **easier and more efficient**. Mastering them will help you write cleaner and faster code!  
