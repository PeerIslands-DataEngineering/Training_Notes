# **Python Classes and Objects** 🚀

## **1. Introduction to Object-Oriented Programming (OOP)**
Object-Oriented Programming (OOP) is a programming paradigm that revolves around **objects** and **classes**. It allows us to model real-world entities and organize code efficiently.

### **Key OOP Concepts:**
- **Class** → Blueprint for creating objects.
- **Object** → Instance of a class.
- **Encapsulation** → Bundling data & methods together.
- **Inheritance** → Deriving a new class from an existing one.
- **Polymorphism** → Using a unified interface for different data types.
- **Abstraction** → Hiding implementation details.

---

## **2. Creating Classes and Objects**
A **class** is defined using the `class` keyword, and an **object** is an instance of a class.

### **Example 1: Defining a Class and Creating an Object**
```python
class Car:
    def __init__(self, brand, model):
        self.brand = brand  # Instance variable
        self.model = model

    def show_details(self):
        print(f"Car: {self.brand} {self.model}")

# Creating an object
car1 = Car("Toyota", "Corolla")
car1.show_details()  # Output: Car: Toyota Corolla
```
### **Explanation:**
- `class Car:` → Defines a class.
- `__init__(self, brand, model)` → Constructor to initialize object properties.
- `self.brand`, `self.model` → Instance variables.
- `car1 = Car("Toyota", "Corolla")` → Creates an object of the class.

---

## **3. The `__init__` Constructor Method**
The `__init__` method initializes object properties.

### **Example 2: Using `__init__`**
```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def display(self):
        print(f"Employee: {self.name}, Salary: {self.salary}")

emp1 = Employee("Alice", 50000)
emp1.display()  # Output: Employee: Alice, Salary: 50000
```
✔ The `__init__` method is automatically called when an object is created.

---

## **4. Instance and Class Variables**
- **Instance Variables**: Unique to each object (`self.var_name`).
- **Class Variables**: Shared among all objects (`ClassName.var_name`).

### **Example 3: Instance vs. Class Variables**
```python
class Company:
    company_name = "TechCorp"  # Class variable

    def __init__(self, emp_name):
        self.emp_name = emp_name  # Instance variable

emp1 = Company("John")
emp2 = Company("Emma")

print(emp1.company_name)  # Output: TechCorp
print(emp2.company_name)  # Output: TechCorp

Company.company_name = "NewTech"
print(emp1.company_name)  # Output: NewTech
```
✔ Changing a **class variable** affects all objects.

---

## **5. Instance Methods, Class Methods, and Static Methods**
| Method Type | Use Case | Uses `self`? | Uses `cls`? |
|------------|---------|------------|------------|
| **Instance Method** | Operates on an instance | ✅ Yes | ❌ No |
| **Class Method** | Operates on the class | ❌ No | ✅ Yes |
| **Static Method** | Independent method | ❌ No | ❌ No |

### **Example 4: Different Method Types**
```python
class Student:
    school_name = "ABC School"  # Class variable

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def show(self):  # Instance method
        print(f"Student: {self.name}, Age: {self.age}")

    @classmethod
    def change_school(cls, new_name):  # Class method
        cls.school_name = new_name

    @staticmethod
    def info():  # Static method
        print("This is a static method in the Student class.")

# Creating objects
s1 = Student("Alice", 16)

# Calling methods
s1.show()  # Output: Student: Alice, Age: 16
Student.change_school("XYZ School")
print(Student.school_name)  # Output: XYZ School
Student.info()  # Output: This is a static method in the Student class.
```
✔ **Instance methods** work with object data.  
✔ **Class methods** modify class-level attributes.  
✔ **Static methods** don’t modify instance/class data.

---

## **6. Encapsulation (Data Hiding)**
Encapsulation restricts direct access to variables and allows controlled modification.

### **Example 5: Private Variables**
```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # Private variable

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance

# Creating an object
acc = BankAccount(1000)
acc.deposit(500)
print(acc.get_balance())  # Output: 1500

# print(acc.__balance)  # Error! Cannot access private variable
```
✔ `__balance` is **private** and cannot be accessed directly.

---

## **7. Inheritance (Reusing Code)**
A child class can inherit attributes and methods from a parent class.

### **Example 6: Single Inheritance**
```python
class Animal:
    def speak(self):
        print("Animal makes a sound")

class Dog(Animal):  # Inheriting Animal class
    def speak(self):
        print("Dog barks")

dog = Dog()
dog.speak()  # Output: Dog barks
```
✔ **The `Dog` class inherits the `Animal` class but overrides `speak()`.**

---

### **Example 7: Multi-level Inheritance**
```python
class Grandparent:
    def family_name(self):
        print("Smith Family")

class Parent(Grandparent):
    pass

class Child(Parent):
    pass

c = Child()
c.family_name()  # Output: Smith Family
```
✔ Inheritance can extend multiple levels.

---

## **8. Polymorphism (Multiple Forms)**
Polymorphism allows different classes to have the same method name but different behaviors.

### **Example 8: Polymorphism in Methods**
```python
class Cat:
    def speak(self):
        return "Meow"

class Dog:
    def speak(self):
        return "Bark"

animals = [Cat(), Dog()]

for animal in animals:
    print(animal.speak())  # Output: Meow  Bark
```
✔ The `speak()` method behaves differently for `Cat` and `Dog`.

---

## **9. Abstraction (Hiding Implementation)**
Abstraction hides implementation details using **abstract classes**.

### **Example 9: Abstract Class**
```python
from abc import ABC, abstractmethod

class Vehicle(ABC):
    @abstractmethod
    def start(self):
        pass

class Car(Vehicle):
    def start(self):
        print("Car starts with a key")

car = Car()
car.start()  # Output: Car starts with a key
```
✔ Abstract classes **cannot be instantiated** directly.

---

## **10. Magic (Dunder) Methods**
Special methods in Python begin and end with `__`.

### **Example 10: `__str__` and `__repr__`**
```python
class Book:
    def __init__(self, title, author):
        self.title = title
        self.author = author

    def __str__(self):
        return f"Book: {self.title} by {self.author}"

book = Book("Python Basics", "Alice")
print(book)  # Output: Book: Python Basics by Alice
```
✔ `__str__` returns a user-friendly representation.

---

## **Summary Table**
| Concept | Description |
|---------|-------------|
| **Class** | Blueprint for objects |
| **Object** | Instance of a class |
| **Encapsulation** | Restrict direct access to data |
| **Inheritance** | Reuse code from a parent class |
| **Polymorphism** | Same interface, different behavior |
| **Abstraction** | Hide implementation details |
| **Magic Methods** | Special methods like `__init__`, `__str__` |

Python’s OOP makes code **structured, reusable, and modular**.  
