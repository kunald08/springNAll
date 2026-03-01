# Python Collections — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. What are the four main collection types in Python? Compare them.

**Answer:**
Python has four built-in collection types:

| Type | Mutable | Ordered | Duplicates | Syntax |
|------|---------|---------|------------|--------|
| **list** | Yes | Yes | Yes | `[1, 2, 3]` |
| **tuple** | No | Yes | Yes | `(1, 2, 3)` |
| **dict** | Yes | Yes* | Keys: No, Values: Yes | `{"a": 1}` |
| **set** | Yes | No | No | `{1, 2, 3}` |

*Dicts preserve insertion order since Python 3.7 (guaranteed by language spec).

I choose between them based on the use case: **list** for ordered, changeable sequences; **tuple** for fixed data that shouldn't change; **dict** for key-value lookups; **set** for unique elements and fast membership testing.

---

## 2. What is the difference between a list and a tuple?

**Answer:**
The main difference is **mutability**:

- **Lists** are **mutable** — I can add, remove, or change elements after creation. Syntax: `[1, 2, 3]`
- **Tuples** are **immutable** — once created, they cannot be modified. Syntax: `(1, 2, 3)`

This leads to practical differences:
- Tuples are **faster** than lists because Python can optimize their memory layout
- Tuples are **hashable** (if their contents are hashable), so they can be used as **dictionary keys** and **set elements**, while lists cannot
- Tuples use **less memory** than lists because they don't need extra space for dynamic resizing
- Tuples convey **intent** — when I return `(x, y)` it signals "this is a fixed record," while a list signals "this is a collection you might modify"

I use tuples for fixed data like database rows, function return values, and dictionary keys. Lists for collections I need to modify.

---

## 3. How does list indexing and slicing work?

**Answer:**
Python lists support both **positive indexing** (starting from 0) and **negative indexing** (starting from -1 at the end):

```python
fruits = ["apple", "banana", "cherry", "date"]
fruits[0]     # "apple" — first element
fruits[-1]    # "date" — last element
```

**Slicing** extracts a sub-list using `list[start:stop:step]`:
```python
fruits[1:3]     # ["banana", "cherry"] — index 1 to 2 (stop is exclusive)
fruits[:2]      # ["apple", "banana"] — from beginning
fruits[2:]      # ["cherry", "date"] — to end
fruits[::2]     # ["apple", "cherry"] — every 2nd element
fruits[::-1]    # ["date", "cherry", "banana", "apple"] — reversed
```

The key things to remember: slicing never raises `IndexError` even with out-of-range indices, and slicing always returns a **new list** — it doesn't modify the original.

---

## 4. What is the difference between `append()`, `extend()`, and `insert()`?

**Answer:**
All three add elements to a list, but differently:

- **`append(item)`** — adds the item as a **single element** at the end. If I append a list, the list itself becomes one element:
  ```python
  a = [1, 2]
  a.append([3, 4])   # [1, 2, [3, 4]] — nested list!
  ```

- **`extend(iterable)`** — adds each element from the iterable **individually** to the end:
  ```python
  a = [1, 2]
  a.extend([3, 4])   # [1, 2, 3, 4] — flat list
  ```

- **`insert(index, item)`** — adds an item at a **specific position**, shifting existing elements:
  ```python
  a = [1, 2, 3]
  a.insert(1, "x")   # [1, "x", 2, 3]
  ```

All three modify the list **in place** and return `None`. The `+` operator also concatenates lists but creates a **new** list instead of modifying in place.

---

## 5. How do you remove elements from a list?

**Answer:**
There are several ways:

- **`remove(value)`** — removes the **first occurrence** of the value. Raises `ValueError` if not found:
  ```python
  [1, 2, 3, 2].remove(2)   # [1, 3, 2]
  ```

- **`pop(index)`** — removes and **returns** the element at the given index. Without arguments, removes the last element:
  ```python
  a = [1, 2, 3]
  a.pop()     # Returns 3, list becomes [1, 2]
  a.pop(0)    # Returns 1, list becomes [2]
  ```

