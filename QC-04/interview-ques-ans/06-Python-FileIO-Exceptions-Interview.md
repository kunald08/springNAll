# Python File I/O, Exception Handling & Modules — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. How do you read and write files in Python?

**Answer:**
The recommended way is using the `with` statement (context manager), which automatically closes the file when the block ends — even if an error occurs:

```python
# Reading
with open("data.txt", "r", encoding="utf-8") as file:
    content = file.read()

# Writing (creates new or overwrites existing)
with open("output.txt", "w", encoding="utf-8") as file:
    file.write("Hello, World!")

# Appending (adds to end)
with open("log.txt", "a", encoding="utf-8") as file:
    file.write("New log entry\n")
```

The key file modes are: `"r"` for read (default), `"w"` for write (overwrites!), `"a"` for append, `"rb"`/`"wb"` for binary. I always specify `encoding="utf-8"` to avoid encoding issues. Without the `with` statement, I'd have to manually call `file.close()` in a `finally` block, which is error-prone.

---

## 2. What is the difference between `read()`, `readline()`, and `readlines()`?

**Answer:**
- **`read()`** — reads the **entire file** as a single string. Good for small files, but can cause memory issues with large files:
  ```python
  content = file.read()          # "line1\nline2\nline3"
  ```

- **`readline()`** — reads **one line** at a time (including the newline character). Returns an empty string at EOF:
  ```python
  first_line = file.readline()   # "line1\n"
  ```

- **`readlines()`** — reads **all lines** into a **list**, each element being a line:
  ```python
  lines = file.readlines()       # ["line1\n", "line2\n", "line3"]
  ```

For large files, the most **memory-efficient** approach is iterating line by line:
```python
with open("big_file.txt") as f:
    for line in f:               # File object is an iterator
        process(line)
```
This reads one line at a time without loading the entire file into memory.

---

## 3. What is the `with` statement? Why should you always use it with files?

**Answer:**
The `with` statement is a **context manager** that ensures resources are properly cleaned up after use, even if an exception occurs. For files, it guarantees the file is closed:

```python
with open("data.txt") as file:
    data = file.read()
# File is automatically closed here — guaranteed
```

Without `with`, I'd have to do:
```python
file = open("data.txt")
try:
    data = file.read()
finally:
    file.close()     # Must be in finally to ensure it runs
```

The `with` approach is cleaner, less error-prone, and the Pythonic standard. It works with any object that implements the **context manager protocol** (`__enter__` and `__exit__` methods) — not just files, but also database connections, locks, and network sockets.

---

## 4. How do you work with CSV files in Python?

**Answer:**
Python's built-in `csv` module handles CSV reading and writing:

```python
import csv

# Reading
with open("data.csv") as file:
    reader = csv.reader(file)
    header = next(reader)           # Skip header row
    for row in reader:
        print(row)                  # Each row is a list

# Using DictReader — maps each row to a dictionary
with open("data.csv") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row["name"], row["age"])   # Access by column name

# Writing
with open("output.csv", "w", newline="") as file:
    writer = csv.writer(file)
    writer.writerow(["name", "age"])     # Header
    writer.writerow(["Alice", 25])       # Data row
```

I use `DictReader` when I want to access columns by name (more readable). The `newline=""` parameter in writing prevents extra blank lines on Windows. For complex data analysis, I'd use `pandas` instead, but the built-in `csv` module works well for straightforward tasks.

---

## 5. How do you work with JSON files in Python?

**Answer:**
Python has a built-in `json` module for JSON serialization and deserialization:

```python
import json

# Reading JSON from a file
with open("config.json") as file:
    data = json.load(file)           # Returns a Python dict/list

# Writing Python data to a JSON file
with open("output.json", "w") as file:
    json.dump(data, file, indent=4)  # indent for pretty printing

# Converting between JSON strings and Python objects
json_string = json.dumps({"name": "Alice", "age": 25})   # Dict → JSON string
python_dict = json.loads(json_string)                      # JSON string → Dict
```

