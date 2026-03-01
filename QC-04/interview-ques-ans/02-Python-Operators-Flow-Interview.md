# Python Operators & Program Flow — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. What are the different types of operators in Python?

**Answer:**
Python supports seven categories of operators:

1. **Arithmetic** — `+`, `-`, `*`, `/`, `//`, `%`, `**` — for math operations
2. **Comparison (Relational)** — `==`, `!=`, `>`, `<`, `>=`, `<=` — they compare values and return `True` or `False`
3. **Logical** — `and`, `or`, `not` — combine or negate boolean expressions
4. **Assignment** — `=`, `+=`, `-=`, `*=`, etc. — assign or update variable values
5. **Identity** — `is`, `is not` — check if two variables point to the same object in memory
6. **Membership** — `in`, `not in` — check if a value exists in a sequence like a list, string, or dict
7. **Bitwise** — `&`, `|`, `^`, `~`, `<<`, `>>` — operate on individual bits of integers

---

## 2. What is the difference between `/` and `//` in Python?

**Answer:**
`/` is **true division** — it always returns a **float**, even if both operands are integers. So `10 / 2` gives `5.0`, not `5`.

`//` is **floor division** — it divides and then **rounds down** to the nearest integer. With two integers, the result is an `int`: `17 // 5` gives `3`. With floats, the result is a `float` but still floored: `17.0 // 5` gives `3.0`.

An important gotcha: floor division rounds **toward negative infinity**, not toward zero. So `-17 // 5` gives `-4`, not `-3`, because the true result is -3.4 and the floor of that is -4.

---

## 3. What is the `%` (modulus) operator? Where do you use it?

**Answer:**
The modulus operator returns the **remainder** after division. `17 % 5` gives `2` because 17 divided by 5 is 3 with a remainder of 2.

I commonly use it for:
- **Checking even/odd:** `if n % 2 == 0` means the number is even
- **Cycling through indices:** `index % length` wraps around — useful for circular buffers or rotating through a list
- **Checking divisibility:** `if n % 3 == 0` means n is divisible by 3
- **Extracting digits:** `n % 10` gives the last digit of a number

---

## 4. What is the difference between `and`, `or`, `not` operators?

**Answer:**
These are **logical operators** that work on boolean values:

- `and` — returns `True` only if **both** operands are `True`. It **short-circuits**: if the first operand is `False`, Python doesn't even evaluate the second because the result is already `False`.

- `or` — returns `True` if **at least one** operand is `True`. It also short-circuits: if the first operand is `True`, the second is skipped.

- `not` — negates a boolean value. `not True` is `False`, `not False` is `True`.

An important Python nuance: `and` and `or` don't necessarily return `True` or `False` — they return one of the **operand values**. `"hello" and "world"` returns `"world"`. `"" or "default"` returns `"default"`. This is because Python evaluates **truthiness**, and the operators return the value that determined the result.

---

## 5. What is short-circuit evaluation?

**Answer:**
Short-circuit evaluation means Python **stops evaluating** a logical expression as soon as the result is determined:

With `and`: if the first condition is `False`, Python skips the second because `False and anything` is always `False`.
With `or`: if the first condition is `True`, Python skips the second because `True or anything` is always `True`.

This is not just an optimization — it's a **feature** I can rely on. For example:
```python
if user is not None and user.is_active:
    ...
```
If `user` is `None`, Python short-circuits and never calls `user.is_active`, avoiding an `AttributeError`. Without short-circuiting, this would crash.

---

## 6. What is the difference between `is` and `==`?

**Answer:**
`==` checks **value equality** — do two objects have the same content?
`is` checks **identity** — are they the **exact same object** in memory?

```python
a = [1, 2, 3]
b = [1, 2, 3]
a == b   # True — same values
a is b   # False — different objects in memory
```

I use `is` specifically for checking against **singletons** like `None`: `if x is None` is the correct and Pythonic way. For everything else, I use `==`. There's a CPython optimization where small integers (-5 to 256) and some strings are **interned** (cached), so `5 is 5` might return `True`, but this is an implementation detail I should never rely on.

---

## 7. What are membership operators (`in` and `not in`)?

**Answer:**
The `in` operator checks whether a value exists in a sequence or collection, and `not in` checks the opposite:

```python
"a" in "apple"          # True — substring check
3 in [1, 2, 3]          # True — list membership
"name" in {"name": "Alice"}  # True — checks keys in dicts
```

It works with strings, lists, tuples, sets, and dictionaries. For **dictionaries**, `in` checks the **keys**, not the values. For **sets**, membership check is **O(1)** because sets use hash tables internally, which makes them ideal when I need frequent lookups.

---

## 8. How does `if/elif/else` work in Python?

**Answer:**
Python uses `if`, `elif` (short for "else if"), and `else` for conditional logic:

```python
score = 85
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
```

Python evaluates conditions **top to bottom** and executes the **first** block whose condition is `True`, then skips the rest. If none match, the `else` block runs. The `elif` and `else` are optional.

Importantly, Python uses **indentation** to define blocks — not curly braces. This is enforced by the interpreter, and inconsistent indentation causes an `IndentationError`.

---

## 9. What is the ternary operator in Python?

**Answer:**
The ternary operator, or **conditional expression**, is a one-line shorthand for `if/else`:

```python
result = "Even" if x % 2 == 0 else "Odd"
```

The syntax is `value_if_true if condition else value_if_false`. It reads almost like English. I use it for simple assignments or return values where a full `if/else` block would be overkill. But for complex logic, I prefer the full `if/else` for readability.

---

## 10. What is the `match-case` statement in Python?

**Answer:**
`match-case` was introduced in **Python 3.10** as structural pattern matching. It's somewhat like `switch-case` in other languages but much more powerful:

```python
match command:
    case "start":
        start_server()
    case "stop":
        stop_server()
    case _:
        print("Unknown command")
```

The `_` is a wildcard that matches anything, like a default case. But what makes it special compared to a simple switch is that it can match **patterns** — destructuring sequences, matching object attributes, and combining patterns with guards (`case x if x > 0`). It's designed for structural pattern matching, not just value comparison.

---

## 11. What is the difference between `for` and `while` loops?

**Answer:**
A `for` loop iterates over a **sequence** (list, string, range, etc.) — I use it when I know the number of iterations or I'm iterating over a collection:
```python
for item in [1, 2, 3]:
    print(item)
```

A `while` loop runs as long as a **condition** is `True` — I use it when I don't know how many iterations I need, like waiting for user input or processing until a condition changes:
```python
while count > 0:
    count -= 1
```

In practice, `for` loops are more common in Python because Python's iteration protocol makes it natural to loop over values rather than using index-based counting.

---

## 12. What is the `range()` function?

**Answer:**
`range()` generates a sequence of numbers, commonly used with `for` loops. It takes up to three arguments:

- `range(stop)` — generates 0 to stop-1
- `range(start, stop)` — generates start to stop-1
- `range(start, stop, step)` — generates with a given step

```python
range(5)        # 0, 1, 2, 3, 4
range(2, 8)     # 2, 3, 4, 5, 6, 7
range(0, 10, 2) # 0, 2, 4, 6, 8
range(5, 0, -1) # 5, 4, 3, 2, 1
```

An important point: `range()` is **lazy** — it doesn't create the entire list in memory. It generates numbers on-the-fly as needed. So `range(1000000)` uses barely any memory, unlike `list(range(1000000))` which creates a full list. This makes it extremely efficient.

---

## 13. What are `break`, `continue`, and `pass`?

**Answer:**
These are loop control statements:

- **`break`** — immediately **exits** the loop entirely. The loop is done, and execution moves to the next statement after the loop.

- **`continue`** — **skips** the rest of the current iteration and jumps back to the top of the loop for the next iteration.

- **`pass`** — does **nothing**. It's a placeholder. I use it when a block is syntactically required but I don't want it to do anything yet — like an empty function body or a TODO.

```python
for i in range(10):
    if i == 5:
        break       # Stops at 5, loop ends
    if i % 2 == 0:
        continue    # Skip even numbers, go to next iteration
    print(i)        # Prints 1, 3
```

---

## 14. What is the `else` clause on loops?

**Answer:**
This is one of Python's unique features — loops can have an `else` clause:

```python
for item in items:
    if item == target:
        print("Found!")
        break
else:
    print("Not found.")
```

The `else` block runs **only if the loop completed without hitting a `break`**. If the loop finishes naturally (exhausts all iterations), `else` runs. If `break` interrupted the loop, `else` is skipped.