- **`del list[index]`** — removes by index, can also delete slices:
  ```python
  del a[0]        # Remove first element
  del a[1:3]      # Remove a slice
  ```

- **`clear()`** — removes all elements: `a.clear()` → `[]`

The key distinction: `remove()` works by **value**, `pop()` and `del` work by **index**, and `pop()` is the only one that **returns** the removed element.

---

## 6. What is a list comprehension? Give some examples.

**Answer:**
A list comprehension is a concise, Pythonic way to create lists by combining a loop and an optional condition in a single expression:

```python
# Syntax: [expression for item in iterable if condition]

# Basic
squares = [x ** 2 for x in range(10)]

# With condition (filtering)
evens = [x for x in range(20) if x % 2 == 0]

# Transform + filter
upper_long = [word.upper() for word in words if len(word) > 3]

# Nested loop — flattening a 2D list
matrix = [[1, 2], [3, 4], [5, 6]]
flat = [num for row in matrix for num in row]   # [1, 2, 3, 4, 5, 6]
```

They're faster than equivalent `for` loops with `append()` because the looping happens at C level internally. However, I avoid using them for side effects or complex logic — if it's hard to read on one line, a regular loop is better.

Python also supports **dict comprehensions** `{k: v for ...}`, **set comprehensions** `{x for ...}`, and **generator expressions** `(x for ...)`.

---

## 7. What is the difference between shallow copy and deep copy?

**Answer:**
**Shallow copy** creates a new container but the elements inside are still **references** to the same objects:
```python
import copy
original = [[1, 2], [3, 4]]
shallow = copy.copy(original)    # or original.copy() or original[:]

shallow[0][0] = 99
print(original[0][0])   # 99 — inner list was shared!
```

**Deep copy** creates a completely **independent** copy — new container AND new copies of all nested objects:
```python
deep = copy.deepcopy(original)
deep[0][0] = 99
print(original[0][0])   # Still 1 — completely independent
```

For flat lists (no nested mutable objects), shallow copy is sufficient. For nested structures, I need deep copy. Common shallow copy methods: `list.copy()`, slicing `[:]`, `list()` constructor, and `copy.copy()`.

---

## 8. What are dictionaries? How do they work internally?

**Answer:**
A dictionary is a **key-value** data structure — I store and retrieve values using unique keys. Since Python 3.7, dicts preserve **insertion order**.

```python
user = {"name": "Alice", "age": 25, "city": "NYC"}
user["name"]       # "Alice"
user["email"] = "a@b.com"   # Add new key-value pair
```

Internally, dictionaries use **hash tables**. When I insert a key, Python:
1. Computes the key's **hash** using its `__hash__()` method
2. Uses that hash to find a slot in an internal array
3. Stores the key-value pair in that slot

This is why dictionary lookups are **O(1)** average time — Python jumps directly to the slot instead of searching. It's also why keys **must be hashable** (immutable) — if a key's hash changed after insertion, the dict would break. Strings, numbers, tuples are valid keys; lists, dicts, sets are not.

---

## 9. What is the difference between `dict[key]` and `dict.get(key)`?

**Answer:**
Both retrieve a value by key, but they handle **missing keys** differently:

- `dict[key]` — raises a **`KeyError`** if the key doesn't exist
- `dict.get(key, default)` — returns the **default value** (or `None`) if the key doesn't exist, never raises an error

```python
user = {"name": "Alice"}

user["age"]           # KeyError!
user.get("age")       # None
user.get("age", 0)    # 0 (custom default)
```

I use `dict[key]` when I'm **sure** the key exists or when a missing key is a bug I want to catch immediately. I use `dict.get()` when the key is optional and I want to provide a fallback. There's also `dict.setdefault(key, default)` which returns the value if the key exists, or sets and returns the default if it doesn't.

---

## 10. What are some important dictionary methods?

**Answer:**
The most commonly used ones:

- `dict.keys()` — returns all keys as a **view** object
- `dict.values()` — returns all values as a view
- `dict.items()` — returns all key-value pairs as tuples — this is what I use in `for` loops:
  ```python
  for key, value in user.items():
      print(f"{key}: {value}")
  ```
- `dict.update(other)` — merges another dict into this one; existing keys are overwritten
- `dict.pop(key)` — removes and returns the value for the key
- `dict.setdefault(key, default)` — returns value if key exists; otherwise sets it to default and returns it

Since Python 3.9, we can also merge dicts with `|`: `merged = dict1 | dict2`, and update with `|=`.

---

## 11. What are sets? When would you use them?

**Answer:**
A set is an **unordered** collection of **unique** elements. It automatically removes duplicates and doesn't support indexing:

```python
numbers = {1, 2, 3, 2, 1}     # {1, 2, 3} — duplicates removed
```

I use sets when I need:
- **Deduplication** — quickest way to remove duplicates: `unique = list(set(my_list))`
- **Fast membership testing** — `x in my_set` is **O(1)**, versus O(n) for lists
- **Mathematical set operations** — union, intersection, difference, symmetric difference

Sets are implemented using hash tables (like dict keys without values), which is why they're fast for lookups but can't contain mutable elements — items must be hashable.

---

## 12. What are the set operations available in Python?

**Answer:**
Python supports standard mathematical set operations:

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b     # Union: {1, 2, 3, 4, 5, 6} — all elements from both
a & b     # Intersection: {3, 4} — common elements
a - b     # Difference: {1, 2} — in a but not in b
a ^ b     # Symmetric difference: {1, 2, 5, 6} — in either but not both
a <= b    # Subset check: False
a >= b    # Superset check: False
```

These operators have method equivalents: `.union()`, `.intersection()`, `.difference()`, `.symmetric_difference()`, `.issubset()`, `.issuperset()`. The method forms can accept any iterable, while operators require both operands to be sets.

---

## 13. What is a `frozenset`?

**Answer:**
A `frozenset` is an **immutable** version of a set. Once created, I cannot add or remove elements:

```python
fs = frozenset([1, 2, 3])
fs.add(4)   # AttributeError — immutable!
```

The main use case is when I need a set as a **dictionary key** or as an element of another set — which requires hashability. Regular sets are mutable and therefore unhashable:

```python
# Regular set can't be a dict key or set element
{frozenset([1, 2]): "valid"}   # Works!
{{1, 2}: "invalid"}            # TypeError!
```

`frozenset` supports all the read operations and set operations (union, intersection, etc.) but not modification operations.

---

## 14. What is the difference between `sort()` and `sorted()`?

**Answer:**
- **`list.sort()`** — sorts the list **in place**, modifies the original, returns **`None`**:
  ```python
  nums = [3, 1, 4, 1, 5]
  nums.sort()        # nums is now [1, 1, 3, 4, 5]
  ```

- **`sorted(iterable)`** — returns a **new sorted list**, original is unchanged:
  ```python
  nums = [3, 1, 4, 1, 5]
  result = sorted(nums)   # result = [1, 1, 3, 4, 5], nums unchanged
  ```

Both accept `key` (a function for custom sorting) and `reverse=True`:
```python
students = [("Alice", 85), ("Bob", 72)]
sorted(students, key=lambda s: s[1], reverse=True)
```

The key difference: `sort()` is a list method (only works on lists), while `sorted()` works on **any iterable** — strings, tuples, dicts, generators. I use `sorted()` when I need the original intact, and `sort()` for in-place performance.

---

## 15. What is tuple unpacking?

**Answer:**
Tuple unpacking (or sequence unpacking) allows me to assign multiple variables from a tuple (or any iterable) in a single statement:

```python
coordinates = (10, 20, 30)
x, y, z = coordinates    # x=10, y=20, z=30

# Swap variables without a temp variable
a, b = b, a

# In for loops with tuples
for name, score in [("Alice", 90), ("Bob", 85)]:
    print(f"{name}: {score}")
