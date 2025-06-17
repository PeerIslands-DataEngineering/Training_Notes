Python provides **inbuilt functions** to work with **strings, lists, tuples, and sets** efficiently. Let's explore them in detail! 🚀  

---

# **📌 1. Python Strings (`str`)**  
A string in Python is an **immutable sequence of characters** enclosed in quotes (`' '` or `" "`).  

## **🔹 Creating Strings**
```python
text = "Hello, Python!"
print(text)
```

## **🔹 Common String Methods**
| Method | Description | Example |
|--------|------------|---------|
| `len()` | Returns length of the string | `len("Python") → 6` |
| `lower()` | Converts to lowercase | `"HELLO".lower() → "hello"` |
| `upper()` | Converts to uppercase | `"hello".upper() → "HELLO"` |
| `strip()` | Removes leading & trailing spaces | `"  hello  ".strip() → "hello"` |
| `replace(old, new)` | Replaces occurrences | `"hello".replace("h", "H") → "Hello"` |
| `split(sep)` | Splits into a list | `"a,b,c".split(",") → ['a', 'b', 'c']` |
| `join(iterable)` | Joins elements with a separator | `" ".join(['Hello', 'World']) → "Hello World"` |
| `find(sub)` | Returns first occurrence index | `"Python".find("y") → 1` |
| `count(sub)` | Counts occurrences | `"apple".count("p") → 2` |
| `startswith(sub)` | Checks if it starts with a substring | `"hello".startswith("h") → True` |
| `endswith(sub)` | Checks if it ends with a substring | `"hello".endswith("o") → True` |

### **🔹 String Slicing**
```python
text = "Python"
print(text[0:3])  # Output: Pyt
print(text[-3:])  # Output: hon
```

---

# **📌 2. Python Lists (`list`)**  
Lists are **ordered, mutable**, and can contain **mixed data types**.  

## **🔹 Creating Lists**
```python
fruits = ["apple", "banana", "cherry"]
numbers = [1, 2, 3, 4]
```

## **🔹 Common List Methods**
| Method | Description | Example |
|--------|------------|---------|
| `len(list)` | Returns length of the list | `len([1,2,3]) → 3` |
| `append(value)` | Adds an item to the end | `fruits.append("mango")` |
| `insert(index, value)` | Inserts at a specific index | `fruits.insert(1, "grape")` |
| `remove(value)` | Removes the first occurrence | `fruits.remove("apple")` |
| `pop(index)` | Removes & returns an element | `fruits.pop(2)` |
| `index(value)` | Returns first occurrence index | `fruits.index("banana")` |
| `count(value)` | Counts occurrences | `fruits.count("apple")` |
| `sort()` | Sorts in ascending order | `numbers.sort()` |
| `reverse()` | Reverses list order | `numbers.reverse()` |
| `extend(iterable)` | Extends list with elements | `fruits.extend(["pear", "orange"])` |

### **🔹 List Slicing**
```python
numbers = [10, 20, 30, 40, 50]
print(numbers[1:4])  # Output: [20, 30, 40]
```

---

# **📌 3. Python Tuples (`tuple`)**  
Tuples are **ordered, immutable**, and **faster than lists**.

## **🔹 Creating Tuples**
```python
colors = ("red", "green", "blue")
```

## **🔹 Common Tuple Methods**
| Method | Description | Example |
|--------|------------|---------|
| `len(tuple)` | Returns length of the tuple | `len((1,2,3)) → 3` |
| `index(value)` | Returns first occurrence index | `colors.index("red")` |
| `count(value)` | Counts occurrences | `colors.count("blue")` |

### **🔹 Tuple Slicing**
```python
numbers = (10, 20, 30, 40, 50)
print(numbers[:3])  # Output: (10, 20, 30)
```

---

# **📌 4. Python Sets (`set`)**  
Sets are **unordered, mutable, and contain only unique values**.  

## **🔹 Creating Sets**
```python
unique_numbers = {1, 2, 3, 4, 4, 2}
print(unique_numbers)  # Output: {1, 2, 3, 4}
```

## **🔹 Common Set Methods**
| Method | Description | Example |
|--------|------------|---------|
| `add(value)` | Adds an element | `s.add(5)` |
| `remove(value)` | Removes an element (error if not found) | `s.remove(3)` |
| `discard(value)` | Removes element (no error if absent) | `s.discard(3)` |
| `pop()` | Removes a random element | `s.pop()` |
| `clear()` | Removes all elements | `s.clear()` |
| `union(set2)` | Returns union of sets | `s1.union(s2)` |
| `intersection(set2)` | Returns common elements | `s1.intersection(s2)` |
| `difference(set2)` | Returns difference (s1 - s2) | `s1.difference(s2)` |
| `issubset(set2)` | Checks if subset | `s1.issubset(s2)` |

### **🔹 Set Operations**
```python
A = {1, 2, 3, 4}
B = {3, 4, 5, 6}
print(A | B)  # Union: {1, 2, 3, 4, 5, 6}
print(A & B)  # Intersection: {3, 4}
print(A - B)  # Difference: {1, 2}
```

---

# **🚀 Summary Table**
| Data Structure | Features | Mutable | Ordered | Allows Duplicates |
|---------------|----------|---------|---------|------------------|
| **String (`str`)** | Sequence of characters | ❌ No | ✅ Yes | ✅ Yes |
| **List (`list`)** | Ordered collection | ✅ Yes | ✅ Yes | ✅ Yes |
| **Tuple (`tuple`)** | Immutable list | ❌ No | ✅ Yes | ✅ Yes |
| **Set (`set`)** | Unordered unique elements | ✅ Yes | ❌ No | ❌ No |

---
