# Python Functions — From Zero to Expert

## Table of Contents
1. [What is a Function?](#1-what-is-a-function)
2. [Defining and Calling Functions](#2-defining-and-calling-functions)
3. [Parameters and Arguments](#3-parameters-and-arguments)
4. [Positional Arguments](#4-positional-arguments)
5. [Keyword Arguments](#5-keyword-arguments)
6. [Default Parameter Values](#6-default-parameter-values)
7. [`*args` — Variable Positional Arguments](#7-args)
8. [`**kwargs` — Variable Keyword Arguments](#8-kwargs)
9. [Return Values](#9-return-values)
10. [Returning Multiple Values](#10-returning-multiple-values)
11. [Scope — Local, Global, Nonlocal](#11-scope)
12. [Lambda Functions](#12-lambda-functions)
13. [Higher-Order Functions](#13-higher-order-functions)
14. [map(), filter(), reduce()](#14-map-filter-reduce)
15. [Nested Functions](#15-nested-functions)
16. [Closures](#16-closures)
17. [Decorators](#17-decorators)
18. [Recursion](#18-recursion)
19. [Type Hints (Optional but Recommended)](#19-type-hints)
20. [Docstrings (Best Practice)](#20-docstrings)

---

## 1. What is a Function?

A **function** is a named, reusable block of code that does one specific task. Instead of writing the same code over and over, you write it once inside a function and **call** it (run it) whenever you need it.

```
┌──────────────────────────────────────────────────────────┐
│  Without functions:                                      │
│                                                          │
│  print("Hello, Alice!")    ← same code                   │
│  print("Hello, Bob!")      ← repeated                    │
│  print("Hello, Charlie!")  ← 3 times                     │
│                                                          │
│  With a function:                                        │
│                                                          │
│  def greet(name):           ← write ONCE                 │
│      print(f"Hello, {name}!")                            │
│                                                          │
│  greet("Alice")             ← call 3 times               │
│  greet("Bob")                                            │
│  greet("Charlie")                                        │
└──────────────────────────────────────────────────────────┘
```

### Why Use Functions?

- **DRY principle** (Don't Repeat Yourself) — write once, use many times
- **Readability** — break a large problem into small, named pieces
- **Maintainability** — fix a bug in one place, fixed everywhere
- **Testability** — test each function independently

---

## 2. Defining and Calling Functions

### Syntax

```python
def function_name(parameters):
    """Optional docstring."""
    # Function body (indented)
    # code here
    return value    # Optional
```

- `def` — keyword meaning "define a function"
- `function_name` — name you choose (follow snake_case convention)
- `parameters` — inputs the function expects (optional)
- `return` — sends a value back to the caller (optional; if omitted, returns `None`)

### Basic Example

```python
def greet():
    print("Hello, World!")

# Calling the function:
greet()     # Hello, World!
greet()     # Hello, World!  (can call multiple times)
```

### Function With a Parameter

```python
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")    # Hello, Alice!
greet("Bob")      # Hello, Bob!
```

### Functions Are Objects

This is a key concept in Python: **functions are first-class objects**. You can:
- Assign them to variables
- Pass them as arguments to other functions
- Return them from functions
- Store them in lists and dictionaries

```python
def say_hello():
    print("Hello!")

# Assign function to a variable (no parentheses — we are NOT calling it)
action = say_hello
action()    # Hello!   (calling through the variable)

# Check that it's a function object
print(type(say_hello))   # <class 'function'>
```

---

## 3. Parameters and Arguments

These words are often used interchangeably but have a precise meaning:

```
┌──────────────────────────────────────────────────────────┐
│  PARAMETER — the variable in the function definition     │
│                                                          │
│  def greet(name):    ← 'name' is a PARAMETER             │
│      print(name)                                         │
│                                                          │
│  ARGUMENT — the actual value passed when calling         │
│                                                          │
│  greet("Alice")      ← "Alice" is an ARGUMENT            │
└──────────────────────────────────────────────────────────┘
```

---

## 4. Positional Arguments

By default, arguments are matched to parameters **in order (position)**:

```python
def describe_pet(animal, name):
    print(f"I have a {animal} named {name}.")

describe_pet("dog", "Rex")     # I have a dog named Rex.
describe_pet("cat", "Whiskers") # I have a cat named Whiskers.

# Wrong order = wrong meaning
describe_pet("Rex", "dog")     # I have a Rex named dog.  ← BAD
```

---

## 5. Keyword Arguments

You can pass arguments by name (in any order):

```python
def describe_pet(animal, name):
    print(f"I have a {animal} named {name}.")

# Using keyword arguments — order doesn't matter
describe_pet(name="Rex", animal="dog")     # I have a dog named Rex.
describe_pet(animal="cat", name="Whiskers") # I have a cat named Whiskers.
```

### Mixing Positional and Keyword

```python
describe_pet("dog", name="Rex")   # OK — positional first, then keyword
# describe_pet(animal="dog", "Rex")   ← SyntaxError! keyword can't come before positional
```

**Rule:** Positional arguments must come **before** keyword arguments.

---

## 6. Default Parameter Values

You can give parameters a **default value**. If the caller doesn't provide that argument, the default is used:

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")                # Hello, Alice!  (uses default)
greet("Bob", "Good morning")  # Good morning, Bob!  (overrides default)
greet("Charlie", greeting="Hi")  # Hi, Charlie!
```

### ⚠ Mutable Default Argument — A Classic Python Bug!

**NEVER use a mutable object (list, dict, set) as a default parameter value:**

```python
# ❌ WRONG — the list is created ONCE when the function is defined,
# not every time the function is called!
def add_item(item, items=[]):
    items.append(item)
    return items

print(add_item("apple"))    # ['apple']
print(add_item("banana"))   # ['apple', 'banana']  ← Where did apple come from?!
print(add_item("cherry"))   # ['apple', 'banana', 'cherry']  ← It's persisting!
```

**Fix:** Use `None` as the default and create the list inside the function:

```python
# ✔ CORRECT
def add_item(item, items=None):
    if items is None:
        items = []    # New list created EACH time the function is called
    items.append(item)
    return items

print(add_item("apple"))    # ['apple']
print(add_item("banana"))   # ['banana']  ← Fresh list each time
```

### Parameters Order Rules

```python
def func(pos_only, default="val", *args, keyword_only, **kwargs):
    pass
#       ↑           ↑              ↑        ↑              ↑
#  positional    default       *args   keyword-only     **kwargs
```

Order: positional → default → `*args` → keyword-only → `**kwargs`

---

## 7. `*args` — Variable Positional Arguments

`*args` lets a function accept **any number of positional arguments**. All extra positional arguments are packed into a **tuple** named `args`.

```python
def add_all(*args):
    print(type(args))   # <class 'tuple'>
    print(args)         # (1, 2, 3, 4, 5)
    return sum(args)

print(add_all(1, 2))           # 3
print(add_all(1, 2, 3))        # 6
print(add_all(1, 2, 3, 4, 5))  # 15
```

### Combining with Regular Parameters

```python
def greet(greeting, *names):
    for name in names:
        print(f"{greeting}, {name}!")

greet("Hello", "Alice", "Bob", "Charlie")
# Hello, Alice!
# Hello, Bob!
# Hello, Charlie!
```

### Unpacking When Calling

You can also use `*` to **unpack** a list/tuple into positional arguments:

```python
def add(a, b, c):
    return a + b + c

numbers = [1, 2, 3]
print(add(*numbers))   # 6 — unpacks the list into 3 arguments
```

---

## 8. `**kwargs` — Variable Keyword Arguments

`**kwargs` lets a function accept **any number of keyword arguments**. They are packed into a **dictionary** named `kwargs`.

```python
def print_info(**kwargs):
    print(type(kwargs))   # <class 'dict'>
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=25, city="NYC")
# name: Alice
# age: 25
# city: NYC
```

### Combining `*args` and `**kwargs`

```python
def everything(*args, **kwargs):
    print("Positional args:", args)
    print("Keyword args:", kwargs)

everything(1, 2, 3, name="Alice", age=25)
# Positional args: (1, 2, 3)
# Keyword args: {'name': 'Alice', 'age': 25}
```

### Unpacking a Dictionary When Calling

```python
def greet(name, age, city):
    print(f"{name}, {age}, {city}")

person = {"name": "Alice", "age": 25, "city": "NYC"}
greet(**person)   # Alice, 25, NYC
```

---

## 9. Return Values

`return` sends a value back from the function to the caller:

```python
def add(a, b):
    result = a + b
    return result

total = add(3, 5)
print(total)    # 8
```

### Return Exits the Function Immediately

Once `return` is hit, the function stops — no more code in the function runs:

```python
def check_positive(n):
    if n < 0:
        return "negative"   # ← Function stops here if n < 0
    if n == 0:
        return "zero"       # ← Function stops here if n == 0
    return "positive"       # ← Only runs if both checks above were False

print(check_positive(-5))   # negative
print(check_positive(0))    # zero
print(check_positive(7))    # positive
```

### Return `None`

If a function has no `return` statement (or just `return` with no value), it returns `None`:

```python
def greet(name):
    print(f"Hello, {name}!")
    # No return statement

result = greet("Alice")   # Hello, Alice!
print(result)             # None
```

---

## 10. Returning Multiple Values

Python functions can return multiple values — they are packed into a **tuple**:

```python
def get_min_max(numbers):
    return min(numbers), max(numbers)   # Returns a tuple

result = get_min_max([3, 1, 8, 2, 5])
print(result)          # (1, 8)
print(type(result))    # <class 'tuple'>

# Unpack into separate variables:
minimum, maximum = get_min_max([3, 1, 8, 2, 5])
print(minimum)   # 1
print(maximum)   # 8

# Another example
def divide(a, b):
    quotient = a // b
    remainder = a % b
    return quotient, remainder

q, r = divide(17, 5)
print(f"17 ÷ 5 = {q} remainder {r}")   # 17 ÷ 5 = 3 remainder 2
```

---

## 11. Scope

**Scope** determines where a variable can be seen and accessed. Python uses the **LEGB Rule**:

```
┌──────────────────────────────────────────────────────────┐
│  LEGB Rule — Python's Variable Lookup Order              │
│                                                          │
│  L — Local:     Variables inside the current function    │
│  E — Enclosing: Variables in the enclosing function      │
│                 (for nested functions)                   │
│  G — Global:    Variables at the top of the module       │
│  B — Built-in:  Built-in names (print, len, range, etc.) │
│                                                          │
│  Python searches in this order: L → E → G → B            │
└──────────────────────────────────────────────────────────┘
```

### Local Scope

Variables created inside a function are **local** — only visible inside that function:

```python
def my_function():
    x = 10       # Local variable
    print(x)     # Works: 10

my_function()
print(x)         # ❌ NameError: name 'x' is not defined
```

### Global Scope

Variables created outside any function are **global** — visible everywhere:

```python
name = "Alice"    # Global variable

def greet():
    print(f"Hello, {name}")   # Can READ global variable

greet()   # Hello, Alice
```

### Modifying a Global Variable Inside a Function

By default, you cannot re-assign a global variable from inside a function. You must declare it with `global`:

```python
counter = 0    # Global

def increment():
    global counter     # Tells Python: use the GLOBAL counter, not a new local one
    counter += 1

increment()
increment()
print(counter)   # 2
```

**Best Practice:** Avoid using `global` — it makes code hard to understand. Pass the variable as a parameter and return the updated value instead:

```python
# Better approach:
def increment(counter):
    return counter + 1

counter = 0
counter = increment(counter)
counter = increment(counter)
print(counter)   # 2
```

### `nonlocal` — For Nested Functions

`nonlocal` allows modifying a variable in the **enclosing** function's scope (not global, not local):

```python
def outer():
    count = 0

    def inner():
        nonlocal count    # Use the outer function's 'count'
        count += 1
        print(count)

    inner()    # 1
    inner()    # 2
    inner()    # 3

outer()
```

---

## 12. Lambda Functions

A **lambda** is an **anonymous** (nameless) function defined in a single line. It is used for simple, short operations.

### Syntax

```python
lambda parameters: expression
```

The expression is automatically returned (no `return` keyword needed).

### Comparison: Regular Function vs Lambda

```python
# Regular function
def square(x):
    return x ** 2

# Equivalent lambda
square = lambda x: x ** 2

print(square(4))   # 16 (both work the same)
```

### Multiple Parameters

```python
add = lambda a, b: a + b
print(add(3, 5))    # 8

multiply = lambda a, b, c: a * b * c
print(multiply(2, 3, 4))    # 24
```

### When to Use Lambda

Lambdas are most useful when you need a **short function as an argument** to another function. You don't bother naming it because it's used only once:

```python
# Sort a list of tuples by the second element
points = [(3, 7), (1, 9), (5, 2), (4, 6)]
points.sort(key=lambda point: point[1])
print(points)   # [(5, 2), (4, 6), (3, 7), (1, 9)]

# Sort a list of strings by their length
words = ["banana", "apple", "cherry", "fig"]
words.sort(key=lambda word: len(word))
print(words)    # ['fig', 'apple', 'banana', 'cherry']

# Sort a list of dicts by a field
students = [
    {"name": "Alice", "grade": 88},
    {"name": "Bob", "grade": 95},
    {"name": "Charlie", "grade": 72}
]
students.sort(key=lambda s: s["grade"], reverse=True)
# [{'name': 'Bob', 'grade': 95}, {'name': 'Alice', 'grade': 88}, ...]
```

### Lambda Limitations

A lambda can only have a **single expression** — no multiple lines, no statements, no `if` blocks (well, you can use ternary):

```python
classify = lambda x: "positive" if x > 0 else ("zero" if x == 0 else "negative")
print(classify(5))    # positive
print(classify(0))    # zero
print(classify(-3))   # negative
```

---

## 13. Higher-Order Functions

A **higher-order function** is a function that:
- **Accepts** another function as an argument, OR
- **Returns** a function as its result

This is possible because functions are first-class objects in Python.

```python
def apply_twice(func, value):
    return func(func(value))   # Calls func twice on value

def double(x):
    return x * 2

print(apply_twice(double, 3))   # 12 (double(double(3)) = double(6) = 12)

# Using lambda
print(apply_twice(lambda x: x + 10, 5))   # 25 (5+10=15, 15+10=25)
```

---

## 14. map(), filter(), reduce()

These are built-in higher-order functions that operate on iterables.

### `map(function, iterable)` — Apply Function to Every Element

```python
numbers = [1, 2, 3, 4, 5]

# Square every number
squares = list(map(lambda x: x ** 2, numbers))
print(squares)   # [1, 4, 9, 16, 25]

# Convert strings to integers
str_nums = ["1", "2", "3", "4"]
int_nums = list(map(int, str_nums))   # int is a function itself!
print(int_nums)   # [1, 2, 3, 4]

# map() returns a lazy iterator — wrap with list() to see all values
```

### `filter(function, iterable)` — Keep Elements Where Function Returns True

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Keep only even numbers
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)   # [2, 4, 6, 8, 10]

# Keep words with more than 4 characters
words = ["hi", "hello", "hey", "greetings", "bye"]
long_words = list(filter(lambda w: len(w) > 4, words))
print(long_words)   # ['hello', 'greetings']
```

### `reduce(function, iterable)` — Reduce to a Single Value

`reduce` is in the `functools` module (not built-in):

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# Sum all numbers: ((((1+2)+3)+4)+5) = 15
total = reduce(lambda acc, x: acc + x, numbers)
print(total)   # 15

# Product of all numbers
product = reduce(lambda acc, x: acc * x, numbers)
print(product)   # 120

# With initial value (second argument after iterable)
total_with_initial = reduce(lambda acc, x: acc + x, numbers, 100)
print(total_with_initial)   # 115 (100 + 1+2+3+4+5)
```

```
┌──────────────────────────────────────────────────────────┐
│  How reduce works:                                       │
│                                                          │
│  reduce(+, [1, 2, 3, 4, 5])                              │
│          Step 1: 1 + 2 = 3                               │
│          Step 2: 3 + 3 = 6                               │
│          Step 3: 6 + 4 = 10                              │
│          Step 4: 10 + 5 = 15                             │
│          Result: 15                                      │
└──────────────────────────────────────────────────────────┘
```

### Modern Preference: Use Comprehensions

While `map` and `filter` are valid, most Python developers prefer **list comprehensions** (covered in the Collections file):

```python
# map equivalent
squares = [x ** 2 for x in numbers]

# filter equivalent
evens = [x for x in numbers if x % 2 == 0]
```

---

## 15. Nested Functions

A function defined **inside another function** is called a nested (inner) function:

```python
def outer():
    print("Outer function starts")

    def inner():
        print("Inner function runs")

    inner()   # CAN call inner here
    print("Outer function ends")

outer()
# inner()   ← ❌ NameError: inner is not visible outside outer
```

**Why use nested functions?**
- Hide helper functions that are only needed by one function
- Create closures (see next section)
- Decorators (see later)

---

## 16. Closures

A **closure** is a function that "remembers" the variables from its enclosing scope, even after the outer function has finished executing.

```python
def make_multiplier(factor):
    # 'factor' is a local variable of make_multiplier

    def multiply(number):
        return number * factor   # Captures 'factor' from the enclosing scope

    return multiply    # Returns the inner function (not calling it!)

# Create two different multiplier functions:
double = make_multiplier(2)
triple = make_multiplier(3)

print(double(5))   # 10  — 'factor' is remembered as 2
print(triple(5))   # 15  — 'factor' is remembered as 3
print(double(7))   # 14
```

`double` and `triple` are **closures** — they each remember their own `factor` value.

**Real-world use:**
```python
def make_greeting(greeting):
    def greet(name):
        return f"{greeting}, {name}!"
    return greet

hello = make_greeting("Hello")
hi = make_greeting("Hi")

print(hello("Alice"))   # Hello, Alice!
print(hi("Bob"))        # Hi, Bob!
```

---

## 17. Decorators

A **decorator** is a function that **wraps another function** to modify or enhance its behavior — without changing the original function's code.

```
┌──────────────────────────────────────────────────────────┐
│  Decorator Pattern:                                      │
│                                                          │
│  Original function: does the core job                    │
│  Decorator: adds extra behavior (logging, timing, auth)  │
│                                                          │
│  Result: enhanced function = decorator wraps original    │
└──────────────────────────────────────────────────────────┘
```

### Building a Decorator Step by Step

```python
# Step 1: A simple decorator that adds a border around any function's output
def add_border(func):
    def wrapper(*args, **kwargs):
        print("=" * 30)
        result = func(*args, **kwargs)  # Call the original function
        print("=" * 30)
        return result
    return wrapper   # Return the wrapper function

# Step 2: Apply the decorator manually
def greet(name):
    print(f"Hello, {name}!")

greet = add_border(greet)   # Wrap it manually
greet("Alice")
# ==============================
# Hello, Alice!
# ==============================
```

### The `@` Syntax (Syntactic Sugar)

Python provides a clean way to apply decorators using `@`:

```python
def add_border(func):
    def wrapper(*args, **kwargs):
        print("=" * 30)
        result = func(*args, **kwargs)
        print("=" * 30)
        return result
    return wrapper

@add_border    # ← Decorator applied here (equivalent to greet = add_border(greet))
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
# ==============================
# Hello, Alice!
# ==============================
```

### Practical Decorator: Timing a Function

```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
    print("Done!")

slow_function()
# Done!
# slow_function took 1.0012 seconds
```

### `functools.wraps` — Preserve Function Metadata

When you wrap a function, the wrapper hides the original function's name and docstring. Fix this with `@functools.wraps`:

```python
import functools

def my_decorator(func):
    @functools.wraps(func)   # Preserves func's __name__, __doc__
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def my_func():
    """My function's docstring."""
    pass

print(my_func.__name__)   # my_func (not 'wrapper')
print(my_func.__doc__)    # My function's docstring.
```

---

## 18. Recursion

**Recursion** is when a function calls **itself**. It is useful for problems that can be broken into smaller identical sub-problems.

### How Recursion Works

```
┌──────────────────────────────────────────────────────────┐
│  factorial(4)                                            │
│    = 4 * factorial(3)                                    │
│         = 3 * factorial(2)                               │
│              = 2 * factorial(1)                          │
│                   = 1   ← BASE CASE (stops recursion)    │
│              = 2 * 1 = 2                                 │
│         = 3 * 2 = 6                                      │
│    = 4 * 6 = 24                                          │
└──────────────────────────────────────────────────────────┘
```

```python
def factorial(n):
    # Base case: stop recursion here
    if n == 0 or n == 1:
        return 1
    # Recursive case: function calls itself
    return n * factorial(n - 1)

print(factorial(5))   # 120
print(factorial(0))   # 1
```

**Every recursive function MUST have:**
1. **Base case** — the stopping condition (prevents infinite recursion)
2. **Recursive case** — calls itself with a smaller problem

### Fibonacci Using Recursion

```python
def fibonacci(n):
    if n <= 1:
        return n   # f(0) = 0, f(1) = 1
    return fibonacci(n - 1) + fibonacci(n - 2)

for i in range(10):
    print(fibonacci(i), end=" ")
# 0 1 1 2 3 5 8 13 21 34
```

⚠ This naive recursive Fibonacci is **exponentially slow** for large n. Use memoization or iteration for real code:

```python
from functools import lru_cache

@lru_cache(maxsize=None)   # Cache results to avoid recalculating
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(100))   # Very fast now!
```

### Recursion Limit

Python limits recursion depth to ~1000 to prevent stack overflow:

```python
import sys
print(sys.getrecursionlimit())   # 1000 (default)
sys.setrecursionlimit(2000)      # Increase if needed (use carefully)
```

---

## 19. Type Hints

Python 3.5+ supports optional **type hints** (also called type annotations). They don't enforce types at runtime but:
- Improve code readability
- Enable IDE autocompletion and error detection
- Help tools like `mypy` catch bugs before running

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b

def get_price(item: str, quantity: int = 1) -> float:
    prices = {"apple": 0.5, "banana": 0.3}
    return prices.get(item, 0) * quantity
```

### Complex Type Hints

```python
from typing import List, Dict, Tuple, Optional, Union

def process_numbers(numbers: List[int]) -> List[float]:
    return [x / 2 for x in numbers]

def find_user(user_id: int) -> Optional[Dict[str, str]]:
    # Optional means it can return Dict or None
    users = {1: {"name": "Alice"}}
    return users.get(user_id)

def add_flexible(a: Union[int, float], b: Union[int, float]) -> float:
    return float(a + b)

# Python 3.10+ — cleaner syntax
def add_new(a: int | float, b: int | float) -> float:
    return float(a + b)
```

---

## 20. Docstrings

A **docstring** is a string literal placed right after the `def` line to document a function. It becomes the `__doc__` attribute of the function.

```python
def calculate_bmi(weight_kg: float, height_m: float) -> float:
    """
    Calculate the Body Mass Index (BMI).

    BMI is calculated as weight in kilograms divided by
    height in meters squared.

    Parameters:
        weight_kg (float): Weight in kilograms.
        height_m (float): Height in meters.

    Returns:
        float: The BMI value.

    Raises:
        ValueError: If height_m is zero (division by zero).

    Examples:
        >>> calculate_bmi(70, 1.75)
        22.857142857142858
        >>> calculate_bmi(90, 1.80)
        27.777777777777775
    """
    if height_m == 0:
        raise ValueError("Height cannot be zero.")
    return weight_kg / (height_m ** 2)

# Accessing the docstring:
print(calculate_bmi.__doc__)

# In VS Code / PyCharm: hover over the function call to see the docstring popup
```

---

## Summary

```
┌────────────────────────────────────────────────────────────┐
│               Python Functions Summary                     │
│                                                            │
│  def name(params): → define a function                     │
│  Parameters: positional, keyword, default, *args, **kwargs │
│  return: sends value back (default = None)                 │
│  Multiple return values → packed into a tuple              │
│                                                            │
│  Scope (LEGB): Local → Enclosing → Global → Built-in       │
│  global keyword: modify global from inside function        │
│  nonlocal keyword: modify enclosing from inner function    │
│                                                            │
│  Lambda: anonymous one-liner function                      │
│  lambda x: expression                                      │
│                                                            │
│  Higher-order functions: accept/return functions           │
│  map(): apply to every element                             │
│  filter(): keep elements where function returns True       │
│  reduce(): fold list to single value                       │
│                                                            │
│  Closures: inner function captures outer variables         │
│  Decorators: wrap functions to add behavior (@syntax)      │
│  Recursion: function calls itself (needs base case!)       │
│  Type hints: str, int, float, Optional, List, Dict, etc.   │
└────────────────────────────────────────────────────────────┘
```

**Next:** [04-Python-Collections.md](04-Python-Collections.md) — Lists, Tuples, Dictionaries, and Sets.