```

Python 3 also supports **extended unpacking** with `*`:
```python
first, *rest = [1, 2, 3, 4, 5]     # first=1, rest=[2,3,4,5]
first, *middle, last = [1, 2, 3, 4] # first=1, middle=[2,3], last=4
```

It's an incredibly useful feature that makes code cleaner and more readable.

---

## 16. What is a `namedtuple`?

**Answer:**
A `namedtuple` from the `collections` module creates a tuple subclass with **named fields**, so I can access elements by name instead of index:

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])
p = Point(10, 20)
print(p.x)     # 10 — readable!
print(p[0])    # 10 — still works like a regular tuple
```

It combines the readability of a class with the efficiency of a tuple — it's **immutable**, **hashable**, and uses **less memory** than a regular class. I use it for simple data containers like database rows, configuration settings, or function return values where I want named access without the overhead of defining a full class.

In modern Python (3.6+), `typing.NamedTuple` offers a class-based syntax with type hints.

---

## 17. What other useful collections are in the `collections` module?

**Answer:**
The `collections` module provides specialized container types:

- **`defaultdict`** — a dict that provides a default value for missing keys, avoiding `KeyError`:
  ```python
  from collections import defaultdict
  word_count = defaultdict(int)
  word_count["hello"] += 1    # No KeyError, defaults to 0
  ```

- **`Counter`** — counts occurrences of elements:
  ```python
  from collections import Counter
  Counter("mississippi")   # Counter({'s': 4, 'i': 4, 'p': 2, 'm': 1})
  ```

- **`deque`** — double-ended queue with O(1) append/pop from both ends (list has O(n) for `pop(0)`):
  ```python
  from collections import deque
  dq = deque([1, 2, 3])
  dq.appendleft(0)    # O(1) — much faster than list.insert(0, item)
  ```

- **`OrderedDict`** — dict that remembers insertion order (less needed since Python 3.7 when regular dicts became ordered)

---

## 18. How do you iterate over a dictionary?

**Answer:**
There are several ways to iterate:

```python
user = {"name": "Alice", "age": 25, "city": "NYC"}

# Iterate over keys (default)
for key in user:
    print(key)

# Iterate over values
for value in user.values():
    print(value)

# Iterate over key-value pairs (most common)
for key, value in user.items():
    print(f"{key}: {value}")
```

The `for key in dict` pattern iterates over **keys** by default. Using `.items()` with tuple unpacking is the most Pythonic way when I need both key and value. All three view objects (`.keys()`, `.values()`, `.items()`) are **dynamic views** — they reflect changes to the dict in real time.

---

## 19. How do you merge two dictionaries?

**Answer:**
Several approaches, depending on Python version:

```python
d1 = {"a": 1, "b": 2}
d2 = {"b": 3, "c": 4}

# Python 3.9+ — merge operator (cleanest)
merged = d1 | d2           # {"a": 1, "b": 3, "c": 4}

# Python 3.5+ — unpacking
merged = {**d1, **d2}      # Same result

# Any version — update (modifies in place)
d1.update(d2)              # d1 becomes {"a": 1, "b": 3, "c": 4}
```

In all cases, when keys conflict, the **last value wins** — `d2`'s value overwrites `d1`'s. The `|` operator is the most modern and readable approach.

---

## 20. When would you choose a list vs set vs dict vs tuple?

**Answer:**
My decision depends on the use case:

- **List** — when I need an **ordered, modifiable** collection. E.g., collecting results, maintaining a sequence, stack/queue operations.

- **Tuple** — when the data is **fixed and shouldn't change**. E.g., returning multiple values from a function, dictionary keys, database records.

- **Dictionary** — when I need **key-value associations** and fast lookups by key. E.g., configuration settings, caching, JSON-like data structures.

- **Set** — when I need **unique elements** and fast membership testing. E.g., removing duplicates, checking if an item exists, mathematical set operations.

Performance-wise: list lookup is O(n), dict/set lookup is O(1). So if I'm doing frequent "is this element present?" checks, I should convert my list to a set. For ordered unique elements in Python 3.7+, I might use `dict.fromkeys()` since dict preserves insertion order.
