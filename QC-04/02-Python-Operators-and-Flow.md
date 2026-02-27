# Python Operators & Program Flow — From Zero to Expert

## Table of Contents
1. [Arithmetic Operators](#1-arithmetic-operators)
2. [Comparison (Relational) Operators](#2-comparison-operators)
3. [Logical Operators](#3-logical-operators)
4. [Assignment Operators](#4-assignment-operators)
5. [Identity Operators](#5-identity-operators)
6. [Membership Operators](#6-membership-operators)
7. [Bitwise Operators](#7-bitwise-operators)
8. [Operator Precedence](#8-operator-precedence)
9. [if / elif / else — Conditional Statements](#9-if-elif-else)
10. [Nested if Statements](#10-nested-if)
11. [Ternary (One-line if-else)](#11-ternary)
12. [match-case (Python 3.10+)](#12-match-case)
13. [for Loops](#13-for-loops)
14. [range()](#14-range)
15. [while Loops](#15-while-loops)
16. [break, continue, pass](#16-break-continue-pass)
17. [else on Loops](#17-else-on-loops)
18. [Nested Loops](#18-nested-loops)
19. [Comprehensions (Preview)](#19-comprehensions)

---

## 1. Arithmetic Operators

Arithmetic operators perform mathematical calculations.

```python
a = 17
b = 5

print(a + b)    # 22   — Addition
print(a - b)    # 12   — Subtraction
print(a * b)    # 85   — Multiplication
print(a / b)    # 3.4  — True Division (always float)
print(a // b)   # 3    — Floor Division (integer result, rounds DOWN)
print(a % b)    # 2    — Modulus (remainder)
print(a ** b)   # 1419857 — Exponentiation (17 to the power 5)
```

### Under the Hood: Floor Division vs True Division

```
┌──────────────────────────────────────────────────────────┐
│  True Division:    17 / 5  = 3.4   (exact result)        │
│  Floor Division:   17 // 5 = 3     (rounds DOWN always)  │
│                                                          │
│  Floor Division with negatives:                          │
│  -17 // 5  = -4   (NOT -3! Floor means TOWARD -infinity) │
│  17 // -5  = -4   (same reason)                          │
└──────────────────────────────────────────────────────────┘
```

```python
print(-17 // 5)    # -4  (floors: -3.4 → -4)
print(17 // -5)    # -4  (floors: -3.4 → -4)
```

### Modulus (%) — Very Useful in Programming

The modulus operator returns the **remainder** after division:

```python
print(10 % 3)   # 1   (10 = 3×3 + 1)
print(15 % 5)   # 0   (15 = 5×3 + 0 — perfectly divisible)
print(7 % 2)    # 1   (7 = 2×3 + 1 — odd number check!)
```

**Why programmers use `%`:**

```python
# Check if a number is even or odd
number = 8
if number % 2 == 0:
    print(f"{number} is even")
else:
    print(f"{number} is odd")

# Cycling/wrapping (e.g., index that wraps around a list)
index = 7
size = 4
print(index % size)   # 3 (wraps around: 0,1,2,3,0,1,2,3...)
```

---

## 2. Comparison Operators

Comparison operators **compare** two values and always return a `bool` (`True` or `False`).

```python
a = 10
b = 20

print(a == b)    # False  — Equal to
print(a != b)    # True   — Not equal to
print(a > b)     # False  — Greater than
print(a < b)     # True   — Less than
print(a >= b)    # False  — Greater than or equal to
print(a <= b)    # True   — Less than or equal to
```

### Comparing Strings

Strings are compared **lexicographically** (like a dictionary, character by character using Unicode values):

```python
print("apple" == "apple")   # True
print("apple" == "Apple")   # False (case-sensitive!)
print("apple" < "banana")   # True  ('a' < 'b' in alphabet)
print("b" > "a")            # True
print("Z" < "a")            # True  (uppercase Z = 90, lowercase a = 97)
```

### Chained Comparisons (Python-specific and elegant!)

Python allows chaining comparison operators — this is not possible in most other languages:

```python
age = 25

# Traditional way
if age >= 18 and age <= 65:
    print("Working age")

# Python's elegant way (chained)
if 18 <= age <= 65:
    print("Working age")   # Same result — much cleaner!

# Another example
x = 5
print(1 < x < 10)    # True  (x is between 1 and 10)
print(1 < x < 4)     # False (x is not less than 4)
```

---

## 3. Logical Operators

Logical operators combine multiple boolean expressions.

```python
a = True
b = False

print(a and b)    # False  — AND: True only if BOTH are True
print(a or b)     # True   — OR:  True if AT LEAST ONE is True
print(not a)      # False  — NOT: Inverts the boolean value
```

### Truth Tables

**`and`:**
```
True  and True  = True
True  and False = False
False and True  = False
False and False = False
```

**`or`:**
```
True  or True  = True
True  or False = True
False or True  = True
False or False = False
```

**`not`:**
```
not True  = False
not False = True
```

### Short-Circuit Evaluation

This is very important to understand:

```python
# AND — if the left side is False, Python STOPS (right side not evaluated)
result = False and (1/0)   # No ZeroDivisionError! 1/0 never runs
print(result)              # False

# OR — if the left side is True, Python STOPS (right side not evaluated)
result = True or (1/0)     # No ZeroDivisionError! 1/0 never runs
print(result)              # True
```

**Why does this matter?**

```python
name = None

# Safe: checks name is not None BEFORE calling .upper()
# If name is None, the 'or' short-circuits and returns the default
display = name or "Anonymous"
print(display)   # "Anonymous"

# Using 'and' short-circuit to avoid errors
user = None
# Without short-circuit: user.name would crash if user is None
username = user and user.name   # Safe: returns None if user is None
```

### Logical Operators with Non-Boolean Values

`and` and `or` don't just return `True/False` — they return one of the actual operands:

```python
# 'or' returns the first TRUTHY value, or the last value
print(0 or "hello")         # "hello"
print("" or "hello")        # "hello"
print("world" or "hello")   # "world" (first truthy value)
print(0 or 0.0)             # 0.0 (both falsy, returns last)

# 'and' returns the first FALSY value, or the last value
print(1 and 2)              # 2 (all truthy, returns last)
print(0 and 2)              # 0 (first falsy, short-circuits)
print("" and "hello")       # "" (first falsy)
```

**Practical use (default values):**
```python
# If config is None or empty, use the default
config_value = None
timeout = config_value or 30   # timeout = 30 (config_value is falsy)
```

---

## 4. Assignment Operators

Assignment operators assign values to variables. The compound ones are shortcuts.

```python
x = 10         # Simple assignment

x += 5         # Same as: x = x + 5   → x is now 15
x -= 3         # Same as: x = x - 3   → x is now 12
x *= 2         # Same as: x = x * 2   → x is now 24
x /= 4         # Same as: x = x / 4   → x is now 6.0
x //= 2        # Same as: x = x // 2  → x is now 3.0
x **= 3        # Same as: x = x ** 3  → x is now 27.0
x %= 5         # Same as: x = x % 5   → x is now 2.0

# Walrus Operator := (Python 3.8+) — Assign AND return in one expression
numbers = [1, 2, 3, 4, 5]
if (n := len(numbers)) > 3:
    print(f"List is long: {n} elements")   # List is long: 5 elements
```

---

## 5. Identity Operators

Identity operators check if two variables point to the **same object in memory**, not just equal values.

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)     # True  — values are equal
print(a is b)     # False — different objects in memory
print(a is c)     # True  — c points to the SAME object as a
print(a is not b) # True  — they are different objects
```

```
┌──────────────────────────────────────────────────────────┐
│  == checks VALUE equality                                │
│  is checks IDENTITY (same memory address)                │
│                                                          │
│  Memory:                                                 │
│  a → [0x1234] → [1, 2, 3]                                │
│  b → [0x5678] → [1, 2, 3]   ← different box, same value  │
│  c → [0x1234] → [1, 2, 3]   ← SAME box as a              │
└──────────────────────────────────────────────────────────┘
```

**Rule:** Always use `is` to check for `None`, not `==`:
```python
x = None
if x is None:        # ✔ Correct
    print("Nothing")

if x == None:        # Works but not recommended
    print("Nothing")
```

---

## 6. Membership Operators

Membership operators check if a value exists inside a collection (string, list, tuple, dict, set).

```python
fruits = ["apple", "banana", "cherry"]

print("apple" in fruits)       # True
print("grape" in fruits)       # False
print("grape" not in fruits)   # True

# Works on strings too
sentence = "Hello, World!"
print("World" in sentence)     # True
print("Python" in sentence)    # False

# Works on dictionaries (checks KEYS by default)
person = {"name": "Alice", "age": 25}
print("name" in person)        # True
print("Alice" in person)       # False (Alice is a value, not a key)
print("Alice" in person.values())  # True
```

---

## 7. Bitwise Operators

Bitwise operators work directly on the **binary representation** of integers. Used in low-level programming, networking, and flags.

```python
a = 12   # Binary: 1100
b = 10   # Binary: 1010

print(a & b)    # 8    → 1000 (AND: 1 if BOTH bits are 1)
print(a | b)    # 14   → 1110 (OR:  1 if EITHER bit is 1)
print(a ^ b)    # 6    → 0110 (XOR: 1 if bits are DIFFERENT)
print(~a)       # -13  (NOT: inverts all bits — two's complement)
print(a << 2)   # 48   (Left shift: 1100 → 110000, multiply by 4)
print(a >> 2)   # 3    (Right shift: 1100 → 0011, divide by 4)
```

**Practical bit tricks:**
```python
# Check if number is even (last bit is 0 for even)
print(4 & 1)   # 0 — even
print(5 & 1)   # 1 — odd

# Fast multiplication/division by powers of 2
x = 7
print(x << 1)   # 14 (× 2)
print(x << 3)   # 56 (× 8)
print(x >> 1)   # 3  (÷ 2, truncated)
```

---

## 8. Operator Precedence

When multiple operators appear together, Python follows a strict order (PEMDAS-like):

```
┌────────────────────────────────────────────────────────────┐
│         Python Operator Precedence (Highest to Lowest)    │
│                                                            │
│  1. ()              — Parentheses (highest priority)       │
│  2. **              — Exponentiation                       │
│  3. +x, -x, ~x      — Unary plus, minus, bitwise NOT       │
│  4. *, /, //, %     — Multiplication, division, modulus    │
│  5. +, -            — Addition, subtraction                │
│  6. <<, >>          — Bit shifts                           │
│  7. &               — Bitwise AND                          │
│  8. ^               — Bitwise XOR                          │
│  9. |               — Bitwise OR                           │
│  10. ==,!=,>,<,>=,<= — Comparison                          │
│  11. is, is not     — Identity                             │
│  12. in, not in     — Membership                           │
│  13. not            — Logical NOT                          │
│  14. and            — Logical AND                          │
│  15. or             — Logical OR (lowest priority)         │
└────────────────────────────────────────────────────────────┘
```

```python
# Without knowing precedence, this can surprise you:
print(2 + 3 * 4)      # 14, NOT 20 (* before +)
print((2 + 3) * 4)    # 20 (parentheses override)
print(2 ** 3 ** 2)    # 512 (** is right-associative: 3**2=9, 2**9=512)
print(not True or True)  # True (not True = False, then False or True = True)
```

**Best Practice:** When in doubt, use parentheses to make your intent clear.

---

## 9. if / elif / else

Conditional statements let your program make decisions and execute different code based on conditions.

### Basic if

```python
temperature = 35

if temperature > 30:
    print("It's hot!")
    print("Stay hydrated.")
```

The code inside the `if` block only runs when the condition is `True`.

### if-else

```python
age = 16

if age >= 18:
    print("You can vote.")
else:
    print("You cannot vote yet.")
```

```
┌──────────────────────────────────────────────────────┐
│               if-else Flow                           │
│                                                      │
│         ┌─────────────────┐                          │
│         │   Condition?    │                          │
│         └────────┬────────┘                          │
│            True  │  False                            │
│            ┌─────┘  └─────┐                          │
│            ▼              ▼                          │
│       if-block        else-block                     │
│            └─────┐  ┌─────┘                          │
│                  ▼  ▼                                │
│            Rest of program                           │
└──────────────────────────────────────────────────────┘
```

### if-elif-else (Multiple Conditions)

`elif` is short for "else if". Can chain as many `elif` as needed:

```python
score = 75

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"

print(f"Your grade is: {grade}")   # Your grade is: C
```

**Important:** Python checks conditions **top to bottom** and stops at the FIRST true condition. Once a condition matches, the rest are skipped.

```python
x = 5

if x > 0:
    print("x is positive")    # This runs
elif x > 3:
    print("x is greater than 3")  # This NEVER runs (already matched above)
```

### Real-World Example: Login System

```python
username = input("Username: ")
password = input("Password: ")

if username == "admin" and password == "secret123":
    print("Welcome, Admin!")
elif username == "admin" and password != "secret123":
    print("Wrong password!")
else:
    print("User not found.")
```

---

## 10. Nested if

You can put `if` statements inside other `if` statements:

```python
age = 20
has_id = True

if age >= 18:
    if has_id:
        print("Entry allowed.")
    else:
        print("Age OK, but bring your ID.")
else:
    print("You must be 18 or older.")
```

**Warning:** Avoid deep nesting (more than 2-3 levels) — it makes code hard to read. Flatten using `and`:

```python
# Better:
if age >= 18 and has_id:
    print("Entry allowed.")
elif age >= 18:
    print("Age OK, but bring your ID.")
else:
    print("You must be 18 or older.")
```

---

## 11. Ternary (One-line if-else)

Python's ternary operator lets you write a simple `if-else` in one line:

```python
# Syntax: value_if_true  if  condition  else  value_if_false
age = 20
status = "adult" if age >= 18 else "minor"
print(status)    # adult

# Another example
x = 10
label = "positive" if x > 0 else ("zero" if x == 0 else "negative")

# Use for simple assignments — don't chain complex logic in one line
```

---

## 12. match-case (Python 3.10+)

`match-case` is Python's version of the `switch` statement from other languages. Added in Python 3.10.

```python
command = "quit"

match command:
    case "start":
        print("Starting...")
    case "stop":
        print("Stopping...")
    case "quit":
        print("Quitting...")    # This runs
    case _:                     # _ is the wildcard (default case)
        print(f"Unknown command: {command}")
```

### Pattern Matching with Values

```python
point = (1, 0)

match point:
    case (0, 0):
        print("Origin")
    case (x, 0):
        print(f"On X-axis at x={x}")   # This matches (1, 0)
    case (0, y):
        print(f"On Y-axis at y={y}")
    case (x, y):
        print(f"Point at ({x}, {y})")
```

Match-case is more powerful than traditional switch — it can match patterns, not just values.

---

## 13. for Loops

A `for` loop **iterates** over a sequence (list, string, range, tuple, dictionary, etc.) and executes the body once for each item.

### Basic for Loop

```python
fruits = ["apple", "banana", "cherry"]

for fruit in fruits:
    print(fruit)

# Output:
# apple
# banana
# cherry
```

The variable `fruit` (the loop variable) takes on the value of each item one at a time.

### Iterating Over a String

```python
for char in "Hello":
    print(char)

# Output:
# H
# e
# l
# l
# o
```

### `enumerate()` — Get Index and Value Together

A very common pattern: you need both the index and the value:

```python
fruits = ["apple", "banana", "cherry"]

for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Output:
# 0: apple
# 1: banana
# 2: cherry

# Start index at 1 instead of 0:
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")
# 1. apple  2. banana  3. cherry
```

### `zip()` — Iterate Over Two Lists Together

```python
names = ["Alice", "Bob", "Charlie"]
scores = [95, 82, 78]

for name, score in zip(names, scores):
    print(f"{name}: {score}")

# Output:
# Alice: 95
# Bob: 82
# Charlie: 78
```

### Iterating Over a Dictionary

```python
person = {"name": "Alice", "age": 25, "city": "NYC"}

# Iterate over keys (default)
for key in person:
    print(key)

# Iterate over values
for value in person.values():
    print(value)

# Iterate over key-value pairs
for key, value in person.items():
    print(f"{key}: {value}")
```

---

## 14. range()

`range()` generates a sequence of numbers. It is very memory-efficient — it does NOT create the list in memory; it generates numbers on demand.

```python
# range(stop) — generates 0, 1, 2, ..., stop-1
for i in range(5):
    print(i)   # 0 1 2 3 4

# range(start, stop) — generates start, start+1, ..., stop-1
for i in range(2, 7):
    print(i)   # 2 3 4 5 6

# range(start, stop, step) — control step size
for i in range(0, 20, 5):
    print(i)   # 0 5 10 15

# Negative step — count backwards
for i in range(10, 0, -2):
    print(i)   # 10 8 6 4 2

# Convert to a list if you need all values at once
print(list(range(5)))        # [0, 1, 2, 3, 4]
print(list(range(1, 10, 2))) # [1, 3, 5, 7, 9]
```

### Common Pattern: Traditional index-based loop

```python
fruits = ["apple", "banana", "cherry"]

for i in range(len(fruits)):
    print(f"Item {i}: {fruits[i]}")

# But prefer enumerate() when you need the index:
for i, fruit in enumerate(fruits):
    print(f"Item {i}: {fruit}")
```

---

## 15. while Loops

A `while` loop keeps executing **as long as** the condition is `True`. Use it when you don't know in advance how many iterations you need.

### Basic while Loop

```python
count = 0

while count < 5:
    print(f"Count is {count}")
    count += 1    # ← MUST update the condition variable, or infinite loop!

print("Done!")

# Output:
# Count is 0
# Count is 1
# Count is 2
# Count is 3
# Count is 4
# Done!
```

### Infinite Loop + break

Sometimes you intentionally want a loop to run forever until something happens:

```python
while True:    # Infinite loop
    user_input = input("Enter 'quit' to exit: ")
    if user_input == "quit":
        break  # Exit the loop
    print(f"You entered: {user_input}")

print("Goodbye!")
```

### while for Input Validation

```python
while True:
    age = input("Enter your age (must be a positive number): ")
    if age.isdigit() and int(age) > 0:
        age = int(age)
        break
    print("Invalid input. Try again.")

print(f"Your age is {age}.")
```

### for vs while — When to Use Which

```
┌────────────────────────────────────────────────────────────┐
│  Use FOR when:                                             │
│  - You know the number of iterations in advance            │
│  - You are iterating over a collection                     │
│  - You use range()                                         │
│                                                            │
│  Use WHILE when:                                           │
│  - You don't know how many iterations are needed           │
│  - You are waiting for a condition to become false         │
│  - Reading until end of file                               │
│  - Game loops ("keep playing until game over")             │
└────────────────────────────────────────────────────────────┘
```

---

## 16. break, continue, pass

### `break` — Exit the Loop Immediately

```python
for i in range(10):
    if i == 5:
        break       # Stop the loop entirely when i reaches 5
    print(i)

# Output: 0 1 2 3 4
```

### `continue` — Skip to Next Iteration

```python
for i in range(10):
    if i % 2 == 0:
        continue    # Skip even numbers
    print(i)

# Output: 1 3 5 7 9
```

### `pass` — Do Nothing (Placeholder)

`pass` is a null statement. It does nothing. Used as a placeholder when Python syntax requires a block but you have nothing to put there yet:

```python
# You're planning to write this later
if condition:
    pass   # TODO: implement this

# Empty function (required by syntax to have a body)
def my_function():
    pass

# Empty class
class MyClass:
    pass
```

### Comparison

```
┌────────────────────────────────────────────────────────────┐
│  break    → Exit loop completely                           │
│  continue → Skip current iteration, go to next             │
│  pass     → Do nothing, continue normally                  │
└────────────────────────────────────────────────────────────┘
```

---

## 17. else on Loops

Python has a unique feature: you can attach an `else` block to a loop. The `else` block runs **only if the loop completed naturally** (without a `break`).

```python
# Example: Search for a number
numbers = [1, 3, 5, 7, 9]
target = 6

for num in numbers:
    if num == target:
        print(f"Found {target}!")
        break
else:
    # This runs ONLY if the loop didn't hit a break
    print(f"{target} was not found in the list.")

# Output: 6 was not found in the list.
```

```python
# Another example: Check for prime number
n = 17
for i in range(2, n):
    if n % i == 0:
        print(f"{n} is NOT prime (divisible by {i})")
        break
else:
    print(f"{n} IS prime!")    # This runs if no break occurred
```

This pattern is very Pythonic and avoids the need for a `found` flag variable.

---

## 18. Nested Loops

A loop inside another loop. The inner loop completes all its iterations for each single iteration of the outer loop.

```python
for row in range(1, 4):        # Outer: 1, 2, 3
    for col in range(1, 4):    # Inner: 1, 2, 3 (for EACH outer iteration)
        print(f"({row},{col})", end=" ")
    print()    # Newline after each row

# Output:
# (1,1) (1,2) (1,3)
# (2,1) (2,2) (2,3)
# (3,1) (3,2) (3,3)
```

### Multiplication Table

```python
for i in range(1, 6):
    for j in range(1, 6):
        print(f"{i*j:3}", end="")   # :3 = width of 3 for alignment
    print()

# Output:
#   1  2  3  4  5
#   2  4  6  8 10
#   3  6  9 12 15
#   4  8 12 16 20
#   5 10 15 20 25
```

### `break` in Nested Loops

`break` only exits the **innermost loop** it is in:

```python
for i in range(3):
    for j in range(3):
        if j == 1:
            break          # Only breaks the INNER loop
        print(f"{i},{j}")
    # Inner loop breaks, outer loop CONTINUES

# Output:
# 0,0
# 1,0
# 2,0
```

To break out of multiple nested loops, you can use a flag variable or refactor into a function and use `return`.

---

## 19. Comprehensions (Preview)

Comprehensions are a concise, Pythonic way to create collections. They use a loop-like syntax on one line. We cover these in full in the Collections file, but here is a preview:

```python
# List comprehension — create a list using a for loop in one line

# Regular way:
squares = []
for x in range(1, 6):
    squares.append(x ** 2)

# Comprehension way:
squares = [x ** 2 for x in range(1, 6)]
print(squares)   # [1, 4, 9, 16, 25]

# With a condition:
evens = [x for x in range(20) if x % 2 == 0]
print(evens)   # [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

---

## Summary

```
┌────────────────────────────────────────────────────────────┐
│              Operators & Flow Summary                      │
│                                                            │
│  Arithmetic:  +  -  *  /  //  %  **                        │
│  Comparison:  ==  !=  >  <  >=  <=                         │
│  Logical:     and  or  not  (short-circuit!)               │
│  Assignment:  =  +=  -=  *=  /=  //=  **=  %=              │
│  Identity:    is  is not                                   │
│  Membership:  in  not in                                   │
│                                                            │
│  if/elif/else: checks conditions top-to-bottom             │
│  ternary: value_if_true if condition else value_if_false   │
│  for: iterate over sequences                               │
│  while: loop while condition is True                       │
│  break: exit loop  | continue: skip iteration              │
│  else on loop: runs if no break occurred                   │
│  range(start, stop, step): generates number sequences      │
└────────────────────────────────────────────────────────────┘
```

**Next:** [03-Python-Functions.md](03-Python-Functions.md) — Functions, Parameters, Return Values, and Lambdas.