It's a clean alternative to using a flag variable like `found = False`. The naming is a bit confusing — it might make more sense to think of it as "no-break" rather than "else."

---

## 15. What are list comprehensions? Why use them?

**Answer:**
A list comprehension is a concise way to create lists by combining a `for` loop and an optional `if` condition in a single line:

```python
# Traditional way
squares = []
for x in range(10):
    squares.append(x ** 2)

# List comprehension — same result
squares = [x ** 2 for x in range(10)]

# With a condition
evens = [x for x in range(20) if x % 2 == 0]
```

I use them because they're **more readable** for simple transformations, **more Pythonic**, and **faster** than the equivalent `for` loop with `append()` — the latter has function call overhead for each `append`. However, for complex logic or side effects, I'd use a regular loop instead.

Python also has **dictionary comprehensions** (`{k: v for ...}`), **set comprehensions** (`{x for ...}`), and **generator expressions** (`(x for ...)` — lazy, memory-efficient).

---

## 16. What is operator precedence in Python?

**Answer:**
Operator precedence determines the order in which operations are evaluated. From highest to lowest priority:

1. `()` — Parentheses (highest)
2. `**` — Exponentiation
3. `+x`, `-x`, `~x` — Unary operators
4. `*`, `/`, `//`, `%` — Multiplication, division
5. `+`, `-` — Addition, subtraction
6. `<<`, `>>` — Bitwise shifts
7. `&` — Bitwise AND
8. `^` — Bitwise XOR
9. `|` — Bitwise OR
10. `==`, `!=`, `>`, `<`, `>=`, `<=`, `is`, `in` — Comparisons
11. `not` — Logical NOT
12. `and` — Logical AND
13. `or` — Logical OR (lowest)

My rule of thumb: when in doubt, use **parentheses** to make intent clear. Readable code is always better than clever code.

---

## 17. What is the `walrus operator` (`:=`)?

**Answer:**
The walrus operator `:=` was introduced in **Python 3.8**. It's an **assignment expression** — it assigns a value to a variable and returns that value in the same expression:

```python
# Without walrus operator
line = input()
while line != "quit":
    process(line)
    line = input()

# With walrus operator — no code duplication
while (line := input()) != "quit":
    process(line)
```

It's useful in `while` loops and `if` statements where I need to both compute a value and test it. It reduces code duplication and avoids repeating expressions. The name "walrus" comes from the `:=` looking like a walrus on its side.

---

## 18. What are truthy and falsy values in Python?

**Answer:**
In Python, every object has a **boolean value** — it's either "truthy" or "falsy."

**Falsy values** (evaluate to `False`):
- `False`, `None`
- Zero of any numeric type: `0`, `0.0`, `0j`
- Empty sequences and collections: `""`, `[]`, `()`, `{}`, `set()`
- Objects whose `__bool__()` returns `False` or `__len__()` returns `0`

**Everything else is truthy** — any non-zero number, any non-empty collection, any object by default.

This is why I can write:
```python
if my_list:     # instead of: if len(my_list) > 0
    process(my_list)
```
It's more Pythonic and cleaner. The `bool()` function tells me the truthiness of any value.

---

## 19. Can Python `for` loop work like a traditional C-style loop?

**Answer:**
Not directly. Python's `for` loop is a **for-each** loop — it iterates over items in a sequence, not using index counters like C's `for (int i = 0; i < n; i++)`.

However, I can achieve similar behavior using `range()`:
```python
for i in range(10):    # i goes from 0 to 9
    print(i)
```

And if I need both the index and the value, I use `enumerate()`:
```python
for index, value in enumerate(["a", "b", "c"]):
    print(index, value)
```

`enumerate()` is the Pythonic way rather than manually tracking an index variable.

---

## 20. What are chained comparisons in Python?

**Answer:**
Python allows me to **chain** comparison operators, which is more readable and closer to mathematical notation:

```python
# Instead of: if x > 0 and x < 10
if 0 < x < 10:
    print("Single digit positive")

# You can chain multiple
if 0 < x < y < 100:
    print("Both in range")
```

Under the hood, `a < b < c` is equivalent to `a < b and b < c`, but with the advantage that `b` is only evaluated once. This is unique to Python and makes boundary checks much cleaner.
