### **Polymorphism**
Polymorphism allows objects of different classes to be treated as objects of a common superclass. In Python, polymorphism is achieved through **method overriding**, **method overloading (via default arguments or `*args`/`**kwargs`)**, and **duck typing**.

---

## **1. Polymorphism with Method Overriding**
Method overriding allows a subclass to provide a different implementation of a method that is already defined in its superclass.

```python
class Animal:
    def speak(self):
        return "Animal makes a sound"

class Dog(Animal):
    def speak(self):
        return "Bark"

class Cat(Animal):
    def speak(self):
        return "Meow"

# Using polymorphism
animals = [Dog(), Cat(), Animal()]

for animal in animals:
    print(animal.speak())  # Output: Bark, Meow, Animal makes a sound
```
🔹 **Explanation:**  
- `Dog` and `Cat` override the `speak()` method of `Animal`.
- The `speak()` method behaves differently depending on the object's class.

---

## **2. Polymorphism with Method Overloading (via Default Arguments & `*args`)**
Python does not support method overloading (multiple methods with the same name but different parameters). Instead, we use default arguments or `*args`.

```python
class MathOperations:
    def add(self, a, b, c=0):
        return a + b + c  # Default argument handles two or three arguments

math = MathOperations()
print(math.add(2, 3))       # Output: 5
print(math.add(2, 3, 4))    # Output: 9
```
🔹 **Explanation:**  
- The third parameter has a default value (`0`), allowing method overloading.

Using `*args` for more flexibility:
```python
class MathOperations:
    def add(self, *args):
        return sum(args)  # Supports any number of arguments

math = MathOperations()
print(math.add(2, 3))         # Output: 5
print(math.add(2, 3, 4, 5))   # Output: 14
```

---

## **3. Polymorphism with Duck Typing**
Duck typing is a form of dynamic polymorphism where methods are called on objects regardless of their actual class, as long as they implement the expected behavior.

```python
class Bird:
    def fly(self):
        return "Bird is flying"

class Airplane:
    def fly(self):
        return "Airplane is flying"

class Fish:
    def swim(self):
        return "Fish is swimming"

# Polymorphic function
def lift_off(entity):
    return entity.fly()  # Calls fly() method if it exists

print(lift_off(Bird()))      # Output: Bird is flying
print(lift_off(Airplane()))  # Output: Airplane is flying
# print(lift_off(Fish()))    # AttributeError: Fish has no fly() method
```
🔹 **Explanation:**  
- `Bird` and `Airplane` both have `fly()`, so `lift_off()` works.
- `Fish` does not have `fly()`, causing an error (polymorphism fails).

---

## **4. Polymorphism with Operator Overloading (`__add__`, `__mul__`, etc.)**
Python allows **operator overloading** by overriding special methods.

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):  # Overloading `+` operator
        return Vector(self.x + other.x, self.y + other.y)

    def __str__(self):
        return f"({self.x}, {self.y})"

v1 = Vector(2, 3)
v2 = Vector(4, 5)
v3 = v1 + v2  # Uses __add__
print(v3)  # Output: (6, 8)
```
🔹 **Explanation:**  
- `__add__()` allows `+` to work with `Vector` objects.

---

## **Final Thoughts**
✔ **Method Overriding** – Different behavior in subclasses  
✔ **Method Overloading (via default args or `*args`)** – Multiple argument handling  
✔ **Duck Typing** – Calling methods based on behavior, not class type  
✔ **Operator Overloading** – Custom behavior for `+`, `-`, `*`, etc.
