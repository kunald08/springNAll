# Python Basics — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. What is Python? Why is it so popular?

**Answer:**
Python is a **high-level, interpreted, general-purpose** programming language created by **Guido van Rossum** in 1991. The reason it's so popular is a combination of things — the syntax is very close to plain English which makes it extremely readable, it supports multiple paradigms like object-oriented, procedural, and functional programming, and it has a massive ecosystem of libraries for everything from web development to machine learning.

It's cross-platform, so I can write code on Windows and run it on Linux without changes. And being open-source with a huge community means I can find a library or a solution for almost any problem. Companies like Instagram, Netflix, and Spotify use Python heavily in production.

---

## 2. Is Python interpreted or compiled?

**Answer:**
Python is generally called an **interpreted language**, but technically it's **both compiled and interpreted**. Here's what happens under the hood:

When I run a `.py` file, the Python interpreter first **compiles** the source code into an intermediate form called **bytecode** — those `.pyc` files you see in the `__pycache__` folder. Then the **Python Virtual Machine (PVM)** interprets and executes that bytecode line by line.

So there IS a compilation step, but unlike C or Java, it's not a separate manual step — it happens automatically and transparently. The key difference from fully compiled languages is that the bytecode is not converted to native machine code; it runs on the PVM.

---

## 3. What is the difference between CPython, PyPy, Jython, and IronPython?

**Answer:**
These are different **implementations** of the Python language:

- **CPython** — the default and most widely used implementation, written in C. When we download Python from python.org, we get CPython. It compiles Python code to bytecode and executes it on a virtual machine.

- **PyPy** — an alternative implementation that uses **JIT (Just-In-Time) compilation**, making it significantly faster than CPython for long-running programs because it compiles hot code paths to native machine code.

- **Jython** — Python that runs on the **Java Virtual Machine**, allowing us to use Java libraries directly in Python code.

- **IronPython** — Python for the **.NET framework**, allowing integration with C# and .NET libraries.

In most production environments, CPython is the standard.

---

## 4. What are Python's key features?

**Answer:**
The key features I'd highlight are:

- **Easy to learn and read** — the syntax enforces clean code through indentation
- **Dynamically typed** — I don't need to declare variable types, Python infers them at runtime
- **Strongly typed** — though dynamic, Python doesn't allow implicit type mixing like adding a string to an integer
- **Garbage collected** — memory management is handled automatically using reference counting and a cyclic garbage collector
- **Extensive standard library** — "batteries included" philosophy, where modules for file I/O, networking, regular expressions, etc. come built-in
- **Cross-platform** — write once, runs on Windows, Linux, and Mac
- **Supports multiple paradigms** — OOP, procedural, and functional programming
- **Huge third-party ecosystem** — over 400,000 packages on PyPI

---

## 5. What is PEP 8?

**Answer:**
PEP 8 is the **official style guide for Python code**. PEP stands for Python Enhancement Proposal. PEP 8 specifically defines coding conventions to make Python code more readable and consistent across the community.

Some key PEP 8 rules: use **4 spaces** for indentation (never tabs), limit lines to **79 characters**, use **snake_case** for variables and functions, **PascalCase** for classes, and surround top-level functions with **two blank lines**. Following PEP 8 isn't enforced by the interpreter, but it's considered a best practice, and tools like `flake8` and `black` help ensure compliance.

---

## 6. What are variables in Python? How are they different from other languages?

**Answer:**
In Python, a variable is simply a **name that references an object in memory**. Unlike languages like Java or C where a variable is a named memory location that holds a value, in Python, variables are more like **labels or tags** that point to objects.

When I write `x = 10`, Python creates an integer object `10` in memory, and `x` is just a reference pointing to that object. If I then write `y = x`, both `x` and `y` point to the same object — they don't each get their own copy.

Also, Python is **dynamically typed**, so I don't need to declare the type. The same variable can refer to different types at different times: `x = 10` then `x = "hello"` is perfectly valid.

---

## 7. What are Python's built-in data types?

**Answer:**
Python has several built-in data types, and I'd categorize them like this:

- **Numeric:** `int` (arbitrary precision integers), `float` (double-precision floating point), `complex` (like `3+4j`)
- **Sequence:** `str` (immutable text), `list` (mutable ordered collection), `tuple` (immutable ordered collection)
- **Mapping:** `dict` (key-value pairs)
- **Set:** `set` (mutable unordered unique elements), `frozenset` (immutable set)
- **Boolean:** `bool` (`True` or `False`, subclass of `int`)
- **None:** `NoneType` — represents absence of value

