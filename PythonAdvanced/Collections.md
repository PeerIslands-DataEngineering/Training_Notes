# **Python Collections Module**  
The **collections** module in Python provides specialized container data types that extend Python's built-in types (`list`, `dict`, `set`, `tuple`). These data structures improve efficiency, code readability, and provide additional functionality.

---

## **1. Counter (Counting Hashable Objects)**
The `Counter` class is a dictionary subclass used for counting elements in an iterable.

### **Key Methods**
| Method | Description |
|---------|------------|
| `Counter(iterable)` | Creates a Counter object from an iterable |
| `.most_common(n)` | Returns the `n` most common elements |
| `.elements()` | Returns an iterator over elements repeating as per their counts |
| `.subtract(iterable)` | Subtracts element counts like a multiset |
| `.update(iterable)` | Adds counts from another iterable |

### **Example**
```python
from collections import Counter

# Creating a counter
word_counts = Counter("chai shop chai shop metro")
print(word_counts)  
# Output: Counter({' ': 4, 'c': 2, 'h': 2, 'a': 2, 'i': 2, 's': 2, 'o': 2, 'p': 2, 'm': 1, 'e': 1, 't': 1, 'r': 1})

# Using most_common()
print(word_counts.most_common(2))  
# Output: [(' ', 4), ('c', 2)]

# Using update()
word_counts.update("train station")
print(word_counts["t"])  # Increased count

# Using subtract()
word_counts.subtract("chai")
print(word_counts["c"])  # Reduced count
```
---

## **2. defaultdict (Handling Missing Keys Gracefully)**
`defaultdict` extends `dict` by returning a **default value** when accessing a missing key.

### **Key Methods**
| Method | Description |
|---------|------------|
| `defaultdict(default_factory)` | Creates a dictionary with a default value type |
| `.default_factory` | Function returning default value (e.g., `int`, `list`) |

### **Example**
```python
from collections import defaultdict

# Default value as an integer (0)
int_dict = defaultdict(int)
int_dict["chai"] += 1
print(int_dict["chai"])  # Output: 1

# Default value as a list
list_dict = defaultdict(list)
list_dict["metro"].append("Chennai")
print(list_dict)  # Output: {'metro': ['Chennai']}
```
---

## **3. OrderedDict (Dictionary with Order Preservation)**
Maintains the **insertion order** of keys (since Python 3.7, regular `dict` does this too).

### **Key Methods**
| Method | Description |
|---------|------------|
| `.move_to_end(key, last=True)` | Moves key to end or beginning |
| `.popitem(last=True)` | Removes and returns last (or first) item |

### **Example**
```python
from collections import OrderedDict

# Ordered dictionary
ordered = OrderedDict()
ordered["chai"] = 10
ordered["shop"] = 20
ordered["metro"] = 30
print(ordered)  # Order is preserved

# Move "chai" to end
ordered.move_to_end("chai")
print(ordered)

# Remove last item
ordered.popitem()
print(ordered)
```
---

## **4. ChainMap (Multiple Dicts Merged as One)**
`ChainMap` allows **combining multiple dictionaries** while maintaining individual dictionary structures.

### **Key Methods**
| Method | Description |
|---------|------------|
| `ChainMap(dict1, dict2, ...)` | Creates a ChainMap with multiple dictionaries |
| `.maps` | Returns the list of dictionaries |
| `.parents` | Returns a new ChainMap excluding the first dictionary |
| `.new_child()` | Adds a new dictionary layer |

### **Example**
```python
from collections import ChainMap

branch_a = {"tea": 5, "sugar": 10}
branch_b = {"milk": 8, "masala": 6}
inventory = ChainMap(branch_a, branch_b)

# Lookup prioritizes first dict
print(inventory["tea"])  # 5 (from branch_a)

# Adding new layer
new_layer = inventory.new_child({"chai": 15})
print(new_layer["chai"])  # 15
```
---

## **5. deque (Fast Append/Pop Operations)**
`deque` (double-ended queue) provides **O(1) time complexity** for append and pop operations.

### **Key Methods**
| Method | Description |
|---------|------------|
| `.append(x)` | Adds to the right end |
| `.appendleft(x)` | Adds to the left end |
| `.pop()` | Removes from the right end |
| `.popleft()` | Removes from the left end |
| `.extend(iterable)` | Adds multiple items to the right |
| `.extendleft(iterable)` | Adds multiple items to the left (reversed) |
| `.rotate(n)` | Rotates the deque by `n` steps |
| `.reverse()` | Reverses the deque |

### **Example**
```python
from collections import deque

queue = deque(["chai", "shop", "metro"])

# Append
queue.append("train")
print(queue)  # deque(['chai', 'shop', 'metro', 'train'])

# Pop left
queue.popleft()
print(queue)  # deque(['shop', 'metro', 'train'])

# Rotate
queue.rotate(1)
print(queue)  # deque(['train', 'shop', 'metro'])
```
---

## **6. namedtuple (Immutable Lightweight Objects)**
A `namedtuple` is an immutable, lightweight alternative to a **class**. It makes tuples more readable by assigning field names.

### **Key Methods**
| Method | Description |
|---------|------------|
| `namedtuple("TypeName", ["field1", "field2"])` | Creates a named tuple class |
| `.fieldname` | Accesses field values |
| `.asdict()` | Converts namedtuple to a dictionary |
| `_replace(**kwargs)` | Returns a new namedtuple with modified values |

### **Example**
```python
from collections import namedtuple

# Define a named tuple
MetroStation = namedtuple("MetroStation", ["name", "zone", "platforms"])

# Create an instance
station1 = MetroStation(name="Central", zone=1, platforms=4)

print(station1.name)  # Central
print(station1.zone)  # 1

# Convert to dictionary
print(station1._asdict())  

# Replace a field value
station2 = station1._replace(platforms=5)
print(station2)
```
---

## **7. UserDict, UserList, UserString (Custom Wrapper Classes)**
These classes allow **subclassing built-in types** to modify behavior.

### **UserDict (Custom Dict Wrapper)**
```python
from collections import UserDict

class MetroCard(UserDict):
    def __init__(self, balance=0):
        super().__init__()
        self.data["balance"] = balance

    def deduct_fare(self, amount):
        if self.data["balance"] >= amount:
            self.data["balance"] -= amount
        else:
            print("Insufficient balance!")

card = MetroCard(100)
card.deduct_fare(30)
print(card["balance"])  # 70
```

### **UserList (Custom List Wrapper)**
```python
from collections import UserList

class PositiveList(UserList):
    def append(self, item):
        if item < 0:
            raise ValueError("Only positive numbers allowed")
        super().append(item)

pl = PositiveList()
pl.append(5)
pl.append(-1)  # Raises ValueError
```

### **UserString (Custom String Wrapper)**
```python
from collections import UserString

class TalkingPoster(UserString):
    def reverse_speak(self):
        return self[::-1]

poster = TalkingPoster("Chennai")
print(poster.reverse_speak())  # iannehC
```
---

## **Summary Table**
| Collection | Use Case |
|------------|----------|
| `Counter` | Counting occurrences |
| `defaultdict` | Handling missing keys |
| `OrderedDict` | Maintaining key order |
| `ChainMap` | Combining multiple dicts |
| `deque` | Fast queue operations |
| `namedtuple` | Immutable object representation |
| `UserDict` | Custom dictionary behavior |
| `UserList` | Custom list behavior |
| `UserString` | Custom string behavior |

---
