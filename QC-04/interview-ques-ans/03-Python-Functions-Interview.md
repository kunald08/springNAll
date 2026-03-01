# Python Functions — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. What is a function in Python? Why do we use functions?

**Answer:**
A function is a **reusable block of code** defined with the `def` keyword that performs a specific task. I call it by its name followed by parentheses.

We use functions because of the **DRY principle** — Don't Repeat Yourself. Instead of writing the same code in multiple places, I write it once in a function and call it wherever needed. Functions make code **readable** (breaking a large program into named chunks), **maintainable** (fix a bug in one place, fixed everywhere), and **testable** (I can test each function independently).

```python
def greet(name):
    return f"Hello, {name}!"

greet("Alice")  # "Hello, Alice!"
```

---

## 2. What is the difference between parameters and arguments?

**Answer:**
**Parameters** are the variables listed in the **function definition** — they're placeholders for values the function expects.

**Arguments** are the actual **values** passed when **calling** the function.

```python
def add(a, b):       # a and b are PARAMETERS
    return a + b

add(3, 5)            # 3 and 5 are ARGUMENTS
```

Think of parameters as the "slots" and arguments as the "values plugged into those slots." In practice, people often use the terms interchangeably, but the distinction is important.

---

## 3. What are the different types of arguments in Python?

**Answer:**
Python supports four types of arguments:

1. **Positional arguments** — matched by position: `add(3, 5)` — 3 goes to the first parameter, 5 to the second.

2. **Keyword arguments** — explicitly named: `add(a=3, b=5)` — order doesn't matter because I'm specifying which parameter gets which value.

3. **Default arguments** — parameters with default values: `def greet(name="World")`. If no argument is passed, the default is used.

4. **Variable-length arguments:**
   - `*args` — collects extra positional arguments into a **tuple**: `def func(*args)`
   - `**kwargs` — collects extra keyword arguments into a **dictionary**: `def func(**kwargs)`

The order in a function definition must be: positional → `*args` → keyword-only → `**kwargs`.

---

## 4. What is `*args` and `**kwargs`? When do you use them?

**Answer:**
`*args` allows a function to accept any number of **positional arguments**. They're collected into a **tuple**:
```python
def add(*args):
    return sum(args)

add(1, 2, 3, 4)  # args = (1, 2, 3, 4), returns 10
```

`**kwargs` allows a function to accept any number of **keyword arguments**. They're collected into a **dictionary**:
```python
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=25)
# kwargs = {"name": "Alice", "age": 25}
```

I use them when I don't know in advance how many arguments a function might receive — for example, in wrapper functions, decorators, or functions that delegate to other functions. The names `args` and `kwargs` are conventions; the `*` and `**` are the actual syntax.

---

## 5. What is the difference between `return` and `print`?

**Answer:**
`return` sends a value **back to the caller** and terminates the function. The returned value can be stored, used in expressions, or passed to other functions.

`print()` sends text to the **console** for display. It does not affect the function's output — the function still returns `None` unless there's an explicit `return`.

```python
def multiply_return(a, b):
    return a * b         # Returns value, caller can use it

def multiply_print(a, b):
    print(a * b)         # Displays value, but returns None

result = multiply_return(3, 4)  # result = 12
result = multiply_print(3, 4)   # Prints 12, but result = None
```

In production code, functions should return values. Print is for debugging and user-facing output only.

---

## 6. What happens if a function doesn't have a `return` statement?

**Answer:**
If a function doesn't have an explicit `return` statement, or if it has a bare `return` without a value, it returns **`None`** by default.

```python
def greet(name):
    print(f"Hello, {name}")    # No return statement

result = greet("Alice")
print(result)    # None
```

Every function in Python always returns something — if I don't specify, it's `None`. This is also true when the function reaches the end of its body without hitting a `return`.

---

## 7. How can a function return multiple values?

**Answer:**
A Python function can return multiple values by returning them as a **tuple**, and I can **unpack** them on the receiving end:

```python
def min_max(numbers):
    return min(numbers), max(numbers)    # Returns a tuple

low, high = min_max([3, 1, 7, 2, 9])    # Tuple unpacking
print(low)    # 1
print(high)   # 9
```

Under the hood, `return min_val, max_val` is actually returning a single tuple `(min_val, max_val)`. Python's tuple unpacking makes it look like multiple return values. I could also return a `dict` or a `namedtuple` if I want named results, which is more readable for complex returns.

---

## 8. What is variable scope in Python? Explain LEGB rule.

**Answer:**
Scope determines where a variable is accessible. Python follows the **LEGB rule** — it looks up variable names in this order:

1. **L — Local:** Inside the current function
2. **E — Enclosing:** Inside any enclosing (outer) functions (for nested functions)
3. **G — Global:** At the module level (top-level of the file)
4. **B — Built-in:** Python's built-in names like `print`, `len`, `range`

