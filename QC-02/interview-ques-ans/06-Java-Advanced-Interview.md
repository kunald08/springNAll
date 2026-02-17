# Java Advanced — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview.

---

## 1. What is a Lambda expression?

**Answer:**
A lambda is an **anonymous function** — a short way to implement a functional interface without creating a full class. It was introduced in Java 8.

```java
// Before lambdas:
Comparator<String> comp = new Comparator<String>() {
    public int compare(String a, String b) { return a.compareTo(b); }
};

// With lambda:
Comparator<String> comp = (a, b) -> a.compareTo(b);
```

Syntax: `(parameters) -> expression` or `(parameters) -> { statements; }`

Under the hood, lambdas are **not anonymous inner classes**. The compiler generates a private static method and uses `invokedynamic` bytecode instruction. At runtime, `LambdaMetafactory` creates the functional interface implementation. This is faster than anonymous classes because there's no extra `.class` file and the instance can be cached.

---

## 2. What is a Functional Interface?

**Answer:**
A functional interface has exactly **one abstract method**. Lambdas can only implement functional interfaces. The `@FunctionalInterface` annotation is optional but recommended — the compiler verifies there's exactly one abstract method.

Java provides built-in functional interfaces in `java.util.function`:

| Interface | Input → Output | Method |
|---|---|---|
| `Predicate<T>` | T → boolean | `test(T)` |
| `Function<T,R>` | T → R | `apply(T)` |
| `Consumer<T>` | T → void | `accept(T)` |
| `Supplier<T>` | () → T | `get()` |
| `UnaryOperator<T>` | T → T | `apply(T)` |
| `BinaryOperator<T>` | (T,T) → T | `apply(T,T)` |

---

## 3. What are Method References?

**Answer:**
Method references are a shorthand for lambdas that just call an existing method. There are four types:

1. **Static method:** `Integer::parseInt` → `s -> Integer.parseInt(s)`
2. **Bound instance:** `System.out::println` → `s -> System.out.println(s)`
3. **Unbound instance:** `String::toUpperCase` → `s -> s.toUpperCase()`
4. **Constructor:** `ArrayList::new` → `() -> new ArrayList<>()`

I use method references when the lambda simply delegates to an existing method without any transformation.

---

## 4. What is the Stream API? How does it work?

**Answer:**
The Stream API (Java 8) lets me process collections **declaratively** — I describe what I want, not how to do it.

A stream pipeline has three parts:
1. **Source** — a collection, array, or generator
2. **Intermediate operations** — transform the stream (lazy! nothing executes yet): `filter()`, `map()`, `sorted()`, `distinct()`, `flatMap()`
3. **Terminal operation** — triggers execution and produces a result: `collect()`, `forEach()`, `count()`, `reduce()`, `findFirst()`

```java
List<String> names = employees.stream()
    .filter(e -> e.getSalary() > 50000)    // Keep high earners
    .map(Employee::getName)                 // Extract names
    .sorted()                               // Sort alphabetically
    .collect(Collectors.toList());           // Collect into list
```

