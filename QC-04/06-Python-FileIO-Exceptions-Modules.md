# Python File I/O, Exception Handling & Modules — From Zero to Expert

## Table of Contents
1. [File I/O — Introduction](#1-file-io-introduction)
2. [Opening and Closing Files](#2-opening-and-closing-files)
3. [Reading Files](#3-reading-files)
4. [Writing Files](#4-writing-files)
5. [Appending to Files](#5-appending-to-files)
6. [Working with File Paths](#6-working-with-file-paths)
7. [Working with CSV Files](#7-csv-files)
8. [Working with JSON Files](#8-json-files)
9. [Exception Handling — Introduction](#9-exception-handling-introduction)
10. [try / except](#10-try-except)
11. [Multiple except Clauses](#11-multiple-except)
12. [else and finally](#12-else-and-finally)
13. [Raising Exceptions](#13-raising-exceptions)
14. [Custom Exceptions](#14-custom-exceptions)
15. [Python Modules](#15-python-modules)
16. [Import Statements](#16-import-statements)
17. [Python Packages](#17-python-packages)
18. [pip — Python Package Manager](#18-pip)
19. [Virtual Environments](#19-virtual-environments)
20. [Useful Standard Library Modules](#20-standard-library)

---

## 1. File I/O Introduction

**File I/O** (Input/Output) means reading data from files and writing data to files. Almost every real program works with files — reading config files, logging, saving user data, etc.

```
┌──────────────────────────────────────────────────────────┐
│              File Operations Flow                        │
│                                                          │
│  1. Open the file   → get a FILE OBJECT                  │
│  2. Read/Write      → use file object methods            │
│  3. Close the file  → release the resource               │
│                                                          │
│  ALWAYS close files after you're done!                   │
│  Use 'with' statement — it closes automatically.         │
└──────────────────────────────────────────────────────────┘
```

### File Modes

| Mode | Symbol | Meaning |
|------|--------|---------|
| Read | `'r'` | Read only (default). File must exist. |
| Write | `'w'` | Write (creates new OR overwrites existing!) |
| Append | `'a'` | Append to end (creates if not exists) |
| Read+Write | `'r+'` | Read and write. File must exist. |
| Write+Read | `'w+'` | Write and read. Overwrites. |
| Binary Read | `'rb'` | Read binary (images, videos, etc.) |
| Binary Write | `'wb'` | Write binary |
| Exclusive Create | `'x'` | Create new file, fails if exists |

---

## 2. Opening and Closing Files

### The `open()` Function

```python
# Syntax: open(filename, mode, encoding)
file = open("myfile.txt", "r", encoding="utf-8")
content = file.read()
file.close()   # MUST close! Otherwise resource leak.
```

### The `with` Statement — Always Use This!

The `with` statement is the **correct** and **recommended** way to work with files. It automatically closes the file when the block ends — even if an error occurs:

```python
# This is the best practice
with open("myfile.txt", "r", encoding="utf-8") as file:
    content = file.read()
    print(content)
# File is automatically closed here — no need to call file.close()

# Without 'with', you must manually close (error-prone):
file = open("myfile.txt", "r")
try:
    content = file.read()
finally:
    file.close()   # Must be in finally to ensure it always runs
```

**Rule: Always use `with open(...)` for file operations.**

---

## 3. Reading Files

First, let's create a file to work with. Save this content as `sample.txt`:
```
Alice, 25, New York
Bob, 30, London
Charlie, 22, Tokyo
```

### Reading the Entire File

```python
with open("sample.txt", "r", encoding="utf-8") as f:
    content = f.read()    # Reads ENTIRE file as one string
    print(content)
    print(type(content))  # <class 'str'>
```

### Reading Line by Line

```python
# Method 1: readline() — reads ONE line at a time
with open("sample.txt", "r", encoding="utf-8") as f:
    line1 = f.readline()    # "Alice, 25, New York\n"
    line2 = f.readline()    # "Bob, 30, London\n"
    print(line1.strip())    # strip() removes trailing \n
    print(line2.strip())

# Method 2: readlines() — reads ALL lines into a LIST
with open("sample.txt", "r", encoding="utf-8") as f:
    lines = f.readlines()
    print(lines)   # ['Alice, 25, New York\n', 'Bob, 30, London\n', ...]
    for line in lines:
        print(line.strip())

# Method 3: Iterate over file object (BEST — memory efficient)
with open("sample.txt", "r", encoding="utf-8") as f:
    for line in f:         # Reads one line at a time — doesn't load whole file
        print(line.strip())
```

### Reading Large Files Efficiently

```python
# Read in chunks — useful for very large files
with open("large_file.txt", "r", encoding="utf-8") as f:
    while True:
        chunk = f.read(1024)   # Read 1024 characters at a time
        if not chunk:
            break
        # Process the chunk
        print(chunk, end="")
```

### File Pointer — `seek()` and `tell()`

```python
with open("sample.txt", "r", encoding="utf-8") as f:
    print(f.tell())       # 0 — cursor is at beginning

    line1 = f.readline()
    print(f.tell())       # Position after first line

    f.seek(0)             # Move cursor back to beginning
    line1_again = f.readline()
    print(line1 == line1_again)   # True
```

---

## 4. Writing Files

### Writing Text

```python
# 'w' mode — creates new file OR overwrites existing file
with open("output.txt", "w", encoding="utf-8") as f:
    f.write("Hello, World!\n")    # write() does NOT add newline automatically
    f.write("This is line 2.\n")
    f.write("This is line 3.\n")

# Result in output.txt:
# Hello, World!
# This is line 2.
# This is line 3.
```

### Writing Multiple Lines with `writelines()`

```python
lines = ["First line\n", "Second line\n", "Third line\n"]

with open("output.txt", "w", encoding="utf-8") as f:
    f.writelines(lines)   # Writes all items in the list
                          # Note: writelines does NOT add \n between them automatically!
```

### Building Content Before Writing

```python
people = [
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 30},
]

with open("people.txt", "w", encoding="utf-8") as f:
    for person in people:
        f.write(f"{person['name']}, {person['age']}\n")
```

---

## 5. Appending to Files

`'a'` mode adds to the END of an existing file (does not overwrite):

```python
# First, create the file
with open("log.txt", "w") as f:
    f.write("Log started\n")

# Append new entries later
with open("log.txt", "a") as f:
    f.write("User logged in\n")

with open("log.txt", "a") as f:
    f.write("User performed action X\n")

# log.txt now contains:
# Log started
# User logged in
# User performed action X
```

---

## 6. Working with File Paths

Use the `pathlib` module (Python 3.4+) for cross-platform path handling. It is cleaner than string manipulation:

```python
from pathlib import Path

# Create Path objects
current_dir = Path(".")               # Current directory
home_dir = Path.home()                # User's home directory (e.g., /home/alice)
abs_path = Path("/home/alice/file.txt")

# Path operations
p = Path("data/reports/2024/report.csv")
print(p.name)         # report.csv
print(p.stem)         # report
print(p.suffix)       # .csv
print(p.parent)       # data/reports/2024
print(p.parts)        # ('data', 'reports', '2024', 'report.csv')

# Join paths (cross-platform, handles / and \ correctly)
folder = Path("data") / "reports" / "2024"
file_path = folder / "report.csv"
print(file_path)   # data/reports/2024/report.csv

# Check existence
print(file_path.exists())   # True/False
print(file_path.is_file())  # True if it's a file
print(file_path.is_dir())   # True if it's a directory

# Create directories
folder.mkdir(parents=True, exist_ok=True)   # Creates all parent dirs, no error if exists

# List files in a directory
data_dir = Path("data")
for item in data_dir.iterdir():
    print(item)

# Find all .txt files recursively
for txt_file in Path(".").rglob("*.txt"):
    print(txt_file)

# Reading/writing with pathlib
text = file_path.read_text(encoding="utf-8")   # Reads entire file as string
file_path.write_text("Hello!", encoding="utf-8")  # Writes string to file
```

---

## 7. CSV Files

CSV (Comma-Separated Values) is a common format for tabular data (like Excel spreadsheets):

```python
import csv

# Reading CSV files
with open("students.csv", "r", newline="", encoding="utf-8") as f:
    reader = csv.reader(f)
    header = next(reader)   # Read first row (header)
    print(f"Columns: {header}")
    for row in reader:
        print(row)   # Each row is a list

# Better: use DictReader — each row becomes a dict with column names as keys
with open("students.csv", "r", newline="", encoding="utf-8") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["name"], row["grade"])

# Writing CSV files
data = [
    ["name", "age", "city"],   # Header row
    ["Alice", 25, "New York"],
    ["Bob", 30, "London"],
    ["Charlie", 22, "Tokyo"],
]

with open("output.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerows(data)   # Write all rows at once

# Better: use DictWriter
fieldnames = ["name", "age", "city"]
people = [
    {"name": "Alice", "age": 25, "city": "New York"},
    {"name": "Bob", "age": 30, "city": "London"},
]

with open("people.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    writer.writeheader()     # Writes the header row
    writer.writerows(people)
```

---

## 8. JSON Files

JSON (JavaScript Object Notation) is the standard format for data exchange in web APIs. Python's `json` module handles it easily:

```python
import json

# Python dict ↔ JSON string
person = {
    "name": "Alice",
    "age": 25,
    "city": "New York",
    "hobbies": ["reading", "coding"],
    "address": {
        "street": "123 Main St",
        "zip": "10001"
    }
}

# WRITING: Python object → JSON file
with open("person.json", "w", encoding="utf-8") as f:
    json.dump(person, f, indent=4)   # indent=4 makes it human-readable
# person.json:
# {
#     "name": "Alice",
#     "age": 25,
#     ...
# }

# READING: JSON file → Python object
with open("person.json", "r", encoding="utf-8") as f:
    loaded = json.load(f)
print(type(loaded))       # <class 'dict'>
print(loaded["name"])     # Alice

# JSON STRING conversion (not files):
json_str = json.dumps(person, indent=2)   # dict → JSON string
print(type(json_str))     # <class 'str'>

back_to_dict = json.loads(json_str)       # JSON string → dict
print(back_to_dict["age"])   # 25
```

**JSON ↔ Python Type Mapping:**

| JSON | Python |
|------|--------|
| object | dict |
| array | list |
| string | str |
| number (int) | int |
| number (float) | float |
| true / false | True / False |
| null | None |

---

## 9. Exception Handling Introduction

An **exception** is an error that occurs **during runtime** (while the program is running). Without handling, exceptions crash your program with an error message:

```
┌──────────────────────────────────────────────────────────┐
│  Common Python Exceptions                                │
│                                                          │
│  ValueError:      Invalid value (int("hello"))           │
│  TypeError:       Wrong type ("5" + 5)                   │
│  NameError:       Variable not defined (print(xyz))      │
│  IndexError:      List index out of range ([1,2][5])     │
│  KeyError:        Dict key not found (d["x"] missing)    │
│  ZeroDivisionError: Division by zero (x / 0)             │
│  FileNotFoundError: File doesn't exist                   │
│  AttributeError:  Object has no such attribute           │
│  ImportError:     Module not found                       │
│  StopIteration:   Iterator exhausted                     │
│  OverflowError:   Number too large (for floats)          │
│  MemoryError:     Not enough memory                      │
│  RecursionError:  Maximum recursion depth exceeded        │
└──────────────────────────────────────────────────────────┘
```

### Exception Hierarchy

```
BaseException
├── SystemExit
├── KeyboardInterrupt  (Ctrl+C)
└── Exception          ← catch this for general errors
    ├── ValueError
    ├── TypeError
    ├── NameError
    ├── OSError
    │   ├── FileNotFoundError
    │   ├── PermissionError
    │   └── IsADirectoryError
    ├── RuntimeError
    │   └── RecursionError
    ├── ArithmeticError
    │   ├── ZeroDivisionError
    │   └── OverflowError
    ├── LookupError
    │   ├── IndexError
    │   └── KeyError
    └── ...
```

---

## 10. try / except

The `try/except` block lets you **catch** exceptions and handle them gracefully instead of crashing:

```python
# Without exception handling — CRASHES
result = int("hello")   # ValueError: invalid literal for int()

# With exception handling — GRACEFUL
try:
    result = int("hello")
    print(result)
except ValueError:
    print("That's not a valid number!")

print("Program continues normally...")
```

```
┌──────────────────────────────────────────────────────────┐
│  try/except Flow:                                        │
│                                                          │
│  try:                                                    │
│      risky code here                                     │
│                                                          │
│  If no exception → skip except block                     │
│  If exception:                                           │
│    → Match the exception type                            │
│    → Run the matching except block                       │
│    → Continue after the try/except                       │
└──────────────────────────────────────────────────────────┘
```

### Catching the Exception Object

Use `as` to get the exception object and access its message:

```python
try:
    x = 1 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")             # Error: division by zero
    print(f"Type: {type(e).__name__}")  # Type: ZeroDivisionError
```

---

## 11. Multiple except Clauses

Handle different errors differently:

```python
def get_user_age():
    try:
        age_str = input("Enter your age: ")
        age = int(age_str)             # Could raise ValueError
        result = 100 / age             # Could raise ZeroDivisionError
        print(f"You have {result:.1f}% of a century lived.")

    except ValueError:
        print("Please enter a valid integer!")

    except ZeroDivisionError:
        print("Age cannot be zero!")

    except KeyboardInterrupt:
        print("\nInput cancelled by user.")

    except Exception as e:
        # Catch-all for any other unexpected exception
        print(f"Unexpected error: {e}")
```

### Catching Multiple Exceptions in One Line

```python
try:
    result = int("bad") / 0
except (ValueError, ZeroDivisionError) as e:
    print(f"Math error: {e}")
```

### Bare `except` — Avoid This!

```python
# ❌ AVOID — catches EVERYTHING including SystemExit, KeyboardInterrupt
try:
    pass
except:
    pass

# ✔ Use except Exception to catch only regular exceptions
try:
    pass
except Exception as e:
    print(f"Error: {e}")
```

---

## 12. else and finally

### `else` Block

Runs ONLY if the `try` block completed without any exception:

```python
try:
    result = int("42")
except ValueError:
    print("Not a valid number!")
else:
    # Only runs if no exception occurred
    print(f"Conversion successful! Result: {result}")
    # Do more processing here...
```

### `finally` Block

Runs **ALWAYS** — whether an exception occurred or not. Perfect for cleanup code (closing files, releasing resources):

```python
file = None
try:
    file = open("data.txt", "r")
    content = file.read()
    result = int(content)    # Might fail
except FileNotFoundError:
    print("File not found!")
except ValueError:
    print("File contains non-numeric data!")
else:
    print(f"Number from file: {result}")
finally:
    if file:
        file.close()         # Always close the file!
    print("Cleanup done.")  # Always runs!
```

### Full try/except/else/finally Pattern

```python
try:
    # Code that might raise an exception
    risky_operation()

except SpecificError as e:
    # Handle specific errors
    handle_error(e)

except AnotherError:
    # Handle another type of error
    handle_other()

else:
    # Runs only if NO exception occurred in try block
    success_code()

finally:
    # ALWAYS runs (exception or not)
    cleanup_code()
```

---

## 13. Raising Exceptions

You can **raise** exceptions yourself using the `raise` keyword to signal error conditions in your own code:

```python
def set_age(age):
    if not isinstance(age, int):
        raise TypeError(f"Age must be an integer, got {type(age).__name__}")
    if age < 0:
        raise ValueError("Age cannot be negative!")
    if age > 150:
        raise ValueError("Age seems unreasonably large!")
    return age


try:
    set_age("twenty")    # TypeError
except TypeError as e:
    print(f"Type error: {e}")

try:
    set_age(-5)          # ValueError
except ValueError as e:
    print(f"Value error: {e}")
```

### Re-raising Exceptions

Sometimes you handle an exception partially and want to pass it up the call chain:

```python
def process_file(filename):
    try:
        with open(filename) as f:
            return int(f.read())
    except FileNotFoundError:
        print(f"Warning: {filename} not found. Using default.")
        return 0
    except ValueError as e:
        print(f"Error parsing file content: {e}")
        raise    # Re-raise the same exception to let the caller handle it
```

---

## 14. Custom Exceptions

Create your own exception classes by inheriting from `Exception`:

```python
# Define custom exceptions
class AppError(Exception):
    """Base exception for our application."""
    pass

class DatabaseError(AppError):
    """Raised when a database operation fails."""
    def __init__(self, query, message):
        self.query = query
        self.message = message
        super().__init__(f"Database error on query '{query}': {message}")

class AuthError(AppError):
    """Raised when authentication fails."""
    def __init__(self, username):
        self.username = username
        super().__init__(f"Authentication failed for user: '{username}'")

class InsufficientFundsError(AppError):
    """Raised when attempting to withdraw more than available balance."""
    def __init__(self, balance, amount):
        self.balance = balance
        self.amount = amount
        super().__init__(f"Cannot withdraw ${amount}. Balance is only ${balance}.")


# Use custom exceptions
def authenticate(username, password):
    valid_users = {"alice": "pass123"}
    if username not in valid_users or valid_users[username] != password:
        raise AuthError(username)
    return True

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(balance, amount)
    return balance - amount

try:
    authenticate("alice", "wrong")
except AuthError as e:
    print(e)   # Authentication failed for user: 'alice'

try:
    withdraw(100, 200)
except InsufficientFundsError as e:
    print(e)   # Cannot withdraw $200. Balance is only $100.
    print(f"You need ${e.amount - e.balance} more.")  # Access custom attributes
```

---

## 15. Python Modules

A **module** is simply a Python file (`.py`) that contains code (functions, classes, variables) you can use in other Python files.

### Creating a Module

Create a file called `math_utils.py`:

```python
# math_utils.py

PI = 3.14159265358979

def circle_area(radius):
    """Calculate the area of a circle."""
    return PI * radius ** 2

def factorial(n):
    """Calculate the factorial of a non-negative integer."""
    if n < 0:
        raise ValueError("n must be non-negative")
    if n == 0:
        return 1
    return n * factorial(n - 1)

class Calculator:
    def add(self, a, b):
        return a + b
    def subtract(self, a, b):
        return a - b
```

### Why Modules? The `__name__` Variable

Every Python file knows if it is being **run directly** or **imported** as a module:

```python
# math_utils.py

def greet():
    print("Hello!")

# This block only runs when the file is executed directly, NOT when imported
if __name__ == "__main__":
    print("Running math_utils.py directly")
    greet()
```

This is a very important Python pattern — always protect test code with `if __name__ == "__main__":`.

---

## 16. Import Statements

### Basic Import

```python
import math_utils

print(math_utils.PI)                    # Access via module name
print(math_utils.circle_area(5))
```

### Import with Alias

```python
import math_utils as mu

print(mu.PI)
print(mu.circle_area(5))
```

### Import Specific Names

```python
from math_utils import circle_area, PI

print(PI)           # No module name needed
print(circle_area(5))
```

### Import All (Wildcard) — Avoid in Production

```python
from math_utils import *    # Imports everything (not recommended)
# Pollutes namespace — you don't know where each name came from
```

### Importing Standard Library Modules

```python
import os
import sys
import math
import random
import datetime
from datetime import date, datetime, timedelta
import re
import json
import pathlib
from pathlib import Path
import collections
from collections import Counter, defaultdict
```

---

## 17. Python Packages

A **package** is a folder containing Python modules and a special `__init__.py` file. It organizes related modules into a namespace hierarchy.

### Package Structure Example

```
my_project/
├── main.py
└── mypackage/
    ├── __init__.py          ← Makes the folder a package
    ├── greetings.py
    ├── math_tools.py
    └── shapes/
        ├── __init__.py      ← Sub-package
        ├── circle.py
        └── rectangle.py
```

### `__init__.py`

When you import a package, Python runs its `__init__.py`. You can put initialization code here or expose a clean API:

```python
# mypackage/__init__.py
from .greetings import hello, goodbye    # . means "current package"
from .math_tools import factorial

__all__ = ["hello", "goodbye", "factorial"]   # Controls 'from mypackage import *'
```

### Importing from Packages

```python
from mypackage import hello               # If exposed in __init__.py
from mypackage.greetings import hello     # Direct import
from mypackage.shapes.circle import Circle
import mypackage.math_tools as mt

# Relative imports (inside a package):
# from . import greetings           # Same package
# from .greetings import hello      # Specific from same package
# from ..utils import helper        # One level up
```

---

## 18. pip — Python Package Manager

`pip` is Python's package manager. It lets you install third-party packages from **PyPI** (Python Package Index — pypi.org):

```bash
# Install a package
pip install requests         # Install the 'requests' library
pip install django           # Install Django
pip install numpy pandas     # Install multiple packages at once

# Install a specific version
pip install requests==2.28.0

# Upgrade a package
pip install --upgrade requests

# Uninstall a package
pip uninstall requests

# List installed packages
pip list

# Show information about a package
pip show requests

# Check for outdated packages
pip list --outdated

# Save requirements to a file
pip freeze > requirements.txt

# Install all packages from requirements.txt
pip install -r requirements.txt
```

### requirements.txt

A `requirements.txt` file lists all the packages your project needs. Share it so others can replicate your environment:

```
# requirements.txt
django==4.2.0
djangorestframework==3.14.0
requests==2.31.0
python-dotenv==1.0.0
```

---

## 19. Virtual Environments

A **virtual environment** is an isolated Python environment for a specific project. Each project has its own installed packages — they don't interfere with each other.

```
┌──────────────────────────────────────────────────────────┐
│  Without virtual environments:                           │
│                                                          │
│  Project A needs Django 3.2                              │
│  Project B needs Django 4.2                              │
│  Both share the SAME global Python → CONFLICT!           │
│                                                          │
│  With virtual environments:                              │
│                                                          │
│  Project A → its own venv → Django 3.2                   │
│  Project B → its own venv → Django 4.2                   │
│  Each project isolated → No conflicts!                   │
└──────────────────────────────────────────────────────────┘
```

### Creating and Using Virtual Environments

```bash
# Step 1: Create a virtual environment (creates a 'venv' folder)
python3 -m venv venv

# Step 2: Activate the virtual environment
# On Mac/Linux:
source venv/bin/activate

# On Windows:
venv\Scripts\activate

# Your terminal prompt changes to show (venv) when activated:
# (venv) $ 

# Step 3: Now install packages — they go into THIS venv only
pip install django
pip install requests

# Step 4: Work on your project...

# Step 5: Deactivate when done
deactivate

# Step 6: Add 'venv/' to .gitignore — don't commit it!
echo "venv/" >> .gitignore
```

### In VS Code — Selecting a Virtual Environment

1. Press `Ctrl + Shift + P`
2. Type: `Python: Select Interpreter`
3. Choose the interpreter from your `venv` folder (it will show the path containing `venv`)

VS Code will then use this interpreter for running files and terminals.

---

## 20. Useful Standard Library Modules

Python comes with a massive "batteries included" standard library. Here are the most useful ones:

### `os` — Operating System Interface

```python
import os

print(os.getcwd())           # Current working directory
os.chdir("/tmp")             # Change directory
print(os.listdir("."))       # List files in current dir
os.mkdir("new_folder")       # Create directory
os.makedirs("a/b/c", exist_ok=True)  # Create nested dirs
os.remove("file.txt")        # Delete a file
os.rename("old.txt", "new.txt")      # Rename/move
print(os.path.exists("file.txt"))    # Check if exists
print(os.path.isfile("file.txt"))    # Is it a file?
print(os.path.isdir("folder"))       # Is it a directory?
print(os.path.join("folder", "file.txt"))  # OS-appropriate path
print(os.environ.get("HOME"))  # Get environment variable
```

### `sys` — System-Specific Functionality

```python
import sys

print(sys.version)          # Python version info
print(sys.platform)         # 'win32', 'linux', 'darwin'
print(sys.argv)             # Command-line arguments list
                            # sys.argv[0] is the script name
sys.exit(0)                 # Exit program (0 = success, non-0 = error)
print(sys.path)             # List of paths Python searches for modules
print(sys.maxsize)          # Maximum int size
```

### `datetime` — Dates and Times

```python
from datetime import datetime, date, time, timedelta

# Current date and time
now = datetime.now()
print(now)                  # 2024-03-15 14:30:22.123456
print(now.year)             # 2024
print(now.month)            # 3
print(now.day)              # 15
print(now.hour)             # 14
print(now.strftime("%Y-%m-%d %H:%M:%S"))  # Format as string

# Create specific dates
birthday = date(1995, 6, 15)
print(birthday)             # 1995-06-15

# Arithmetic with timedelta
tomorrow = date.today() + timedelta(days=1)
next_week = datetime.now() + timedelta(weeks=1)
duration = timedelta(hours=2, minutes=30)

# Parse string to datetime
dt = datetime.strptime("2024-03-15 14:30", "%Y-%m-%d %H:%M")

# Calculate age
from dateutil.relativedelta import relativedelta  # pip install python-dateutil
born = date(1998, 7, 4)
age = relativedelta(date.today(), born)
print(f"Age: {age.years} years, {age.months} months")
```

### `random` — Random Numbers and Choices

```python
import random

print(random.random())          # Float between 0 and 1
print(random.randint(1, 10))    # Integer between 1 and 10 (inclusive)
print(random.uniform(1.0, 5.0)) # Float between 1.0 and 5.0
print(random.choice(["a", "b", "c"]))  # Random item from sequence
print(random.choices(["a", "b", "c"], weights=[1, 2, 1], k=5))  # Weighted choices
print(random.sample(range(100), 10))  # 10 unique items from range (no replacement)

items = [1, 2, 3, 4, 5]
random.shuffle(items)           # Shuffle list IN PLACE
print(items)

# Reproducible randomness (testing)
random.seed(42)
print(random.randint(1, 100))  # Always same result with same seed
```

### `math` — Mathematical Functions

```python
import math

print(math.pi)           # 3.141592...
print(math.e)            # 2.718281...
print(math.sqrt(16))     # 4.0
print(math.ceil(3.1))    # 4
print(math.floor(3.9))   # 3
print(math.fabs(-5))     # 5.0 (float absolute value)
print(math.log(100, 10)) # 2.0 (log base 10)
print(math.log2(8))      # 3.0
print(math.log(math.e))  # 1.0 (natural log)
print(math.sin(math.pi / 2))  # 1.0
print(math.factorial(10))     # 3628800
print(math.gcd(48, 18))       # 6 (greatest common divisor)
print(math.isnan(float('nan')))  # True
print(math.isinf(float('inf')))  # True
```

### `re` — Regular Expressions

```python
import re

text = "My phone is 123-456-7890 and email is alice@example.com"

# Search for a pattern
match = re.search(r"\d{3}-\d{3}-\d{4}", text)  # Find phone number
if match:
    print(match.group())    # 123-456-7890

# Find all matches
emails = re.findall(r"\b[\w.]+@[\w.]+\.\w+\b", text)
print(emails)   # ['alice@example.com']

# Substitute
clean = re.sub(r"\d{3}-\d{3}-\d{4}", "XXX-XXX-XXXX", text)  # Redact phone

# Check if string COMPLETELY matches pattern
valid_email = re.fullmatch(r"[\w.]+@[\w.]+\.\w+", "alice@example.com")

# Split
parts = re.split(r"\s+", "  hello   world  ")  # Split on whitespace
```

### `itertools` — Powerful Iteration Tools

```python
import itertools

# chain — combine multiple iterables
print(list(itertools.chain([1, 2], [3, 4], [5])))
# [1, 2, 3, 4, 5]

# combinations — all combinations of r items (no repetition, order doesn't matter)
print(list(itertools.combinations("ABCD", 2)))
# [('A','B'), ('A','C'), ('A','D'), ('B','C'), ('B','D'), ('C','D')]

# permutations — all ordered arrangements
print(list(itertools.permutations([1, 2, 3])))

# product — cartesian product (like nested for loops)
print(list(itertools.product("AB", [1, 2])))
# [('A',1), ('A',2), ('B',1), ('B',2)]

# groupby — group consecutive items
data = [("Alice", "HR"), ("Bob", "HR"), ("Charlie", "IT"), ("Dave", "IT")]
for dept, people in itertools.groupby(data, key=lambda x: x[1]):
    print(dept, list(people))

# count — infinite counter
counter = itertools.count(start=1, step=2)  # 1, 3, 5, 7, ...
first_five = [next(counter) for _ in range(5)]
print(first_five)   # [1, 3, 5, 7, 9]
```

---

## Summary

```
┌────────────────────────────────────────────────────────────┐
│         File I/O, Exceptions, Modules Summary              │
│                                                            │
│  FILE I/O:                                                 │
│  Always use: with open(filename, mode, encoding="utf-8")   │
│  Modes: 'r' read, 'w' write, 'a' append, 'rb' binary read │
│  Methods: read(), readline(), readlines(), write()         │
│  Use pathlib.Path for cross-platform path handling         │
│  csv module: reader/writer, DictReader/DictWriter          │
│  json module: dump/load (files), dumps/loads (strings)     │
│                                                            │
│  EXCEPTIONS:                                               │
│  try → risky code                                          │
│  except ExcType as e → handle specific exception           │
│  else → runs only if NO exception                          │
│  finally → ALWAYS runs (cleanup)                           │
│  raise ExcType("message") → raise your own exception       │
│  class MyError(Exception): pass → custom exception         │
│                                                            │
│  MODULES & PACKAGES:                                       │
│  import module_name                                        │
│  from module import name                                   │
│  import module as alias                                    │
│  __name__ == "__main__" → protect executable code          │
│  package = folder with __init__.py                         │
│  pip install package_name                                  │
│  pip freeze > requirements.txt                             │
│  Virtual env: python -m venv venv && source venv/bin/activate│
└────────────────────────────────────────────────────────────┘
```

**Next:** [07-Django-Framework.md](07-Django-Framework.md) — Django Web Framework.
