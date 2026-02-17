# Java Basics — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview.

---

## 1. What is Java? Why is it platform-independent?

**Answer:**
Java is an **object-oriented, platform-independent** programming language. The key to platform independence is the **JVM (Java Virtual Machine)**. When I compile a `.java` file, the compiler produces **bytecode** (`.class` file), NOT native machine code. This bytecode runs on any JVM, regardless of the underlying OS.

So the principle is **"Write Once, Run Anywhere"** — I write my code once, compile it to bytecode, and that bytecode runs on Windows, Linux, or Mac — as long as there's a JVM installed.

---

## 2. Explain JDK, JRE, and JVM.

**Answer:**
Think of them as nesting layers:

- **JVM** (Java Virtual Machine) — the engine that runs bytecode. It handles class loading, memory management, garbage collection, and executing the program. It's an abstract specification — each OS has its own JVM implementation.

- **JRE** (Java Runtime Environment) — JVM + the standard class libraries (like `java.lang`, `java.util`). It's what you need to **run** Java programs.

- **JDK** (Java Development Kit) — JRE + development tools like the compiler (`javac`), debugger, and other utilities. It's what you need to **develop** Java programs.

In short: JDK ⊃ JRE ⊃ JVM.

---

## 3. How does the JVM work internally?

**Answer:**
When I run a Java program, the JVM goes through three major phases:

1. **Class Loader** — loads `.class` files into memory. There's a hierarchy: Bootstrap ClassLoader (loads core Java classes), Extension ClassLoader, and Application ClassLoader (loads my classes).

2. **Runtime Data Areas** — memory is divided into:
   - **Method Area** — stores class metadata, static variables, and the constant pool
   - **Heap** — stores all objects (shared across threads)
   - **Stack** — one per thread, stores method frames with local variables and partial results
   - **PC Register** — tracks the current instruction per thread
   - **Native Method Stack** — for native (C/C++) method calls

3. **Execution Engine** — executes bytecode using:
   - **Interpreter** — reads and executes bytecode line by line (slow)
   - **JIT Compiler** — identifies "hot" methods (frequently called) and compiles them to native machine code for faster execution
   - **Garbage Collector** — automatically frees memory of unreachable objects

---

## 4. What are the 8 primitive data types in Java?

**Answer:**
Java has 8 primitives that store values directly on the stack:

| Type | Size | Default | Range |
|---|---|---|---|
| `byte` | 1 byte | 0 | -128 to 127 |
| `short` | 2 bytes | 0 | -32,768 to 32,767 |
| `int` | 4 bytes | 0 | ±2.1 billion |
| `long` | 8 bytes | 0L | ±9.2 quintillion |
| `float` | 4 bytes | 0.0f | ~7 decimal digits precision |
| `double` | 8 bytes | 0.0d | ~15 decimal digits precision |
| `char` | 2 bytes | '\u0000' | Unicode characters |
| `boolean` | ~1 bit | false | true/false |

Everything else in Java is a **reference type** — the variable stores a reference (pointer) to an object on the heap.

---

## 5. What is the difference between Stack and Heap memory?

**Answer:**
- **Stack** — stores method call frames, local variables, and references. Each thread has its own stack. It's LIFO (Last In, First Out). When a method finishes, its frame is popped off. Stack is fast but small.

- **Heap** — stores all objects and their instance variables. Shared across all threads. Managed by the Garbage Collector. Heap is large but slower than stack.

When I write `String name = new String("Hello")`:
- The reference `name` is on the **stack**
- The actual String object `"Hello"` is on the **heap**

---

## 6. Is Java pass-by-value or pass-by-reference?

**Answer:**
Java is **always pass-by-value**. But the confusion comes from what's being passed:

- For **primitives**, the actual value is copied. Changing it inside the method doesn't affect the original.
- For **objects**, the reference (memory address) is copied. So inside the method, I can modify the object's state (because both references point to the same object), but I can NOT make the original reference point to a different object.