The mapping is: JSON object → Python `dict`, JSON array → Python `list`, `null` → `None`, `true`/`false` → `True`/`False`. Note that `json.load()` reads from a **file**, while `json.loads()` reads from a **string** (the "s" stands for "string"). This is commonly used in REST APIs and configuration management.

---

## 6. What is exception handling? Why is it important?

**Answer:**
Exception handling is Python's mechanism for dealing with **runtime errors gracefully** instead of letting the program crash. When an error occurs during execution, Python raises an **exception object**. If I don't handle it, the program terminates with a traceback.

Using `try/except`, I can catch these exceptions and respond appropriately — showing user-friendly messages, retrying operations, logging errors, or falling back to defaults:

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    result = 0
    print("Cannot divide by zero, using default")
```

It's important because in real applications, many things can go wrong — network failures, invalid user input, missing files, database errors. Exception handling lets me build **robust, production-ready** software that handles failures gracefully instead of crashing.

---

## 7. Explain the `try/except/else/finally` block.

**Answer:**
These four clauses work together for complete exception handling:

```python
try:
    file = open("data.txt")
    data = file.read()
except FileNotFoundError:
    print("File not found!")           # Runs ONLY if exception occurs
except PermissionError:
    print("No permission!")
else:
    print(f"Read {len(data)} chars")   # Runs ONLY if NO exception
finally:
    print("Cleanup done")             # ALWAYS runs, no matter what
```

- **`try`** — code that might raise an exception
- **`except`** — handles specific exceptions. I can have multiple `except` blocks for different exception types
- **`else`** — runs only if the `try` block succeeded without any exception. It's useful for separating "normal" code from error handling
- **`finally`** — runs ALWAYS, whether an exception occurred or not. Perfect for cleanup like closing connections or releasing resources

The `else` and `finally` are optional. Best practice: catch **specific** exceptions, not bare `except:` which catches everything including `KeyboardInterrupt` and `SystemExit`.

---

## 8. What is the exception hierarchy in Python?

**Answer:**
All exceptions inherit from `BaseException`. The main branch for errors is `Exception`:

```
BaseException
├── SystemExit
├── KeyboardInterrupt
├── GeneratorExit
└── Exception
    ├── ValueError
    ├── TypeError
    ├── KeyError
    ├── IndexError
    ├── FileNotFoundError
    ├── ZeroDivisionError
    ├── AttributeError
    ├── ImportError
    ├── RuntimeError
    └── ... many more
```

This hierarchy matters because `except Exception` catches all standard errors but NOT `SystemExit`, `KeyboardInterrupt`, or `GeneratorExit` — which is usually what I want. A bare `except:` catches everything, which is dangerous because it would catch Ctrl+C (KeyboardInterrupt) and prevent the user from stopping the program.

Always catch the most **specific** exception first, because Python checks `except` blocks **top to bottom**, and the first match wins.

---

## 9. How do you raise exceptions? When should you?

**Answer:**
I use the `raise` keyword to throw an exception explicitly:

```python
def set_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    if not isinstance(age, int):
        raise TypeError("Age must be an integer")
    return age
```

I raise exceptions when:
- **Input validation fails** — bad arguments that the function can't handle
- **Preconditions aren't met** — something that should be true isn't
- **Impossible states** — the program reaches a point that shouldn't be possible

I can also **re-raise** an exception after logging it:
```python
try:
    risky_operation()
except Exception as e:
    logging.error(f"Error: {e}")
    raise    # Re-raises the same exception
```

The principle is: **raise exceptions for exceptional situations**, not for normal control flow. A missing file might be exceptional; reaching the end of a list is not.

---

## 10. How do you create custom exceptions?

**Answer:**
I create custom exceptions by inheriting from `Exception` (or a more specific built-in exception):

```python
class InsufficientFundsError(Exception):
    def __init__(self, balance, amount):
        self.balance = balance
        self.amount = amount
        super().__init__(
            f"Cannot withdraw ${amount}. Balance: ${balance}"
        )

class BankAccount:
    def __init__(self, balance):
        self.balance = balance

    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientFundsError(self.balance, amount)
        self.balance -= amount
