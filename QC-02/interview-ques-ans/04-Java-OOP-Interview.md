# Java OOP — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview.

---

## 1. What are the four pillars of OOP?

**Answer:**
The four pillars are:

1. **Encapsulation** — bundling data (fields) and methods that operate on that data into a single class, and restricting direct access using access modifiers. I make fields private and expose public getters/setters. This protects internal state and lets me add validation.

2. **Inheritance** — a class can inherit fields and methods from another class using `extends`. It promotes code reuse. For example, `Dog extends Animal` — Dog gets all of Animal's behavior plus its own.

3. **Polymorphism** — "many forms." The same method call behaves differently depending on the actual object type. A `Animal` reference can point to a `Dog` or `Cat`, and calling `speak()` gives different results. This is achieved through method overriding (runtime polymorphism) and method overloading (compile-time polymorphism).

4. **Abstraction** — hiding complex implementation details and showing only what's necessary. Achieved through abstract classes and interfaces. For example, I call `list.add()` without knowing if it's an ArrayList or LinkedList internally.

---

## 2. What is the difference between an abstract class and an interface?

**Answer:**
| Feature | Abstract Class | Interface |
|---|---|---|
| Methods | Can have both abstract and concrete | Abstract by default (Java 8+: can have default and static) |
| Fields | Can have instance variables | Only public static final constants |
| Constructors | Yes | No |
| Inheritance | Single (`extends` one class) | Multiple (`implements` many interfaces) |
| Access modifiers | Any | Public (implicitly) |

**When to use which:**
- Use an **abstract class** when classes share common state and behavior (IS-A relationship) — like `Vehicle` with fields `speed` and `fuel`.
- Use an **interface** when unrelated classes share capabilities (CAN-DO relationship) — like `Serializable`, `Comparable`, `Flyable`.

Since Java 8 added default methods to interfaces, the line has blurred, but the key difference remains: an abstract class can have constructors and mutable state, and you can only extend one.

---

## 3. What is polymorphism? Explain runtime vs compile-time.

**Answer:**
Polymorphism means "many forms" — the same method name behaves differently based on the object or parameters.

**Compile-time polymorphism (Method Overloading):**
Same method name, different parameters, resolved at compile time.
```java
int add(int a, int b) { return a + b; }
double add(double a, double b) { return a + b; }
```

**Runtime polymorphism (Method Overriding):**
A subclass provides its own implementation of a parent method, resolved at runtime via the actual object type.
```java
Animal animal = new Dog();  // Reference type: Animal, Object type: Dog
animal.speak();              // Calls Dog's speak(), not Animal's!
```

Under the hood, the JVM uses a **virtual method table (vtable)** — each class has a table mapping method signatures to actual implementations. At runtime, the JVM looks up the vtable of the actual object type.

---

## 4. What is the difference between method overloading and overriding?

**Answer:**
| Aspect | Overloading | Overriding |
|---|---|---|
| Where | Same class | Subclass overrides parent |
| Parameters | Must differ | Must be identical |
| Return type | Can differ | Same or covariant (subtype) |
| Access | Can differ | Cannot be more restrictive |
| Resolution | Compile time | Runtime |
| Static methods | Can be overloaded | Cannot be overridden (hidden) |

Key rule for overriding: the subclass method must have the **same signature**, can have a **covariant return type** (a subtype of the original return type), and **cannot throw broader checked exceptions**.

---

## 5. What is encapsulation? Give a real-world example.

**Answer:**
Encapsulation is about **hiding internal state** and controlling access through methods. I make fields `private` and provide `public` getters and setters. This way, I can add validation, logging, or computed values without changing the external API.

```java
public class BankAccount {
    private double balance;  // Hidden!

    public void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("Amount must be positive");
        this.balance += amount;  // Validation before modifying state
    }

    public double getBalance() { return balance; }  // Read-only access
}
```

Real-world analogy: an ATM. You interact with buttons and a screen (public interface), but you can't directly access the cash inside (private state). The ATM controls how you deposit and withdraw.

---

## 6. Can you explain the equals() and hashCode() contract?

**Answer:**
The contract states:
1. If `a.equals(b)` is true, then `a.hashCode() == b.hashCode()` MUST be true.
2. If hash codes are equal, `equals()` may or may not be true (hash collisions are allowed).
3. If `a.equals(b)` is false, hash codes CAN still be equal.

**Why this matters:** HashMap uses `hashCode()` to find the bucket, then `equals()` to find the exact entry within that bucket. If I override `equals()` without overriding `hashCode()`, two logically equal objects could end up in different buckets, and HashMap lookups would fail.

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Employee that = (Employee) o;
    return id == that.id && Objects.equals(name, that.name);
}

