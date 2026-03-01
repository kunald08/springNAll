# Python OOP — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. What is Object-Oriented Programming? What are the four pillars?

**Answer:**
Object-Oriented Programming is a programming paradigm that organizes code around **objects** — self-contained units that combine **data** (attributes) and **behavior** (methods). Instead of writing procedures that manipulate data, I model real-world entities as objects.

The four pillars are:

1. **Encapsulation** — bundling data and methods together inside a class and restricting direct access to internal state. It hides complexity and protects data integrity.

2. **Inheritance** — a child class inherits attributes and methods from a parent class. It promotes code reuse — I write common behavior once in the parent and specialize in children.

3. **Polymorphism** — "many forms." The same interface can have different implementations. A method call on different objects can behave differently depending on the object's class.

4. **Abstraction** — hiding complex implementation details and exposing only what's necessary. The user of a class knows **what** it does, not **how** it does it.

---

## 2. What is the difference between a class and an object?

**Answer:**
A **class** is a **blueprint** or template — it defines what attributes and methods its objects will have. It doesn't consume memory for data until an object is created.

An **object** (or instance) is a **concrete entity** created from that blueprint. Each object has its own copy of instance attributes.

```python
class Car:                     # Class — the blueprint
    def __init__(self, brand):
        self.brand = brand

toyota = Car("Toyota")         # Object — a specific car
honda = Car("Honda")           # Another object — different data, same structure
```

Think of it like: the class is the architectural plan, and objects are the actual houses built from that plan. Each house has its own address, color, and residents, but they all follow the same structural design.

---

## 3. What is `__init__`? Is it a constructor?

**Answer:**
`__init__` is the **initializer** method — it's called automatically when a new object is created. It sets up the object's initial state by assigning values to instance attributes.

```python
class User:
    def __init__(self, name, email):
        self.name = name       # Instance attribute
        self.email = email
```

Technically, `__init__` is **not** the constructor — `__new__` is. `__new__` actually creates the object (allocates memory), and then `__init__` initializes it with data. But in practice, we almost never override `__new__`, so `__init__` is what we call the "constructor" in everyday conversation.

The `self` parameter refers to the **current instance** being created. Python passes it automatically — I don't provide it when calling.

---

## 4. What is `self` in Python?

**Answer:**
`self` is a reference to the **current instance** of the class. It's the first parameter of every instance method and is how the method knows which object's data to work with.

```python
class Dog:
    def __init__(self, name):
        self.name = name          # Assigns to THIS object's name

    def bark(self):
        print(f"{self.name} says Woof!")

buddy = Dog("Buddy")
buddy.bark()    # Python translates this to Dog.bark(buddy)
```

When I call `buddy.bark()`, Python automatically passes `buddy` as the `self` argument. Unlike Java's `this`, `self` is **explicit** in Python — it must be listed as the first parameter, though naming it `self` is a strong convention, not a requirement.

---

## 5. What is the difference between instance attributes and class attributes?

**Answer:**
**Instance attributes** are defined in `__init__` using `self.attr = value`. Each object has its **own copy**:
```python
class Dog:
    def __init__(self, name):
        self.name = name    # Each dog has its own name
```

**Class attributes** are defined directly in the class body, **shared** by all instances:
```python
class Dog:
    species = "Canis familiaris"    # Shared by ALL dogs

    def __init__(self, name):
        self.name = name            # Unique to each dog
```

When I access an attribute, Python looks at the **instance first**, then the **class**. If I modify a class attribute through an instance (`buddy.species = "new"`), it actually creates an instance attribute on that object, shadowing the class attribute — the class attribute remains unchanged for other instances.

I use class attributes for constants or data shared across all instances, like counters or configuration.

---

## 6. What are the different types of methods in Python?

**Answer:**
Python has three types of methods:

**Instance methods** — take `self` as the first parameter. They can access and modify both instance and class attributes:
```python
def greet(self):
    return f"Hello, {self.name}"
```

**Class methods** — decorated with `@classmethod`, take `cls` (the class itself) as the first parameter. Used for factory methods or methods that need the class but not an instance:
```python
@classmethod
def from_string(cls, data):
    return cls(*data.split(","))
```