```

Custom exceptions make code **more readable** and allow callers to catch **specific** error types rather than parsing error messages. I create them when built-in exceptions don't convey enough meaning about what went wrong. It's a best practice to create an application-level base exception and derive specific ones from it.

---

## 11. What is the difference between syntax errors and exceptions?

**Answer:**
**Syntax errors** are caught by the **parser** before the program runs. They mean the code itself is malformed — wrong structure, missing colons, bad indentation:
```python
if True    # SyntaxError: missing colon — program never starts
    print("hello")
```

**Exceptions** occur **during runtime** — the code is syntactically valid but something goes wrong during execution:
```python
x = 10 / 0    # ZeroDivisionError — syntax is fine, math is not
```

Syntax errors cannot be handled with `try/except` because the code never gets to execute. Exceptions can and should be handled. In production, I use linters and type checkers to catch potential errors before runtime, but runtime exceptions need proper handling in the code.

---

## 12. What are Python modules?

**Answer:**
A module is simply a **Python file** (`.py`) containing functions, classes, and variables that I can import and use in other files:

```python
# math_utils.py — this IS a module
def add(a, b):
    return a + b

PI = 3.14159
```

```python
# main.py — importing the module
import math_utils
print(math_utils.add(3, 5))
print(math_utils.PI)
```

Modules serve two purposes: **code organization** (breaking a large program into manageable files) and **code reuse** (using the same code across multiple programs). Python comes with a rich **standard library** of modules — `os`, `sys`, `json`, `datetime`, `re`, `math`, and many more. Third-party modules are installed via `pip`.

---

## 13. What are the different ways to import a module?

**Answer:**
Python provides several import styles:

```python
# Import the entire module
import math
math.sqrt(16)        # Must use module prefix

# Import with alias
import numpy as np
np.array([1, 2, 3])

# Import specific items
from math import sqrt, pi
sqrt(16)             # No prefix needed

# Import everything (avoid in production!)
from math import *
sqrt(16)             # No prefix, but pollutes namespace
```

**Best practices:**
- `import module` is the safest — makes it clear where each function comes from
- `from module import specific_item` is fine for commonly used items
- `from module import *` should be avoided because it pollutes the namespace and makes it unclear where functions come from
- Use **aliases** for long module names: `import pandas as pd`

Imports are executed **once** — Python caches them in `sys.modules`. Re-importing the same module just returns the cached version.

---

## 14. What is the difference between a module and a package?

**Answer:**
A **module** is a single Python file (`.py`).

A **package** is a **directory of modules** that contains an `__init__.py` file (which can be empty). Packages let me organize related modules into a folder hierarchy:

```
mypackage/               # Package
├── __init__.py          # Makes it a package
├── module_a.py          # Module
├── module_b.py          # Module
└── subpackage/          # Sub-package
    ├── __init__.py
    └── module_c.py
```

```python
from mypackage import module_a
from mypackage.subpackage import module_c
```

In Python 3.3+, there are also **namespace packages** (implicit packages without `__init__.py`), but explicit packages with `__init__.py` are still the convention. The `__init__.py` can also contain initialization code or define what gets exported with `__all__`.

---

## 15. What is `pip`? How do you manage dependencies?

**Answer:**
`pip` is Python's **package manager** — it installs third-party packages from **PyPI** (Python Package Index, with over 400,000 packages):

```bash
pip install requests              # Install a package
pip install requests==2.28.0      # Install specific version
pip uninstall requests            # Uninstall
pip list                          # Show installed packages
pip freeze > requirements.txt     # Export dependencies
pip install -r requirements.txt   # Install from requirements file
```

**`requirements.txt`** is the standard way to pin dependencies for a project. For more modern dependency management, I use `pyproject.toml` (PEP 621). Tools like **Poetry** or **Pipenv** offer enhanced features like lock files and automated virtual environment management.

Best practice: always install packages in a **virtual environment**, never in the system Python, to avoid dependency conflicts between projects.

---

## 16. What are virtual environments? Why are they important?

**Answer:**
A virtual environment is an **isolated Python environment** with its own interpreter and packages, independent of the system Python and other projects:

```bash
python -m venv myenv          # Create virtual environment
source myenv/bin/activate     # Activate (Linux/Mac)
myenv\Scripts\activate        # Activate (Windows)
pip install flask             # Install inside this environment only
deactivate                    # Leave the environment
```

They're important because different projects may need **different versions** of the same package. Project A might need Django 3.2 while Project B needs Django 4.0. Without virtual environments, they'd conflict.

Virtual environments provide:
- **Isolation** — each project has its own dependencies
- **Reproducibility** — I can recreate the exact environment on another machine
- **Clean system** — no clutter in the system Python

Alternatives include `conda` (popular in data science), `Poetry`, and `Pipenv`.

---

## 17. What is `if __name__ == "__main__":`? Why is it used?

**Answer:**
Every Python file has a built-in variable `__name__`. When a file is **run directly**, `__name__` is set to `"__main__"`. When it's **imported** as a module, `__name__` is set to the module's name.

```python
# utils.py
def add(a, b):
    return a + b

