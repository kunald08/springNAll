# Python Basics — From Zero to Expert

## Table of Contents
1. [What is Python?](#1-what-is-python)
2. [How Python Works Under the Hood](#2-how-python-works-under-the-hood)
3. [Installing Python](#3-installing-python)
4. [Setting Up Your IDE — VS Code & PyCharm](#4-setting-up-your-ide)
5. [Your First Python Program](#5-your-first-python-program)
6. [Python Syntax Rules](#6-python-syntax-rules)
7. [Variables](#7-variables)
8. [Data Types](#8-data-types)
9. [Numbers — int, float, complex](#9-numbers)
10. [Strings](#10-strings)
11. [Booleans](#11-booleans)
12. [Type Conversion (Casting)](#12-type-conversion)
13. [The `type()` Function](#13-the-type-function)
14. [Comments](#14-comments)
15. [User Input](#15-user-input)

---

## 1. What is Python?

Python is a **high-level, interpreted, general-purpose** programming language created by **Guido van Rossum** and first released in **1991**.

The name "Python" does not come from the snake — Guido van Rossum was a fan of the British comedy show *Monty Python's Flying Circus*.

### Why Python?

```
┌──────────────────────────────────────────────────────────┐
│                  Why Python is Everywhere                │
│                                                          │
│  ✔ Easy to read and write (close to plain English)       │
│  ✔ Few lines of code = lots of power                     │
│  ✔ Works on Windows, Mac, Linux — no changes needed      │
│  ✔ Massive library ecosystem (pip install anything)      │
│  ✔ #1 language for Data Science, ML, AI, Web Dev         │
│  ✔ Huge community — easy to find help                    │
│  ✔ Free and open-source                                  │
└──────────────────────────────────────────────────────────┘
```

### Where is Python Used?

| Field | Examples |
|-------|---------|
| Web Development | Django, Flask, FastAPI |
| Data Science | Pandas, NumPy, Matplotlib |
| Machine Learning / AI | TensorFlow, PyTorch, scikit-learn |
| Automation / Scripting | Automating file tasks, web scraping |
| DevOps | Ansible, AWS scripts |
| Cybersecurity | Penetration testing, ethical hacking |
| Game Development | Pygame |

---

## 2. How Python Works Under the Hood

This is where most beginners get confused. Let's break it down step-by-step.

### Compiled vs Interpreted Languages

In languages like C or Java, you write code → a **compiler** converts it to machine code → then it runs.

In Python, you write code → an **interpreter** reads it line-by-line and executes it directly.

```
┌─────────────────────────────────────────────────────────────┐
│                   How Python Executes Code                  │
│                                                             │
│   You write:  hello.py  (plain text, human-readable)        │
│                    │                                        │
│                    ▼                                        │
│        Python Interpreter (CPython by default)              │
│                    │                                        │
│                    ▼                                        │
│         Bytecode (.pyc files in __pycache__ folder)         │
│                    │                                        │
│                    ▼                                        │
│          Python Virtual Machine (PVM) executes it           │
│                    │                                        │
│                    ▼                                        │
│            Output on your screen                            │
└─────────────────────────────────────────────────────────────┘
```

### What is CPython?

When you download Python from python.org, you are downloading **CPython** — the reference implementation of Python written in C language. It is the most common one.

Other implementations exist:
- **PyPy** — Faster Python using JIT (Just-In-Time) compilation
- **Jython** — Python that runs on Java Virtual Machine
- **IronPython** — Python for .NET framework

### The `__pycache__` Folder

When you run a Python file, Python compiles it into **bytecode** (`.pyc` files) and stores them in a folder called `__pycache__`. Next time you run the same file with no changes, Python uses the cached bytecode — making startup faster. You can safely ignore this folder; Python manages it automatically.

---

## 3. Installing Python

### Step 1: Check if Python is Already Installed

Open your terminal (Command Prompt on Windows, Terminal on Mac/Linux):

```bash
python --version
# or
python3 --version
```

If you see `Python 3.x.x`, Python is already installed.

### Step 2: Download Python

Go to **https://www.python.org/downloads/**

- Always download the **latest stable Python 3.x** version (NOT Python 2 — it is dead since 2020).
- Click the big yellow **"Download Python 3.x.x"** button.

### Step 3: Install on Windows

1. Run the downloaded `.exe` file.
2. **VERY IMPORTANT**: Check the box that says **"Add Python to PATH"** before clicking Install.
3. Click **"Install Now"**.
4. After installation, open Command Prompt and type `python --version` to confirm.

```
┌─────────────────────────────────────────────────────────┐
│  ⚠ IMPORTANT: Add Python to PATH                       │
│                                                         │
│  [✔] Add Python 3.x to PATH  ← MUST CHECK THIS BOX      │
│                                                         │
│  If you miss this, Python won't work in the terminal.   │
│  You can fix it later by re-running the installer.      │
└─────────────────────────────────────────────────────────┘
```

### Step 4: Install on Mac

Option A — Official Installer:
1. Download the `.pkg` file from python.org
2. Double-click and follow the instructions

Option B — Using Homebrew (recommended for developers):
```bash
# Install Homebrew first (if not installed):
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Then install Python:
brew install python3
```

### Step 5: Install on Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install python3 python3-pip
```

### Verify Installation

```bash
python3 --version      # Should print Python 3.x.x
pip3 --version         # pip is the Python package manager
```

---

## 4. Setting Up Your IDE

An **IDE** (Integrated Development Environment) is a software application that lets you write, run, and debug code comfortably. Think of it as a smart text editor built for programming.

### Option A — VS Code (Recommended for Beginners)

VS Code is free, lightweight, and very popular.

**Steps:**

1. Download VS Code from **https://code.visualstudio.com/**
2. Install it on your system.
3. Open VS Code.
4. Go to the **Extensions** panel (left sidebar, looks like 4 squares icon, or press `Ctrl+Shift+X`).
5. Search for **"Python"** and install the extension by **Microsoft**.
6. Now create a new file: `File → New File → Save as hello.py`
7. VS Code will detect it's a Python file and activate Python features.

**Key VS Code shortcuts you MUST know:**
| Shortcut | Action |
|----------|--------|
| `Ctrl + S` | Save file |
| `F5` | Run the file in debug mode |
| `Ctrl + F5` | Run the file without debugging |
| `Ctrl + `` ` `` | Open/close terminal |
| `Ctrl + Shift + P` | Command palette (search any feature) |
| `Ctrl + /` | Comment/uncomment line |

**Selecting the Python Interpreter in VS Code:**
1. Press `Ctrl + Shift + P`
2. Type: `Python: Select Interpreter`
3. Choose the Python version you installed (e.g., Python 3.11.x)

### Option B — PyCharm (Most Powerful Python IDE)

PyCharm is built specifically for Python. The **Community Edition** is free.

**Steps:**

1. Download from **https://www.jetbrains.com/pycharm/**
2. Choose **Community Edition** (free).
3. Install and open PyCharm.
4. Click **"New Project"**.
5. Choose a folder for your project.
6. Make sure **"New environment using Virtualenv"** is selected (this creates an isolated Python environment for your project — explained later).
7. Click **Create**.

**Key PyCharm shortcuts:**
| Shortcut | Action |
|----------|--------|
| `Shift + F10` | Run the current file |
| `Shift + F9` | Debug the current file |
| `Ctrl + Alt + L` | Reformat code |
| `Ctrl + /` | Comment/uncomment line |
| `Alt + Enter` | Quick fixes and suggestions |

### The Python Interactive Shell (REPL)

Python has a special interactive mode called **REPL** (Read-Eval-Print Loop). You can run Python code one line at a time without creating a file.

Open your terminal and type `python3`:

```
$ python3
Python 3.11.0 (default, ...)
>>> 2 + 2
4
>>> print("Hello")
Hello
>>> exit()   ← to quit
```

This is great for testing small snippets of code quickly.

---

## 5. Your First Python Program

### Create the File

In VS Code or PyCharm, create a file called `hello.py` and type:

```python
print("Hello, World!")
```

### Run It

**In VS Code**: Click the green ▶ Run button at the top right, or press `Ctrl + F5`.

**In PyCharm**: Right-click in the file → "Run hello" or press `Shift + F10`.

**In Terminal**: Navigate to the folder containing your file:
```bash
cd /path/to/your/folder
python3 hello.py
```

**Output:**
```
Hello, World!
```

Congratulations! You just ran your first Python program.

### What `print()` Does

`print()` is a built-in Python **function** that displays output to the screen (the terminal). Whatever you put inside the parentheses gets printed.

```python
print("Hello, World!")      # Prints a string
print(42)                   # Prints a number
print(3.14)                 # Prints a decimal
print(True)                 # Prints a boolean
print()                     # Prints an empty line
```

---

## 6. Python Syntax Rules

Python has strict but simple syntax rules. Understanding them will save you from countless errors.

### Rule 1: Indentation is EVERYTHING

In most languages, curly braces `{}` define code blocks. In Python, **indentation (spaces/tabs)** defines code blocks.

```python
# CORRECT - indented block under if
if True:
    print("This is inside the if block")
    print("This is also inside")

print("This is OUTSIDE the if block")
```

```python
# WRONG - missing indentation
if True:
print("This will cause an IndentationError!")
```

**Standard:** Use **4 spaces** per indentation level (not tabs — most style guides recommend spaces in Python).

```
┌──────────────────────────────────────────────────────────┐
│              Python Indentation Structure                │
│                                                          │
│  Level 0:  top-level code (no indentation)               │
│      if condition:                                       │
│  Level 1:      code inside if (4 spaces)                 │
│          if another:                                     │
│  Level 2:          code inside nested if (8 spaces)      │
└──────────────────────────────────────────────────────────┘
```

### Rule 2: Statements End at the Line

No semicolons needed at the end of lines (unlike Java, C, JavaScript):

```python
x = 10          # Correct
y = 20          # Correct
z = x + y       # Correct

x = 10;         # Works but unnecessary and unpythonic
```

### Rule 3: Case Sensitivity

Python is **case-sensitive**. `name`, `Name`, and `NAME` are three different variables.

```python
name = "Alice"
Name = "Bob"
NAME = "Charlie"

print(name)   # Prints: Alice
print(Name)   # Prints: Bob
print(NAME)   # Prints: Charlie
```

### Rule 4: No Need to Declare Variable Types

Python automatically figures out the type of a variable:

```python
x = 10         # Python knows this is an integer
y = "Hello"    # Python knows this is a string
z = 3.14       # Python knows this is a float
```

### Rule 5: Line Continuation

If a statement is too long, break it across lines using `\` or inside brackets:

```python
# Using backslash
total = 1 + 2 + 3 + \
        4 + 5 + 6

# Using parentheses (preferred)
total = (1 + 2 + 3 +
         4 + 5 + 6)
```

---

## 7. Variables

A **variable** is a name that holds a value. Think of it as a labeled box where you store data.

```
┌─────────────────────────────────────────────────────┐
│                  Variable = Labeled Box             │
│                                                     │
│   name = "Alice"                                    │
│    ↑          ↑                                     │
│  label      value stored inside the box             │
│                                                     │
│   When Python sees `name` later, it opens           │
│   the box and gives you "Alice"                     │
└─────────────────────────────────────────────────────┘
```

### Creating Variables

```python
age = 25                    # integer
name = "Alice"              # string
height = 5.7                # float
is_student = True           # boolean
```

The `=` sign is the **assignment operator** — it puts the value on the right into the variable on the left.

### Naming Rules (MUST FOLLOW)

| Rule | Example |
|------|---------|
| Must start with a letter or underscore | `name`, `_hidden` ✔ |
| Cannot start with a number | `2name` ✗ |
| Can only contain letters, numbers, underscores | `my_var1` ✔ |
| Cannot be a Python keyword | `if`, `for`, `while` ✗ |
| Case-sensitive | `Name ≠ name` |

### Naming Conventions (SHOULD FOLLOW — PEP 8 Style)

Python has a style guide called **PEP 8**. Everyone in the Python community follows it.

```python
# ✔ Use snake_case for variable and function names
first_name = "Alice"
total_price = 99.99
is_logged_in = False

# ✔ Use UPPER_CASE for constants
MAX_SIZE = 100
PI = 3.14159

# ✔ Use PascalCase for class names
class BankAccount:
    pass
```

### Multiple Assignment

```python
# Assign the same value to multiple variables
x = y = z = 0

# Assign different values in one line (tuple unpacking)
a, b, c = 1, 2, 3
print(a)  # 1
print(b)  # 2
print(c)  # 3

# Swap values (Python magic — no temp variable needed!)
x, y = 10, 20
x, y = y, x
print(x)  # 20
print(y)  # 10
```

### Dynamic Typing

Python is **dynamically typed** — a variable can hold any type and can change type:

```python
x = 10          # x is an int
print(type(x))  # <class 'int'>

x = "Hello"     # Now x is a string (no error!)
print(type(x))  # <class 'str'>

x = 3.14        # Now x is a float
print(type(x))  # <class 'float'>
```

This is different from Java where `int x = 10` fixes `x` as an integer forever.

---

## 8. Data Types

Python has several built-in data types. Here is the full picture:

```
┌────────────────────────────────────────────────────────────┐
│                   Python Built-in Data Types               │
│                                                            │
│  Text:       str                                           │
│  Numeric:    int, float, complex                           │
│  Sequence:   list, tuple, range                            │
│  Mapping:    dict                                          │
│  Set:        set, frozenset                                │
│  Boolean:    bool                                          │
│  Binary:     bytes, bytearray, memoryview                  │
│  None:       NoneType                                      │
└────────────────────────────────────────────────────────────┘
```

In this file, we cover: `int`, `float`, `complex`, `str`, and `bool`. The rest are covered in the Collections file.

---

## 9. Numbers

Python has three numeric types: `int`, `float`, and `complex`.

### Integer (`int`)

Integers are **whole numbers** — no decimal point. They can be positive, negative, or zero. Python integers have **unlimited size** (no overflow like in Java or C).

```python
age = 25
temperature = -10
big_number = 1000000000000000000000   # Python handles this easily!
zero = 0

print(type(age))   # <class 'int'>
```

**Integer Literals in different bases:**

```python
# Decimal (base 10) — normal
decimal = 255

# Binary (base 2) — prefix 0b
binary = 0b11111111
print(binary)   # 255

# Octal (base 8) — prefix 0o
octal = 0o377
print(octal)    # 255

# Hexadecimal (base 16) — prefix 0x
hexadecimal = 0xFF
print(hexadecimal)  # 255
```

**Underscores for readability (Python 3.6+):**

```python
million = 1_000_000
credit_card = 1234_5678_9012_3456
print(million)      # 1000000 (underscore is ignored, just visual)
```

### Float (`float`)

Floats are **decimal numbers**. Internally, they are stored as **64-bit IEEE 754 double-precision** numbers.

```python
price = 19.99
pi = 3.14159
temperature = -2.5
scientific = 1.5e3    # 1.5 × 10³ = 1500.0
small = 1.5e-3        # 1.5 × 10⁻³ = 0.0015

print(type(price))   # <class 'float'>
print(scientific)    # 1500.0
```

**⚠ Floating-Point Precision Warning:**

```python
# This is a classic gotcha in ALL programming languages
print(0.1 + 0.2)    # Output: 0.30000000000000004  (NOT 0.3!)
```

Why? Because computers store numbers in binary, and `0.1` cannot be represented exactly in binary (just like 1/3 cannot be written exactly in decimal). This is called **floating-point representation error**.

**Fix:** For money or precise decimal calculations, use the `decimal` module:

```python
from decimal import Decimal

result = Decimal("0.1") + Decimal("0.2")
print(result)   # 0.3  (exact!)
```

### Complex (`complex`)

Complex numbers have a **real** part and an **imaginary** part. Used in scientific computing and signal processing.

```python
c = 3 + 5j    # 3 is real, 5j is imaginary (Python uses j, not i)
print(c)           # (3+5j)
print(c.real)      # 3.0
print(c.imag)      # 5.0
print(type(c))     # <class 'complex'>
```

### Number Operations

```python
a = 10
b = 3

print(a + b)    # 13  (addition)
print(a - b)    # 7   (subtraction)
print(a * b)    # 30  (multiplication)
print(a / b)    # 3.3333... (true division — always returns float)
print(a // b)   # 3   (floor division — rounds DOWN to nearest integer)
print(a % b)    # 1   (modulus — remainder after division)
print(a ** b)   # 1000 (exponentiation — 10 to the power of 3)
```

### Useful Math Functions

```python
import math

print(abs(-5))           # 5   (absolute value)
print(round(3.7))        # 4   (rounds to nearest integer)
print(round(3.14159, 2)) # 3.14 (round to 2 decimal places)
print(math.sqrt(16))     # 4.0 (square root)
print(math.floor(3.9))   # 3   (always rounds DOWN)
print(math.ceil(3.1))    # 4   (always rounds UP)
print(math.pow(2, 10))   # 1024.0 (2 to the power 10)
print(max(3, 7, 2))      # 7   (maximum of values)
print(min(3, 7, 2))      # 2   (minimum of values)
print(sum([1, 2, 3, 4])) # 10  (sum of a list)
```

---

## 10. Strings

A **string** is a sequence of characters — letters, numbers, spaces, symbols. In Python, strings are enclosed in either single quotes `'...'` or double quotes `"..."`. Both work identically.

```python
name = "Alice"
city = 'New York'
message = "It's a great day!"   # Single quote inside double quotes
quote = 'He said "Hello"'       # Double quote inside single quotes
```

### Multi-line Strings

Use triple quotes `"""..."""` or `'''...'''`:

```python
paragraph = """
This is a multi-line string.
It spans across multiple lines.
Python preserves all the newlines and spaces.
"""

print(paragraph)
```

### String as a Sequence

A string is a **sequence of characters**, so you can access individual characters by their **index** (position). Indexing starts at `0`.

```
┌──────────────────────────────────────────────────────┐
│  String Indexing                                     │
│                                                      │
│  s = "Hello"                                         │
│       H  e  l  l  o                                  │
│       0  1  2  3  4    ← Positive index              │
│      -5 -4 -3 -2 -1   ← Negative index (from end)   │
└──────────────────────────────────────────────────────┘
```

```python
s = "Hello"
print(s[0])    # H  (first character)
print(s[1])    # e
print(s[-1])   # o  (last character)
print(s[-2])   # l  (second from last)
```

### String Slicing

Slicing lets you extract a **substring**. Syntax: `s[start:stop:step]`
- `start` — index to start from (inclusive)
- `stop` — index to stop at (exclusive — not included)
- `step` — how many characters to skip (default 1)

```python
s = "Hello, World!"

print(s[0:5])    # Hello    (characters 0, 1, 2, 3, 4)
print(s[7:12])   # World
print(s[:5])     # Hello    (start defaults to 0)
print(s[7:])     # World!   (stop defaults to end)
print(s[:])      # Hello, World!  (full copy)
print(s[::2])    # Hlo ol!  (every 2nd character)
print(s[::-1])   # !dlroW ,olleH  (REVERSED! step=-1)
```

### String Immutability

Strings in Python are **immutable** — once created, you cannot change individual characters:

```python
s = "Hello"
s[0] = "J"    # ❌ TypeError: 'str' object does not support item assignment

# To "change" a string, create a new one:
s = "J" + s[1:]
print(s)    # Jello
```

### String Methods

Python strings come with many built-in methods (functions that belong to the string):

```python
s = "  Hello, World!  "

# Case methods
print(s.upper())          # "  HELLO, WORLD!  "
print(s.lower())          # "  hello, world!  "
print(s.title())          # "  Hello, World!  "
print(s.capitalize())     # "  hello, world!  " → first char uppercase

# Whitespace methods
print(s.strip())          # "Hello, World!"  (removes leading/trailing spaces)
print(s.lstrip())         # "Hello, World!  " (removes left spaces only)
print(s.rstrip())         # "  Hello, World!" (removes right spaces only)

# Search methods
s2 = "Hello, World!"
print(s2.find("World"))   # 7 (returns index of first occurrence, -1 if not found)
print(s2.index("World"))  # 7 (same as find but raises ValueError if not found)
print(s2.count("l"))      # 3 (how many times 'l' appears)

# Check methods (return True/False)
print("hello123".isalnum())   # True  (only letters and numbers)
print("hello".isalpha())      # True  (only letters)
print("123".isdigit())        # True  (only digits)
print("   ".isspace())        # True  (only whitespace)
print("Hello World".istitle()) # True (title case)

# Replace and split
print(s2.replace("World", "Python"))  # "Hello, Python!"
print(s2.split(", "))                 # ['Hello', 'World!']
print(", ".join(["Hello", "World"]))  # "Hello, World"

# Strip specific characters
print("***Hello***".strip("*"))   # "Hello"

# Check start/end
print(s2.startswith("Hello"))  # True
print(s2.endswith("!"))        # True
```

### String Formatting — 4 Ways

**Way 1 — Old Style (% operator):** (Not recommended, old Python 2 style)
```python
name = "Alice"
age = 25
print("My name is %s and I am %d years old." % (name, age))
```

**Way 2 — `.format()` method:**
```python
print("My name is {} and I am {} years old.".format(name, age))
print("My name is {0} and I am {1} years old.".format(name, age))
print("My name is {name} and I am {age} years old.".format(name="Alice", age=25))
```

**Way 3 — f-strings (Python 3.6+, RECOMMENDED):**

F-strings are the modern, clean way to format strings. Put `f` before the quote, then use `{}` with the variable name inside:

```python
name = "Alice"
age = 25
height = 5.678

print(f"My name is {name} and I am {age} years old.")
# Output: My name is Alice and I am 25 years old.

# You can put expressions inside {}
print(f"In 10 years, I will be {age + 10}.")

# Format numbers
print(f"Height: {height:.2f}")   # 5.68 (2 decimal places)
print(f"Age: {age:05d}")         # 00025 (pad with zeros to width 5)

# Debugging shortcut (Python 3.8+)
x = 42
print(f"{x = }")    # x = 42
```

**Way 4 — Template Strings:**
```python
from string import Template
t = Template("My name is $name and I am $age years old.")
print(t.substitute(name="Alice", age=25))
```

### String Escape Characters

Escape characters are special character sequences that represent characters you cannot type directly:

| Escape | Meaning | Example |
|--------|---------|---------|
| `\n` | New line | `"Hello\nWorld"` |
| `\t` | Tab | `"Name\tAge"` |
| `\\` | Backslash | `"C:\\Users\\Alice"` |
| `\'` | Single quote | `'It\'s fine'` |
| `\"` | Double quote | `"He said \"Hi\""` |
| `\r` | Carriage return | (Windows line ending) |

```python
print("Line 1\nLine 2\nLine 3")
# Output:
# Line 1
# Line 2
# Line 3

print("Name\tAge\tCity")
print("Alice\t25\tNYC")
```

### Raw Strings

If you do NOT want escape characters to be processed, use a **raw string** (prefix `r`):

```python
# Without raw string — \n is a new line
path = "C:\new_folder\test.txt"
print(path)    # C:
               # ew_folder   est.txt   (BAD!)

# With raw string — \n is literally \n
path = r"C:\new_folder\test.txt"
print(path)    # C:\new_folder\test.txt  (CORRECT!)
```

Raw strings are very useful for **file paths on Windows** and **regular expressions**.

### String Length

```python
s = "Hello"
print(len(s))   # 5
```

### `in` operator with Strings

```python
s = "Hello, World!"
print("World" in s)     # True
print("Python" in s)    # False
print("Python" not in s) # True
```

---

## 11. Booleans

A **boolean** represents one of two values: `True` or `False`. (Note: capital T and F — these are Python keywords.)

```python
a = True
b = False
print(type(a))   # <class 'bool'>
```

### Where Booleans Come From

Booleans are the result of **comparisons** and **logical operations**:

```python
print(5 > 3)     # True
print(5 < 3)     # False
print(5 == 5)    # True   (== is equality check, not assignment)
print(5 != 3)    # True   (!= is "not equal to")
```

### Truthy and Falsy Values

In Python, every value has a boolean meaning. This is important for `if` statements.

**Falsy values** (evaluate to `False`):
- `False`
- `0` (zero integer)
- `0.0` (zero float)
- `""` (empty string)
- `[]` (empty list)
- `{}` (empty dict)
- `()` (empty tuple)
- `None`

**Truthy values** (evaluate to `True`):
- Everything else (non-zero numbers, non-empty strings, non-empty collections...)

```python
# You can check truthiness with bool()
print(bool(0))       # False
print(bool(1))       # True
print(bool(-5))      # True (non-zero = truthy)
print(bool(""))      # False
print(bool("Hi"))    # True
print(bool([]))      # False
print(bool([1,2]))   # True
print(bool(None))    # False
```

### Using Booleans in Practice

```python
is_raining = True
has_umbrella = False

if is_raining and not has_umbrella:
    print("You will get wet!")
else:
    print("You are fine.")
```

### `None` — The Absence of Value

`None` is Python's way of representing "nothing" or "no value" (similar to `null` in Java/JavaScript):

```python
result = None

if result is None:
    print("No result yet")   # This prints

# Use 'is None' and 'is not None' to check for None
# Do NOT use == None (it works but 'is' is the correct way)
```

---

## 12. Type Conversion

Converting one type to another is called **type casting** or **type conversion**.

### Implicit Conversion (Python does it automatically)

```python
x = 5     # int
y = 2.0   # float

result = x + y
print(result)        # 7.0 (int + float = float automatically)
print(type(result))  # <class 'float'>
```

Python automatically converts `int` to `float` when needed to avoid losing information.

### Explicit Conversion (You do it manually)

```python
# int() — converts to integer
print(int(3.9))       # 3   (truncates, does NOT round)
print(int("42"))      # 42  (string to int — string MUST be a valid integer)
print(int(True))      # 1
print(int(False))     # 0

# float() — converts to float
print(float(5))        # 5.0
print(float("3.14"))   # 3.14
print(float("1e3"))    # 1000.0

# str() — converts to string
print(str(42))         # "42"
print(str(3.14))       # "3.14"
print(str(True))       # "True"

# bool() — converts to boolean
print(bool(0))    # False
print(bool(1))    # True

# Common error with int():
# int("3.14")   → ValueError! (can't convert a float string directly to int)
# Fix:
print(int(float("3.14")))   # 3 (first convert to float, then to int)
```

### Real-world Example

```python
# input() always returns a STRING
age_str = input("Enter your age: ")   # User types: 25
print(type(age_str))                  # <class 'str'>

# Convert to int to do math
age = int(age_str)
print(f"In 10 years you will be {age + 10}")
```

This is the most common conversion beginners need: `input()` always returns a string, so you must convert to `int` or `float` when doing math.

---

## 13. The `type()` Function

`type()` tells you exactly what type a value is:

```python
print(type(42))         # <class 'int'>
print(type(3.14))       # <class 'float'>
print(type("Hello"))    # <class 'str'>
print(type(True))       # <class 'bool'>
print(type(None))       # <class 'NoneType'>
print(type([1, 2, 3]))  # <class 'list'>
```

### Using `isinstance()` — Better for Type Checking

```python
x = 42
print(isinstance(x, int))         # True
print(isinstance(x, float))       # False
print(isinstance(x, (int, float))) # True (is it int OR float?)
```

`isinstance()` is preferred over `type() ==` because it also handles inheritance (important in OOP).

---

## 14. Comments

Comments are notes in your code written for humans — Python ignores them completely.

### Single-line Comments

```python
# This is a single-line comment
x = 10   # This comment is after a statement

# Comments explain WHY, not WHAT:
# Bad comment:  x = x + 1  # adds 1 to x
# Good comment: x = x + 1  # Increment counter after each successful login
```

### Multi-line Comments

Python does not have a true multi-line comment syntax. You use multiple `#` lines:

```python
# This is the first line of a long comment.
# This is the second line.
# This is the third line.
```

### Docstrings (Documentation Strings)

Docstrings are multi-line strings used to document functions, classes, and modules. They use triple quotes and are placed right after the `def` or `class` line:

```python
def greet(name):
    """
    Greets the user by name.
    
    Parameters:
        name (str): The name of the user.
    
    Returns:
        str: A greeting message.
    """
    return f"Hello, {name}!"
```

You can access a docstring with:
```python
print(greet.__doc__)
```

IDEs show docstrings as tooltips when you hover over a function — very useful!

---

## 15. User Input

`input()` pauses the program, shows an optional prompt, waits for the user to type something and press Enter, then returns what they typed as a **string**.

```python
name = input("Enter your name: ")
print(f"Hello, {name}!")
```

```
Enter your name: Alice
Hello, Alice!
```

### Important: `input()` Always Returns a String

```python
number = input("Enter a number: ")
print(type(number))   # <class 'str'>  ← NOT int!

# To use as a number:
number = int(input("Enter a number: "))
print(number * 2)   # Now it works mathematically
```

### Handling Invalid Input (Beginner-safe way)

```python
try:
    age = int(input("Enter your age: "))
    print(f"You are {age} years old.")
except ValueError:
    print("That's not a valid number!")
```

(We cover `try/except` in depth in the Exception Handling file.)

---

## Summary

```
┌────────────────────────────────────────────────────────────┐
│                    Python Basics Summary                   │
│                                                            │
│  ✔ Python is interpreted, high-level, dynamically typed    │
│  ✔ Indentation defines code blocks (4 spaces is standard)  │
│  ✔ Variables hold any type and can change type             │
│  ✔ int = whole numbers (unlimited size)                    │
│  ✔ float = decimals (beware floating-point errors)         │
│  ✔ str = sequence of characters, immutable, index from 0   │
│  ✔ bool = True or False (truthy/falsy concept is key)      │
│  ✔ None = absence of a value                               │
│  ✔ type() checks type, isinstance() is better practice     │
│  ✔ input() always returns a string — convert when needed   │
│  ✔ f-strings are the modern way to format strings          │
└────────────────────────────────────────────────────────────┘
```

**Next:** [02-Python-Operators-and-Flow.md](02-Python-Operators-and-Flow.md) — Operators, if-else, and Loops.
