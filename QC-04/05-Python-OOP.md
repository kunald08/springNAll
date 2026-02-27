# Python Object-Oriented Programming (OOP) — From Zero to Expert

## Table of Contents
1. [What is OOP?](#1-what-is-oop)
2. [Classes and Objects](#2-classes-and-objects)
3. [The `__init__` Constructor](#3-the-__init__-constructor)
4. [Instance Attributes vs Class Attributes](#4-attributes)
5. [Instance Methods, Class Methods, Static Methods](#5-methods)
6. [Encapsulation — Access Modifiers](#6-encapsulation)
7. [Properties — Getters and Setters](#7-properties)
8. [Inheritance](#8-inheritance)
9. [Method Overriding](#9-method-overriding)
10. [super()](#10-super)
11. [Multiple Inheritance and MRO](#11-multiple-inheritance)
12. [Polymorphism](#12-polymorphism)
13. [Duck Typing](#13-duck-typing)
14. [Abstract Classes](#14-abstract-classes)
15. [Magic (Dunder) Methods](#15-magic-methods)
16. [Operator Overloading](#16-operator-overloading)
17. [Dataclasses](#17-dataclasses)
18. [Class Relationships: Composition vs Inheritance](#18-composition)
19. [SOLID Principles (Brief Overview)](#19-solid)

---

## 1. What is OOP?

**Object-Oriented Programming (OOP)** is a programming paradigm that organizes code around **objects** rather than functions and logic.

An **object** is a self-contained unit that combines:
- **Data** (attributes/fields): what the object *has*
- **Behavior** (methods/functions): what the object *can do*

```
┌──────────────────────────────────────────────────────────┐
│  Real-World Analogy: A Dog                               │
│                                                          │
│  Attributes (Data):                                      │
│    name = "Buddy"                                        │
│    breed = "Labrador"                                    │
│    age = 3                                               │
│                                                          │
│  Methods (Behavior):                                     │
│    bark()   → prints "Woof!"                             │
│    eat()    → eats food                                  │
│    sleep()  → sleeps                                     │
│                                                          │
│  In code: dog = Dog("Buddy", "Labrador", 3)              │
│           dog.bark()                                     │
└──────────────────────────────────────────────────────────┘
```

### The 4 Pillars of OOP

```
┌────────────────────────────────────────────────────────────┐
│  1. ENCAPSULATION  — Bundle data + methods together        │
│                      Hide internal details                 │
│                                                            │
│  2. INHERITANCE    — Child class inherits from parent      │
│                      Reuse and extend existing code        │
│                                                            │
│  3. POLYMORPHISM   — Same interface, different behavior    │
│                      "One interface, many forms"           │
│                                                            │
│  4. ABSTRACTION    — Hide complexity, show simple interface │
│                      "What it does, not how it does it"    │
└────────────────────────────────────────────────────────────┘
```

---

## 2. Classes and Objects

A **class** is a **blueprint/template** for creating objects. An **object** is an **instance** of a class.

```
┌──────────────────────────────────────────────────────────┐
│  Class = Blueprint/Cookie Cutter                         │
│  Object = Actual thing made from the blueprint/cookie    │
│                                                          │
│  Dog (class) → defines what all dogs have and can do     │
│                                                          │
│  buddy = Dog(...)  → a specific dog (object/instance)    │
│  rex   = Dog(...)  → another specific dog                │
│  fido  = Dog(...)  → yet another specific dog            │
└──────────────────────────────────────────────────────────┘
```

### Creating a Class

```python
# Define the blueprint
class Dog:
    pass    # Empty class for now

# Create instances (objects) from the class
buddy = Dog()
rex = Dog()
fido = Dog()

print(type(buddy))   # <class '__main__.Dog'>
print(buddy)         # <__main__.Dog object at 0x...>
```

### A Complete Class

```python
class Dog:
    # Class attribute — shared by ALL dogs
    species = "Canis familiaris"

    # Constructor — called when creating a new Dog object
    def __init__(self, name, breed, age):
        # Instance attributes — unique to each dog
        self.name = name
        self.breed = breed
        self.age = age

    # Instance method — behavior that uses instance's own data
    def bark(self):
        return f"{self.name} says: Woof!"

    def describe(self):
        return f"{self.name} is a {self.age}-year-old {self.breed}."


# Create instances
buddy = Dog("Buddy", "Labrador", 3)
rex = Dog("Rex", "German Shepherd", 5)

# Use them
print(buddy.bark())        # Buddy says: Woof!
print(rex.describe())      # Rex is a 5-year-old German Shepherd.
print(buddy.name)          # Buddy
print(buddy.species)       # Canis familiaris
print(Dog.species)         # Canis familiaris  (can access via class too)
```

---

## 3. The `__init__` Constructor

`__init__` is a **special method** (called a "dunder" or "magic" method) that is automatically called when you create a new object. It initializes the object's attributes.

```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner          # Assign to the instance
        self.balance = balance      # Can have defaults too
        self.transactions = []      # Start with empty transaction history

    def deposit(self, amount):
        self.balance += amount
        self.transactions.append(f"Deposited ${amount}")
        return self.balance

    def withdraw(self, amount):
        if amount > self.balance:
            return "Insufficient funds!"
        self.balance -= amount
        self.transactions.append(f"Withdrew ${amount}")
        return self.balance

    def get_statement(self):
        print(f"Account: {self.owner}")
        print(f"Balance: ${self.balance}")
        print("Transactions:")
        for t in self.transactions:
            print(f"  {t}")


# Create accounts
alice_account = BankAccount("Alice", 1000)
bob_account = BankAccount("Bob")       # balance defaults to 0

alice_account.deposit(500)
alice_account.withdraw(200)
alice_account.get_statement()

# Output:
# Account: Alice
# Balance: $1300
# Transactions:
#   Deposited $500
#   Withdrew $200
```

### `self` — What It Is

`self` is a reference to the **current object instance**. It is the first parameter in every instance method. Python passes it automatically — you don't provide it when calling:

```python
buddy = Dog("Buddy", "Labrador", 3)
buddy.bark()    # Python translates this to: Dog.bark(buddy)
#                                                       ↑ self
```

`self` is just a convention — you could name it anything, but `self` is the universal Python convention. Always use `self`.

---

## 4. Attributes

### Instance Attributes

Created with `self.attribute_name = value` inside `__init__`. Each **object** has its own copy:

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius    # Each circle has its own radius

c1 = Circle(5)
c2 = Circle(10)

print(c1.radius)   # 5
print(c2.radius)   # 10

c1.radius = 7      # Only c1's radius changes
print(c1.radius)   # 7
print(c2.radius)   # 10 (unchanged)
```

### Class Attributes

Defined directly in the class body (not inside a method). **Shared** by ALL instances:

```python
class Dog:
    species = "Canis familiaris"    # Class attribute — same for ALL dogs
    count = 0                       # Track how many dogs were created

    def __init__(self, name):
        self.name = name
        Dog.count += 1    # Modify class attribute through the class name

d1 = Dog("Buddy")
d2 = Dog("Rex")
d3 = Dog("Fido")

print(Dog.count)    # 3
print(d1.count)     # 3 (can also access via instance)

# Overriding class attribute on a specific instance:
d1.species = "Special Dog"    # Creates a NEW instance attribute for d1
print(d1.species)    # Special Dog (d1's own attribute shadows class attribute)
print(d2.species)    # Canis familiaris (d2 still uses class attribute)
```

### Adding/Deleting Attributes Dynamically

```python
class Person:
    def __init__(self, name):
        self.name = name

p = Person("Alice")
p.age = 25           # Add new attribute dynamically
p.city = "NYC"
print(p.age)         # 25
del p.city           # Delete an attribute
# print(p.city)      ← AttributeError
```

---

## 5. Methods

### Instance Methods

Most common type. Takes `self` as the first parameter. Accesses instance attributes:

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):              # Instance method
        return self.width * self.height

    def perimeter(self):         # Instance method
        return 2 * (self.width + self.height)

    def is_square(self):
        return self.width == self.height

r = Rectangle(4, 6)
print(r.area())        # 24
print(r.perimeter())   # 20
print(r.is_square())   # False
```

### Class Methods

Takes `cls` (the class itself) as the first parameter. Decorated with `@classmethod`. Used for factory methods (alternative constructors):

```python
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_string):
        """Alternative constructor: create a Date from a 'YYYY-MM-DD' string."""
        year, month, day = map(int, date_string.split("-"))
        return cls(year, month, day)   # cls() creates a new Date instance

    @classmethod
    def today(cls):
        """Alternative constructor: create today's Date."""
        from datetime import date
        d = date.today()
        return cls(d.year, d.month, d.day)

    def __str__(self):
        return f"{self.year}-{self.month:02d}-{self.day:02d}"


# Normal constructor
d1 = Date(2024, 3, 15)
print(d1)   # 2024-03-15

# Using class method (alternative constructor)
d2 = Date.from_string("2024-12-25")
print(d2)   # 2024-12-25

d3 = Date.today()
print(d3)   # Current date
```

### Static Methods

Decorated with `@staticmethod`. Does not take `self` or `cls`. Just a regular function logically grouped inside the class:

```python
class MathUtils:
    @staticmethod
    def add(a, b):
        return a + b

    @staticmethod
    def is_prime(n):
        if n < 2:
            return False
        for i in range(2, int(n ** 0.5) + 1):
            if n % i == 0:
                return False
        return True

# Call via class (or instance, but class is clearer)
print(MathUtils.add(3, 5))         # 8
print(MathUtils.is_prime(17))      # True
print(MathUtils.is_prime(20))      # False
```

---

## 6. Encapsulation

**Encapsulation** means bundling data and the methods that operate on that data, while **restricting direct access** to some components. This protects the internal state from being accidentally corrupted from outside.

### Access Modifiers in Python

Python doesn't have strict `private`/`protected` keywords like Java. Instead, it uses naming conventions:

```
┌──────────────────────────────────────────────────────────┐
│  Python Naming Conventions for Access Control            │
│                                                          │
│  public:     name      → Anyone can access               │
│  protected:  _name     → "Please don't use outside class"│
│                          (Convention only, not enforced)  │
│  private:    __name    → Name mangling applied            │
│                          (Harder to access from outside) │
└──────────────────────────────────────────────────────────┘
```

```python
class Employee:
    def __init__(self, name, salary, ssn):
        self.name = name           # Public — accessible anywhere
        self._department = "IT"    # Protected — "intended for class use"
        self.__salary = salary     # Private — name mangled to _Employee__salary
        self.__ssn = ssn           # Very sensitive data

    def get_salary(self):          # Public method to access private data safely
        return self.__salary

    def give_raise(self, amount):
        if amount > 0:
            self.__salary += amount
        else:
            print("Raise must be positive.")

    def _internal_review(self):    # Protected method
        return f"Reviewing {self.name}'s performance"

emp = Employee("Alice", 60000, "123-45-6789")

print(emp.name)          # Alice  (public — fine)
print(emp._department)   # IT  (works but you shouldn't use it)
# print(emp.__salary)    # ← AttributeError!  (cannot access directly)
print(emp.get_salary())  # 60000  (access through public method)

# Name mangling — Python renames __salary to _Employee__salary
print(emp._Employee__salary)   # 60000 (possible but never do this in real code!)

emp.give_raise(5000)
print(emp.get_salary())   # 65000
```

---

## 7. Properties — Getters and Setters

Python's **`@property`** decorator provides a clean, Pythonic way to use getter and setter methods that look like regular attribute access:

```python
class Temperature:
    def __init__(self, celsius=0):
        self._celsius = celsius   # Store internally in Celsius

    @property
    def celsius(self):
        """Getter — access temperature in Celsius."""
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        """Setter — set temperature, with validation."""
        if value < -273.15:
            raise ValueError("Temperature below absolute zero is not possible!")
        self._celsius = value

    @celsius.deleter
    def celsius(self):
        """Deleter — called when using 'del obj.celsius'."""
        print("Deleting temperature")
        del self._celsius

    @property
    def fahrenheit(self):
        """Computed property — no setter (read-only)."""
        return (self._celsius * 9/5) + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self._celsius = (value - 32) * 5/9


# Usage looks like regular attribute access, but runs getter/setter methods
t = Temperature(25)
print(t.celsius)     # 25  (calls getter)
print(t.fahrenheit)  # 77.0  (computed property)

t.celsius = 100      # Calls setter
print(t.fahrenheit)  # 212.0

t.fahrenheit = 32    # Calls fahrenheit setter
print(t.celsius)     # 0.0

# t.celsius = -300   ← ValueError!
```

---

## 8. Inheritance

**Inheritance** allows a **child (subclass/derived class)** to inherit attributes and methods from a **parent (superclass/base class)**. The child can also add its own attributes/methods or override inherited ones.

```
┌──────────────────────────────────────────────────────────┐
│         Inheritance Hierarchy Example                    │
│                                                          │
│                  Animal (parent)                         │
│                 /        \                               │
│              Dog          Cat                            │
│             /   \                                        │
│         Labrador  Poodle                                 │
│                                                          │
│  Labrador IS-A Dog, IS-A Animal                          │
│  Dog IS-A Animal                                         │
└──────────────────────────────────────────────────────────┘
```

```python
# Parent (base) class
class Animal:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def speak(self):
        return f"{self.name} makes a sound."

    def __str__(self):
        return f"{self.name} ({type(self).__name__}, age {self.age})"


# Child (derived) class — inherits from Animal
class Dog(Animal):
    def __init__(self, name, age, breed):
        super().__init__(name, age)    # Call parent's __init__
        self.breed = breed             # Dog-specific attribute

    def speak(self):                   # Override parent's speak method
        return f"{self.name} says: Woof!"

    def fetch(self):                   # New method only for Dog
        return f"{self.name} fetches the ball!"


class Cat(Animal):
    def __init__(self, name, age, indoor):
        super().__init__(name, age)
        self.indoor = indoor

    def speak(self):
        return f"{self.name} says: Meow!"

    def purr(self):
        return f"{self.name} is purring..."


# Usage
buddy = Dog("Buddy", 3, "Labrador")
whiskers = Cat("Whiskers", 5, indoor=True)

print(buddy.speak())     # Buddy says: Woof!
print(whiskers.speak())  # Whiskers says: Meow!
print(buddy.fetch())     # Buddy fetches the ball!
print(str(buddy))        # Buddy (Dog, age 3)
print(buddy.age)         # 3 (inherited from Animal)

# Check relationships
print(isinstance(buddy, Dog))      # True
print(isinstance(buddy, Animal))   # True (IS-A relationship)
print(isinstance(buddy, Cat))      # False
print(issubclass(Dog, Animal))     # True
```

---

## 9. Method Overriding

When a child class defines a method with the **same name** as a parent's method, the child's method **overrides** the parent's:

```python
class Shape:
    def area(self):
        return 0

    def describe(self):
        return f"I am a shape with area {self.area():.2f}"


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):     # Override Shape.area()
        import math
        return math.pi * self.radius ** 2


class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):     # Override Shape.area()
        return self.width * self.height


c = Circle(5)
r = Rectangle(4, 6)

print(c.describe())   # I am a shape with area 78.54  (uses Circle.area())
print(r.describe())   # I am a shape with area 24.00  (uses Rectangle.area())
```

---

## 10. `super()`

`super()` gives you access to the **parent class** from inside the child class. Most commonly used to call the parent's `__init__`:

```python
class Vehicle:
    def __init__(self, make, model, year):
        self.make = make
        self.model = model
        self.year = year

    def start(self):
        return f"{self.make} {self.model} is starting..."


class Electric(Vehicle):
    def __init__(self, make, model, year, battery_kwh):
        super().__init__(make, model, year)   # Initialize Vehicle attributes
        self.battery_kwh = battery_kwh         # Electric-specific attribute

    def start(self):
        parent_start = super().start()         # Call parent's start()
        return f"{parent_start} (Silently, using {self.battery_kwh}kWh battery)"


class HybridCar(Electric):
    def __init__(self, make, model, year, battery_kwh, fuel_type):
        super().__init__(make, model, year, battery_kwh)
        self.fuel_type = fuel_type

    def start(self):
        electric_start = super().start()
        return f"{electric_start} + {self.fuel_type} backup"


tesla = Electric("Tesla", "Model 3", 2023, 75)
print(tesla.start())
# Tesla Model 3 is starting... (Silently, using 75kWh battery)

prius = HybridCar("Toyota", "Prius", 2022, 8, "Gasoline")
print(prius.start())
# Toyota Prius is starting... (Silently, using 8kWh battery) + Gasoline backup
```

---

## 11. Multiple Inheritance and MRO

Python supports inheriting from **multiple parent classes**:

```python
class Swimmer:
    def swim(self):
        return "Swimming!"

class Runner:
    def run(self):
        return "Running!"

class Triathlete(Swimmer, Runner):   # Inherits from both
    def compete(self):
        return f"{self.swim()} and {self.run()}"


athlete = Triathlete()
print(athlete.swim())      # Swimming!
print(athlete.run())       # Running!
print(athlete.compete())   # Swimming! and Running!
```

### Method Resolution Order (MRO)

When multiple parent classes have a method with the same name, Python uses the **MRO algorithm (C3 linearization)** to decide which one to use:

```python
class A:
    def greet(self):
        return "Hello from A"

class B(A):
    def greet(self):
        return "Hello from B"

class C(A):
    def greet(self):
        return "Hello from C"

class D(B, C):   # Inherits from B first, then C
    pass


d = D()
print(d.greet())         # Hello from B  (B comes before C in D's inheritance list)
print(D.__mro__)         # Shows the full method resolution order
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)
```

MRO reads left to right, depth-first — but with C3 linearization to handle diamond inheritance correctly.

---

## 12. Polymorphism

**Polymorphism** means "many forms" — the same interface works on different types. The same method name behaves differently depending on the object it is called on.

```python
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

class Cow(Animal):
    def speak(self):
        return "Moo!"


# Polymorphism in action — same code works for all animal types
animals = [Dog(), Cat(), Cow(), Dog()]

for animal in animals:
    print(animal.speak())   # Each object uses its OWN speak() method

# Output:
# Woof!
# Meow!
# Moo!
# Woof!
```

### Polymorphism with Functions

```python
def make_sound(animal):
    """Works with ANY animal that has a speak() method."""
    print(animal.speak())

make_sound(Dog())   # Woof!
make_sound(Cat())   # Meow!
make_sound(Cow())   # Moo!
```

---

## 13. Duck Typing

**"If it walks like a duck and quacks like a duck, it is a duck."**

Python does NOT care about the actual type of an object. If an object has the right method, Python will use it — no inheritance needed:

```python
class Dog:
    def speak(self):
        return "Woof!"

class Robot:
    def speak(self):
        return "Beep boop!"

class TextToSpeech:
    def speak(self):
        return "I am a computer voice."


# make_sound doesn't care about types — just needs a speak() method
def make_sound(anything):
    print(anything.speak())

make_sound(Dog())           # Woof!
make_sound(Robot())         # Beep boop!
make_sound(TextToSpeech())  # I am a computer voice.
```

This is why Python doesn't need interfaces like Java. The behavior is what matters, not the type.

---

## 14. Abstract Classes

An **abstract class** is a class that **cannot be instantiated directly**. It defines a template/interface that subclasses must implement. Useful when you want to force child classes to implement certain methods.

```python
from abc import ABC, abstractmethod

class Shape(ABC):   # Inherit from ABC to make it abstract
    @abstractmethod
    def area(self) -> float:
        """Must be implemented by all subclasses."""
        pass

    @abstractmethod
    def perimeter(self) -> float:
        """Must be implemented by all subclasses."""
        pass

    def describe(self):    # This is a concrete method — not abstract
        return f"Area: {self.area():.2f}, Perimeter: {self.perimeter():.2f}"


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):    # MUST implement this
        import math
        return math.pi * self.radius ** 2

    def perimeter(self):    # MUST implement this
        import math
        return 2 * math.pi * self.radius


class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2

    def perimeter(self):
        return 4 * self.side


# shape = Shape()  ← TypeError: Cannot instantiate abstract class!
c = Circle(5)
s = Square(4)
print(c.describe())   # Area: 78.54, Perimeter: 31.42
print(s.describe())   # Area: 16.00, Perimeter: 16.00
```

---

## 15. Magic (Dunder) Methods

Magic methods (also called "dunder" methods — for Double UNDERscore) let you define how built-in Python operations behave for your custom objects.

```python
class Book:
    def __init__(self, title, author, pages):
        self.title = title
        self.author = author
        self.pages = pages

    def __str__(self):
        """Called by str(), print()."""
        return f'"{self.title}" by {self.author}'

    def __repr__(self):
        """Called by repr(), used in REPL. Should show how to recreate the object."""
        return f'Book(title="{self.title}", author="{self.author}", pages={self.pages})'

    def __len__(self):
        """Called by len()."""
        return self.pages

    def __eq__(self, other):
        """Called by == operator."""
        if not isinstance(other, Book):
            return NotImplemented
        return self.title == other.title and self.author == other.author

    def __lt__(self, other):
        """Called by < operator."""
        return self.pages < other.pages

    def __contains__(self, text):
        """Called by 'in' operator."""
        return text.lower() in self.title.lower()

    def __getitem__(self, index):
        """Called by [] operator. Simplified: return page number."""
        return f"Page {index} of {self.title}"


b1 = Book("Python Crash Course", "Eric Matthes", 544)
b2 = Book("Fluent Python", "Luciano Ramalho", 792)

print(str(b1))        # "Python Crash Course" by Eric Matthes
print(repr(b1))       # Book(title="Python Crash Course", ...)
print(len(b1))        # 544
print(b1 == b2)       # False
print(b1 < b2)        # True (544 < 792 pages)
print("Python" in b1) # True
print(b1[42])         # Page 42 of Python Crash Course

# Sorting works because __lt__ is defined
books = [b2, b1]
books.sort()
print([str(b) for b in books])
# ['"Python Crash Course" by Eric Matthes', '"Fluent Python" by Luciano Ramalho']
```

### Essential Dunder Methods Quick Reference

| Method | Triggered By | Purpose |
|--------|-------------|---------|
| `__init__` | `Class()` | Initialize object |
| `__str__` | `str(obj)`, `print(obj)` | User-friendly string |
| `__repr__` | `repr(obj)`, REPL | Developer string |
| `__len__` | `len(obj)` | Length |
| `__eq__` | `obj == other` | Equality |
| `__lt__`, `__gt__` | `<`, `>` | Comparison |
| `__add__` | `obj + other` | Addition |
| `__contains__` | `x in obj` | Membership |
| `__getitem__` | `obj[key]` | Indexing |
| `__setitem__` | `obj[key] = val` | Item assignment |
| `__iter__` | `for x in obj` | Iteration |
| `__next__` | `next(obj)` | Next item in iteration |
| `__call__` | `obj()` | Make object callable |
| `__enter__`, `__exit__` | `with obj:` | Context manager |
| `__del__` | Object garbage collected | Destructor |

---

## 16. Operator Overloading

Using dunder methods, you can define what `+`, `-`, `*`, and other operators mean for your classes:

```python
class Vector:
    """2D vector with x and y components."""

    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"Vector({self.x}, {self.y})"

    def __add__(self, other):       # v1 + v2
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):       # v1 - v2
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar):      # v * 3
        return Vector(self.x * scalar, self.y * scalar)

    def __rmul__(self, scalar):     # 3 * v  (reversed operands)
        return self.__mul__(scalar)

    def __abs__(self):              # abs(v) — magnitude
        return (self.x ** 2 + self.y ** 2) ** 0.5

    def __eq__(self, other):        # v1 == v2
        return self.x == other.x and self.y == other.y

    def __neg__(self):              # -v (unary negation)
        return Vector(-self.x, -self.y)


v1 = Vector(2, 3)
v2 = Vector(1, 4)

print(v1 + v2)    # Vector(3, 7)
print(v1 - v2)    # Vector(1, -1)
print(v1 * 3)     # Vector(6, 9)
print(3 * v1)     # Vector(6, 9)
print(abs(v1))    # 3.605551275...
print(-v1)        # Vector(-2, -3)
```

---

## 17. Dataclasses

`@dataclass` (Python 3.7+) automatically generates boilerplate methods (`__init__`, `__repr__`, `__eq__`) based on class annotations — saving you from writing repetitive code:

```python
from dataclasses import dataclass, field

@dataclass
class Point:
    x: float
    y: float
    z: float = 0.0   # Default value


@dataclass
class Student:
    name: str
    grade: float
    courses: list = field(default_factory=list)   # Mutable default

    def gpa_label(self):
        return "Pass" if self.grade >= 60 else "Fail"


# __init__ is auto-generated:
p1 = Point(1.0, 2.0)
p2 = Point(3.0, 4.0, 5.0)
print(p1)            # Point(x=1.0, y=2.0, z=0.0)
print(p1 == Point(1.0, 2.0))  # True (auto __eq__)

alice = Student("Alice", 88.5, ["Math", "Physics"])
print(alice)         # Student(name='Alice', grade=88.5, courses=['Math', 'Physics'])
print(alice.gpa_label())   # Pass

# Frozen dataclass (immutable — like a NamedTuple but with class features)
from dataclasses import dataclass

@dataclass(frozen=True)
class Color:
    r: int
    g: int
    b: int

RED = Color(255, 0, 0)
# RED.r = 100   ← FrozenInstanceError!
```

---

## 18. Composition vs Inheritance

### "Favor Composition Over Inheritance" — Famous OOP Principle

**Inheritance** ("IS-A"): Use when the child truly IS a type of the parent.
**Composition** ("HAS-A"): Use when an object contains other objects to delegate work.

```python
# Inheritance: a car IS-A vehicle (OK)
class Vehicle:
    def move(self):
        return "Moving"

class Car(Vehicle):
    pass

# Composition: a car HAS-A engine (better for complex relationships)
class Engine:
    def __init__(self, horsepower):
        self.horsepower = horsepower

    def start(self):
        return f"Engine with {self.horsepower}hp started"

class GPS:
    def navigate(self, destination):
        return f"Navigating to {destination}"

class Car:
    def __init__(self, make, model, hp):
        self.make = make
        self.model = model
        self.engine = Engine(hp)    # HAS-A Engine
        self.gps = GPS()            # HAS-A GPS

    def start(self):
        return self.engine.start()   # Delegates to engine

    def navigate(self, destination):
        return self.gps.navigate(destination)


my_car = Car("Toyota", "Camry", 200)
print(my_car.start())              # Engine with 200hp started
print(my_car.navigate("Airport"))  # Navigating to Airport
```

Composition is more flexible: you can swap the `Engine` for a different one without changing `Car`'s inheritance structure.

---

## 19. SOLID Principles (Brief Overview)

SOLID is a set of 5 OOP design principles for writing maintainable code:

```
┌────────────────────────────────────────────────────────────┐
│  S — Single Responsibility Principle                       │
│      A class should have only ONE reason to change.        │
│      Do ONE thing well.                                    │
│                                                            │
│  O — Open/Closed Principle                                 │
│      Open for extension, closed for modification.          │
│      Add new features without changing existing code.      │
│                                                            │
│  L — Liskov Substitution Principle                         │
│      Child class objects should work wherever parent       │
│      class objects are used, without breaking the code.    │
│                                                            │
│  I — Interface Segregation Principle                       │
│      Don't force classes to implement methods they         │
│      don't use. Prefer smaller, focused interfaces.        │
│                                                            │
│  D — Dependency Inversion Principle                        │
│      High-level modules should not depend on low-level     │
│      modules. Both should depend on abstractions.          │
└────────────────────────────────────────────────────────────┘
```

---

## Summary

```
┌────────────────────────────────────────────────────────────┐
│                     OOP Summary                            │
│                                                            │
│  class Name:  → defines a blueprint                        │
│  def __init__(self, ...): → initializes attributes         │
│  self.attr → instance attribute (per object)               │
│  ClassName.attr → class attribute (shared by all)          │
│                                                            │
│  Instance method: def method(self): ...                    │
│  Class method: @classmethod def method(cls): ...           │
│  Static method: @staticmethod def method(): ...            │
│                                                            │
│  Encapsulation: self._protected, self.__private            │
│  @property: Pythonic getter/setter                         │
│                                                            │
│  Inheritance: class Child(Parent):                         │
│  super(): call parent's methods                            │
│  Override: redefine parent's method in child               │
│                                                            │
│  Polymorphism: same method name, different behavior        │
│  Duck typing: if it has the method, Python will use it     │
│  Abstract class: ABC + @abstractmethod → force override    │
│                                                            │
│  Dunder methods: __str__, __len__, __add__, etc.           │
│  @dataclass: auto-generate __init__, __repr__, __eq__      │
│  Composition (HAS-A) often better than Inheritance (IS-A)  │
└────────────────────────────────────────────────────────────┘
```

**Next:** [06-Python-FileIO-Exceptions-Modules.md](06-Python-FileIO-Exceptions-Modules.md) — File I/O, Exception Handling, and Modules.
