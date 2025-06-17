
---

# **📌 Python Regular Expressions🧩**

## **What is Regex?**  
A **regular expression (regex)** is a **sequence of characters** that defines a search pattern. It is commonly used for:  
- **Searching** for specific patterns in a string  
- **Extracting** information from text  
- **Replacing** text based on a pattern  
- **Validating** input (like emails, phone numbers, etc.)

Regex can be **very powerful**, but it may seem a bit cryptic at first. Let's break it down step by step with examples!

---

## **📌 1. Importing the `re` Module 🧰**  
In Python, **regex operations** are handled by the built-in `re` module. First, let's import it.

```python
import re
```

---

## **📌 2. Basic Regex Functions 🛠️**  
The `re` module provides several functions for working with regular expressions.

### **🔹 `re.match(pattern, string)`**
The `match()` function checks if the **pattern** matches at the **beginning of the string**.

```python
import re
result = re.match(r"Hello", "Hello, world!")
if result:
    print("Match found!")
else:
    print("No match!")
```
**Output:**  
```
Match found!
```

### **🔹 `re.search(pattern, string)`**
The `search()` function searches for the **first occurrence of the pattern** anywhere in the string.

```python
result = re.search(r"world", "Hello, world!")
if result:
    print("Match found!")
else:
    print("No match!")
```
**Output:**  
```
Match found!
```

### **🔹 `re.findall(pattern, string)`**
The `findall()` function finds **all occurrences** of the pattern in the string and returns them as a list.

```python
result = re.findall(r"o", "Hello, world!")
print(result)
```
**Output:**  
```
['o', 'o']
```

### **🔹 `re.sub(pattern, replacement, string)`**
The `sub()` function **replaces** occurrences of the pattern with a specified **replacement**.

```python
result = re.sub(r"world", "Python", "Hello, world!")
print(result)
```
**Output:**  
```
Hello, Python!
```

---

## **📌 3. Regular Expression Syntax 📝**  
Now, let's look at some of the common **regex patterns** and their meanings:

### **🔹 Special Characters:**
- **`.`**: Matches any character except a newline.
- **`^`**: Matches the beginning of the string.
- **`$`**: Matches the end of the string.
- **`*`**: Matches 0 or more repetitions of the preceding character.
- **`+`**: Matches 1 or more repetitions of the preceding character.
- **`?`**: Matches 0 or 1 repetition of the preceding character.
- **`{n}`**: Matches exactly n repetitions of the preceding character.
- **`{n,}`**: Matches n or more repetitions of the preceding character.
- **`{n,m}`**: Matches between n and m repetitions of the preceding character.
- **`[]`**: Matches any single character within the brackets (range of characters).
- **`|`**: OR operator (either of two patterns).
- **`()`**: Grouping, used for capturing specific parts of the match.

### **🔹 Examples:**

#### 1. **Matching Digits**  
To match one or more digits, you can use `\d`:
```python
result = re.findall(r"\d", "The year is 2023")
print(result)
```
**Output:**  
```
['2', '0', '2', '3']
```

#### 2. **Matching Letters**
To match any letter, use `[a-zA-Z]`:
```python
result = re.findall(r"[a-zA-Z]", "Hello 123")
print(result)
```
**Output:**  
```
['H', 'e', 'l', 'l', 'o']
```

#### 3. **Matching Email Address**
Let's create a simple regex pattern to match an email address:
```python
result = re.match(r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}", "test@example.com")
if result:
    print("Valid email!")
else:
    print("Invalid email!")
```
**Output:**  
```
Valid email!
```

#### 4. **Matching Phone Numbers**  
To match a simple phone number format like `(xxx) xxx-xxxx`:
```python
result = re.match(r"\(\d{3}\) \d{3}-\d{4}", "(123) 456-7890")
if result:
    print("Valid phone number!")
else:
    print("Invalid phone number!")
```
**Output:**  
```
Valid phone number!
```

---

## **📌 4. Using Groups in Regex 🔒**  
Grouping allows us to **capture specific portions of the matched string**.

### **Example: Capturing Name and Age**
```python
text = "Alice is 30 years old"
result = re.match(r"([A-Za-z]+) is (\d+) years old", text)
if result:
    print("Name:", result.group(1))  # Group 1 (Name)
    print("Age:", result.group(2))   # Group 2 (Age)
```
**Output:**  
```
Name: Alice
Age: 30
```

---

## **📌 5. Regex Flags ⚡**

### **🔹 `re.IGNORECASE` or `re.I`**  
Matches case-insensitive patterns.
```python
result = re.search(r"hello", "Hello, World!", re.IGNORECASE)
if result:
    print("Match found!")
```

### **🔹 `re.MULTILINE` or `re.M`**  
Matches the pattern across multiple lines (e.g., `^` and `$` will match the start and end of each line).
```python
text = "Line 1\nLine 2"
result = re.findall(r"^Line", text, re.MULTILINE)
print(result)
```
**Output:**  
```
['Line', 'Line']
```

### **🔹 `re.DOTALL` or `re.S`**  
Allows the dot (`.`) to match newline characters as well.
```python
text = "Hello\nworld!"
result = re.findall(r"Hello.world!", text, re.DOTALL)
print(result)
```
**Output:**  
```
['Hello\nworld!']
```

---

## **📌 6. Practical Examples 🛠️**

### **🔹 Validate a Date Format (DD/MM/YYYY)**  
Let's validate if a date is in the format `dd/mm/yyyy`.
```python
def validate_date(date):
    pattern = r"^\d{2}/\d{2}/\d{4}$"
    if re.match(pattern, date):
        print("Valid date!")
    else:
        print("Invalid date!")
```
```python
validate_date("12/05/2023")  # Valid date!
validate_date("32/12/2022")  # Invalid date!
```

### **🔹 Extract URLs from Text**
You can use regex to extract **URLs** from a string:
```python
text = "Visit our website at http://www.example.com or https://www.testsite.org."
urls = re.findall(r"https?://[a-zA-Z0-9.-]+(?:/[a-zA-Z0-9/-]*)?", text)
print(urls)
```
**Output:**  
```
['http://www.example.com', 'https://www.testsite.org']
```

---

## **📌 7. Summary 📝**
Regular Expressions in Python are a **powerful tool** for text manipulation and pattern matching. Key concepts covered include:
- **Basic functions**: `match()`, `search()`, `findall()`, `sub()`
- **Regex syntax**: Special characters, quantifiers, character sets
- **Groups**: Capturing portions of matched text
- **Flags**: Case-insensitive matching, multi-line matching, etc.
- **Practical examples**: Email validation, phone number matching, extracting URLs

---

## **🔹 What's Next?**
- Try experimenting with more complex patterns.  
- Use **regex to validate user input** (e.g., phone numbers, credit card numbers).  
- Practice writing regex for parsing structured data (CSV, logs, etc.).
