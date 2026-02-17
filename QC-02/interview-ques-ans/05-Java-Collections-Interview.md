# Java Collections — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview.

---

## 1. Explain the Java Collections framework hierarchy.

**Answer:**
The Collections framework has two main root interfaces:

- **Collection** (for single elements): `List`, `Set`, `Queue`
- **Map** (for key-value pairs): `HashMap`, `TreeMap`, `LinkedHashMap`

```
Iterable
  └── Collection
       ├── List  → ArrayList, LinkedList, Vector
       ├── Set   → HashSet, LinkedHashSet, TreeSet
       └── Queue → PriorityQueue, ArrayDeque

Map (separate hierarchy)
  └── HashMap, LinkedHashMap, TreeMap, Hashtable
```

The key distinction: `List` allows duplicates and maintains insertion order, `Set` does not allow duplicates, `Queue` follows FIFO, and `Map` stores key-value pairs with unique keys.

---

## 2. What is the difference between ArrayList and LinkedList?

**Answer:**
| Feature | ArrayList | LinkedList |
|---|---|---|
| Internal structure | Dynamic array | Doubly-linked list |
| Random access `get(i)` | O(1) — direct index | O(n) — traverse from head |
| Add/remove at end | O(1) amortized | O(1) |
| Add/remove at middle | O(n) — shifts elements | O(1) if already at position |
| Memory | Less (contiguous array) | More (each node stores two pointers) |

**When to use:** ArrayList for frequent random access and iteration. LinkedList for frequent insertions/deletions at the beginning or middle. In practice, **ArrayList is almost always preferred** due to CPU cache locality — contiguous memory is much faster for iteration than pointer chasing.

---

## 3. How does ArrayList work internally?

**Answer:**
ArrayList uses a **dynamic array** internally. It starts with an initial capacity (default 10). When the array is full and I add another element:

1. A new array is created with **1.5x the old capacity** (old + old >> 1)
2. All elements are **copied** to the new array
3. The old array is garbage collected

This resizing is O(n), but it happens rarely enough that `add()` is **O(1) amortized**. If I know the approximate size upfront, I should use `new ArrayList<>(expectedSize)` to avoid resizing.

---

## 4. How does HashMap work internally?

**Answer:**
This is a very common interview question. HashMap uses an **array of buckets** (called a table) internally.

When I call `put(key, value)`:
1. `key.hashCode()` is called and the hash is spread using `(h >>> 16) ^ h` to reduce collisions
2. The bucket index is calculated: `hash & (n - 1)` where n is the array length
3. If the bucket is empty, a new `Node` is placed there
4. If there's a collision (another key maps to the same bucket), the new node is added to a **linked list** at that bucket
5. **Java 8 optimization:** If a bucket's linked list grows beyond **8 nodes**, it's converted to a **red-black tree** (O(n) → O(log n) lookup)

When I call `get(key)`:
1. Same hash calculation to find the bucket
2. Traverse the list/tree in that bucket, comparing with `equals()` to find the matching key

**Important factors:**
- **Load factor** (default 0.75) — when 75% of buckets are filled, the array doubles in size and all entries are **rehashed**
- **Initial capacity** (default 16) — always a power of 2

---

## 5. What is the difference between HashSet, LinkedHashSet, and TreeSet?

**Answer:**
| Feature | HashSet | LinkedHashSet | TreeSet |
|---|---|---|---|
| Order | No order guaranteed | Insertion order preserved | Sorted (natural or Comparator) |
| Underlying | HashMap | LinkedHashMap | Red-Black Tree (TreeMap) |
| Performance | O(1) add/contains | O(1) add/contains | O(log n) add/contains |
| Null | Allows one null | Allows one null | No null (needs comparison) |

I'd use HashSet for the best performance when order doesn't matter, LinkedHashSet when I need to preserve insertion order, and TreeSet when I need elements automatically sorted.

Fun fact: HashSet is backed by a HashMap where the Set element is the key and a dummy object is the value.

---

