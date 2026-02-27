# Python Collections — Lists, Tuples, Dictionaries, and Sets

## Table of Contents
1. [Overview of Python Collections](#1-overview)
2. [Lists — The Workhorse Collection](#2-lists)
3. [List Methods in Depth](#3-list-methods)
4. [List Slicing](#4-list-slicing)
5. [List Comprehensions](#5-list-comprehensions)
6. [Nested Lists (2D Lists)](#6-nested-lists)
7. [Sorting Lists](#7-sorting)
8. [Copying Lists](#8-copying-lists)
9. [Tuples — Immutable Sequences](#9-tuples)
10. [Tuple Operations and Methods](#10-tuple-operations)
11. [Named Tuples](#11-named-tuples)
12. [Dictionaries — Key-Value Storage](#12-dictionaries)
13. [Dictionary Methods in Depth](#13-dictionary-methods)
14. [Dictionary Comprehensions](#14-dictionary-comprehensions)
15. [Nested Dictionaries](#15-nested-dictionaries)
16. [Sets — Unique Unordered Collections](#16-sets)
17. [Set Operations](#17-set-operations)
18. [frozenset — Immutable Set](#18-frozenset)
19. [Choosing the Right Collection](#19-choosing)
20. [collections Module — Extra Power](#20-collections-module)

---

## 1. Overview

Python has four main built-in collection types:

```
┌───────────────────────────────────────────────────────────────┐
│             Python Collection Types at a Glance               │
│                                                               │
│  Type       │ Mutable │ Ordered │ Allows Duplicates │ Syntax  │
│  ───────────────────────────────────────────────────────────  │
│  list       │  Yes    │  Yes    │       Yes         │  [   ]  │
│  tuple      │  No     │  Yes    │       Yes         │  (   )  │
│  dict       │  Yes    │  Yes*   │ Keys: No, Vals: Yes│  {k:v} │
│  set        │  Yes    │  No     │       No          │  {   }  │
│                                                               │
│  * dict preserves insertion order since Python 3.7            │
└───────────────────────────────────────────────────────────────┘
```

---

## 2. Lists

A **list** is an **ordered, mutable** collection of items. Items can be of any type — even different types in the same list.

### Creating Lists

```python
# Empty list
empty = []
empty2 = list()

# List of integers
numbers = [1, 2, 3, 4, 5]

# List of strings
fruits = ["apple", "banana", "cherry"]

# Mixed types (allowed but uncommon in practice)
mixed = [1, "hello", 3.14, True, None]

# Nested list
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# list() from other iterables
chars = list("hello")      # ['h', 'e', 'l', 'l', 'o']
nums = list(range(5))      # [0, 1, 2, 3, 4]
```

### Accessing Elements — Indexing

```
┌───────────────────────────────────────────────────────────┐
│  fruits = ["apple", "banana", "cherry", "date", "elderberry"] │
│                                                           │
│  Positive index:  0       1        2       3       4      │
│  Negative index: -5      -4       -3      -2      -1      │
│                                                           │
│  fruits[0]  = "apple"    fruits[-1] = "elderberry"        │
│  fruits[2]  = "cherry"   fruits[-2] = "date"              │
└───────────────────────────────────────────────────────────┘
```

```python
fruits = ["apple", "banana", "cherry"]
print(fruits[0])    # apple
print(fruits[-1])   # cherry
print(fruits[1])    # banana
```

### Modifying Lists

Lists are **mutable** — you can change, add, and remove items:

```python
fruits = ["apple", "banana", "cherry"]

# Change an item by index
fruits[1] = "blueberry"
print(fruits)   # ['apple', 'blueberry', 'cherry']

# Change a slice
fruits[0:2] = ["avocado", "blackberry"]
print(fruits)   # ['avocado', 'blackberry', 'cherry']
```

### List Length

```python
print(len(fruits))   # 3
```

---

## 3. List Methods

```python
fruits = ["apple", "banana", "cherry"]

# --- ADDING ELEMENTS ---

# append(item): Add ONE item to the end
fruits.append("date")
print(fruits)   # ['apple', 'banana', 'cherry', 'date']

# insert(index, item): Insert at a specific position
fruits.insert(1, "avocado")
print(fruits)   # ['apple', 'avocado', 'banana', 'cherry', 'date']

# extend(iterable): Add multiple items to the end
fruits.extend(["elderberry", "fig"])
print(fruits)   # ['apple', 'avocado', 'banana', 'cherry', 'date', 'elderberry', 'fig']

# + operator: Concatenates two lists, creates NEW list
combined = fruits + ["grape", "honeydew"]

# * operator: Repeats a list
print([0] * 5)   # [0, 0, 0, 0, 0]

# --- REMOVING ELEMENTS ---

# remove(value): Remove first occurrence of the VALUE
fruits.remove("banana")      # Finds "banana" and removes it
# Raises ValueError if not found

# pop(index): Remove and RETURN item at index (default: last item)
removed = fruits.pop()      # Removes and returns last item
removed = fruits.pop(0)     # Removes and returns item at index 0

# del: Delete by index or slice
del fruits[0]               # Delete item at index 0
del fruits[0:2]             # Delete slice
del fruits                  # Delete the entire list variable

# clear(): Remove ALL items, list becomes empty
fruits.clear()
print(fruits)   # []

# --- SEARCHING ---

fruits = ["apple", "banana", "cherry", "banana"]

# index(value): Return index of first occurrence (raises ValueError if not found)
print(fruits.index("banana"))   # 1

# count(value): Count how many times value appears
print(fruits.count("banana"))   # 2

# in: Check if value exists
print("cherry" in fruits)   # True

# --- OTHER METHODS ---

# reverse(): Reverse the list IN PLACE (modifies original)
fruits.reverse()
print(fruits)   # ['banana', 'cherry', 'banana', 'apple']

# copy(): Return a SHALLOW copy
fruits_copy = fruits.copy()

# sort(): Sort IN PLACE (modifies original, returns None)
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
numbers.sort()
print(numbers)   # [1, 1, 2, 3, 4, 5, 6, 9]
numbers.sort(reverse=True)
print(numbers)   # [9, 6, 5, 4, 3, 2, 1, 1]
```

---

## 4. List Slicing

Slicing extracts a **sub-list**. Syntax: `list[start:stop:step]` (same as string slicing).

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

print(numbers[2:5])     # [2, 3, 4]        (indices 2, 3, 4)
print(numbers[:4])      # [0, 1, 2, 3]     (start to 4)
print(numbers[6:])      # [6, 7, 8, 9]     (6 to end)
print(numbers[::2])     # [0, 2, 4, 6, 8]  (every 2nd)
print(numbers[1::2])    # [1, 3, 5, 7, 9]  (odd indices)
print(numbers[::-1])    # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]   (REVERSED!)
print(numbers[7:2:-1])  # [7, 6, 5, 4, 3]  (from 7 down to 3)

# Slice and replace
numbers[2:5] = [20, 30, 40]
print(numbers)   # [0, 1, 20, 30, 40, 5, 6, 7, 8, 9]

# Delete a slice
del numbers[2:5]
print(numbers)   # [0, 1, 5, 6, 7, 8, 9]
```

---

## 5. List Comprehensions

A **list comprehension** creates a new list in a single, readable line using a for loop (and optional condition) inside square brackets.

### Basic Syntax

```python
new_list = [expression for item in iterable]
new_list = [expression for item in iterable if condition]
```

### Examples

```python
numbers = [1, 2, 3, 4, 5]

# Square each number
squares = [x ** 2 for x in numbers]
print(squares)   # [1, 4, 9, 16, 25]

# Even numbers only
evens = [x for x in range(20) if x % 2 == 0]
print(evens)    # [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

# Strings to uppercase
words = ["hello", "world", "python"]
upper = [word.upper() for word in words]
print(upper)    # ['HELLO', 'WORLD', 'PYTHON']

# With condition: keep only strings longer than 4 chars
long_words = [word for word in words if len(word) > 4]
print(long_words)   # ['hello', 'world', 'python']

# Nested comprehension (flatten a 2D list)
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [x for row in matrix for x in row]
print(flat)   # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# If-else in comprehension (ternary)
labels = ["even" if x % 2 == 0 else "odd" for x in range(6)]
print(labels)   # ['even', 'odd', 'even', 'odd', 'even', 'odd']
```

### Generator Expressions (Memory-Efficient Alternative)

Using `()` instead of `[]` creates a **generator** — computes values on demand without storing the entire list in memory:

```python
# List comprehension — stores ALL values in memory
squares_list = [x ** 2 for x in range(1_000_000)]

# Generator expression — computes one value at a time (much less memory)
squares_gen = (x ** 2 for x in range(1_000_000))

# Use sum/max/min directly on generators:
total = sum(x ** 2 for x in range(1000))
print(total)   # 332833500
```

---

## 6. Nested Lists (2D Lists)

A list can contain other lists — useful for grids, tables, matrices.

```python
# Create a 3x3 matrix
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Access element: matrix[row][col]
print(matrix[0][0])   # 1  (top-left)
print(matrix[1][2])   # 6  (row 1, col 2)
print(matrix[2][1])   # 8  (bottom-middle)

# Iterate
for row in matrix:
    for element in row:
        print(element, end=" ")
    print()

# Create a 3x3 zero matrix (CORRECT way)
rows, cols = 3, 3
zero_matrix = [[0] * cols for _ in range(rows)]
# zero_matrix = [[0, 0, 0], [0, 0, 0], [0, 0, 0]]

# WRONG way (all rows point to the same list object!):
# wrong = [[0] * 3] * 3   ← Don't do this
```

---

## 7. Sorting Lists

```python
numbers = [5, 2, 8, 1, 9, 3]

# sort() — sorts IN PLACE (modifies the original list)
numbers.sort()
print(numbers)   # [1, 2, 3, 5, 8, 9]

numbers.sort(reverse=True)
print(numbers)   # [9, 8, 5, 3, 2, 1]

# sorted() — returns a NEW sorted list (original unchanged)
original = [5, 2, 8, 1]
new_sorted = sorted(original)
print(original)     # [5, 2, 8, 1]  (unchanged!)
print(new_sorted)   # [1, 2, 5, 8]

# Sort by custom key
words = ["banana", "Apple", "cherry", "date"]
words.sort()
print(words)   # ['Apple', 'banana', 'cherry', 'date']  (uppercase first!)

# Case-insensitive sort
words.sort(key=str.lower)
print(words)   # ['Apple', 'banana', 'cherry', 'date']

# Sort list of dicts by a field
students = [
    {"name": "Charlie", "gpa": 3.2},
    {"name": "Alice", "gpa": 3.8},
    {"name": "Bob", "gpa": 3.5}
]
students.sort(key=lambda s: s["gpa"], reverse=True)
for s in students:
    print(s["name"], s["gpa"])
# Alice 3.8  /  Bob 3.5  /  Charlie 3.2

# Sort by multiple keys using itemgetter
from operator import itemgetter
students.sort(key=itemgetter("gpa", "name"))
```

---

## 8. Copying Lists

This is a very common source of bugs — understand it deeply.

```python
original = [1, 2, 3]

# WRONG: This creates an alias, NOT a copy
alias = original
alias.append(4)
print(original)   # [1, 2, 3, 4]  ← ORIGINAL CHANGED! Both point to same object!

# Correct shallow copy methods:
copy1 = original.copy()
copy2 = original[:]
copy3 = list(original)

copy1.append(99)
print(original)   # [1, 2, 3, 4]  (unchanged)
print(copy1)      # [1, 2, 3, 4, 99]
```

### Shallow vs Deep Copy

For **nested lists**, a shallow copy only copies the outer list — inner lists are still shared:

```python
import copy

original = [[1, 2], [3, 4]]

# Shallow copy: inner lists are still shared
shallow = original.copy()
shallow[0].append(99)
print(original)   # [[1, 2, 99], [3, 4]]  ← Inner list changed in original too!

# Deep copy: completely independent copy at all levels
original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)
deep[0].append(99)
print(original)   # [[1, 2], [3, 4]]  ← Not affected!
print(deep)       # [[1, 2, 99], [3, 4]]
```

---

## 9. Tuples

A **tuple** is an **ordered, immutable** sequence. Once created, its elements cannot be changed.

```python
# Creating tuples
empty = ()
single = (42,)       # ← Note the trailing comma — REQUIRED for single-element tuple
single2 = 42,        # Parentheses are optional!

coordinates = (10.5, 20.3)
rgb = (255, 128, 0)
person = ("Alice", 25, "NYC")
mixed = (1, "hello", [1, 2, 3])   # Can contain mutable objects

# tuple() from iterable
t = tuple([1, 2, 3])    # (1, 2, 3)
t = tuple("hello")      # ('h', 'e', 'l', 'l', 'o')
```

### Why Tuples? Immutable = Safe

```
┌──────────────────────────────────────────────────────────┐
│  Use tuples for data that should NEVER change:           │
│                                                          │
│  - RGB color values: RED = (255, 0, 0)                   │
│  - Geographic coordinates: PARIS = (48.8566, 2.3522)    │
│  - Return multiple values from functions                 │
│  - Dictionary keys (lists can't be dict keys!)           │
│  - Data records (like a row in a database)               │
└──────────────────────────────────────────────────────────┘
```

### Tuple Packing and Unpacking

```python
# Packing: creating a tuple by grouping values
point = 10, 20     # (10, 20) — implicit packing

# Unpacking: extracting values from a tuple
x, y = point
print(x)   # 10
print(y)   # 20

# Extended unpacking
first, *rest = (1, 2, 3, 4, 5)
print(first)   # 1
print(rest)    # [2, 3, 4, 5]

*start, last = (1, 2, 3, 4, 5)
print(start)   # [1, 2, 3, 4]
print(last)    # 5

a, *middle, z = (1, 2, 3, 4, 5)
print(a)       # 1
print(middle)  # [2, 3, 4]
print(z)       # 5

# Swap variables using tuple unpacking
a, b = 1, 2
a, b = b, a
print(a, b)    # 2 1
```

---

## 10. Tuple Operations and Methods

Tuples support most read-only operations (since they cannot be modified):

```python
t = (1, 2, 3, 2, 4, 2)

# Indexing and slicing (same as lists)
print(t[0])        # 1
print(t[-1])       # 2
print(t[1:4])      # (2, 3, 2)
print(t[::-1])     # (2, 4, 2, 3, 2, 1)

# Length
print(len(t))      # 6

# count() — count occurrences
print(t.count(2))  # 3

# index() — find first occurrence
print(t.index(3))  # 2

# in operator
print(3 in t)      # True

# Concatenation and repetition
print((1, 2) + (3, 4))   # (1, 2, 3, 4)
print((1, 2) * 3)        # (1, 2, 1, 2, 1, 2)

# Iteration
for item in t:
    print(item)
```

### Performance: Tuples vs Lists

Tuples are **faster** and use **less memory** than lists:

```python
import sys
import timeit

list_size = sys.getsizeof([1, 2, 3, 4, 5])
tuple_size = sys.getsizeof((1, 2, 3, 4, 5))
print(f"List: {list_size} bytes")    # ~104 bytes
print(f"Tuple: {tuple_size} bytes")  # ~80 bytes
```

---

## 11. Named Tuples

`namedtuple` lets you access tuple elements by name instead of index — a lightweight alternative to a class for simple data:

```python
from collections import namedtuple

# Define a named tuple type
Point = namedtuple("Point", ["x", "y"])
Person = namedtuple("Person", ["name", "age", "city"])

# Create instances
p = Point(10, 20)
alice = Person("Alice", 25, "New York")

# Access by name (clear and readable)
print(p.x)         # 10
print(alice.name)  # Alice
print(alice.age)   # 25

# Access by index still works
print(p[0])        # 10

# Still immutable
# p.x = 99   ← AttributeError

# Convert to dict
print(alice._asdict())   # OrderedDict([('name', 'Alice'), ...])

# Create new instance with one field changed
alice2 = alice._replace(city="London")
print(alice2)   # Person(name='Alice', age=25, city='London')
```

---

## 12. Dictionaries

A **dictionary** is an **unordered** collection of **key-value pairs**. Keys must be unique and immutable (strings, numbers, tuples). Values can be anything.

```
┌──────────────────────────────────────────────────────────┐
│  Dictionary = Real-world dictionary                      │
│                                                          │
│  Word (key)     Definition (value)                       │
│  ──────────────────────────────────────                  │
│  "apple"    →   "a round red or green fruit"             │
│  "banana"   →   "a long yellow fruit"                    │
│  "cherry"   →   "a small red fruit"                      │
│                                                          │
│  You look up by key → get the value                      │
└──────────────────────────────────────────────────────────┘
```

### Creating Dictionaries

```python
# Empty dict
empty = {}
empty2 = dict()

# Dict literal
person = {
    "name": "Alice",
    "age": 25,
    "city": "New York"
}

# dict() constructor from keyword arguments
person = dict(name="Alice", age=25, city="New York")

# dict() from a list of tuples
person = dict([("name", "Alice"), ("age", 25)])

# Dict with different value types
config = {
    "debug": True,
    "max_retries": 3,
    "timeout": 30.0,
    "allowed_hosts": ["localhost", "example.com"]
}
```

### Accessing Values

```python
person = {"name": "Alice", "age": 25, "city": "NYC"}

# Direct access — raises KeyError if key doesn't exist
print(person["name"])   # Alice
# print(person["email"])  ← KeyError!

# .get() — safe access, returns None (or default) if key missing
print(person.get("name"))          # Alice
print(person.get("email"))         # None (no error)
print(person.get("email", "N/A"))  # N/A (custom default)
```

### Adding, Updating, and Deleting

```python
person = {"name": "Alice", "age": 25}

# Add a new key
person["email"] = "alice@example.com"

# Update existing key
person["age"] = 26

# Update multiple keys at once
person.update({"age": 27, "city": "Boston"})
person.update(city="Chicago")   # Also works with keyword arguments

# Delete a key
del person["email"]

# pop(key) — remove and return the value
age = person.pop("age")

# pop(key, default) — safe removal
score = person.pop("score", 0)   # Returns 0 if "score" doesn't exist

# popitem() — remove and return last inserted (key, value) pair
last_item = person.popitem()
```

### Checking Keys

```python
person = {"name": "Alice", "age": 25}

print("name" in person)       # True
print("email" in person)      # False
print("email" not in person)  # True
```

### Iterating Over a Dictionary

```python
person = {"name": "Alice", "age": 25, "city": "NYC"}

# Iterate over keys (default)
for key in person:
    print(key)

for key in person.keys():
    print(key)

# Iterate over values
for value in person.values():
    print(value)

# Iterate over key-value pairs
for key, value in person.items():
    print(f"{key}: {value}")

# Output:
# name: Alice
# age: 25
# city: NYC
```

---

## 13. Dictionary Methods

```python
person = {"name": "Alice", "age": 25}

# keys() — returns all keys (dict_keys view object)
print(person.keys())       # dict_keys(['name', 'age'])
print(list(person.keys())) # ['name', 'age']

# values() — returns all values
print(person.values())     # dict_values(['Alice', 25])

# items() — returns all (key, value) pairs
print(person.items())      # dict_items([('name', 'Alice'), ('age', 25)])

# get(key, default) — safe access
print(person.get("name"))           # Alice
print(person.get("email", "N/A"))   # N/A

# setdefault(key, default) — if key exists, return its value
#                            if key DOESN'T exist, INSERT key with default and return default
print(person.setdefault("name", "Unknown"))   # Alice (key exists, returns existing value)
print(person.setdefault("email", "N/A"))      # N/A (key didn't exist, now it's added!)
print(person)   # {'name': 'Alice', 'age': 25, 'email': 'N/A'}

# copy() — shallow copy
person_copy = person.copy()

# clear() — empty the dict
person.clear()
print(person)  # {}

# Merge dicts (Python 3.9+): | operator
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 99, "c": 3}
merged = dict1 | dict2
print(merged)   # {'a': 1, 'b': 99, 'c': 3}  (dict2 overwrites dict1 for shared keys)

# Update in-place (Python 3.9+): |= operator
dict1 |= dict2
```

### Counting with Dictionaries

```python
# Count occurrences of each character
text = "hello world"
char_count = {}
for char in text:
    char_count[char] = char_count.get(char, 0) + 1
print(char_count)
# {'h': 1, 'e': 1, 'l': 3, 'o': 2, ' ': 1, 'w': 1, 'r': 1, 'd': 1}

# Better: use Counter from collections module
from collections import Counter
char_count = Counter(text)
print(char_count)
print(char_count.most_common(3))   # 3 most common characters
```

---

## 14. Dictionary Comprehensions

Like list comprehensions but creates a dictionary:

```python
# Syntax: {key_expr: value_expr for item in iterable if condition}

# Square of numbers
squares = {x: x ** 2 for x in range(1, 6)}
print(squares)   # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Filter: keep only even squares
even_squares = {x: x ** 2 for x in range(1, 11) if x % 2 == 0}
print(even_squares)   # {2: 4, 4: 16, 6: 36, 8: 64, 10: 100}

# Invert a dictionary (swap keys and values)
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
print(inverted)   # {1: 'a', 2: 'b', 3: 'c'}

# From two lists using zip
keys = ["name", "age", "city"]
values = ["Alice", 25, "NYC"]
person = {k: v for k, v in zip(keys, values)}
print(person)   # {'name': 'Alice', 'age': 25, 'city': 'NYC'}
```

---

## 15. Nested Dictionaries

A dictionary whose values are themselves dictionaries — commonly used to represent hierarchical data (like JSON):

```python
users = {
    "alice": {
        "age": 25,
        "email": "alice@example.com",
        "roles": ["admin", "user"]
    },
    "bob": {
        "age": 30,
        "email": "bob@example.com",
        "roles": ["user"]
    }
}

# Access nested values
print(users["alice"]["email"])          # alice@example.com
print(users["bob"]["roles"])            # ['user']
print(users["alice"]["roles"][0])       # admin

# Safe access with get() — avoids KeyError
email = users.get("charlie", {}).get("email", "Not found")
print(email)   # Not found

# Iterate over nested dict
for username, info in users.items():
    print(f"User: {username}")
    for key, value in info.items():
        print(f"  {key}: {value}")
```

---

## 16. Sets

A **set** is an **unordered** collection of **unique** items. Duplicates are automatically removed. Sets are great for membership tests and set math operations.

### Creating Sets

```python
# Set literal — use curly braces
fruits = {"apple", "banana", "cherry"}
numbers = {1, 2, 3, 4, 5}

# set() from iterable
s = set([1, 2, 2, 3, 3, 3])
print(s)    # {1, 2, 3} — duplicates removed!

# Set from a string (unique characters)
chars = set("hello")
print(chars)    # {'h', 'e', 'l', 'o'}  — one 'l' not two

# Empty set — MUST use set(), not {} (that creates a dict!)
empty_set = set()
empty_dict = {}   # This is a dict, not a set!
```

### Key Properties

```python
s = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3}
print(s)    # {1, 2, 3, 4, 5, 6, 9} — unordered, no duplicates

# Membership test — very fast (O(1) lookup)
print(5 in s)      # True
print(10 in s)     # False

# Length
print(len(s))      # 7
```

### Modifying Sets

```python
s = {1, 2, 3}

# add() — add one element
s.add(4)
print(s)    # {1, 2, 3, 4}

# update() — add multiple elements from an iterable
s.update([5, 6, 7])
s.update({8, 9})
print(s)    # {1, 2, 3, 4, 5, 6, 7, 8, 9}

# remove() — remove element (raises KeyError if not found)
s.remove(1)

# discard() — remove element (NO error if not found)
s.discard(1)       # No error even though 1 is not there anymore
s.discard(99)      # No error

# pop() — remove and return an ARBITRARY element (sets are unordered)
item = s.pop()

# clear() — remove all elements
s.clear()
```

---

## 17. Set Operations

This is where sets truly shine — mathematical set operations:

```
┌──────────────────────────────────────────────────────────┐
│  Set A = {1, 2, 3, 4, 5}                                 │
│  Set B =       {3, 4, 5, 6, 7}                           │
│                                                          │
│  Union (A | B):                                          │
│  {1, 2, 3, 4, 5, 6, 7} — ALL elements from both         │
│                                                          │
│  Intersection (A & B):                                   │
│  {3, 4, 5} — elements that are in BOTH                   │
│                                                          │
│  Difference (A - B):                                     │
│  {1, 2} — elements in A but NOT in B                     │
│                                                          │
│  Symmetric Difference (A ^ B):                           │
│  {1, 2, 6, 7} — elements in EITHER but NOT BOTH         │
└──────────────────────────────────────────────────────────┘
```

```python
A = {1, 2, 3, 4, 5}
B = {3, 4, 5, 6, 7}

# Union — all elements from both
print(A | B)           # {1, 2, 3, 4, 5, 6, 7}
print(A.union(B))      # same result

# Intersection — elements in BOTH
print(A & B)                   # {3, 4, 5}
print(A.intersection(B))       # same result

# Difference — elements in A but NOT in B
print(A - B)              # {1, 2}
print(A.difference(B))    # same result

# Symmetric Difference — elements in either but NOT both
print(A ^ B)                           # {1, 2, 6, 7}
print(A.symmetric_difference(B))      # same result

# Subset check: is A a subset of B?
print({1, 2}.issubset({1, 2, 3}))      # True
print({1, 2} <= {1, 2, 3})             # True (same)

# Superset check: is A a superset of B?
print({1, 2, 3}.issuperset({1, 2}))    # True
print({1, 2, 3} >= {1, 2})             # True (same)

# Disjoint: do they have NO elements in common?
print({1, 2}.isdisjoint({3, 4}))       # True
print({1, 2}.isdisjoint({2, 3}))       # False
```

### Set Comprehensions

```python
# Unique squares
squares = {x ** 2 for x in range(-5, 6)}
print(squares)   # {0, 1, 4, 9, 16, 25}  — no duplicate 1, 4, 9, etc.
```

### Practical Uses of Sets

```python
# Remove duplicates from a list (order not preserved)
nums = [1, 2, 2, 3, 3, 3, 4]
unique_nums = list(set(nums))
print(unique_nums)   # [1, 2, 3, 4]

# To preserve order while removing duplicates (Python 3.7+):
from dict.fromkeys import dict
unique_ordered = list(dict.fromkeys(nums))
# Better way:
seen = set()
unique_ordered = [x for x in nums if not (x in seen or seen.add(x))]

# Find common elements in two lists
list1 = [1, 2, 3, 4, 5]
list2 = [4, 5, 6, 7, 8]
common = list(set(list1) & set(list2))
print(common)   # [4, 5]

# Find elements in list1 but not list2
only_in_1 = list(set(list1) - set(list2))
print(only_in_1)   # [1, 2, 3]
```

---

## 18. frozenset — Immutable Set

A `frozenset` is a set that cannot be modified after creation. Since it is immutable, it can be used as a **dictionary key** or stored in another set.

```python
fs = frozenset([1, 2, 3, 4])
print(fs)   # frozenset({1, 2, 3, 4})

# All set operations work
A = frozenset([1, 2, 3])
B = frozenset([2, 3, 4])
print(A & B)   # frozenset({2, 3})
print(A | B)   # frozenset({1, 2, 3, 4})

# Can be used as dict key
d = {frozenset([1, 2]): "pair", frozenset([3]): "single"}
print(d[frozenset([1, 2])])   # pair
```

---

## 19. Choosing the Right Collection

```
┌────────────────────────────────────────────────────────────┐
│  "What collection should I use?"                           │
│                                                            │
│  Need ordered items that MAY change? → LIST                │
│  Need ordered items that NEVER change? → TUPLE             │
│  Need fast key-value lookup? → DICT                        │
│  Need unique items + set math? → SET                       │
│  Need unique items as dict key? → FROZENSET                │
│                                                            │
│  Performance Guide:                                        │
│  ─────────────────────────────────────────────────────     │
│  Lookup by index:  list O(1),  tuple O(1)                  │
│  Search (x in ?):  list O(n),  set O(1),  dict O(1)        │
│  Insert/delete at end: list O(1)                           │
│  Insert/delete at start: list O(n) ← use deque instead     │
│  Dict/set lookup is always O(1) — use them for search!     │
└────────────────────────────────────────────────────────────┘
```

---

## 20. collections Module

The `collections` module contains specialized container types beyond the basics.

### `Counter` — Count Frequencies

```python
from collections import Counter

words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
count = Counter(words)
print(count)   # Counter({'apple': 3, 'banana': 2, 'cherry': 1})

print(count["apple"])         # 3
print(count.most_common(2))   # [('apple', 3), ('banana', 2)]

# Arithmetic on counters
c1 = Counter(a=3, b=2)
c2 = Counter(a=1, b=4)
print(c1 + c2)   # Counter({'b': 6, 'a': 4})
print(c1 - c2)   # Counter({'a': 2})  (negative counts removed)
```

### `defaultdict` — Dict with a Default Value

A regular dict raises `KeyError` for missing keys. `defaultdict` automatically creates a default value instead:

```python
from collections import defaultdict

# Default value is 0 (for counting)
word_count = defaultdict(int)
words = ["apple", "banana", "apple", "cherry", "apple"]
for word in words:
    word_count[word] += 1   # No KeyError! Missing keys start at 0
print(dict(word_count))
# {'apple': 3, 'banana': 1, 'cherry': 1}

# Default value is a list (for grouping)
groups = defaultdict(list)
data = [("fruit", "apple"), ("veggie", "carrot"), ("fruit", "banana")]
for category, item in data:
    groups[category].append(item)
print(dict(groups))
# {'fruit': ['apple', 'banana'], 'veggie': ['carrot']}
```

### `OrderedDict` — Preserves Insertion Order (Pre-3.7)

Regular dicts preserve insertion order since Python 3.7, so `OrderedDict` is less needed now. But it has some extra features:

```python
from collections import OrderedDict

od = OrderedDict()
od["first"] = 1
od["second"] = 2
od["third"] = 3
print(list(od.keys()))   # ['first', 'second', 'third']  (order preserved)

# move_to_end
od.move_to_end("first")
print(list(od.keys()))   # ['second', 'third', 'first']
```

### `deque` — Double-Ended Queue

A deque is a list-like collection optimized for **fast appends and pops from BOTH ends** (O(1) instead of O(n) for lists):

```python
from collections import deque

d = deque([1, 2, 3, 4, 5])

d.append(6)        # Add to right
d.appendleft(0)    # Add to left
print(d)   # deque([0, 1, 2, 3, 4, 5, 6])

d.pop()            # Remove from right
d.popleft()        # Remove from left
print(d)   # deque([1, 2, 3, 4, 5])

# Rotate: move items from one end to the other
d.rotate(2)        # Move 2 items from right to left
print(d)   # deque([4, 5, 1, 2, 3])
d.rotate(-2)       # Rotate back
print(d)   # deque([1, 2, 3, 4, 5])

# maxlen: fixed-size queue (oldest items dropped automatically)
recent = deque(maxlen=3)
for i in range(7):
    recent.append(i)
    print(list(recent))
# [0]  [0, 1]  [0, 1, 2]  [1, 2, 3]  [2, 3, 4]  [3, 4, 5]  [4, 5, 6]
```

---

## Summary

```
┌────────────────────────────────────────────────────────────┐
│                  Collections Summary                       │
│                                                            │
│  LIST []:   ordered, mutable, duplicates ok                │
│  TUPLE ():  ordered, immutable, fast, use for fixed data   │
│  DICT {}:   key-value, O(1) lookup by key, unique keys     │
│  SET {}:    unordered, unique, O(1) lookup, set math       │
│                                                            │
│  List comprehension:   [expr for x in iter if cond]        │
│  Dict comprehension:   {k: v for x in iter}                │
│  Set comprehension:    {expr for x in iter}                │
│  Generator:            (expr for x in iter)                │
│                                                            │
│  Shallow copy: .copy(), [:]  — only copies outer layer     │
│  Deep copy: copy.deepcopy() — independent at all levels    │
│                                                            │
│  Extra: Counter, defaultdict, OrderedDict, deque           │
└────────────────────────────────────────────────────────────┘
```

**Next:** [05-Python-OOP.md](05-Python-OOP.md) — Object-Oriented Programming in Python.
