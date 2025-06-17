# **Python Inheritance**
Inheritance is a fundamental concept in Object-Oriented Programming (OOP) that allows a class to inherit properties and behaviors from another class. This promotes **code reuse**, **modularity**, and **extensibility**.

---

## **1. What is Inheritance?**
Inheritance allows a child class (subclass) to acquire the attributes and methods of a parent class (superclass).

### **Key Terminology**
- **Parent Class (Superclass)** → The base class that provides attributes and methods.
- **Child Class (Subclass)** → The derived class that inherits from the parent.
- **Method Overriding** → A child class can modify a method from the parent.
- **`super()`** → Used to call methods of the parent class.

---

## **2. Types of Inheritance in Python**
Python supports several types of inheritance:

1. **Single Inheritance**
2. **Multi-Level Inheritance**
3. **Multiple Inheritance**
4. **Hierarchical Inheritance**
5. **Hybrid Inheritance**

---

## **3. Single Inheritance**
A child class inherits from a single parent class.

### **Example 1: Single Inheritance**
```python
class Animal:  # Parent class
    def sound(self):
        print("Animals make different sounds")

class Dog(Animal):  # Child class inheriting Animal
    def bark(self):
        print("Dog barks")

# Creating an object
dog = Dog()
dog.sound()  # Output: Animals make different sounds
dog.bark()   # Output: Dog barks
```
✔ The `Dog` class **inherits** the `sound()` method from `Animal`.

---

## **4. Multi-Level Inheritance**
A child class inherits from another child class, forming a chain.

### **Example 2: Multi-Level Inheritance**
```python
class Animal:
    def sound(self):
        print("Animals make sounds")

class Mammal(Animal):  # Inherits Animal
    def type(self):
        print("Mammals give birth to young ones")

class Dog(Mammal):  # Inherits Mammal
    def bark(self):
        print("Dog barks")

dog = Dog()
dog.sound()  # Output: Animals make sounds
dog.type()   # Output: Mammals give birth to young ones
dog.bark()   # Output: Dog barks
```
✔ `Dog` inherits from `Mammal`, which in turn inherits from `Animal`.

---

## **5. Multiple Inheritance**
A child class inherits from multiple parent classes.

### **Example 3: Multiple Inheritance**
```python
class Engine:
    def engine_type(self):
        print("This is a petrol engine")

class Wheels:
    def wheels_count(self):
        print("This vehicle has 4 wheels")

class Car(Engine, Wheels):  # Inheriting from both Engine and Wheels
    def car_type(self):
        print("This is a sedan")

car = Car()
car.engine_type()   # Output: This is a petrol engine
car.wheels_count()  # Output: This vehicle has 4 wheels
car.car_type()      # Output: This is a sedan
```
✔ `Car` inherits methods from **both** `Engine` and `Wheels`.

---

## **6. Hierarchical Inheritance**
Multiple child classes inherit from a single parent class.

### **Example 4: Hierarchical Inheritance**
```python
class Animal:
    def sound(self):
        print("Animals make sounds")

class Dog(Animal):  # Child class 1
    def bark(self):
        print("Dog barks")

class Cat(Animal):  # Child class 2
    def meow(self):
        print("Cat meows")

dog = Dog()
cat = Cat()

dog.sound()  # Output: Animals make sounds
dog.bark()   # Output: Dog barks

cat.sound()  # Output: Animals make sounds
cat.meow()   # Output: Cat meows
```
✔ Both `Dog` and `Cat` inherit `sound()` from `Animal`.

---

## **7. Hybrid Inheritance**
A combination of **multiple** inheritance types.

### **Example 5: Hybrid Inheritance**
```python
class Vehicle:
    def general_info(self):
        print("Vehicles are used for transportation")

class Engine:
    def engine_type(self):
        print("This vehicle has a powerful engine")

class Car(Vehicle, Engine):  # Multiple Inheritance
    def car_type(self):
        print("This is a luxury car")

class SportsCar(Car):  # Multi-level Inheritance
    def sports_features(self):
        print("Sports cars are very fast")

sports_car = SportsCar()
sports_car.general_info()  # Output: Vehicles are used for transportation
sports_car.engine_type()   # Output: This vehicle has a powerful engine
sports_car.car_type()      # Output: This is a luxury car
sports_car.sports_features()  # Output: Sports cars are very fast
```
✔ `SportsCar` inherits from `Car`, which inherits from both `Vehicle` and `Engine`.

---

## **8. Method Overriding in Inheritance**
A child class can override a method from the parent class.

### **Example 6: Overriding Parent Methods**
```python
class Animal:
    def sound(self):
        print("Animals make different sounds")

class Dog(Animal):
    def sound(self):  # Overriding the parent method
        print("Dog barks")

dog = Dog()
dog.sound()  # Output: Dog barks
```
✔ The `Dog` class **overrides** the `sound()` method from `Animal`.

---

## **9. Using `super()` to Call Parent Methods**
The `super()` function calls a method from the parent class.

### **Example 7: Using `super()`**
```python
class Animal:
    def sound(self):
        print("Animals make sounds")

class Dog(Animal):
    def sound(self):
        super().sound()  # Calling the parent class method
        print("Dog barks")

dog = Dog()
dog.sound()
```
✔ Output:
```
Animals make sounds
Dog barks
```
✔ `super().sound()` **calls** the `sound()` method from `Animal`.

---

## **10. The Method Resolution Order (MRO)**
In multiple inheritance, Python follows the **C3 Linearization Algorithm (MRO)** to determine method calls.

### **Example 8: MRO in Multiple Inheritance**
```python
class A:
    def show(self):
        print("Class A")

class B(A):
    def show(self):
        print("Class B")

class C(A):
    def show(self):
        print("Class C")

class D(B, C):  # Multiple inheritance
    pass

obj = D()
obj.show()  # Output: Class B
```
✔ Python follows the MRO (`D → B → C → A`).

To see the **MRO order**, use:
```python
print(D.mro())  
```
✔ Output:
```
[<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
```

---

## **11. Abstract Classes and Inheritance**
Abstract classes **cannot** be instantiated and must be inherited.

### **Example 9: Abstract Class with Inheritance**
```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def sound(self):
        pass  # Abstract method

class Dog(Animal):
    def sound(self):
        print("Dog barks")

dog = Dog()
dog.sound()  # Output: Dog barks
```
✔ **Abstract methods** must be implemented in child classes.

---

## **Summary**
| Inheritance Type | Description |
|------------------|-------------|
| **Single** | One class inherits another. |
| **Multi-Level** | Chain of inheritance (A → B → C). |
| **Multiple** | One class inherits from multiple parents. |
| **Hierarchical** | One parent, multiple children. |
| **Hybrid** | Combination of different inheritance types. |

✔ **Method Overriding** allows redefining parent methods.  
✔ **`super()`** calls parent class methods.  
✔ **MRO** determines method resolution order.

Python inheritance makes code **efficient, reusable, and modular**.  