## 6. What is the difference between HashMap, LinkedHashMap, and TreeMap?

**Answer:**
| Feature | HashMap | LinkedHashMap | TreeMap |
|---|---|---|---|
| Order | No guaranteed order | Insertion order (or access order) | Sorted by key |
| Performance | O(1) get/put | O(1) get/put | O(log n) get/put |
| Null keys | One null key allowed | One null key allowed | No null keys |
| Underlying | Array + linked list/tree | HashMap + doubly-linked list | Red-Black Tree |

LinkedHashMap can also be configured for **access-order** (most recently accessed last), making it useful for implementing an **LRU cache**.

---

## 7. What is the difference between Comparable and Comparator?

**Answer:**
- **Comparable** — the class itself defines its natural ordering by implementing `compareTo()`. There's only ONE natural order.
```java
class Employee implements Comparable<Employee> {
    public int compareTo(Employee other) { return this.name.compareTo(other.name); }
}
Collections.sort(employees);  // Uses natural order (by name)
```

- **Comparator** — an external class/lambda defines ordering. I can have MULTIPLE different orderings.
```java
employees.sort(Comparator.comparing(Employee::getSalary));          // by salary
employees.sort(Comparator.comparing(Employee::getName).reversed()); // by name descending
```

Use Comparable for the "default" sorting, and Comparator when I need custom or multiple sort orders.

---

## 8. What is ConcurrentModificationException?

**Answer:**
It's thrown when a collection is **modified while being iterated** (not through the iterator itself). Collections use a `modCount` field — if it changes during iteration, the iterator detects it and throws this exception (fail-fast behavior).

```java
// ❌ Throws ConcurrentModificationException
for (String s : list) {
    if (s.equals("remove")) list.remove(s);
}

// ✅ Safe — use Iterator.remove()
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("remove")) it.remove();
}

// ✅ Also safe — removeIf (Java 8+)
list.removeIf(s -> s.equals("remove"));
```

For concurrent access, I'd use thread-safe collections like `ConcurrentHashMap` or `CopyOnWriteArrayList`.

---

## 9. What is the difference between Iterator and ListIterator?

**Answer:**
- **Iterator** — forward-only traversal. Available for all Collections. Has `hasNext()`, `next()`, `remove()`.
- **ListIterator** — bidirectional traversal. Only for Lists. Adds `hasPrevious()`, `previous()`, `add()`, `set()`, and `nextIndex()/previousIndex()`.

---

## 10. When would you use a Queue or Deque?

**Answer:**
- **Queue** — FIFO (First In, First Out). Used for task scheduling, message processing, BFS traversal. `offer()` to add, `poll()` to remove.
- **Deque** (Double-ended queue) — can add/remove from both ends. `ArrayDeque` is the recommended implementation for both stack (LIFO) and queue (FIFO) use cases. It's faster than both `Stack` (legacy) and `LinkedList` for these purposes.
- **PriorityQueue** — elements are ordered by priority (natural ordering or Comparator), not FIFO. Backed by a binary heap. `poll()` always returns the smallest element.

---

## 11. What is the difference between fail-fast and fail-safe iterators?

**Answer:**
- **Fail-fast** (ArrayList, HashMap) — immediately throws `ConcurrentModificationException` if the collection is modified during iteration. They work on the original collection.
- **Fail-safe** (ConcurrentHashMap, CopyOnWriteArrayList) — they work on a copy or snapshot of the collection, so modifications during iteration don't cause exceptions. The trade-off is higher memory usage and potentially stale data.

---

## 12. Why is the initial capacity of HashMap a power of 2?

**Answer:**
Because the bucket index is calculated using `hash & (capacity - 1)` instead of `hash % capacity`. Bitwise AND is much faster than modulo. And this only works correctly when the capacity is a power of 2, because `(2^n - 1)` produces a bitmask of all 1s (e.g., 15 = 0b1111), distributing entries evenly across all buckets.

---

*Back to: [05-Java-Collections.md](../05-Java-Collections.md)*