Key characteristics: streams are **lazy** (intermediate operations don't execute until a terminal operation is called), **consumable** (can only be used once), and can be **parallel** for multi-core processing.

---

## 5. What is the difference between map() and flatMap()?

**Answer:**
- **`map()`** transforms each element one-to-one. If the function returns a collection, you get a stream of collections (nested).
- **`flatMap()`** transforms each element and flattens the result into a single stream.

```java
// map: Stream<List<String>> — nested!
orders.stream().map(Order::getItems)  // Each order has a list of items

// flatMap: Stream<String> — flattened!
orders.stream().flatMap(order -> order.getItems().stream())
```

Think of it as: `map` wraps, `flatMap` unwraps and merges.

---

## 6. What is Optional? Why use it?

**Answer:**
Optional is a container that may or may not hold a value. It's Java's way of explicitly handling the absence of a value instead of returning null, which eliminates `NullPointerException`.

```java
Optional<User> user = repository.findById(id);
String name = user
    .map(User::getName)
    .map(String::toUpperCase)
    .orElse("UNKNOWN");
```

Best practices:
- ✅ Use as method return type
- ❌ Don't use as method parameters or class fields
- ❌ Never call `get()` without checking — use `orElse()`, `orElseGet()`, or `orElseThrow()`

---

## 7. What is multithreading? How do you create threads in Java?

**Answer:**
Multithreading is running multiple threads concurrently within a single process. Each thread has its own stack but shares the heap with other threads.

Two ways to create threads:
1. **Implement Runnable** (preferred — since you can still extend another class):
```java
Thread t = new Thread(() -> System.out.println("Hello from thread"));
t.start();
```

2. **Extend Thread class** (less flexible):
```java
class MyThread extends Thread { public void run() { ... } }
```

Always call `start()`, not `run()`. `start()` creates a new OS thread; `run()` executes in the current thread.

In modern Java, I'd use **ExecutorService** for thread pool management instead of creating threads manually, and **Virtual Threads** (Java 21) for massive concurrency.

---

## 8. What is synchronization? Why is it needed?

**Answer:**
Synchronization prevents **race conditions** — when multiple threads access shared data simultaneously and the result depends on execution order.

```java
// Without sync: count++ is NOT atomic (read → increment → write)
// Two threads can read the same value and both write the same result

public synchronized void increment() {  // Only one thread at a time
    count++;
}
```

Under the hood, every Java object has a **monitor (intrinsic lock)**. When a thread enters a `synchronized` block, it acquires the lock; other threads wait. When it exits, the lock is released.

Alternatives: `AtomicInteger` (lock-free, CAS-based), `ReentrantLock` (more flexible than synchronized), `volatile` keyword (ensures visibility but not atomicity).

---

## 9. What is a deadlock? How do you prevent it?

**Answer:**
Deadlock occurs when two or more threads are **waiting for each other's locks** and none can proceed.

Example: Thread A holds Lock 1 and waits for Lock 2. Thread B holds Lock 2 and waits for Lock 1. Both wait forever.

Prevention strategies:
1. **Lock ordering** — always acquire locks in the same global order
2. **tryLock with timeout** — use `ReentrantLock.tryLock(timeout)` so threads give up instead of waiting forever
3. **Avoid nested locks** — minimize the number of locks held simultaneously
4. **Use higher-level concurrency utilities** — `java.util.concurrent` classes like `ConcurrentHashMap`, `BlockingQueue`

---

## 10. Explain the Producer-Consumer problem.

**Answer:**
It's a classic concurrency problem: a producer generates data and puts it in a shared buffer, while a consumer takes data from the buffer.

The challenges:
- Producer must wait if the buffer is full
- Consumer must wait if the buffer is empty
- Both must access the buffer in a thread-safe way

Java's solution is `BlockingQueue`:
```java
BlockingQueue<Integer> queue = new LinkedBlockingQueue<>(10);
// Producer: queue.put(item);   // Blocks if full
// Consumer: queue.take();       // Blocks if empty
```

`BlockingQueue` handles all the synchronization internally.

---

## 11. What is the Reflection API?

**Answer:**
Reflection lets me inspect and manipulate classes, methods, and fields **at runtime**. I can get a class's metadata, invoke private methods, access private fields, and create instances dynamically.

```java
Class<?> clazz = Class.forName("com.example.Employee");
Method m = clazz.getDeclaredMethod("getName");
m.setAccessible(true);  // Access private method
m.invoke(employeeInstance);
```

It's used by frameworks like Spring (dependency injection), Hibernate (ORM mapping), and JUnit (test discovery). However, it's slow (bypasses JIT optimizations), breaks encapsulation, and has no compile-time safety, so I'd only use it when truly necessary.

---

## 12. What is the Java Date/Time API (java.time)?

**Answer:**
Introduced in Java 8, `java.time` replaces the flawed `java.util.Date` and `Calendar` classes. The key classes are:

- `LocalDate` — date only (no time, no timezone)
- `LocalTime` — time only
- `LocalDateTime` — date + time (no timezone)
- `ZonedDateTime` — date + time + timezone
- `Instant` — machine timestamp (epoch seconds)
- `Duration` — time-based amount (hours, minutes, seconds)
- `Period` — date-based amount (years, months, days)

All classes are **immutable** and **thread-safe**. Operations like `plusDays()` or `minusHours()` return new instances.

---

## 13. What is the difference between parallel streams and threads?

**Answer:**
Parallel streams use the **ForkJoinPool** (shared common pool) to split work across multiple threads automatically. They're simpler to use but give less control.

When to use parallel streams:
- Large dataset (>10,000 elements)
- CPU-intensive operations (computation, not I/O)
- Stateless, independent operations

When NOT to:
- Small datasets (overhead exceeds benefit)
- I/O-bound tasks (threads would block)
- Order-sensitive operations
- Shared mutable state

For fine-grained control over threading, I'd use `ExecutorService` or virtual threads instead.

---

*Back to: [06-Java-Advanced.md](../06-Java-Advanced.md)*