**Static methods** — decorated with `@staticmethod`, take no implicit parameter. They're utility functions that logically belong to the class but don't need access to instance or class state:
```python
@staticmethod
def is_valid_email(email):
    return "@" in email
```

The key distinction: instance methods know the instance, class methods know the class, static methods know neither.

---

## 7. What is encapsulation in Python? How do you achieve it?

**Answer:**
Encapsulation is **bundling data and methods together** while **controlling access** to internal state. It prevents external code from directly modifying an object's data in unexpected ways.

Python doesn't have strict access modifiers like Java, but uses **naming conventions**:

- `public` — regular name: `self.name` — accessible from anywhere
- `_protected` — single underscore: `self._name` — a hint saying "don't access this from outside the class," but Python **doesn't enforce** it
- `__private` — double underscore: `self.__name` — triggers **name mangling**, where Python internally renames it to `_ClassName__name`, making it harder (but not impossible) to access from outside

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance    # Name-mangled

    def deposit(self, amount):
        if amount > 0:             # Controlled access
            self.__balance += amount
```

Python's philosophy is "we're all consenting adults here" — conventions over enforcement. The underscore is a signal to other developers, not a hard barrier.

---

## 8. What are properties in Python? Why use getters and setters?

**Answer:**
Properties allow me to define methods that behave like attributes — providing controlled access with a clean syntax using the `@property` decorator:

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value < 0:
            raise ValueError("Radius cannot be negative")
        self._radius = value

    @property
    def area(self):
        return 3.14159 * self._radius ** 2

c = Circle(5)
c.radius = 10     # Calls the setter — looks like attribute access
print(c.area)     # Calls the getter — computed dynamically
```

The benefit is that I can start with a simple attribute and later add validation, computation, or logging **without changing the external interface**. Callers still use `obj.radius`, not `obj.get_radius()`. This is Pythonic — we don't write Java-style getters/setters unless we need the control.

---

## 9. What is inheritance? What types does Python support?

**Answer:**
Inheritance lets a child class **inherit** attributes and methods from a parent class, allowing code reuse and extension:

```python
class Animal:
    def speak(self):
        return "..."

class Dog(Animal):           # Dog inherits from Animal
    def speak(self):         # Overrides parent method
        return "Woof!"
```

Python supports:
- **Single inheritance** — one parent: `class Dog(Animal)`
- **Multiple inheritance** — multiple parents: `class FlyingFish(Fish, Bird)`
- **Multilevel inheritance** — chain: `class GoldenRetriever(Dog)` → `Dog(Animal)` → `Animal`
- **Hierarchical inheritance** — multiple children from one parent: `Dog(Animal)`, `Cat(Animal)`

Multiple inheritance is powerful but can be complex. Python resolves method calls using the **MRO (Method Resolution Order)** — the C3 linearization algorithm — which I can inspect with `ClassName.__mro__` or `ClassName.mro()`.

---

## 10. What is `super()` and why is it used?

**Answer:**
`super()` returns a proxy object that delegates method calls to the **parent class**. It's primarily used to call the parent's `__init__` or overridden methods:

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)    # Calls Animal.__init__
        self.breed = breed
```

Without `super()`, the parent's `__init__` wouldn't run, and `self.name` wouldn't be set.

`super()` is especially important with **multiple inheritance** because it follows the **MRO** (Method Resolution Order), ensuring each parent class's method is called exactly once and in the correct order. Calling `Parent.__init__(self, ...)` directly can break the MRO chain and cause issues with diamond inheritance patterns.

---

## 11. What is method overriding?

**Answer:**
Method overriding is when a child class provides its **own implementation** of a method that's already defined in the parent class. When I call that method on the child object, the child's version runs:

```python
class Shape:
    def area(self):
        return 0

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):                    # Overrides Shape.area()
        return 3.14159 * self.radius ** 2

c = Circle(5)
c.area()    # Calls Circle's area(), not Shape's
```

Overriding is a key part of **polymorphism** — different subclasses can provide different behaviors for the same method name. The parent method can still be called using `super().area()` if I need to extend rather than fully replace the behavior.

Python doesn't have method overloading (same name, different parameters) — if I define two methods with the same name, the second simply replaces the first.

---

## 12. What is polymorphism? Explain with an example.

**Answer:**
Polymorphism means "many forms" — the same interface works with different types, and the appropriate behavior is determined at runtime:

```python
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