if __name__ == "__main__":
    # This code only runs when utils.py is executed directly
    print(add(3, 5))    # Testing
```

If I run `python utils.py`, the condition is `True` and the test code runs. If another file does `import utils`, the condition is `False` and the test code is skipped.

I use it to:
- Put **test/demo code** that shouldn't run on import
- Create files that work as both **importable modules** and **standalone scripts**
- Define the **entry point** of an application

---

## 18. What are some useful standard library modules?

**Answer:**
Python's standard library is extensive — "batteries included." Key modules:

- **`os`** — operating system interaction: file paths, directory operations, environment variables
- **`sys`** — system-specific parameters: command-line arguments, Python path, exit
- **`datetime`** — date and time handling
- **`json`** — JSON encoding/decoding
- **`re`** — regular expressions
- **`math`** — mathematical functions (sqrt, ceil, floor, etc.)
- **`random`** — random number generation
- **`collections`** — specialized containers (Counter, defaultdict, deque, namedtuple)
- **`itertools`** — efficient iteration tools (chain, product, permutations)
- **`functools`** — higher-order functions (reduce, lru_cache, wraps)
- **`pathlib`** — modern path handling (preferred over `os.path`)
- **`logging`** — production-grade logging
- **`unittest`** — testing framework
- **`csv`** — CSV file processing

The strength of the standard library is that I don't need to install anything extra for these common tasks.

---

## 19. What is the `os` module vs `pathlib`?

**Answer:**
Both handle file system operations, but `pathlib` (introduced in Python 3.4) is the **modern, object-oriented** approach:

```python
# os module — string-based, procedural
import os
path = os.path.join("home", "user", "documents", "file.txt")
exists = os.path.exists(path)
filename = os.path.basename(path)

# pathlib — object-oriented, more readable
from pathlib import Path
path = Path("home") / "user" / "documents" / "file.txt"
exists = path.exists()
filename = path.name
content = path.read_text()    # Read entire file in one line
```

`pathlib` is preferred in modern Python because it's more readable, less error-prone (no string concatenation issues with separators), and provides convenient methods like `read_text()`, `write_text()`, `glob()`, and `iterdir()`. The `/` operator for path joining is particularly elegant.

---

## 20. How does Python handle file encoding? Why does it matter?

**Answer:**
File encoding determines how text characters are represented as bytes. **UTF-8** is the universal standard that can represent every character in every language.

```python
# Always specify encoding explicitly
with open("file.txt", "r", encoding="utf-8") as f:
    content = f.read()
```

It matters because if I read a file with the wrong encoding, I get garbled text (`Ã©` instead of `é`) or a `UnicodeDecodeError`. The default encoding varies by OS — Windows might use `cp1252`, while Linux uses UTF-8.

Best practices:
- **Always specify `encoding="utf-8"`** explicitly when opening text files
- When dealing with unknown encodings, use `errors="replace"` or `errors="ignore"` to handle problematic characters
- For binary files (images, PDFs), use binary mode (`"rb"`, `"wb"`) — encoding doesn't apply

Python 3 strings are Unicode by default, but files on disk are bytes. The encoding bridges that gap.