@Override
public int hashCode() {
    return Objects.hash(id, name);
}
```

**Rule: Always override both or neither.**

---

## 7. How does Garbage Collection work in Java?

**Answer:**
The Garbage Collector automatically frees memory occupied by objects that are **no longer reachable** (no references pointing to them).

Java uses **Generational GC** — the heap is divided into:
- **Young Generation** (Eden + Survivor spaces) — new objects go here. Minor GC runs frequently and is fast.
- **Old Generation** — objects that survive multiple GC cycles are promoted here. Major GC runs less frequently and is slower.
- **Metaspace** — stores class metadata (replaced PermGen in Java 8).

The process:
1. New objects are allocated in Eden.
2. When Eden fills up, **Minor GC** runs — live objects move to Survivor space, dead objects are freed.
3. Objects surviving multiple minor GCs are promoted to Old Generation.
4. When Old Gen fills up, **Major GC** (or Full GC) runs — which is slower and can cause pauses.

I can't force GC, only suggest it with `System.gc()`, which the JVM may ignore.

---

## 8. What are checked vs unchecked exceptions?

**Answer:**
- **Checked exceptions** (compile-time) — the compiler forces me to handle them with try-catch or declare them with `throws`. Examples: `IOException`, `SQLException`, `FileNotFoundException`. These represent recoverable conditions.

- **Unchecked exceptions** (runtime) — subclasses of `RuntimeException`. The compiler doesn't force handling. Examples: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `IllegalArgumentException`. These usually indicate programming bugs.

- **Errors** — subclasses of `Error`. Serious problems I shouldn't try to handle. Examples: `OutOfMemoryError`, `StackOverflowError`.

In practice, I use checked exceptions for business-level recoverable errors (like "file not found") and unchecked exceptions for programming mistakes (like "null input").

---

## 9. Explain try-with-resources.

**Answer:**
Try-with-resources (Java 7+) automatically closes resources that implement `AutoCloseable` when the try block finishes — even if an exception occurs.

```java
// Before Java 7 — manual close in finally
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("file.txt"));
    // use reader
} finally {
    if (reader != null) reader.close();
}

// Java 7+ — automatic close
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    // use reader
}  // reader is automatically closed here!
```

It's cleaner, less error-prone, and handles the case where both the try block and the close method throw exceptions (the close exception is **suppressed** and attached to the primary exception).

---

## 10. What is the difference between `this` and `super`?

**Answer:**
- **`this`** refers to the **current object**. Used to differentiate between instance variables and parameters with the same name, and to call another constructor in the same class (`this(args)`).
- **`super`** refers to the **parent class**. Used to call parent's constructor (`super(args)` — must be first line in constructor), and to call parent's overridden method (`super.method()`).

---

## 11. What are Sealed classes? (Java 17+)

**Answer:**
Sealed classes restrict which classes can extend them using the `permits` keyword:

```java
public sealed class Shape permits Circle, Rectangle, Triangle { }
```

Only `Circle`, `Rectangle`, and `Triangle` can extend `Shape` — no one else can. The permitted subclasses must be `final`, `sealed`, or `non-sealed`.

This is useful for creating closed hierarchies where I know all possible subtypes at compile time, enabling exhaustive pattern matching in switch expressions.

---

## 12. What are Records? (Java 16+)

**Answer:**
Records are a concise way to create **immutable data carrier classes**. The compiler auto-generates the constructor, getters, `equals()`, `hashCode()`, and `toString()`.

```java
public record Employee(String name, int age) { }
// That's it! No boilerplate.

Employee emp = new Employee("Alice", 30);
emp.name();    // "Alice" (getter — no 'get' prefix)
emp.age();     // 30
```

Records are ideal for DTOs, value objects, and any class whose purpose is simply to hold data. They're implicitly final and can implement interfaces but cannot extend other classes.

---

## 13. What are Virtual Threads? (Java 21)

**Answer:**
Virtual threads are lightweight threads managed by the JVM, not the OS. Traditional platform threads are expensive — each one maps to an OS thread and uses ~1MB of stack. Virtual threads can exist in **millions** because they're just Java objects scheduled on a small pool of carrier (platform) threads.

```java
Thread.startVirtualThread(() -> {
    // This runs on a virtual thread — very cheap!
});
```

They're ideal for I/O-bound tasks (like handling HTTP requests) where threads spend most of their time waiting. Instead of managing thread pools, I can create one virtual thread per request.

---

*Back to: [04-Java-OOP.md](../04-Java-OOP.md)*
