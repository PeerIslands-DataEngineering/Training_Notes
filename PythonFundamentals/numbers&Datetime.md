Python provides powerful support for **numeric operations** and **date/time manipulations** using built-in features and the `datetime` module. Let’s explore them in **detail** with examples! 🚀  

---

# **📌 1. Python Numbers**
Python supports multiple numeric data types:  
- **Integers (`int`)**: Whole numbers (e.g., `10, -5, 1000`)  
- **Floating-point (`float`)**: Decimal numbers (e.g., `3.14, -0.5, 2.718`)  
- **Complex (`complex`)**: Numbers with real and imaginary parts (e.g., `3 + 4j`)  

## **🔹 Type Checking & Conversion**
```python
a = 10   # Integer
b = 3.14 # Float
c = 2 + 3j # Complex

print(type(a))  # Output: <class 'int'>
print(type(b))  # Output: <class 'float'>
print(type(c))  # Output: <class 'complex'>

# Type Conversion
print(float(a))  # Convert int to float → 10.0
print(int(b))    # Convert float to int → 3
print(complex(a)) # Convert int to complex → (10+0j)
```

---

## **🔹 Mathematical Functions**
Python provides **built-in math functions** for calculations.

| Function | Description | Example |
|----------|------------|---------|
| `abs(x)` | Absolute value | `abs(-10) → 10` |
| `pow(x, y)` | `x` raised to power `y` | `pow(2, 3) → 8` |
| `round(x, n)` | Rounds `x` to `n` decimal places | `round(3.14159, 2) → 3.14` |
| `max(iterable)` | Largest value | `max([1, 5, 3]) → 5` |
| `min(iterable)` | Smallest value | `min([1, 5, 3]) → 1` |
| `sum(iterable)` | Sum of values | `sum([1, 2, 3]) → 6` |

```python
print(abs(-7))  # Output: 7
print(pow(2, 5))  # Output: 32
print(round(3.14159, 3))  # Output: 3.142
print(max(10, 20, 5, 8))  # Output: 20
print(sum([1, 2, 3, 4, 5]))  # Output: 15
```

---

## **🔹 Using the `math` Module**
For advanced mathematical functions, import the `math` module.

```python
import math

print(math.sqrt(16))  # Output: 4.0
print(math.factorial(5))  # Output: 120
print(math.log10(100))  # Output: 2.0
print(math.ceil(3.4))  # Output: 4 (Round up)
print(math.floor(3.9))  # Output: 3 (Round down)
print(math.pi)  # Output: 3.141592653589793
```

---

## **🔹 Random Numbers using `random` Module**
Python's `random` module generates **random numbers**.

```python
import random

print(random.randint(1, 10))  # Random integer between 1 and 10
print(random.uniform(1, 5))  # Random float between 1 and 5
print(random.choice(["apple", "banana", "cherry"]))  # Random element
```

---

# **📌 2. Python Date & Time (`datetime` module)**
Python provides the `datetime` module for handling **dates and times**.

```python
import datetime

now = datetime.datetime.now()  # Current date & time
print(now)  # Output: 2025-02-27 14:30:45.123456
print(now.date())  # Output: 2025-02-27
print(now.time())  # Output: 14:30:45.123456
print(now.year, now.month, now.day)  # Output: 2025 2 27
```

---

## **🔹 Creating Specific Dates**
```python
custom_date = datetime.date(2025, 12, 25)  # Year, Month, Day
print(custom_date)  # Output: 2025-12-25
```

## **🔹 Formatting Dates (`strftime`)**
| Directive | Description | Example Output |
|-----------|------------|---------------|
| `%Y` | Full year | `2025` |
| `%m` | Month (01-12) | `02` |
| `%d` | Day (01-31) | `27` |
| `%H` | Hour (00-23) | `14` |
| `%M` | Minute (00-59) | `30` |
| `%S` | Second (00-59) | `45` |

```python
formatted_date = now.strftime("%Y-%m-%d %H:%M:%S")
print(formatted_date)  # Output: 2025-02-27 14:30:45
```

---

## **🔹 Parsing Strings to Date (`strptime`)**
```python
date_string = "2025-02-27"
parsed_date = datetime.datetime.strptime(date_string, "%Y-%m-%d")
print(parsed_date)  # Output: 2025-02-27 00:00:00
```

---

## **🔹 Date Arithmetic (Adding/Subtracting Days)**
```python
from datetime import timedelta

today = datetime.date.today()
tomorrow = today + timedelta(days=1)
yesterday = today - timedelta(days=1)

print("Today:", today)
print("Tomorrow:", tomorrow)
print("Yesterday:", yesterday)
```

---

## **🔹 Comparing Dates**
```python
date1 = datetime.date(2025, 2, 27)
date2 = datetime.date(2024, 2, 27)

if date1 > date2:
    print("date1 is after date2")
else:
    print("date1 is before date2")
```

---

## **🔹 Getting the Current Timezone**
```python
import pytz  # External module

utc_time = datetime.datetime.now(pytz.utc)
india_time = utc_time.astimezone(pytz.timezone("Asia/Kolkata"))

print("UTC Time:", utc_time)
print("India Time:", india_time)
```

---

# **🚀 Summary**
Python provides **powerful tools** for handling **numbers and dates/times**.  
✔ **Numeric operations** using built-in math functions and `math` module.  
✔ **Random numbers** using `random` module.  
✔ **Date and time handling** using `datetime` and `pytz` for timezones.  