An important distinction is that `str`, `tuple`, and `frozenset` are **immutable** — once created, they cannot be changed. `list`, `dict`, and `set` are **mutable**.

---

## 8. What is the difference between mutable and immutable types?

**Answer:**
**Mutable** objects can be modified after creation — their internal state can change without creating a new object. Examples are `list`, `dict`, and `set`. If I have `my_list = [1, 2, 3]` and do `my_list.append(4)`, the same object in memory is modified.

**Immutable** objects **cannot** be changed after creation. If I try to modify them, Python creates a **new** object instead. Examples are `int`, `float`, `str`, `tuple`, and `frozenset`. When I do `x = "hello"` and then `x = x + " world"`, Python doesn't modify the original string — it creates a completely new string object and `x` now points to that new object.

This matters for performance, hashing (only immutable types can be dictionary keys), and understanding how Python handles memory.

---

## 9. What is dynamic typing vs static typing?

**Answer:**
In **statically typed** languages like Java or C, I must declare the type of a variable at compile time — `int x = 10;` — and that variable can only hold that type throughout its lifetime. The compiler checks types before the program runs.

In **dynamically typed** languages like Python, the type is associated with the **value/object**, not the variable. I can write:
```python
x = 10        # x refers to an int
x = "hello"   # now x refers to a str
x = [1, 2, 3] # now x refers to a list
```
Type checking happens at **runtime**, not at compile time. The advantage is flexibility and faster development. The tradeoff is that type errors only show up when the code actually runs, which is why Python 3.5+ introduced **type hints** — they're optional annotations that help with readability and tooling, but Python doesn't enforce them at runtime.

---

## 10. What is the difference between `is` and `==`?

**Answer:**
`==` checks for **value equality** — do these two objects have the same content?

`is` checks for **identity** — are these the exact same object in memory?

For example:
```python
a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)   # True — same values
print(a is b)   # False — different objects in memory

c = a
print(a is c)   # True — same object, c is just another reference
```

There's a nuance with **small integers and interned strings**: Python caches integers from -5 to 256 and some short strings, so `a = 5; b = 5; a is b` returns `True` because Python reuses the same object. But this is an implementation detail of CPython and should never be relied upon. Always use `==` for value comparison and `is` only when I specifically want to check identity, like `if x is None`.

---

## 11. What is `None` in Python?

**Answer:**
`None` is Python's way of representing the **absence of a value** or a **null value**. It's the sole instance of the `NoneType` class, and it's a **singleton** — there's only ever one `None` object in memory.

Common uses: as a default return value for functions that don't explicitly return anything, as a default parameter in function definitions, and as an initializer when I want to declare a variable without assigning a meaningful value yet.

The correct way to check for `None` is using `is`, not `==`:
```python
if result is None:
    print("No result")
```
Because `None` is a singleton, `is None` is both correct and faster than `== None`.

---

## 12. How does Python manage memory?

**Answer:**
Python manages memory automatically through two mechanisms:

1. **Reference Counting** — every object has a counter tracking how many references point to it. When a new reference is created, the count goes up; when a reference goes out of scope or is deleted, the count goes down. When the count hits **zero**, the memory is immediately deallocated.

2. **Garbage Collector** — reference counting alone can't handle **circular references** (where two objects reference each other). Python has a generational garbage collector that periodically detects and cleans up these cycles. It divides objects into three generations — new objects start in generation 0, and if they survive a collection cycle, they get promoted to the next generation. Long-lived objects are checked less frequently.

Additionally, Python uses a **private heap** for all objects and data structures, and the memory manager handles allocation internally using memory pools for small objects (the `pymalloc` allocator).

---

## 13. What is the difference between `type()` and `isinstance()`?

**Answer:**
`type()` returns the **exact type** of an object:
```python
type(42)        # <class 'int'>
type("hello")   # <class 'str'>
```

`isinstance()` checks if an object is an instance of a class **or any of its parent classes**:
```python
isinstance(42, int)     # True
isinstance(True, int)   # True — because bool is a subclass of int
type(True) == int        # False — exact type is bool, not int
```

In interviews and in production code, **`isinstance()` is generally preferred** because it respects inheritance hierarchies. `type()` is used when I need the exact type and don't want subclass matching.

---

## 14. What is type conversion (casting) in Python?

**Answer:**
Type conversion means converting a value from one data type to another. Python supports two types:

**Implicit conversion (coercion):** Python automatically converts types when it makes sense. For example, adding an `int` and a `float` — Python promotes the `int` to `float`:
```python
result = 5 + 3.2   # int + float → float (8.2)
```