class Duck:
    def speak(self):
        return "Quack!"

# Same function works with different types
def make_sound(animal):
    print(animal.speak())

make_sound(Dog())    # "Woof!"
make_sound(Cat())    # "Meow!"
make_sound(Duck())   # "Quack!"
```

Python implements polymorphism through **duck typing** — "if it walks like a duck and quacks like a duck, it's a duck." I don't need to declare an interface or inherit from a common parent. As long as the object has the method I'm calling, it works. This makes Python incredibly flexible compared to statically typed languages.

---

## 13. What is duck typing?

**Answer:**
Duck typing is Python's approach to type checking — instead of checking what an object **is** (its class), Python checks what it **can do** (its methods and attributes). The name comes from: "If it walks like a duck and quacks like a duck, then it is a duck."

```python
class FileWriter:
    def write(self, data):
        # writes to file

class DatabaseWriter:
    def write(self, data):
        # writes to database

def save(writer, data):
    writer.write(data)    # Don't care about the type, just need .write()
```

I don't need `FileWriter` and `DatabaseWriter` to share a parent class or implement an interface. As long as they have a `write()` method, the `save()` function works with both. This is fundamental to Python's design philosophy and makes code more flexible and loosely coupled.

---

## 14. What are abstract classes and abstract methods?

**Answer:**
An abstract class is a class that **cannot be instantiated** — it's meant to be subclassed. It can define **abstract methods** that child classes **must** implement. Python uses the `abc` module:

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass

    def description(self):              # Concrete method — optional to override
        return "I am a shape"

# shape = Shape()   # TypeError — can't instantiate abstract class

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):                     # Must implement
        return 3.14159 * self.radius ** 2

    def perimeter(self):                # Must implement
        return 2 * 3.14159 * self.radius
```

Abstract classes define a **contract** — they say "if you're going to be a Shape, you MUST implement `area()` and `perimeter()`." If a child class doesn't implement all abstract methods, it also becomes abstract and can't be instantiated.

---

## 15. What are magic (dunder) methods?

**Answer:**
Magic methods (or dunder methods — "double underscore") are special methods surrounded by double underscores like `__init__`, `__str__`, `__repr__`. They let me define how objects behave with built-in Python operations:

```python
class Book:
    def __init__(self, title, pages):
        self.title = title
        self.pages = pages

    def __str__(self):               # Called by print(), str()
        return f"'{self.title}'"

    def __repr__(self):              # Called in console, debugging
        return f"Book('{self.title}', {self.pages})"

    def __len__(self):               # Called by len()
        return self.pages

    def __eq__(self, other):         # Called by ==
        return self.title == other.title

    def __lt__(self, other):         # Called by <
        return self.pages < other.pages
```

Magic methods are Python's way of supporting **operator overloading** and integrating custom classes with Python's built-in behavior. When I write `len(book)`, Python calls `book.__len__()`. When I write `book1 == book2`, Python calls `book1.__eq__(book2)`. This is what makes Python's object model so powerful and consistent.

---

## 16. What is the difference between `__str__` and `__repr__`?

**Answer:**
Both return string representations of an object, but for different audiences:

- **`__str__`** — for **end users**. Called by `print()` and `str()`. Should be readable and user-friendly.
- **`__repr__`** — for **developers**. Called in the console, debug output, and when `__str__` isn't defined. Should be unambiguous and ideally a valid Python expression that could recreate the object.

```python
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"{self.name}, age {self.age}"   # User-friendly

    def __repr__(self):
        return f"User('{self.name}', {self.age})"   # Developer-friendly
```

The rule of thumb: `__repr__` is for **debugging**, `__str__` is for **display**. If I only implement one, I implement `__repr__`, because Python uses it as a fallback when `__str__` isn't defined, but not vice versa.

---

## 17. What is operator overloading?

**Answer:**
Operator overloading lets me define how built-in operators (`+`, `-`, `*`, `==`, `<`, etc.) work with my custom objects by implementing the corresponding magic methods:

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):        # Enables v1 + v2
        return Vector(self.x + other.x, self.y + other.y)

    def __mul__(self, scalar):       # Enables v * 3
        return Vector(self.x * scalar, self.y * scalar)

    def __eq__(self, other):         # Enables v1 == v2
        return self.x == other.x and self.y == other.y

