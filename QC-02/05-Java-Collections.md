# Java Collections Framework — From Zero to Expert

## Table of Contents
1. [Overview of Collections Hierarchy](#1-collections-hierarchy)
2. [List Interface & Implementations](#2-list-interface)
3. [Set Interface & Implementations](#3-set-interface)
4. [Queue Interface & Implementations](#4-queue-interface)
5. [Map Interface & Implementations](#5-map-interface)
6. [Iterators](#6-iterators)
7. [Comparable & Comparator](#7-comparable-and-comparator)
8. [Collections Utility Class](#8-collections-utility)

---

## 1. Collections Hierarchy

```
                          Iterable<E>
                              │
                         Collection<E>
                        /      |       \
                    List<E>  Set<E>   Queue<E>
                    /   |      |   \      |    \
           ArrayList  LinkedList  HashSet  TreeSet  PriorityQueue  ArrayDeque
              Vector                LinkedHashSet
               Stack

                          Map<K,V>   (separate hierarchy — NOT a Collection!)
                         /    |    \
                  HashMap  TreeMap  LinkedHashMap
                  Hashtable
```

### Why Collections?

Arrays are **fixed-size** and have limited features. Collections are:
- **Dynamic size** — grow and shrink automatically
- **Type-safe** with generics — `List<String>` instead of `Object[]`
- **Rich API** — sorting, searching, filtering, etc.
- **Different data structures** — choose the right one for your use case

### Generics — Type Safety

```java
// Without generics (Java 1.4 and earlier) — dangerous!
List list = new ArrayList();
list.add("hello");
list.add(42);                   // No error at compile time!
String s = (String) list.get(1); // ClassCastException at RUNTIME! 💥

// With generics — safe!
List<String> list = new ArrayList<>();  // Diamond operator <>
list.add("hello");
// list.add(42);                // ❌ COMPILE ERROR! Type safety!
String s = list.get(0);        // No cast needed!
```

---

## 2. List Interface

**List** = Ordered collection that allows duplicates. Elements have an index (0-based).

### ArrayList

The most commonly used List. Backed by a **dynamic array**.

```java
import java.util.ArrayList;
import java.util.List;

List<String> names = new ArrayList<>();

// Add elements
names.add("Alice");              // [Alice]
names.add("Bob");                // [Alice, Bob]
names.add("Charlie");            // [Alice, Bob, Charlie]
names.add(1, "David");           // [Alice, David, Bob, Charlie] (insert at index 1)

// Access elements
String first = names.get(0);     // "Alice"
int size = names.size();          // 4

// Modify
names.set(0, "Anna");            // [Anna, David, Bob, Charlie]

// Remove
names.remove("Bob");              // [Anna, David, Charlie] (by value)
names.remove(0);                  // [David, Charlie] (by index)

// Search
boolean has = names.contains("David");  // true
int idx = names.indexOf("David");       // 0

// Check empty
boolean empty = names.isEmpty();  // false

// Convert to array
String[] arr = names.toArray(new String[0]);

// Clear all
names.clear();                    // []
```

#### ArrayList Under the Hood

```
Default initial capacity: 10

When ArrayList is full:
  1. Creates a new array with 1.5x the size (newCapacity = oldCapacity + oldCapacity >> 1)
  2. Copies all elements to the new array
  3. Discards the old array

┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│ A │ B │ C │ D │ E │   │   │   │   │   │  capacity: 10, size: 5
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘

After adding 5 more (full), then adding 1 more:
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│ A │ B │ C │ D │ E │ F │ G │ H │ I │ J │ K │   │   │   │   │  capacity: 15, size: 11
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘

Performance:
  get(index)    → O(1)  — Direct access by index (arr[i])
  add(element)  → O(1) amortized — Usually just arr[size++], occasionally O(n) to resize
  add(index, e) → O(n)  — Must shift elements to the right
  remove(index) → O(n)  — Must shift elements to the left
  contains(e)   → O(n)  — Must scan the whole list
```

### LinkedList

Backed by a **doubly-linked list**. Each element is a **node** with prev/next pointers.

```java
import java.util.LinkedList;

LinkedList<String> list = new LinkedList<>();
list.add("Alice");
list.add("Bob");
list.add("Charlie");

// LinkedList-specific methods (as a Deque)
list.addFirst("First");     // [First, Alice, Bob, Charlie]
list.addLast("Last");       // [First, Alice, Bob, Charlie, Last]
list.getFirst();             // "First"
list.getLast();              // "Last"
list.removeFirst();          // [Alice, Bob, Charlie, Last]
list.removeLast();           // [Alice, Bob, Charlie]
```

#### LinkedList Under the Hood

```
Head                                                  Tail
 ↓                                                     ↓
[null|Alice|→] ⟷ [←|Bob|→] ⟷ [←|Charlie|null]

Each node:
┌────────────────────────┐
│  prev │ data │ next    │
└────────────────────────┘

Performance:
  get(index)    → O(n)  — Must traverse from head or tail!
  add(element)  → O(1)  — Just link a new node at the end
  addFirst(e)   → O(1)  — Link at the front
  remove(index) → O(n)  — Find the node (O(n)), then unlink (O(1))
  contains(e)   → O(n)  — Must scan the whole list
```

### ArrayList vs LinkedList

| Operation | ArrayList | LinkedList |
|---|---|---|
| get(index) | **O(1)** ✅ | O(n) ❌ |
| add(end) | O(1) amortized | **O(1)** |
| add(beginning) | O(n) | **O(1)** ✅ |
| remove(middle) | O(n) | O(n)* |
| Memory | Less (just array) | More (node + 2 pointers per element) |
| Cache | **Good** (contiguous memory) | Bad (scattered memory) |

**Use ArrayList** in 95% of cases. LinkedList is rarely better in practice because of poor cache locality.

### List.of() — Immutable Lists (Java 9+)

```java
// Create immutable list (cannot add, remove, or modify)
List<String> immutable = List.of("A", "B", "C");
// immutable.add("D");     // ❌ UnsupportedOperationException!
// immutable.set(0, "X");  // ❌ UnsupportedOperationException!

// Mutable copy of immutable list
List<String> mutable = new ArrayList<>(List.of("A", "B", "C"));
mutable.add("D");  // ✅ Works!
```

---

## 3. Set Interface

**Set** = Collection with **no duplicates**. No guaranteed order (depends on implementation).

### HashSet

Most commonly used Set. Uses a **HashMap** internally.

```java
import java.util.HashSet;
import java.util.Set;

Set<String> fruits = new HashSet<>();
fruits.add("Apple");      // true (added)
fruits.add("Banana");     // true
fruits.add("Apple");      // false (duplicate! not added)
System.out.println(fruits);  // [Banana, Apple] (NO guaranteed order!)

fruits.contains("Apple");    // true — O(1) average!
fruits.remove("Banana");     // true
fruits.size();               // 1
```

#### HashSet Under the Hood

```
HashSet is secretly a HashMap where:
  - Your element is the KEY
  - The value is a dummy constant object

When you add "Apple":
  1. hashCode() of "Apple" → e.g., 63476538
  2. Bucket index = hashCode % numberOfBuckets → e.g., bucket 6
  3. Check if bucket 6 already has "Apple" (using equals())
  4. If not found → add it. If found → ignore (no duplicates!)

Buckets (internal array):
[0] → null
[1] → null
[2] → "Banana" → null
[3] → null
[4] → null
[5] → null
[6] → "Apple" → null
...

Performance:
  add(e)       → O(1) average (O(n) worst case if many hash collisions)
  remove(e)    → O(1) average
  contains(e)  → O(1) average  ← THIS is why you use HashSet!

Load Factor (default 0.75):
  When 75% of buckets are full → resize to 2x and rehash everything
```

### LinkedHashSet

Like HashSet but **maintains insertion order**.

```java
Set<String> ordered = new LinkedHashSet<>();
ordered.add("Charlie");
ordered.add("Alice");
ordered.add("Bob");
System.out.println(ordered);  // [Charlie, Alice, Bob] ← insertion order preserved!
```

Under the hood: Each entry has prev/next pointers forming a linked list.

### TreeSet

**Sorted** Set. Uses a **Red-Black tree** internally.

```java
import java.util.TreeSet;

Set<Integer> sorted = new TreeSet<>();
sorted.add(5);
sorted.add(2);
sorted.add(8);
sorted.add(1);
System.out.println(sorted);  // [1, 2, 5, 8] ← always sorted!

// TreeSet-specific navigation methods
TreeSet<Integer> ts = new TreeSet<>(sorted);
ts.first();          // 1
ts.last();           // 8
ts.higher(2);        // 5 (next higher than 2)
ts.lower(5);         // 2 (next lower than 5)
ts.subSet(2, 8);     // [2, 5] (range, 2 inclusive, 8 exclusive)
```

```
Performance (all O(log n)):
  add, remove, contains → O(log n)  (balanced tree)

Red-Black Tree:
         5 (Black)
        / \
   2 (Red)  8 (Red)
    /
  1 (Black)

Self-balancing BST — guarantees O(log n) operations
```

### Set Comparison

| Feature | HashSet | LinkedHashSet | TreeSet |
|---|---|---|---|
| Order | None | Insertion order | Sorted (natural or Comparator) |
| Performance | O(1) | O(1) | O(log n) |
| Nulls | 1 null allowed | 1 null allowed | ❌ No nulls (needs compareTo) |
| Underlying | HashMap | HashMap + LinkedList | Red-Black Tree |

---

## 4. Queue Interface

**Queue** = First-In-First-Out (FIFO) collection. Elements are added at the tail and removed from the head.

```java
import java.util.LinkedList;
import java.util.Queue;

Queue<String> queue = new LinkedList<>();

// Two sets of methods:
// Throws exception     |  Returns null/false
// add(e)               |  offer(e)      — add to tail
// remove()             |  poll()        — remove from head
// element()            |  peek()        — look at head

queue.offer("Alice");     // [Alice]
queue.offer("Bob");       // [Alice, Bob]
queue.offer("Charlie");   // [Alice, Bob, Charlie]

queue.peek();             // "Alice" (doesn't remove)
queue.poll();             // "Alice" (removes and returns)
// Queue is now: [Bob, Charlie]

Queue<String> empty = new LinkedList<>();
empty.poll();             // null (empty queue, no exception)
// empty.remove();        // ❌ NoSuchElementException!
```

### Deque (Double-Ended Queue)

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<String> deque = new ArrayDeque<>();

// Works as both Stack (LIFO) and Queue (FIFO)!

// As a Queue (FIFO):
deque.offerLast("A");     // Add at tail: [A]
deque.offerLast("B");     // [A, B]
deque.pollFirst();        // Remove from head: "A", deque = [B]

// As a Stack (LIFO):
deque.push("X");          // Push: [X, B]
deque.push("Y");          // Push: [Y, X, B]
deque.pop();              // Pop: "Y", deque = [X, B]
deque.peek();             // "X" (top of stack)
```

### PriorityQueue

Elements are ordered by their **natural order** (or a Comparator). Always dequeues the smallest element.

```java
import java.util.PriorityQueue;

PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(30);
pq.offer(10);
pq.offer(20);

pq.poll();   // 10 (smallest first!)
pq.poll();   // 20
pq.poll();   // 30

// With custom ordering (max-heap):
PriorityQueue<Integer> maxPQ = new PriorityQueue<>(Comparator.reverseOrder());
maxPQ.offer(30);
maxPQ.offer(10);
maxPQ.offer(20);
maxPQ.poll();  // 30 (largest first!)
```

Under the hood: PriorityQueue uses a **binary heap** (min-heap by default).

```
Binary Heap for [10, 30, 20]:
       10
      /  \
    30    20

offer() → O(log n)  (bubble up)
poll()  → O(log n)  (bubble down)
peek()  → O(1)      (root is always min/max)
```

---

## 5. Map Interface

**Map** = Key-Value pairs. Each key is unique. Like a dictionary.

```
NOT part of the Collection interface!

Map<K,V>
  │
  ├── HashMap         — O(1), no order
  ├── LinkedHashMap   — O(1), insertion order
  ├── TreeMap         — O(log n), sorted by keys
  └── Hashtable       — Thread-safe (legacy, use ConcurrentHashMap instead)
```

### HashMap

```java
import java.util.HashMap;
import java.util.Map;

Map<String, Integer> ages = new HashMap<>();

// Put key-value pairs
ages.put("Alice", 30);
ages.put("Bob", 25);
ages.put("Charlie", 35);
ages.put("Alice", 31);      // Overwrites Alice's value! Keys are unique.

// Get value by key
int aliceAge = ages.get("Alice");           // 31
Integer unknown = ages.get("Unknown");      // null (key not found)
int safe = ages.getOrDefault("Unknown", 0); // 0 (default if not found)

// Check existence
ages.containsKey("Alice");     // true
ages.containsValue(25);        // true

// Remove
ages.remove("Charlie");        // removes entry, returns 35
ages.remove("Bob", 99);        // does NOT remove (value doesn't match)

// Size
ages.size();                    // 2

// Iterate
for (Map.Entry<String, Integer> entry : ages.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}

// Iterate keys only
for (String key : ages.keySet()) {
    System.out.println(key);
}

// Iterate values only
for (int value : ages.values()) {
    System.out.println(value);
}

// forEach (Java 8+)
ages.forEach((key, value) -> System.out.println(key + " = " + value));
```

#### HashMap Under the Hood (Java 8+)

```
HashMap uses an array of buckets. Each bucket is:
  - A linked list (when few collisions)
  - A Red-Black tree (when ≥8 collisions in one bucket — "treeification")

Initial capacity: 16 buckets
Load factor: 0.75 (resize when 75% full)

put("Alice", 30):
  1. hashCode("Alice") → 63476538
  2. Bucket index = hash(63476538) & (16-1) → e.g., 6
  3. Check bucket 6: is "Alice" there? No → add new entry

Bucket array:
[0] → null
[1] → null
[2] → Entry("Bob", 25) → null
[3] → null
[4] → null
[5] → null
[6] → Entry("Alice", 31) → null     ← Linked list of entries
...
[15] → null

Hash collision example (two keys in same bucket):
[6] → Entry("Alice", 31) → Entry("SomeOtherKey", 99) → null

When bucket has 8+ entries → converts to Red-Black Tree for O(log n) lookup
When bucket drops below 6 entries → converts back to linked list

Resize: When size > capacity * loadFactor (e.g., 12 out of 16)
  → Double the capacity to 32
  → Rehash ALL entries (expensive!)
```

### LinkedHashMap

Maintains **insertion order** (or optionally access order).

```java
Map<String, Integer> linked = new LinkedHashMap<>();
linked.put("Charlie", 3);
linked.put("Alice", 1);
linked.put("Bob", 2);
System.out.println(linked);  // {Charlie=3, Alice=1, Bob=2} ← insertion order!

// Access-order LinkedHashMap (useful for LRU cache!)
Map<String, Integer> lru = new LinkedHashMap<>(16, 0.75f, true);
// accessOrder=true → most recently accessed element moves to the end
```

### TreeMap

Keys are always **sorted** (by natural order or Comparator).

```java
Map<String, Integer> sorted = new TreeMap<>();
sorted.put("Charlie", 3);
sorted.put("Alice", 1);
sorted.put("Bob", 2);
System.out.println(sorted);  // {Alice=1, Bob=2, Charlie=3} ← sorted by key!

TreeMap<String, Integer> tm = new TreeMap<>(sorted);
tm.firstKey();         // "Alice"
tm.lastKey();          // "Charlie"
tm.higherKey("Bob");   // "Charlie"
tm.subMap("Alice", "Charlie");  // {Alice=1, Bob=2}
```

### Map Comparison

| Feature | HashMap | LinkedHashMap | TreeMap |
|---|---|---|---|
| Order | None | Insertion order | Sorted by key |
| get/put | O(1) | O(1) | O(log n) |
| Null keys | 1 null key allowed | 1 null key allowed | ❌ No null keys |
| Thread-safe | ❌ No | ❌ No | ❌ No |
| Underlying | Array + LinkedList/Tree | HashMap + doubly linked list | Red-Black Tree |

### Useful Map Methods (Java 8+)

```java
Map<String, Integer> map = new HashMap<>();

// putIfAbsent — only put if key doesn't exist
map.putIfAbsent("Alice", 30);  // Added (key didn't exist)
map.putIfAbsent("Alice", 31);  // NOT added (key exists)

// computeIfAbsent — compute value only if key is absent
map.computeIfAbsent("Bob", key -> key.length());  // "Bob" → 3

// merge — combine values
Map<String, Integer> wordCount = new HashMap<>();
for (String word : words) {
    wordCount.merge(word, 1, Integer::sum);  // If exists, add 1 to current value
}

// replaceAll
map.replaceAll((key, val) -> val * 2);  // Double all values
```

---

## 6. Iterators

An **Iterator** lets you traverse a collection element by element.

```java
import java.util.Iterator;

List<String> names = new ArrayList<>(List.of("Alice", "Bob", "Charlie"));

// Using Iterator
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    String name = it.next();
    if (name.equals("Bob")) {
        it.remove();  // ✅ Safe way to remove during iteration!
    }
}
System.out.println(names);  // [Alice, Charlie]

// ❌ WRONG — ConcurrentModificationException!
for (String name : names) {
    if (name.equals("Alice")) {
        names.remove(name);  // ❌ Modifying collection during for-each!
    }
}

// ✅ Alternative: removeIf (Java 8+)
names.removeIf(name -> name.equals("Alice"));
```

### ListIterator (Bidirectional)

```java
List<String> list = new ArrayList<>(List.of("A", "B", "C", "D"));

ListIterator<String> lit = list.listIterator();

// Forward
while (lit.hasNext()) {
    System.out.println(lit.nextIndex() + ": " + lit.next());
}

// Backward
while (lit.hasPrevious()) {
    System.out.println(lit.previous());
}

// Modify during iteration
lit = list.listIterator();
while (lit.hasNext()) {
    String item = lit.next();
    if (item.equals("B")) {
        lit.set("B-modified");  // Replace current element
    }
    if (item.equals("C")) {
        lit.add("C2");          // Insert after current element
    }
}
```

### Iterable Interface

Any class that implements `Iterable<T>` can be used in a for-each loop!

```java
public class NumberRange implements Iterable<Integer> {
    private final int start, end;
    
    public NumberRange(int start, int end) {
        this.start = start;
        this.end = end;
    }
    
    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<Integer>() {
            int current = start;
            
            @Override
            public boolean hasNext() {
                return current <= end;
            }
            
            @Override
            public Integer next() {
                return current++;
            }
        };
    }
}

// Now you can use for-each!
for (int num : new NumberRange(1, 5)) {
    System.out.println(num);  // 1, 2, 3, 4, 5
}
```

---

## 7. Comparable and Comparator

### Comparable — Natural Ordering

```java
// Implement Comparable to define the "natural" sort order of your class
public class Employee implements Comparable<Employee> {
    private String name;
    private int salary;
    
    @Override
    public int compareTo(Employee other) {
        // Return:
        //   negative → this comes BEFORE other
        //   zero     → equal
        //   positive → this comes AFTER other
        return Integer.compare(this.salary, other.salary);  // Sort by salary ascending
    }
}

// Now you can sort:
List<Employee> emps = new ArrayList<>();
emps.add(new Employee("Alice", 75000));
emps.add(new Employee("Bob", 50000));
emps.add(new Employee("Charlie", 90000));

Collections.sort(emps);  // Sorted by salary: Bob, Alice, Charlie
// Or with TreeSet:
Set<Employee> sorted = new TreeSet<>(emps);  // Also sorted!
```

### Comparator — Custom Ordering

```java
// When you want to sort by different criteria without modifying the class

// Sort by name
Comparator<Employee> byName = (e1, e2) -> e1.getName().compareTo(e2.getName());

// Sort by salary descending
Comparator<Employee> bySalaryDesc = (e1, e2) -> Integer.compare(e2.getSalary(), e1.getSalary());

// Using Comparator.comparing() (cleaner!)
Comparator<Employee> byName2 = Comparator.comparing(Employee::getName);
Comparator<Employee> bySal = Comparator.comparing(Employee::getSalary);
Comparator<Employee> bySalDesc = Comparator.comparing(Employee::getSalary).reversed();

// Chaining comparators (sort by dept, then by salary within each dept)
Comparator<Employee> complex = Comparator
    .comparing(Employee::getDepartment)
    .thenComparing(Employee::getSalary)
    .thenComparing(Employee::getName);

// Use the comparator:
Collections.sort(emps, byName);
emps.sort(bySalaryDesc);
TreeSet<Employee> ts = new TreeSet<>(complex);
```

---

## 8. Collections Utility Class

```java
import java.util.Collections;

List<Integer> list = new ArrayList<>(List.of(5, 2, 8, 1, 9));

// Sort
Collections.sort(list);                    // [1, 2, 5, 8, 9]
Collections.sort(list, Comparator.reverseOrder());  // [9, 8, 5, 2, 1]

// Search (list must be sorted!)
int index = Collections.binarySearch(list, 5);

// Min / Max
int min = Collections.min(list);   // 1
int max = Collections.max(list);   // 9

// Reverse
Collections.reverse(list);

// Shuffle
Collections.shuffle(list);

// Frequency
int count = Collections.frequency(list, 5);  // How many times 5 appears

// Unmodifiable wrappers
List<String> unmod = Collections.unmodifiableList(names);
// unmod.add("X");  // ❌ UnsupportedOperationException!

// Synchronized wrappers (thread-safe)
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// Singleton collections
List<String> single = Collections.singletonList("only");
Set<Integer> singleSet = Collections.singleton(42);
Map<String, Integer> singleMap = Collections.singletonMap("key", 1);

// Empty collections
List<String> empty = Collections.emptyList();
```

---

## Choosing the Right Collection — Decision Guide

```
Need key-value pairs?
  YES → Map
    Need sorted keys? → TreeMap
    Need insertion order? → LinkedHashMap
    Neither? → HashMap ✅ (default choice)

Need unique elements?
  YES → Set
    Need sorted? → TreeSet
    Need insertion order? → LinkedHashSet
    Neither? → HashSet ✅ (default choice)

Need ordered, allow duplicates?
  YES → List
    Mostly random access (get by index)? → ArrayList ✅ (default choice)
    Mostly insert/remove at ends? → ArrayDeque or LinkedList

Need FIFO processing?
  YES → Queue
    With priority? → PriorityQueue
    Double-ended? → ArrayDeque ✅

Need LIFO (stack)?
  YES → ArrayDeque (NOT Stack class — it's legacy!)
```

---

## Big-O Complexity Summary

| Collection | get | add | remove | contains | Notes |
|---|---|---|---|---|---|
| ArrayList | O(1) | O(1)* | O(n) | O(n) | *amortized |
| LinkedList | O(n) | O(1) | O(n) | O(n) | O(1) addFirst/Last |
| HashSet | — | O(1) | O(1) | O(1) | No duplicates, no order |
| TreeSet | — | O(log n) | O(log n) | O(log n) | Sorted |
| HashMap | O(1) | O(1) | O(1) | O(1) | Key-value, no order |
| TreeMap | O(log n) | O(log n) | O(log n) | O(log n) | Sorted keys |
| PriorityQueue | — | O(log n) | O(log n) | O(n) | Min/max in O(1) |
| ArrayDeque | O(n) | O(1) | O(1)** | O(n) | **from ends |

---

*Previous: [04-Java-OOP.md](04-Java-OOP.md)*
*Next: [06-Java-Advanced.md](06-Java-Advanced.md)*