```python
x = "global"           # G

def outer():
    x = "enclosing"    # E
    def inner():
        x = "local"    # L
        print(x)       # Prints "local"
    inner()

outer()
```

Python searches from inside out: Local → Enclosing → Global → Built-in. The first match wins. If it doesn't find the variable anywhere, it raises a `NameError`.

---

## 9. What are `global` and `nonlocal` keywords?

**Answer:**
By default, assigning to a variable inside a function creates a **local** variable. If I want to modify a variable from an outer scope, I need these keywords:

**`global`** — tells Python that I want to use and modify the **module-level** variable, not create a local one:
```python
count = 0
def increment():
    global count
    count += 1     # Modifies the global 'count'
```

**`nonlocal`** — tells Python that I want to modify a variable from the **enclosing function's** scope (used in nested functions):
```python
def outer():
    count = 0
    def inner():
        nonlocal count
        count += 1     # Modifies outer's 'count'
    inner()
```

In practice, I avoid `global` because it creates hidden dependencies and makes code harder to test. Instead, I pass values as arguments and return results.

---

## 10. What is a lambda function?

**Answer:**
A lambda function is an **anonymous, single-expression** function defined with the `lambda` keyword:

```python
square = lambda x: x ** 2
square(5)    # 25

# Equivalent to:
def square(x):
    return x ** 2
```

Lambda functions are limited to a **single expression** — no statements, no assignments, no multi-line logic. The expression's result is automatically returned.

I mostly use them as short **throwaway functions** passed to higher-order functions like `sorted()`, `map()`, and `filter()`:
```python
students = [("Alice", 85), ("Bob", 72), ("Charlie", 90)]
sorted(students, key=lambda s: s[1])    # Sort by grade
```

If the logic is complex, I define a regular named function instead — readability matters more.

---

## 11. What are higher-order functions?

**Answer:**
A higher-order function is a function that either **takes a function as an argument** or **returns a function**. This is possible because in Python, **functions are first-class objects** — they can be assigned to variables, passed around, and returned.

```python
# Accepting a function as argument
def apply(func, value):
    return func(value)

apply(str.upper, "hello")    # "HELLO"

# Returning a function
def multiplier(n):
    def multiply(x):
        return x * n
    return multiply

double = multiplier(2)
double(5)    # 10
```

Python has built-in higher-order functions like `map()`, `filter()`, `sorted()`, and `reduce()`. Decorators are also higher-order functions. This is a core concept in functional programming.

---

## 12. What are `map()`, `filter()`, and `reduce()`?

**Answer:**
These are built-in higher-order functions for processing iterables:

**`map(func, iterable)`** — applies a function to **every item** and returns an iterator of results:
```python
list(map(str.upper, ["hello", "world"]))   # ["HELLO", "WORLD"]
```

**`filter(func, iterable)`** — keeps only items where the function returns **`True`**:
```python
list(filter(lambda x: x > 0, [-1, 2, -3, 4]))   # [2, 4]
```

**`reduce(func, iterable)`** — from `functools` module — cumulatively applies a function to reduce the iterable to a **single value**:
```python
from functools import reduce
reduce(lambda a, b: a + b, [1, 2, 3, 4])   # 10 (((1+2)+3)+4)
```

In modern Python, I often prefer **list comprehensions** over `map()` and `filter()` because they're more readable: `[x.upper() for x in words]` instead of `list(map(str.upper, words))`.

---

## 13. What is a closure in Python?

**Answer:**
A closure is a **nested function that remembers and has access to variables from its enclosing function's scope**, even after the outer function has finished executing.

```python
def outer(message):
    def inner():
        print(message)    # 'message' is from the enclosing scope
    return inner

greet = outer("Hello!")   # outer() finishes, but...
greet()                   # Prints "Hello!" — inner() still remembers 'message'
```

Three conditions make a closure:
1. A nested function
2. The inner function references a variable from the outer function
3. The outer function returns the inner function

Closures are the foundation of **decorators** and are useful for creating factory functions, data hiding, and callbacks. The captured variables are stored in the function's `__closure__` attribute.

---

## 14. What are decorators in Python?

**Answer:**
A decorator is a function that **takes another function as input**, adds some behavior to it, and **returns a modified version** without changing the original function's source code. It's the **open/closed principle** in action.

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper

@log_call
def add(a, b):
    return a + b

