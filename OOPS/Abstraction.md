## **Python Abstraction**

### **What is Abstraction?**
Abstraction is one of the key concepts in Object-Oriented Programming (OOP). It hides implementation details and only exposes essential functionalities to the user. This is useful for simplifying complex systems and ensuring encapsulation.

In Python, **abstraction is implemented using abstract classes and methods**, which are defined in the `abc` (Abstract Base Class) module.

---

## **1. Creating an Abstract Class**
An **abstract class** is a class that cannot be instantiated directly and contains at least one abstract method.

- Abstract methods **do not have an implementation** in the base class.
- Subclasses **must override** these methods.

### **Example 1: Abstract Class in Python**
```python
from abc import ABC, abstractmethod

class Animal(ABC):  # Abstract class
    @abstractmethod
    def make_sound(self):  # Abstract method
        pass  # No implementation in the abstract class

class Dog(Animal):
    def make_sound(self):
        return "Bark"

class Cat(Animal):
    def make_sound(self):
        return "Meow"

# animal = Animal()  # This will give an error! Abstract classes cannot be instantiated.

dog = Dog()
cat = Cat()

print(dog.make_sound())  # Output: Bark
print(cat.make_sound())  # Output: Meow
```

### **Explanation**
- `Animal` is an **abstract class**.
- `make_sound()` is an **abstract method** (it has no implementation in `Animal`).
- `Dog` and `Cat` **inherit** from `Animal` and **implement** `make_sound()`.
- Attempting to create an instance of `Animal` will raise an error.

---

## **2. Abstract Class with Concrete Methods**
Abstract classes can also contain **concrete methods** (methods with implementation).

### **Example 2: Abstract Class with Concrete and Abstract Methods**
```python
from abc import ABC, abstractmethod

class Vehicle(ABC):
    def __init__(self, name):
        self.name = name

    @abstractmethod
    def max_speed(self):  # Abstract method
        pass

    def vehicle_info(self):  # Concrete method
        return f"Vehicle: {self.name}"

class Car(Vehicle):
    def max_speed(self):
        return "Max speed: 200 km/h"

class Bike(Vehicle):
    def max_speed(self):
        return "Max speed: 100 km/h"

car = Car("Tesla")
bike = Bike("Ducati")

print(car.vehicle_info())  # Output: Vehicle: Tesla
print(car.max_speed())     # Output: Max speed: 200 km/h

print(bike.vehicle_info()) # Output: Vehicle: Ducati
print(bike.max_speed())    # Output: Max speed: 100 km/h
```

### **Explanation**
- `Vehicle` is an **abstract class**.
- `max_speed()` is an **abstract method**.
- `vehicle_info()` is a **concrete method** (it can be used by all subclasses).
- `Car` and `Bike` **implement** the `max_speed()` method.

---

## **3. Preventing Instantiation of Abstract Classes**
Abstract classes cannot be instantiated directly.

```python
vehicle = Vehicle("Generic")  # This will raise TypeError!
```
**Error:**
```
TypeError: Can't instantiate abstract class Vehicle with abstract methods max_speed
```
This ensures that the base class **only serves as a template** and enforces implementation in subclasses.

---

## **4. Using Abstract Properties**
Python allows defining **abstract properties** in abstract classes.

### **Example 3: Abstract Property**
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @property
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14 * self.radius * self.radius

circle = Circle(5)
print(circle.area)  # Output: 78.5
```

### **Explanation**
- `area` is an **abstract property** in `Shape`.
- The subclass `Circle` **implements** the `area` property.
- The `@property` decorator allows accessing `area` as an **attribute** instead of a method (`circle.area` instead of `circle.area()`).

---

## **5. Real-World Example: Payment System**
A payment system with different payment methods (e.g., `CreditCardPayment`, `PayPalPayment`).

```python
from abc import ABC, abstractmethod

class Payment(ABC):
    @abstractmethod
    def process_payment(self, amount):
        pass

class CreditCardPayment(Payment):
    def process_payment(self, amount):
        return f"Processing credit card payment of ${amount}"

class PayPalPayment(Payment):
    def process_payment(self, amount):
        return f"Processing PayPal payment of ${amount}"

payment1 = CreditCardPayment()
payment2 = PayPalPayment()

print(payment1.process_payment(100))  # Output: Processing credit card payment of $100
print(payment2.process_payment(200))  # Output: Processing PayPal payment of $200
```

### **Use Case**
- The `Payment` class enforces that every payment method **must** have a `process_payment()` method.
- Subclasses provide **specific implementations** for different payment types.

---

## **6. Key Takeaways**
| Concept | Description |
|---------|------------|
| **Abstract Class** | A class that cannot be instantiated and serves as a template. |
| **Abstract Method** | A method that must be implemented in child classes. |
| **Concrete Method** | A method with an implementation in the abstract class. |
| **Abstract Property** | A property that subclasses must implement using `@property`. |
| **Why Use Abstraction?** | Enforces method implementation, improves code structure, and provides a clear blueprint for derived classes. |

---

## **7. When to Use Abstraction?**
- When you want to **enforce a contract** (e.g., all subclasses must implement a method).
- When you want to define a **common interface** for different types of objects.
- When you need **code reusability** by implementing some methods in the base class.

---

### **Conclusion**
- **Abstraction** allows you to create blueprints for classes without implementing all details.
- **Abstract classes** cannot be instantiated and must have at least one **abstract method**.
- **Concrete methods** can be included in abstract classes to provide common functionality.
- **Abstract properties** enforce property implementation in subclasses.