v1 = Vector(1, 2)
v2 = Vector(3, 4)
v3 = v1 + v2      # Calls v1.__add__(v2) → Vector(4, 6)
```

Common operator-to-method mappings: `+` → `__add__`, `-` → `__sub__`, `*` → `__mul__`, `==` → `__eq__`, `<` → `__lt__`, `[]` → `__getitem__`, `()` → `__call__`. This lets my custom objects behave like built-in types.

---

## 18. What are dataclasses?

**Answer:**
Dataclasses, introduced in **Python 3.7**, automatically generate common boilerplate code for classes that primarily store data — `__init__`, `__repr__`, `__eq__`, and more:

```python
from dataclasses import dataclass

@dataclass
class User:
    name: str
    email: str
    age: int = 0    # Default value

# Automatically generates:
# __init__(self, name, email, age=0)
# __repr__ → User(name='Alice', email='a@b.com', age=25)
# __eq__ based on all fields
```

I can add `frozen=True` to make instances immutable, `order=True` to enable comparison operators, and `field()` for advanced configuration. Dataclasses reduce boilerplate dramatically compared to writing everything manually.

I use them whenever I need a class that's mainly a data container — like DTOs, configuration objects, or value objects. For even simpler cases, `namedtuple` works; for more complex validation, `pydantic` is popular.

---

## 19. What is composition vs inheritance? When do you use each?

**Answer:**
**Inheritance** models an "**is-a**" relationship — a Dog IS an Animal:
```python
class Dog(Animal):
    pass
```

**Composition** models a "**has-a**" relationship — a Car HAS an Engine:
```python
class Car:
    def __init__(self):
        self.engine = Engine()    # Car contains an Engine object
```

The general guideline is to **favor composition over inheritance** because:
- Composition is more **flexible** — I can swap components at runtime
- It avoids deep inheritance hierarchies that become fragile
- It reduces **tight coupling** between classes
- Changes to a composed class don't unexpectedly break other classes

I use inheritance when there's a genuine "is-a" relationship and I want to reuse behavior. I use composition when an object needs to use another object's capability without being a subtype of it. In practice, many code smell issues come from overusing inheritance when composition would be cleaner.

---

## 20. What is the MRO (Method Resolution Order)?

**Answer:**
MRO is the order in which Python searches for methods in a class hierarchy, especially important with **multiple inheritance**. Python uses the **C3 Linearization algorithm**:

```python
class A:
    def method(self):
        return "A"

class B(A):
    def method(self):
        return "B"

class C(A):
    def method(self):
        return "C"

class D(B, C):
    pass

print(D.mro())
# [D, B, C, A, object]
```

When I call `D().method()`, Python checks D first, then B, then C, then A, then `object`. The C3 algorithm ensures:
1. Children come before parents
2. The order in the class definition is respected  
3. Each class appears only once
4. It's consistent — the relative ordering is preserved

I can always check the MRO with `ClassName.mro()` or `ClassName.__mro__`. Understanding MRO is essential for debugging method resolution in complex inheritance hierarchies.

---

## 21. What is the difference between `isinstance()` and `type()`?

**Answer:**
`type()` returns the **exact type** of an object:
```python
type(42)    # <class 'int'>
```

`isinstance()` checks if an object is an instance of a class **or any of its parent classes**:
```python
class Animal: pass
class Dog(Animal): pass

d = Dog()
type(d) == Animal        # False — exact type is Dog
isinstance(d, Animal)    # True — Dog IS an Animal (inheritance)
isinstance(d, Dog)       # True
```

**`isinstance()` is preferred** in most cases because it respects inheritance. `type()` should be used only when I specifically want to exclude subclasses.

---

## 22. What is the `__call__` method?

**Answer:**
The `__call__` method makes an instance **callable like a function**. When I "call" an object using parentheses, Python invokes its `__call__` method:

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, value):
        return value * self.factor

double = Multiplier(2)
double(5)     # 10 — calls double.__call__(5)
double(10)    # 20
```

This is powerful because it lets objects act like functions while maintaining state. Real-world uses include decorators implemented as classes, strategy objects, and configurable function-like objects. I can check if something is callable with `callable(obj)`.
