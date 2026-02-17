# Java Advanced — Lambdas, Streams, Multithreading & More

## Table of Contents
1. [Lambda Expressions](#1-lambda-expressions)
2. [Functional Interfaces](#2-functional-interfaces)
3. [Method References](#3-method-references)
4. [Optional Class](#4-optional-class)
5. [Stream API](#5-stream-api)
6. [Date and Time API (java.time)](#6-date-and-time-api)
7. [File I/O — Reading and Writing Files](#7-file-io)
8. [Reflection API](#8-reflection-api)
9. [Multithreading](#9-multithreading)
10. [Reactive Programming](#10-reactive-programming)

---

## 1. Lambda Expressions

A **lambda** is an anonymous function — a short way to write a method without declaring a class.

### Before Lambdas (Java 7 and earlier)

```java
// To sort a list, you had to create an anonymous inner class:
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.compareTo(b);
    }
});
// That's 5 lines for one line of logic!
```

### With Lambdas (Java 8+)

```java
// Same thing in ONE line:
Collections.sort(names, (a, b) -> a.compareTo(b));

// Even shorter with method reference:
Collections.sort(names, String::compareTo);
```

### Lambda Syntax

```java
// Full syntax:
(parameters) -> { statements; return value; }

// Simplified rules:
// 1. One parameter → no parentheses needed
//    x -> x * 2

// 2. No parameters → empty parentheses
//    () -> System.out.println("Hello")

// 3. Single expression → no braces or return needed
//    (a, b) -> a + b

// 4. Multiple statements → braces and explicit return needed
//    (a, b) -> { int sum = a + b; return sum; }

// Examples:
Runnable r = () -> System.out.println("Hello!");

Comparator<String> comp = (a, b) -> a.length() - b.length();

Function<String, Integer> strlen = s -> s.length();

BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

Predicate<String> isEmpty = s -> s.isEmpty();

Consumer<String> printer = s -> System.out.println(s);

Supplier<Double> random = () -> Math.random();
```

### How Lambdas Work Under the Hood

```
Lambda expressions are NOT anonymous inner classes!

Under the hood:
1. Compiler generates a private static method in the same class
2. Uses invokedynamic bytecode instruction (added in Java 7)
3. At runtime, LambdaMetafactory creates an implementation of the functional interface
4. This is FASTER than anonymous inner classes (no extra .class file, no object allocation)

Anonymous inner class:         Lambda:
- Creates ClassName$1.class    - No extra class file
- Creates an object on heap   - May be cached/reused
- Slower startup              - Faster via invokedynamic
```

### Effectively Final Variables

```java
String prefix = "Hello";  // Effectively final (never reassigned)

// Lambda can capture local variables, but they must be effectively final!
Consumer<String> greeter = name -> System.out.println(prefix + " " + name);

// prefix = "Hi";  // ❌ COMPILE ERROR if you try to reassign!
// Because the lambda captured 'prefix' — it can't change!

// Why? Lambdas capture a COPY of the variable. If the variable changed later,
// the lambda would have a stale copy → confusing behavior. Java prevents this.
```

---

## 2. Functional Interfaces

A **functional interface** has exactly **one abstract method**. Lambdas implement functional interfaces.

```java
@FunctionalInterface   // Optional but recommended — compiler checks it has exactly 1 abstract method
public interface MyFunction {
    int apply(int x);
    
    // Can still have default and static methods — they don't count
    default void print() { System.out.println("default"); }
    static void helper() { System.out.println("static"); }
}

MyFunction doubler = x -> x * 2;
System.out.println(doubler.apply(5));  // 10
```

### Built-in Functional Interfaces (java.util.function)

```java
// Predicate<T> — takes T, returns boolean
Predicate<String> isLong = s -> s.length() > 5;
isLong.test("Hello");        // false
isLong.test("Hello World");  // true

// Chaining predicates
Predicate<String> startsWithH = s -> s.startsWith("H");
Predicate<String> combined = isLong.and(startsWithH);   // Both must be true
Predicate<String> either = isLong.or(startsWithH);       // At least one
Predicate<String> notLong = isLong.negate();              // Flip

// Function<T, R> — takes T, returns R
Function<String, Integer> length = String::length;
length.apply("Hello");  // 5

// Chaining functions
Function<Integer, Integer> doubleIt = x -> x * 2;
Function<Integer, Integer> addTen = x -> x + 10;
Function<Integer, Integer> doubleThenAdd = doubleIt.andThen(addTen);   // double, then add 10
Function<Integer, Integer> addThenDouble = doubleIt.compose(addTen);   // add 10, then double
doubleThenAdd.apply(5);  // (5*2) + 10 = 20
addThenDouble.apply(5);  // (5+10) * 2 = 30

// Consumer<T> — takes T, returns nothing (side effects)
Consumer<String> printer = System.out::println;
printer.accept("Hello!");

// Supplier<T> — takes nothing, returns T
Supplier<Double> random = Math::random;
double r = random.get();

// BiFunction<T, U, R> — takes T and U, returns R
BiFunction<String, String, String> concat = (a, b) -> a + b;
concat.apply("Hello", " World");  // "Hello World"

// UnaryOperator<T> — takes T, returns T (same type)
UnaryOperator<String> toUpper = String::toUpperCase;
toUpper.apply("hello");  // "HELLO"

// BinaryOperator<T> — takes T and T, returns T
BinaryOperator<Integer> add = Integer::sum;
add.apply(3, 4);  // 7
```

### Summary Table

| Interface | Input | Output | Method |
|---|---|---|---|
| `Predicate<T>` | T | boolean | test(T) |
| `Function<T,R>` | T | R | apply(T) |
| `Consumer<T>` | T | void | accept(T) |
| `Supplier<T>` | none | T | get() |
| `BiFunction<T,U,R>` | T, U | R | apply(T,U) |
| `BiPredicate<T,U>` | T, U | boolean | test(T,U) |
| `BiConsumer<T,U>` | T, U | void | accept(T,U) |
| `UnaryOperator<T>` | T | T | apply(T) |
| `BinaryOperator<T>` | T, T | T | apply(T,T) |

---

## 3. Method References

A **method reference** is a shorthand for a lambda that just calls an existing method.

```java
// Four types of method references:

// 1. Static method reference: ClassName::staticMethod
Function<String, Integer> parse = Integer::parseInt;   // same as: s -> Integer.parseInt(s)
parse.apply("42");  // 42

// 2. Instance method reference (bound): instance::method
String greeting = "Hello, World!";
Supplier<Integer> len = greeting::length;               // same as: () -> greeting.length()
len.get();  // 13

// 3. Instance method reference (unbound): ClassName::method
// The first parameter becomes the object the method is called on
Function<String, String> upper = String::toUpperCase;   // same as: s -> s.toUpperCase()
upper.apply("hello");  // "HELLO"

BiPredicate<String, String> starts = String::startsWith;  // same as: (s, prefix) -> s.startsWith(prefix)
starts.test("Hello", "He");  // true

// 4. Constructor reference: ClassName::new
Supplier<ArrayList<String>> listMaker = ArrayList::new;   // same as: () -> new ArrayList<>()
Function<String, Employee> empMaker = Employee::new;      // same as: name -> new Employee(name)
```

### When to Use Method References

```java
// Lambda:                        Method Reference:
s -> System.out.println(s)   →   System.out::println
s -> s.toUpperCase()          →   String::toUpperCase
s -> Integer.parseInt(s)      →   Integer::parseInt
() -> new ArrayList<>()       →   ArrayList::new
(a, b) -> a.compareTo(b)     →   String::compareTo

// Rule of thumb: If your lambda JUST calls a method and passes parameters through,
// use a method reference. If you transform or combine, use a lambda.

// ✅ Use method reference:
names.forEach(System.out::println);

// ✅ Use lambda (can't simplify further):
names.forEach(name -> System.out.println("Name: " + name));
```

---

## 4. Optional Class

**Optional** is a container that may or may not hold a value. It eliminates NullPointerException!

```java
import java.util.Optional;

// Creating an Optional
Optional<String> opt1 = Optional.of("Hello");          // Must NOT be null
Optional<String> opt2 = Optional.ofNullable(null);     // Can be null
Optional<String> opt3 = Optional.empty();               // Explicitly empty

// Checking and getting the value
opt1.isPresent();    // true
opt2.isPresent();    // false
opt2.isEmpty();      // true (Java 11+)

opt1.get();          // "Hello"
// opt2.get();       // ❌ NoSuchElementException! Always check first!

// Safe ways to get value
String val1 = opt1.orElse("default");                    // "Hello"
String val2 = opt2.orElse("default");                    // "default"
String val3 = opt2.orElseGet(() -> "computed default");  // Lazy computation
String val4 = opt2.orElseThrow(() -> new RuntimeException("Missing!"));  // Throw if empty

// Conditional execution
opt1.ifPresent(value -> System.out.println(value));         // Prints "Hello"
opt2.ifPresent(value -> System.out.println(value));         // Does nothing

// ifPresentOrElse (Java 9+)
opt2.ifPresentOrElse(
    value -> System.out.println(value),
    () -> System.out.println("No value!")
);

// Transforming
Optional<Integer> length = opt1.map(String::length);        // Optional[5]
Optional<Integer> empty = opt2.map(String::length);          // Optional.empty
Optional<String> upper = opt1.map(String::toUpperCase);      // Optional["HELLO"]

// flatMap — when the function itself returns Optional
Optional<String> result = opt1.flatMap(this::findUser);      // Avoids Optional<Optional<String>>

// Filtering
Optional<String> filtered = opt1.filter(s -> s.length() > 3);  // Optional["Hello"]
Optional<String> filtered2 = opt1.filter(s -> s.length() > 10); // Optional.empty

// Stream (Java 9+)
opt1.stream().forEach(System.out::println);  // Stream of 0 or 1 elements
```

### Optional Best Practices

```java
// ✅ DO: Use as method return type
public Optional<Employee> findById(int id) {
    Employee emp = database.find(id);
    return Optional.ofNullable(emp);
}

// ❌ DON'T: Use as method parameter
public void process(Optional<String> name) { }  // Bad! Just use @Nullable String name

// ❌ DON'T: Use as field
class User {
    Optional<String> middleName;  // Bad! Just use @Nullable String middleName
}

// ❌ DON'T: Use Optional.get() without checking
Optional<String> opt = getOptional();
// opt.get();  // ❌ Use orElse, orElseGet, orElseThrow instead!

// ✅ DO: Chain operations
String result = findById(1)
    .map(Employee::getName)
    .map(String::toUpperCase)
    .orElse("UNKNOWN");
```

---

## 5. Stream API

**Streams** let you process collections of data in a **declarative** way (what, not how) with a pipeline of operations.

```java
// Without streams (imperative):
List<String> names = List.of("Alice", "Bob", "Charlie", "David", "Anna");
List<String> result = new ArrayList<>();
for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name.toUpperCase());
    }
}
Collections.sort(result);

// With streams (declarative):
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))     // Keep names starting with A
    .map(String::toUpperCase)                  // Transform to uppercase
    .sorted()                                  // Sort
    .collect(Collectors.toList());             // Collect into a List
// Result: [ALICE, ANNA]
```

### Stream Pipeline Structure

```
Source → Intermediate Operations → Terminal Operation → Result
         (lazy, return Stream)    (triggers execution)

names.stream()          ← Source
    .filter(...)        ← Intermediate (lazy!)
    .map(...)           ← Intermediate (lazy!)
    .sorted()           ← Intermediate (lazy!)
    .collect(...)       ← Terminal (TRIGGERS everything!)

IMPORTANT: Intermediate operations are LAZY — they do NOTHING until a terminal operation is called!
```

### Creating Streams

```java
// From collection
List<String> list = List.of("a", "b", "c");
Stream<String> s1 = list.stream();

// From array
String[] arr = {"a", "b", "c"};
Stream<String> s2 = Arrays.stream(arr);

// From values
Stream<String> s3 = Stream.of("a", "b", "c");

// Infinite streams
Stream<Integer> s4 = Stream.iterate(0, n -> n + 2);    // 0, 2, 4, 6, ...
Stream<Double> s5 = Stream.generate(Math::random);      // random, random, ...
// MUST use limit() or the stream never ends!
s4.limit(5).forEach(System.out::println);  // 0, 2, 4, 6, 8

// Primitive streams (avoid boxing overhead)
IntStream ints = IntStream.range(1, 6);        // 1, 2, 3, 4, 5
IntStream ints2 = IntStream.rangeClosed(1, 5); // 1, 2, 3, 4, 5
LongStream longs = LongStream.of(1L, 2L, 3L);
DoubleStream doubles = DoubleStream.of(1.0, 2.0, 3.0);
```

### Intermediate Operations

```java
List<Employee> employees = getEmployees();

// filter — keep elements matching a condition
employees.stream()
    .filter(e -> e.getSalary() > 50000)

// map — transform each element
employees.stream()
    .map(Employee::getName)         // Stream<String> of names
    .map(String::toUpperCase)       // Stream<String> of uppercase names

// flatMap — flatten nested structures
List<List<String>> nested = List.of(List.of("a", "b"), List.of("c", "d"));
nested.stream()
    .flatMap(Collection::stream)    // Stream<String>: "a", "b", "c", "d"

// distinct — remove duplicates
Stream.of(1, 2, 2, 3, 3, 3).distinct()  // 1, 2, 3

// sorted — sort elements
employees.stream().sorted(Comparator.comparing(Employee::getSalary))

// peek — look at elements without changing them (for debugging)
employees.stream()
    .filter(e -> e.getSalary() > 50000)
    .peek(e -> System.out.println("After filter: " + e))
    .map(Employee::getName)
    .collect(Collectors.toList());

// limit / skip
Stream.of(1, 2, 3, 4, 5).limit(3)   // 1, 2, 3
Stream.of(1, 2, 3, 4, 5).skip(2)    // 3, 4, 5

// takeWhile / dropWhile (Java 9+)
Stream.of(1, 2, 3, 4, 5, 1).takeWhile(n -> n < 4)  // 1, 2, 3 (stops at first failure)
Stream.of(1, 2, 3, 4, 5, 1).dropWhile(n -> n < 4)  // 4, 5, 1 (drops until first failure)
```

### Terminal Operations

```java
// forEach — perform action on each element (returns void)
names.stream().forEach(System.out::println);

// collect — collect into a collection
List<String> list = stream.collect(Collectors.toList());
Set<String> set = stream.collect(Collectors.toSet());
Map<String, Integer> map = stream.collect(Collectors.toMap(
    Employee::getName,
    Employee::getSalary
));

// toList (Java 16+) — shorthand for unmodifiable list
List<String> list2 = stream.toList();

// count, min, max
long count = stream.count();
Optional<Integer> max = stream.max(Comparator.naturalOrder());
Optional<Integer> min = stream.min(Comparator.naturalOrder());

// reduce — combine all elements into one value
int sum = IntStream.of(1, 2, 3, 4, 5).reduce(0, Integer::sum);  // 15
Optional<Integer> product = Stream.of(1, 2, 3, 4).reduce((a, b) -> a * b);  // 24

// findFirst / findAny
Optional<String> first = names.stream()
    .filter(n -> n.startsWith("A"))
    .findFirst();   // First match

// anyMatch / allMatch / noneMatch
boolean hasAlice = names.stream().anyMatch(n -> n.equals("Alice"));   // true if any match
boolean allLong = names.stream().allMatch(n -> n.length() > 3);       // true if ALL match
boolean noneEmpty = names.stream().noneMatch(String::isEmpty);         // true if NONE match
```

### Collectors — Advanced

```java
import java.util.stream.Collectors;

List<Employee> employees = getEmployees();

// Group by
Map<String, List<Employee>> byDept = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment));
// {"IT": [alice, bob], "Sales": [charlie, david]}

// Group by with counting
Map<String, Long> countByDept = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment, Collectors.counting()));
// {"IT": 2, "Sales": 2}

// Group by with average salary
Map<String, Double> avgSalByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.averagingDouble(Employee::getSalary)
    ));

// Partitioning (split into true/false groups)
Map<Boolean, List<Employee>> partitioned = employees.stream()
    .collect(Collectors.partitioningBy(e -> e.getSalary() > 60000));
// {true: [high-earners], false: [low-earners]}

// Joining strings
String names = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.joining(", ", "[", "]"));   // [Alice, Bob, Charlie]

// Summarizing statistics
IntSummaryStatistics stats = employees.stream()
    .collect(Collectors.summarizingInt(Employee::getSalary));
stats.getCount();    // 4
stats.getSum();      // 270000
stats.getAverage();  // 67500.0
stats.getMin();      // 50000
stats.getMax();      // 90000
```

### Parallel Streams

```java
// Process elements in parallel using multiple threads
long count = employees.parallelStream()
    .filter(e -> e.getSalary() > 50000)
    .count();

// Or convert existing stream to parallel
names.stream()
    .parallel()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

// ⚠️ Parallel streams use ForkJoinPool.commonPool() (shared across the app)
// ⚠️ Not always faster! Overhead of splitting + merging can be more than benefit
// Use when: large dataset (>10,000 elements) + CPU-intensive operations
// Don't use when: small dataset, I/O operations, order matters
```

---

## 6. Date and Time API

Java 8 introduced `java.time` — immutable, thread-safe date/time classes.

```java
import java.time.*;
import java.time.format.*;
import java.time.temporal.*;

// LocalDate — date only (no time, no timezone)
LocalDate today = LocalDate.now();                    // 2026-02-11
LocalDate birthday = LocalDate.of(1995, 6, 15);      // 1995-06-15
LocalDate parsed = LocalDate.parse("2025-03-15");     // From string

// LocalTime — time only (no date, no timezone)
LocalTime now = LocalTime.now();                       // 14:30:45.123456789
LocalTime lunch = LocalTime.of(12, 30);                // 12:30
LocalTime precise = LocalTime.of(14, 30, 15, 123456); // 14:30:15.000123456

// LocalDateTime — date + time (no timezone)
LocalDateTime dateTime = LocalDateTime.now();
LocalDateTime specific = LocalDateTime.of(2025, 3, 15, 14, 30);

// ZonedDateTime — date + time + timezone
ZonedDateTime zdt = ZonedDateTime.now();
ZonedDateTime tokyo = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
ZonedDateTime ny = ZonedDateTime.now(ZoneId.of("America/New_York"));

// Instant — machine timestamp (seconds + nanoseconds since epoch)
Instant instant = Instant.now();
long epochSecond = instant.getEpochSecond();

// Duration — time-based amount (hours, minutes, seconds)
Duration twoHours = Duration.ofHours(2);
Duration between = Duration.between(time1, time2);

// Period — date-based amount (years, months, days)
Period sixMonths = Period.ofMonths(6);
Period between2 = Period.between(date1, date2);

// Manipulating dates (all immutable — returns NEW object!)
LocalDate tomorrow = today.plusDays(1);
LocalDate lastYear = today.minusYears(1);
LocalDate nextFriday = today.with(TemporalAdjusters.next(DayOfWeek.FRIDAY));

// Formatting
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");
String formatted = dateTime.format(formatter);   // "11/02/2026 14:30"
LocalDateTime parsed2 = LocalDateTime.parse("15/03/2025 10:00", formatter);

// Comparing
today.isBefore(tomorrow);  // true
today.isAfter(yesterday);  // true
today.isEqual(today);      // true

// Extracting parts
int year = today.getYear();
Month month = today.getMonth();
int dayOfMonth = today.getDayOfMonth();
DayOfWeek day = today.getDayOfWeek();  // WEDNESDAY
```

---

## 7. File I/O

### Reading Files

```java
import java.nio.file.*;
import java.io.*;

// Read entire file as string (Java 11+)
String content = Files.readString(Path.of("data.txt"));

// Read all lines into a List
List<String> lines = Files.readAllLines(Path.of("data.txt"));

// Read line by line with Stream (memory efficient for large files)
try (Stream<String> stream = Files.lines(Path.of("data.txt"))) {
    stream.filter(line -> line.contains("error"))
          .forEach(System.out::println);
}

// BufferedReader (classic approach)
try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

### Writing Files

```java
// Write string to file (Java 11+)
Files.writeString(Path.of("output.txt"), "Hello, World!");

// Write lines
List<String> lines = List.of("Line 1", "Line 2", "Line 3");
Files.write(Path.of("output.txt"), lines);

// Append to file
Files.writeString(Path.of("output.txt"), "Appended text\n", 
    StandardOpenOption.APPEND, StandardOpenOption.CREATE);

// BufferedWriter
try (BufferedWriter writer = new BufferedWriter(new FileWriter("output.txt"))) {
    writer.write("Hello");
    writer.newLine();
    writer.write("World");
}

// PrintWriter (like System.out but to a file)
try (PrintWriter pw = new PrintWriter(new FileWriter("output.txt"))) {
    pw.println("Hello, World!");
    pw.printf("Name: %s, Age: %d%n", "Alice", 30);
}
```

### File Operations

```java
Path path = Path.of("data.txt");

// Check existence
Files.exists(path);
Files.isDirectory(path);
Files.isRegularFile(path);

// Create directories
Files.createDirectory(Path.of("newDir"));
Files.createDirectories(Path.of("parent/child/grandchild"));

// Copy and Move
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
Files.move(source, target, StandardCopyOption.ATOMIC_MOVE);

// Delete
Files.delete(path);              // Throws if not found
Files.deleteIfExists(path);      // Returns false if not found

// List directory contents
try (Stream<Path> entries = Files.list(Path.of("."))) {
    entries.forEach(System.out::println);
}

// Walk directory tree
try (Stream<Path> walk = Files.walk(Path.of("."))) {
    walk.filter(Files::isRegularFile)
        .filter(p -> p.toString().endsWith(".java"))
        .forEach(System.out::println);
}
```

---

## 8. Reflection API

**Reflection** lets you inspect and manipulate classes, methods, fields at **runtime**. It's how frameworks like Spring work under the hood.

```java
import java.lang.reflect.*;

// Get the Class object
Class<?> clazz = Employee.class;                    // From class literal
Class<?> clazz2 = emp.getClass();                   // From instance
Class<?> clazz3 = Class.forName("com.example.Employee");  // From string (dynamic!)

// Inspect class info
String name = clazz.getName();              // "com.example.Employee"
String simple = clazz.getSimpleName();      // "Employee"
Class<?> parent = clazz.getSuperclass();    // Object.class (or whatever parent)
Class<?>[] interfaces = clazz.getInterfaces();

// Get all fields
Field[] fields = clazz.getDeclaredFields();  // Including private!
for (Field f : fields) {
    System.out.println(f.getType() + " " + f.getName());
}

// Access private field
Field nameField = clazz.getDeclaredField("name");
nameField.setAccessible(true);     // Bypass access control!
String value = (String) nameField.get(emp);   // Read private field!
nameField.set(emp, "New Name");    // Set private field!

// Get all methods
Method[] methods = clazz.getDeclaredMethods();
for (Method m : methods) {
    System.out.println(m.getReturnType() + " " + m.getName());
}

// Invoke a method dynamically
Method greet = clazz.getDeclaredMethod("greet", String.class);
greet.setAccessible(true);
greet.invoke(emp, "World");  // Calls emp.greet("World")

// Create instance dynamically
Constructor<?> constructor = clazz.getDeclaredConstructor(String.class, int.class);
Object newEmp = constructor.newInstance("Dynamic", 25);
```

**⚠️ Reflection is powerful but:**
- Slow (bypasses JIT optimizations)
- Breaks encapsulation (access private fields)
- No compile-time type checking
- Use only when necessary (frameworks, testing, serialization)

---

## 9. Multithreading

### What Is a Thread?

A **thread** is a lightweight unit of execution within a process. Multiple threads can run concurrently.

```
Process (your Java app):
┌─────────────────────────────────────────────────┐
│  Thread 1 (main)    Thread 2         Thread 3   │
│  │                  │                │           │
│  │ Load data        │ Process data   │ Save data │
│  │ Calculate        │ Network call   │ UI update │
│  │ Display          │ Parse response │ Animation │
│  ▼                  ▼                ▼           │
│  All threads share the same HEAP memory          │
│  Each thread has its own STACK                    │
└─────────────────────────────────────────────────┘
```

### Thread States

```
             ┌──────────┐
             │   NEW     │  Thread created, not yet started
             └─────┬─────┘
                   │ start()
             ┌─────▼─────┐
             │ RUNNABLE   │  Running or ready to run
             └──┬──┬──┬───┘
    sleep()     │  │  │     wait() / blocked on I/O or lock
    ┌───────────┘  │  └────────────────┐
    ▼              │                   ▼
┌─────────┐        │            ┌──────────┐
│ TIMED   │        │            │ WAITING/  │
│ WAITING │        │            │ BLOCKED   │
└────┬────┘        │            └─────┬─────┘
     │ timeout     │              │ notify() / lock acquired
     └─────────────┤◄─────────────┘
                   │
             ┌─────▼─────┐
             │TERMINATED  │  Thread finished
             └────────────┘
```

### Creating Threads

```java
// Method 1: Implement Runnable (PREFERRED)
public class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Running in: " + Thread.currentThread().getName());
    }
}

Thread t1 = new Thread(new MyTask());
t1.start();     // start() creates a new thread. DON'T call run() directly!
// t1.run();    // ❌ This runs in the CURRENT thread, not a new one!

// Method 2: Lambda (most common in practice)
Thread t2 = new Thread(() -> {
    System.out.println("Lambda thread: " + Thread.currentThread().getName());
});
t2.start();

// Method 3: Extend Thread class (less flexible — can't extend another class)
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("MyThread running");
    }
}
new MyThread().start();
```

### Thread Methods

```java
Thread t = new Thread(() -> { /* work */ });

t.start();                    // Start the thread
t.join();                     // Wait for t to finish before continuing
t.join(5000);                 // Wait at most 5 seconds
Thread.sleep(1000);           // Pause current thread for 1 second
Thread.yield();               // Hint: give other threads a chance to run
t.isAlive();                  // Is the thread still running?
t.setName("Worker-1");        // Set thread name
t.setPriority(Thread.MAX_PRIORITY);  // Set priority (1-10)
t.setDaemon(true);            // Daemon thread — JVM exits even if this thread is running
Thread.currentThread();       // Get reference to current thread
```

### Synchronization — Preventing Race Conditions

```java
// PROBLEM: Two threads modifying the same variable
public class Counter {
    private int count = 0;
    
    public void increment() {
        count++;  // NOT atomic! Actually: read → increment → write
        // Thread A reads 5
        // Thread B reads 5
        // Thread A writes 6
        // Thread B writes 6   ← Should be 7! LOST UPDATE!
    }
}

// SOLUTION 1: synchronized method
public class Counter {
    private int count = 0;
    
    public synchronized void increment() {  // Only one thread can execute this at a time
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}

// SOLUTION 2: synchronized block (more granular)
public void addToList(String item) {
    // Unsynchronized code runs in parallel
    String processed = item.trim().toUpperCase();
    
    synchronized (this) {   // Only this block is synchronized
        list.add(processed);
    }
}

// SOLUTION 3: AtomicInteger (lock-free, better performance)
import java.util.concurrent.atomic.AtomicInteger;

public class Counter {
    private AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet();   // Thread-safe without synchronized!
    }
}
```

### How Synchronized Works Under the Hood

```
Every Java object has a "monitor" (intrinsic lock):

Object:
┌─────────────────────┐
│ Object Header        │
│ ┌─────────────────┐ │
│ │ Mark Word        │ │ ← Contains lock information
│ │ (lock state,    │ │
│ │  thread owner,  │ │
│ │  hash code)     │ │
│ └─────────────────┘ │
│ Fields...           │
└─────────────────────┘

When a thread enters a synchronized block:
1. Acquires the monitor (lock) of the object
2. Other threads trying to acquire the same lock WAIT
3. When the thread exits the block, it releases the lock
4. A waiting thread acquires the lock and proceeds

Lock escalation (JVM optimization):
1. Biased Locking → assumes only one thread uses the lock (very fast)
2. Thin Lock → CAS (Compare-And-Swap) operation (fast)
3. Fat Lock → OS mutex (slow, when there's heavy contention)
```

### Deadlock

```java
// Two threads each holding a lock the other needs
Object lock1 = new Object();
Object lock2 = new Object();

// Thread A
new Thread(() -> {
    synchronized (lock1) {        // Acquires lock1
        Thread.sleep(100);
        synchronized (lock2) {    // Waits for lock2 (Thread B has it!)
            // ...
        }
    }
}).start();

// Thread B
new Thread(() -> {
    synchronized (lock2) {        // Acquires lock2
        Thread.sleep(100);
        synchronized (lock1) {    // Waits for lock1 (Thread A has it!)
            // ...                // DEADLOCK! Both threads waiting forever!
        }
    }
}).start();

// Prevention:
// 1. Always acquire locks in the SAME order
// 2. Use tryLock() with timeout
// 3. Use higher-level concurrency utilities (java.util.concurrent)
```

### Livelock

```java
// Both threads keep responding to each other but never make progress
// Like two people in a hallway who keep stepping aside for each other

// Thread A: "You go first" → steps aside
// Thread B: "No, you go first" → steps aside
// Thread A: "No really, you go first" → steps aside
// ... forever!

// Unlike deadlock (threads are blocked), livelock threads are ACTIVE but making no progress
```

### Producer-Consumer Problem

```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

// Shared buffer (thread-safe queue)
BlockingQueue<Integer> queue = new LinkedBlockingQueue<>(10);  // Max 10 items

// Producer — produces items
Thread producer = new Thread(() -> {
    try {
        for (int i = 0; i < 100; i++) {
            queue.put(i);                    // Blocks if queue is full!
            System.out.println("Produced: " + i);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

// Consumer — consumes items
Thread consumer = new Thread(() -> {
    try {
        while (true) {
            int item = queue.take();         // Blocks if queue is empty!
            System.out.println("Consumed: " + item);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

producer.start();
consumer.start();
```

### ExecutorService — Thread Pool (Modern Approach)

```java
import java.util.concurrent.*;

// Create a thread pool
ExecutorService executor = Executors.newFixedThreadPool(4);  // 4 worker threads

// Submit tasks (Runnable — no return value)
executor.submit(() -> System.out.println("Task 1"));
executor.submit(() -> System.out.println("Task 2"));

// Submit tasks (Callable — returns a value)
Future<Integer> future = executor.submit(() -> {
    Thread.sleep(1000);
    return 42;
});

int result = future.get();           // Blocks until result is ready
int result2 = future.get(5, TimeUnit.SECONDS);  // With timeout

// Shutdown
executor.shutdown();                  // No new tasks, finish existing
executor.awaitTermination(10, TimeUnit.SECONDS);  // Wait for completion
// executor.shutdownNow();           // Interrupt all tasks immediately
```

---

## 10. Reactive Programming

### Reactive Manifesto

```
A reactive system is:
┌────────────────────────────────────────────────┐
│  RESPONSIVE  — Always responds in a timely way │
│  RESILIENT   — Stays responsive during failure  │
│  ELASTIC     — Stays responsive under load      │
│  MESSAGE-DRIVEN — Uses async message passing    │
└────────────────────────────────────────────────┘
```

### Reactive Streams Specification

```java
// Four interfaces (java.util.concurrent.Flow in Java 9+):

// Publisher — source of data
public interface Publisher<T> {
    void subscribe(Subscriber<? super T> subscriber);
}

// Subscriber — consumer of data
public interface Subscriber<T> {
    void onSubscribe(Subscription subscription);
    void onNext(T item);      // Receive next item
    void onError(Throwable t); // Error occurred
    void onComplete();         // No more items
}

// Subscription — link between publisher and subscriber
public interface Subscription {
    void request(long n);     // Request n more items (BACKPRESSURE!)
    void cancel();            // Cancel subscription
}

// Processor — both publisher and subscriber
public interface Processor<T, R> extends Subscriber<T>, Publisher<R> { }
```

### Backpressure — The Key Concept

```
Without backpressure:
  Producer (1000 items/sec) →→→→→→→→ Consumer (100 items/sec)
                                     ^^^ OVERWHELMED! Out of memory!

With backpressure:
  Producer → "How many can you handle?" → Consumer says "10 please"
  Producer sends 10 → Consumer processes → "10 more please"
  Producer adapts to consumer's speed!
```

### Reactive Programming in Java

```java
// Using java.util.concurrent.Flow (Java 9+)
import java.util.concurrent.Flow;
import java.util.concurrent.SubmissionPublisher;

// Create a publisher
SubmissionPublisher<String> publisher = new SubmissionPublisher<>();

// Create a subscriber
Flow.Subscriber<String> subscriber = new Flow.Subscriber<>() {
    private Flow.Subscription subscription;
    
    @Override
    public void onSubscribe(Flow.Subscription subscription) {
        this.subscription = subscription;
        subscription.request(1);  // Request first item
    }
    
    @Override
    public void onNext(String item) {
        System.out.println("Received: " + item);
        subscription.request(1);  // Request next item
    }
    
    @Override
    public void onError(Throwable throwable) {
        System.err.println("Error: " + throwable.getMessage());
    }
    
    @Override
    public void onComplete() {
        System.out.println("Done!");
    }
};

// Connect and publish
publisher.subscribe(subscriber);
publisher.submit("Hello");
publisher.submit("World");
publisher.close();  // Triggers onComplete
```

---

*Previous: [05-Java-Collections.md](05-Java-Collections.md)*
*Next: [07-Java-JDBC-Networking.md](07-Java-JDBC-Networking.md)*