```java
void modify(int[] arr) {
    arr[0] = 99;        // ✅ Modifies original — same object in heap
    arr = new int[]{1}; // ❌ Doesn't affect original reference — just changes the local copy
}
```

---

## 7. What is String immutability? Why are Strings immutable?

**Answer:**
Once a String object is created, its value **cannot be changed**. Any operation that seems to modify it actually creates a **new String object**.

```java
String s = "Hello";
s.concat(" World");  // Creates a new String, but 's' still points to "Hello"
s = s.concat(" World"); // Now 's' points to the new "Hello World" object
```

Why immutable?
1. **String Pool** — Java caches string literals in a special memory pool. If strings were mutable, changing one would affect all variables sharing that literal.
2. **Security** — strings are used for passwords, URLs, class names. Immutability prevents tampering.
3. **Thread safety** — immutable objects are inherently thread-safe (no synchronization needed).
4. **Hashing** — strings are commonly used as HashMap keys. Immutability guarantees the hash code won't change.

For frequent string modifications, I'd use `StringBuilder` (not thread-safe, faster) or `StringBuffer` (thread-safe, slower).

---

## 8. What is the String Pool?

**Answer:**
The String Pool is a special area in the heap where Java caches **string literals**. When I write `String s = "Hello"`, Java first checks the pool — if "Hello" already exists, it returns the existing reference instead of creating a new object.

```java
String a = "Hello";        // Created in pool
String b = "Hello";        // Reuses same object from pool
System.out.println(a == b); // true — same reference!

String c = new String("Hello"); // Creates NEW object on heap (NOT in pool)
System.out.println(a == c);      // false — different references!
System.out.println(a.equals(c)); // true — same content!
```

This is why we always use `.equals()` to compare strings, not `==`.

---

## 9. What are Wrapper classes? What is autoboxing?

**Answer:**
Wrapper classes wrap primitive types into objects: `int → Integer`, `double → Double`, `boolean → Boolean`, etc. They're needed because Java collections can only hold objects, not primitives.

**Autoboxing** is the automatic conversion from primitive to wrapper:
```java
Integer x = 5;  // Autoboxing: int → Integer (compiler does Integer.valueOf(5))
```

**Unboxing** is the reverse:
```java
int y = x;  // Unboxing: Integer → int (compiler does x.intValue())
```

One gotcha: `Integer.valueOf()` caches values from -128 to 127, so:
```java
Integer a = 127, b = 127;
a == b;  // true (cached)

Integer c = 128, d = 128;
c == d;  // false! (not cached, different objects)
```

---

## 10. What is the difference between == and .equals()?

**Answer:**
- `==` compares **references** (memory addresses) — are these the exact same object?
- `.equals()` compares **content/values** — do these objects have the same data?

For primitives, `==` compares values (since there are no references). For objects, always use `.equals()` for logical equality. I should always override `equals()` (and `hashCode()`) in my custom classes if I want meaningful content comparison.

---

## 11. What is type casting in Java?

**Answer:**
**Widening** (implicit) — smaller to larger type, no data loss:
```java
int i = 100;
long l = i;  // Automatic
```

**Narrowing** (explicit) — larger to smaller type, possible data loss:
```java
double d = 9.78;
int i = (int) d;  // Manual cast, i = 9 (fractional part lost)
```

For objects, I can upcast (child → parent) automatically, and downcast (parent → child) with an explicit cast, but only if the object is actually an instance of the target type — otherwise I get `ClassCastException`. I should always check with `instanceof` before downcasting.

---

## 12. What is the difference between final, finally, and finalize?

**Answer:**
- **final** — a keyword to make things constant:
  - `final` variable → value can't change (constant)
  - `final` method → can't be overridden
  - `final` class → can't be extended (e.g., String)

- **finally** — a block that always executes after try/catch, regardless of whether an exception occurred. Used for cleanup like closing resources.

- **finalize()** — a method from Object class called by the Garbage Collector before destroying an object. It's deprecated since Java 9 — use try-with-resources instead.

---

*Back to: [03-Java-Basics.md](../03-Java-Basics.md)*