**Explicit conversion (casting):** I manually convert using constructor functions:
```python
int("42")       # string → int: 42
float("3.14")   # string → float: 3.14
str(100)        # int → string: "100"
list("hello")   # string → list: ['h', 'e', 'l', 'l', 'o']
```

An important point: not all conversions are valid. `int("hello")` will raise a `ValueError`. And converting a `float` to `int` **truncates** (doesn't round) — `int(3.9)` gives `3`, not `4`.

---

## 15. What are f-strings and why are they preferred?

**Answer:**
F-strings (formatted string literals) were introduced in **Python 3.6**. They let me embed expressions directly inside string literals using curly braces, prefixed with `f`:

```python
name = "Alice"
age = 25
print(f"My name is {name} and I am {age} years old.")
```

They're preferred over older formatting methods because they're **faster** (evaluated at runtime, not through a method call), **more readable** (the variable is right there in the string), and **more powerful** (I can put any valid expression inside the braces):

```python
f"Result: {2 + 3}"          # "Result: 5"
f"Upper: {'hello'.upper()}" # "Upper: HELLO"
f"Price: {9.99:.2f}"        # "Price: 9.99" (format specifier)
```

The older methods — `%` formatting and `.format()` — still work but f-strings are the modern Pythonic way.

---

## 16. What is string immutability?

**Answer:**
Strings in Python are **immutable** — once a string object is created, it cannot be changed. Any operation that appears to modify a string actually creates a **new string object**.

```python
s = "hello"
s[0] = "H"   # TypeError! Can't modify in place
s = s.upper() # Creates a new string "HELLO"; 's' now points to the new object
```

This has performance implications: if I'm concatenating strings in a loop, each `+=` creates a new string, which is O(n²) overall. For building strings incrementally, it's better to collect parts in a **list** and use `"".join()` at the end — that's O(n).

The immutability also means strings can be used as **dictionary keys** and **set elements**, because they're hashable.

---

## 17. How does Python handle integer overflow?

**Answer:**
**It doesn't** — and that's the beauty of Python. Unlike languages like Java where an `int` is limited to 32 bits (about ±2.1 billion) and can overflow, Python integers have **arbitrary precision**. They can grow as large as available memory allows.

```python
x = 10 ** 100   # A googol — perfectly fine
```

Under the hood, CPython represents large integers as arrays of "digits" and dynamically allocates more memory as needed. So Python automatically handles big numbers without any special data type.

The tradeoff is that very large integer operations are slower than fixed-size integers in C or Java, but for normal use cases, this is negligible.

---

## 18. What is the `input()` function? How does it work?

**Answer:**
`input()` reads a line of text from the user via standard input and returns it as a **string**. It optionally takes a prompt message:

```python
name = input("Enter your name: ")  # Always returns a string
age = int(input("Enter your age: "))  # Must cast if I want an integer
```

The important thing to remember is that `input()` **always returns a string**, even if the user types a number. So if I need a number for calculations, I must explicitly convert it using `int()` or `float()`. In Python 2, there was also `raw_input()`, but in Python 3, `input()` does what `raw_input()` used to do.

---

## 19. What is the `__pycache__` folder?

**Answer:**
When Python runs a `.py` file, it first compiles the source code into **bytecode** and caches it in `.pyc` files inside the `__pycache__` directory. The next time the same file is run without any changes, Python skips the compilation step and loads the cached bytecode directly, which makes startup faster.

The files are named with the Python version — like `module.cpython-311.pyc` — so multiple Python versions can coexist. This folder is automatically managed by Python, and I can safely add it to `.gitignore`. Deleting it has no harmful effect; Python simply recompiles the next time.

---

## 20. What is the difference between `print()` and `return`?

**Answer:**
`print()` is a **function** that outputs text to the console — it's for **displaying** information to the user. It doesn't affect the program's logic or pass values between functions.

`return` is a **statement** that sends a value **back to the caller** from inside a function. It's how functions communicate results to the rest of the program.

```python
def add_print(a, b):
    print(a + b)    # Displays 8, but the function returns None

def add_return(a, b):
    return a + b    # Returns 8 to the caller

result = add_print(3, 5)   # Prints 8, but result is None
result = add_return(3, 5)  # result is 8, nothing printed
```

A function that only prints is **not reusable** — I can't use its output in further calculations. A function that returns is **composable** — I can store, pass, or use the result. In production code, functions should almost always `return` rather than `print`.
