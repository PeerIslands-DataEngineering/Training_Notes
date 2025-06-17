# **Python Encapsulation**

## **What is Encapsulation?**
Encapsulation is one of the four fundamental concepts of **Object-Oriented Programming (OOP)**, alongside **Abstraction, Inheritance, and Polymorphism**.

**Encapsulation** is the technique of **hiding implementation details** and **restricting direct access** to object data. Instead of exposing attributes directly, access is provided through methods (getters and setters).

### **Key Benefits of Encapsulation:**
- **Data Protection**: Prevents unintended modification of data.
- **Code Reusability**: Allows controlled data access.
- **Improves Maintainability**: Changes to internal implementation do not affect external code.

---

## **1. Encapsulation in Python: Public, Protected, and Private Members**
Python uses **naming conventions** to define different access levels:

| **Access Modifier** | **Syntax** | **Access Level** |
|-----------------|----------|----------------|
| **Public** | `variable` | Accessible from anywhere |
| **Protected** | `_variable` | Intended for use within the class and subclasses |
| **Private** | `__variable` | Cannot be accessed directly outside the class |

---

## **2. Public Members**
Public attributes and methods can be accessed from anywhere.

### **Example: Public Attributes**
```python
class Car:
    def __init__(self, brand, speed):
        self.brand = brand  # Public attribute
        self.speed = speed  # Public attribute

    def display_info(self):  # Public method
        return f"Brand: {self.brand}, Speed: {self.speed} km/h"

car = Car("Tesla", 200)
print(car.brand)  # Accessing public attribute
print(car.speed)  # Accessing public attribute
print(car.display_info())  # Calling public method
```
**Output:**
```
Tesla
200
Brand: Tesla, Speed: 200 km/h
```
**Explanation:**  
- `brand` and `speed` are **public** and can be accessed directly.
- The `display_info()` method is also **public**.

---

## **3. Protected Members (`_variable`)**
Protected members are **conventionally** meant to be used only inside the class and its subclasses.

### **Example: Protected Attributes**
```python
class Vehicle:
    def __init__(self, brand, speed):
        self._brand = brand  # Protected attribute
        self._speed = speed  # Protected attribute

    def _display_info(self):  # Protected method
        return f"Brand: {self._brand}, Speed: {self._speed} km/h"

class Car(Vehicle):
    def show(self):
        return self._display_info()  # Accessing protected method

car = Car("BMW", 220)
print(car.show())  # Output: Brand: BMW, Speed: 220 km/h
print(car._brand)  # Possible, but not recommended
```
**Explanation:**
- `_brand` and `_speed` are **protected** (indicated by the underscore `_`).
- They **can still be accessed**, but it's a **convention** that they should only be used within the class or subclasses.
- `show()` accesses `_display_info()` from the parent class.

---

## **4. Private Members (`__variable`)**
Private members **cannot be accessed directly** from outside the class.

### **Example: Private Attributes**
```python
class Account:
    def __init__(self, name, balance):
        self.name = name  # Public
        self.__balance = balance  # Private

    def deposit(self, amount):
        self.__balance += amount
        return f"New balance: {self.__balance}"

    def get_balance(self):  # Getter method
        return self.__balance

account = Account("Alice", 1000)

# print(account.__balance)  # This will raise an AttributeError
print(account.get_balance())  # Correct way to access private data
print(account.deposit(500))  # Deposit money and check balance
```
**Output:**
```
1000
New balance: 1500
```
### **Explanation:**
- `__balance` is **private** and cannot be accessed directly (`account.__balance` will raise an error).
- Instead, a **getter method (`get_balance()`)** is used to retrieve the balance.
- A **method (`deposit()`)** allows modifying the balance safely.

### **Accessing Private Attributes Using Name Mangling**
Python internally renames private attributes using **name mangling**:  
`__balance` → `_ClassName__balance`

```python
print(account._Account__balance)  # This works, but it's not recommended
```
**Why?**  
- **Encapsulation is about conventions** rather than strict access control.
- Name mangling prevents accidental access, but it **can still be bypassed**.

---

## **5. Getters and Setters in Encapsulation**
Getters and setters **control access** to private attributes.

### **Example: Using Getters and Setters**
```python
class BankAccount:
    def __init__(self, account_holder, balance):
        self.__account_holder = account_holder  # Private attribute
        self.__balance = balance

    # Getter for account holder
    def get_account_holder(self):
        return self.__account_holder

    # Getter for balance
    def get_balance(self):
        return self.__balance

    # Setter for balance
    def set_balance(self, new_balance):
        if new_balance < 0:
            print("Balance cannot be negative!")
        else:
            self.__balance = new_balance

account = BankAccount("John", 5000)
print(account.get_account_holder())  # Output: John
print(account.get_balance())  # Output: 5000

account.set_balance(6000)  # Updating balance
print(account.get_balance())  # Output: 6000

account.set_balance(-100)  # Invalid update
```
**Output:**
```
John
5000
6000
Balance cannot be negative!
```
### **Explanation:**
- `get_balance()` retrieves the private balance.
- `set_balance(new_balance)` ensures the balance **cannot be negative**.
- **Direct access is prevented, enforcing data integrity.**

---

## **6. Using `@property` for Encapsulation**
Python provides the `@property` decorator as an elegant way to use **getters and setters**.

### **Example: Using `@property`**
```python
class Employee:
    def __init__(self, name, salary):
        self.__name = name
        self.__salary = salary

    @property  # Getter
    def salary(self):
        return self.__salary

    @salary.setter  # Setter
    def salary(self, amount):
        if amount < 0:
            print("Salary cannot be negative!")
        else:
            self.__salary = amount

emp = Employee("Alice", 5000)

print(emp.salary)  # Calls the getter
emp.salary = 6000  # Calls the setter
print(emp.salary)

emp.salary = -1000  # Invalid update
```
**Output:**
```
5000
6000
Salary cannot be negative!
```
**Why Use `@property`?**
- **Makes the syntax clean** (`emp.salary` instead of `emp.get_salary()`).
- **Ensures encapsulation** while keeping the interface intuitive.

---

## **7. Real-World Example: Encapsulation in a Car Class**
```python
class Car:
    def __init__(self, model, fuel_level):
        self.model = model
        self.__fuel_level = fuel_level  # Private attribute

    @property
    def fuel_level(self):
        return self.__fuel_level

    @fuel_level.setter
    def fuel_level(self, amount):
        if 0 <= amount <= 100:
            self.__fuel_level = amount
        else:
            print("Invalid fuel level!")

car = Car("Toyota", 50)
print(car.fuel_level)  # Output: 50

car.fuel_level = 80  # Updating fuel level
print(car.fuel_level)  # Output: 80

car.fuel_level = 200  # Invalid update
```
**Output:**
```
50
80
Invalid fuel level!
```
---

## **Conclusion**
- **Encapsulation** protects sensitive data and ensures controlled access.
- **Public, Protected, and Private attributes** define different access levels.
- **Getters and Setters** manage access to private data.
- **`@property` decorator** provides a cleaner way to use encapsulation.