add(3, 5)
# Output: Calling add → returns 8 → Finished add
```

The `@log_call` syntax is syntactic sugar for `add = log_call(add)`. Decorators are heavily used in frameworks — Django uses `@login_required`, `@api_view`; Flask uses `@app.route()`.

A best practice is to use `@functools.wraps(func)` inside the wrapper to preserve the original function's name, docstring, and metadata.

---

## 15. What is the difference between `@staticmethod` and `@classmethod`?

**Answer:**
While these are OOP concepts, they're implemented as decorators on functions:

**`@staticmethod`** — belongs to the class but doesn't receive any implicit first argument. It's basically a regular function that lives inside the class for organizational purposes:
```python
class Math:
    @staticmethod
    def add(a, b):
        return a + b
```

**`@classmethod`** — receives the **class itself** as the first argument (`cls`), not an instance. It's used for factory methods or methods that need to work with the class:
```python
class User:
    def __init__(self, name):
        self.name = name

    @classmethod
    def from_string(cls, data):
        name = data.split(",")[0]
        return cls(name)     # Creates a new User
```

The key difference: `staticmethod` knows nothing about the class, `classmethod` receives the class and can create instances or access class attributes.

---

## 16. What is recursion? What are its drawbacks?

**Answer:**
Recursion is when a function **calls itself** to solve a problem by breaking it into smaller subproblems. Every recursive function needs a **base case** (when to stop) and a **recursive case** (call itself with smaller input).

```python
def factorial(n):
    if n <= 1:        # Base case
        return 1
    return n * factorial(n - 1)   # Recursive case
```

**Drawbacks:**
- **Stack overflow**: Python has a default recursion limit of 1000. Deep recursion causes `RecursionError`.
- **Performance**: Each call adds a frame to the call stack. Recursive solutions can be slower than iterative ones.
- **Memory**: Each frame consumes memory.

Python does **not** optimize tail recursion, unlike some other languages. So for problems requiring deep recursion (like tree traversals with huge depth), I'd convert to an iterative approach using an explicit stack.

---

## 17. What are type hints in Python?

**Answer:**
Type hints, introduced in **Python 3.5** (PEP 484), are optional annotations that indicate the expected types of function parameters and return values:

```python
def add(a: int, b: int) -> int:
    return a + b

name: str = "Alice"
scores: list[int] = [90, 85, 95]
```

The critical thing to understand is that Python **does not enforce** type hints at runtime — they're purely informational. I can annotate a parameter as `int` and pass a `str`, and Python won't complain. The value comes from:
- **IDE support** — autocompletion and error detection
- **Static analysis tools** like `mypy` that check types before runtime
- **Documentation** — the code itself documents expected types
- **Readability** — other developers immediately understand what a function expects

---

## 18. What is a docstring? How is it different from a comment?

**Answer:**
A docstring is a **string literal** placed as the first statement in a function, class, or module. It serves as the official documentation and is accessible at runtime via the `__doc__` attribute or `help()`:

```python
def calculate_area(radius):
    """Calculate the area of a circle given its radius."""
    return 3.14159 * radius ** 2

print(calculate_area.__doc__)   # Accessible at runtime
```

**Comments** (starting with `#`) are completely ignored by Python — they're for developers reading the source code and are stripped during compilation.

The key difference: docstrings are **part of the program** — they're stored in the object and accessible via tools and reflection. Comments are invisible to the running code. PEP 257 defines docstring conventions.

---

## 19. What is the difference between `*args` unpacking and `*` in function calls?

**Answer:**
The `*` operator serves dual purposes:

**In function definitions**, `*args` **packs** extra positional arguments into a tuple:
```python
def func(*args):    # Packing
    print(args)     # (1, 2, 3)

func(1, 2, 3)
```

**In function calls**, `*` **unpacks** an iterable into separate arguments:
```python
numbers = [1, 2, 3]
func(*numbers)       # Same as func(1, 2, 3)
```

Similarly, `**` packs into a dict in definitions and unpacks a dict into keyword arguments in calls:
```python
config = {"host": "localhost", "port": 8080}
connect(**config)    # Same as connect(host="localhost", port=8080)
```

This symmetry is powerful for creating flexible, generic functions.

---

## 20. What is a generator function? How is it different from a regular function?

**Answer:**
A generator function uses `yield` instead of `return`. When called, it doesn't execute immediately — it returns a **generator object** that produces values **lazily**, one at a time:

```python
def countdown(n):
    while n > 0:
        yield n       # Produces a value and pauses
        n -= 1

for num in countdown(5):
    print(num)    # 5, 4, 3, 2, 1
```

The key differences:
- A regular function runs to completion and returns a single value. A generator **pauses** at each `yield` and resumes from where it left off.
- Regular functions store all results in memory. Generators produce values **one at a time**, making them extremely memory-efficient for large datasets.
- Generator state is maintained automatically between `yield` calls.

I use generators when working with large files, infinite sequences, or pipelines where loading everything into memory isn't feasible. They're the foundation of Python's iteration protocol.
