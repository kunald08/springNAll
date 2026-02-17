# Java OOP — From Zero to Expert

## Table of Contents
1. [Introduction to OOP](#1-introduction-to-oop)
2. [Classes vs Objects](#2-classes-vs-objects)
3. [Class Members — Fields, Methods, Constructors](#3-class-members)
4. [Static Members](#4-static-members)
5. [Inheritance](#5-inheritance)
6. [Interfaces & Abstract Classes](#6-interfaces-and-abstract-classes)
7. [Polymorphism](#7-polymorphism)
8. [Method Overloading](#8-method-overloading)
9. [Method Overriding](#9-method-overriding)
10. [Encapsulation](#10-encapsulation)
11. [Abstraction](#11-abstraction)
12. [The Object Class](#12-the-object-class)
13. [Non-Access Modifiers](#13-non-access-modifiers)
14. [equals(), hashCode(), and Equality](#14-equals-hashcode-and-equality)
15. [Garbage Collection](#15-garbage-collection)
16. [Exceptions — The Complete Guide](#16-exceptions)
17. [Sealed Classes, Records, Pattern Matching, Text Blocks, Virtual Threads](#17-modern-java-features)

---

## 1. Introduction to OOP

**OOP** = **Object-Oriented Programming** — a paradigm where you model the world as **objects** that have **state** (data) and **behavior** (methods).

### The 4 Pillars of OOP

```
┌──────────────────────────────────────────────────────────┐
│                    4 Pillars of OOP                        │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐                      │
│  │ Encapsulation │  │ Abstraction  │                      │
│  │ (Hide data,   │  │ (Hide        │                      │
│  │  expose API)  │  │  complexity) │                      │
│  └──────────────┘  └──────────────┘                      │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐                      │
│  │ Inheritance   │  │ Polymorphism │                      │
│  │ (Reuse code,  │  │ (One         │                      │
│  │  IS-A)        │  │  interface,  │                      │
│  └──────────────┘  │  many forms) │                      │
│                    └──────────────┘                      │
└──────────────────────────────────────────────────────────┘
```

### Why OOP?

```
Procedural Code (without OOP):
  - Data and functions are separate
  - Hard to maintain as code grows
  - Easy to break things

OOP:
  - Data + behavior bundled together
  - Real-world modeling
  - Reusable, maintainable, scalable
```

---

## 2. Classes vs Objects

A **class** is a **blueprint/template**. An **object** is an **instance** of that class.

```java
// CLASS = Blueprint (defines what an Employee looks like)
public class Employee {
    // Fields (state / data / attributes)
    String name;
    int age;
    double salary;
    
    // Constructor (how to create an Employee)
    public Employee(String name, int age, double salary) {
        this.name = name;
        this.age = age;
        this.salary = salary;
    }
    
    // Method (behavior)
    public void introduce() {
        System.out.println("Hi, I'm " + name + ", age " + age);
    }
}

// OBJECTS = Actual instances created from the blueprint
Employee alice = new Employee("Alice", 30, 75000);
Employee bob = new Employee("Bob", 25, 65000);

alice.introduce();  // Hi, I'm Alice, age 30
bob.introduce();    // Hi, I'm Bob, age 25
```

```
Memory Layout:
                    
Stack                    Heap
┌──────────────┐        ┌──────────────────────────┐
│ alice: 0xA1  │───────▶│ Employee@A1               │
│              │        │   name ──▶ "Alice"         │
│              │        │   age: 30                  │
│              │        │   salary: 75000.0          │
├──────────────┤        ├──────────────────────────┤
│ bob: 0xB2    │───────▶│ Employee@B2               │
│              │        │   name ──▶ "Bob"           │
└──────────────┘        │   age: 25                  │
                        │   salary: 65000.0          │
                        └──────────────────────────┘

alice and bob are different objects with different data,
but they share the same class definition (blueprint).
```

---

## 3. Class Members

### Fields (Instance Variables)

```java
public class Car {
    // Instance fields — each object gets its own copy
    String make;
    String model;
    int year;
    double speed = 0;    // Can have default values
    
    // Constant field
    final int MAX_SPEED = 200;  // final = cannot be changed after initialization
}
```

### Constructors

```java
public class Car {
    String make;
    String model;
    int year;
    
    // Default constructor (no parameters)
    // If you don't write ANY constructor, Java provides one automatically:
    //   public Car() { }
    // But if you write ANY constructor, the default one is NOT provided!
    
    // No-arg constructor
    public Car() {
        this("Unknown", "Unknown", 2024);  // Call another constructor with "this()"
    }
    
    // Parameterized constructor
    public Car(String make, String model, int year) {
        this.make = make;     // "this" refers to the current object
        this.model = model;   // Distinguishes parameter from field when names match
        this.year = year;
    }
    
    // Copy constructor (create a copy of another object)
    public Car(Car other) {
        this.make = other.make;
        this.model = other.model;
        this.year = other.year;
    }
}

// Using constructors:
Car c1 = new Car();                           // No-arg → defaults
Car c2 = new Car("Toyota", "Camry", 2024);    // Parameterized
Car c3 = new Car(c2);                          // Copy constructor
```

### The `this` Keyword

```java
public class Employee {
    private String name;
    
    public Employee(String name) {
        this.name = name;     // this.name = field, name = parameter
    }
    
    public Employee getThis() {
        return this;          // Return the current object itself
    }
    
    public void setName(String name) {
        this.name = name;     // Without 'this', you'd be assigning param to itself!
    }
}
```

### Constructor Chaining

```java
public class Employee {
    String name;
    int age;
    String dept;
    
    // Constructor 1
    public Employee(String name) {
        this(name, 0, "Unassigned");  // Calls Constructor 3
    }
    
    // Constructor 2
    public Employee(String name, int age) {
        this(name, age, "Unassigned");  // Calls Constructor 3
    }
    
    // Constructor 3 (the "main" constructor)
    public Employee(String name, int age, String dept) {
        this.name = name;
        this.age = age;
        this.dept = dept;
    }
    
    // this() must be the FIRST statement in the constructor!
}
```

### Initialization Blocks

```java
public class Demo {
    int x;
    static int y;
    
    // Static initialization block — runs ONCE when class is loaded
    static {
        y = 100;
        System.out.println("Static block runs first");
    }
    
    // Instance initialization block — runs EVERY time an object is created
    // Runs BEFORE the constructor
    {
        x = 10;
        System.out.println("Instance block runs second");
    }
    
    public Demo() {
        System.out.println("Constructor runs third");
    }
}

// Order of execution:
// 1. Static block (once)
// 2. Instance block (each time)
// 3. Constructor (each time)
```

---

## 4. Static Members

`static` means the member belongs to the **class**, not to any particular object.

```java
public class MathUtils {
    // Static field — ONE copy shared by ALL instances
    static int instanceCount = 0;
    
    // Static method — can be called without creating an object
    public static int add(int a, int b) {
        return a + b;
    }
    
    // Static method can ONLY access static members!
    // public static void test() {
    //     System.out.println(this.instanceCount);  // ❌ ERROR! No 'this' in static context!
    // }
    
    public MathUtils() {
        instanceCount++;  // Non-static CAN access static
    }
}

// Usage:
int sum = MathUtils.add(5, 3);   // No object needed!
System.out.println(MathUtils.instanceCount);  // Access via class name

MathUtils a = new MathUtils();
MathUtils b = new MathUtils();
System.out.println(MathUtils.instanceCount);  // 2 (shared counter)
```

### Memory: Static vs Instance

```
Method Area (loaded once):
┌────────────────────────────┐
│ MathUtils class data       │
│   static instanceCount: 2  │  ← ONE copy, shared
│   static add() method      │
└────────────────────────────┘

Heap:
┌──────────────────┐  ┌──────────────────┐
│ MathUtils@A      │  │ MathUtils@B      │
│ (no instance     │  │ (no instance     │
│  fields here)    │  │  fields here)    │
└──────────────────┘  └──────────────────┘
```

---

## 5. Inheritance

**Inheritance** lets a class (child/subclass) **inherit** fields and methods from another class (parent/superclass).

```java
// Parent (superclass / base class)
public class Animal {
    protected String name;
    protected int age;
    
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public void eat() {
        System.out.println(name + " is eating");
    }
    
    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// Child (subclass / derived class)
public class Dog extends Animal {    // 'extends' keyword
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age);            // MUST call parent constructor first!
        this.breed = breed;
    }
    
    // Dog inherits eat() and sleep() from Animal
    
    // Dog adds its own method
    public void bark() {
        System.out.println(name + " says Woof!");
    }
    
    // Dog can OVERRIDE parent methods (more in section 9)
    @Override
    public void eat() {
        System.out.println(name + " is chomping food!");
    }
}

// Usage:
Dog rex = new Dog("Rex", 3, "Labrador");
rex.eat();    // "Rex is chomping food!" (overridden)
rex.sleep();  // "Rex is sleeping" (inherited from Animal)
rex.bark();   // "Rex says Woof!" (Dog's own method)
```

### The `super` Keyword

```java
public class Dog extends Animal {
    
    public Dog(String name, int age) {
        super(name, age);      // Call parent constructor (MUST be first line!)
    }
    
    @Override
    public void eat() {
        super.eat();           // Call parent's version of eat()
        System.out.println("...and wants more!");
    }
}
```

### Inheritance Hierarchy

```
      Object          ← Every class implicitly extends Object
        │
      Animal
      /    \
    Dog    Cat
     │
  GuideDog
  
- Java supports SINGLE inheritance only (one parent class)
- A class can implement MULTIPLE interfaces (next section)
- Every class extends Object (directly or indirectly)
```

### What Gets Inherited?

| Member | Inherited? |
|---|---|
| public fields/methods | ✅ Yes |
| protected fields/methods | ✅ Yes (even in different package) |
| package-private (default) | ✅ Only if same package |
| private fields/methods | ❌ No |
| Constructors | ❌ No (but can call with super()) |
| Static methods | ❌ Not truly inherited (hidden, not overridden) |

---

## 6. Interfaces and Abstract Classes

### Abstract Classes

An abstract class **cannot be instantiated**. It may contain abstract methods (no body) that subclasses MUST implement.

```java
// Cannot do: Shape s = new Shape();  ← ERROR!
public abstract class Shape {
    protected String color;
    
    public Shape(String color) {
        this.color = color;
    }
    
    // Concrete method (has a body)
    public String getColor() {
        return color;
    }
    
    // Abstract method (NO body — subclasses MUST implement this)
    public abstract double area();
    
    // Abstract method
    public abstract double perimeter();
}

// Concrete subclass — MUST implement all abstract methods
public class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }
    
    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
    
    @Override
    public double perimeter() {
        return 2 * Math.PI * radius;
    }
}

public class Rectangle extends Shape {
    private double width, height;
    
    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double area() {
        return width * height;
    }
    
    @Override
    public double perimeter() {
        return 2 * (width + height);
    }
}
```

### Interfaces

An interface defines a **contract** — what methods a class must provide.

```java
// Interface: Pure contract (what to do, not how)
public interface Flyable {
    // All methods in an interface are implicitly public abstract
    void fly();
    void land();
    
    // Default method (Java 8+) — provides a body that implementing classes can use or override
    default void glide() {
        System.out.println("Gliding...");
    }
    
    // Static method (Java 8+) — belongs to the interface, not implementing classes
    static int getMaxAltitude() {
        return 35000;
    }
    
    // Constants (implicitly public static final)
    double GRAVITY = 9.81;
}

public interface Swimmable {
    void swim();
}

// A class can implement MULTIPLE interfaces (unlike inheritance)!
public class Duck extends Animal implements Flyable, Swimmable {
    
    public Duck(String name, int age) {
        super(name, age);
    }
    
    @Override
    public void fly() {
        System.out.println(name + " is flying!");
    }
    
    @Override
    public void land() {
        System.out.println(name + " landed!");
    }
    
    @Override
    public void swim() {
        System.out.println(name + " is swimming!");
    }
    
    // Can optionally override default method
    @Override
    public void glide() {
        System.out.println(name + " is gracefully gliding!");
    }
}
```

### Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---|---|---|
| Instantiation | ❌ Cannot | ❌ Cannot |
| Methods | Abstract + concrete | Abstract + default + static |
| Fields | Any type (instance + static) | Only public static final (constants) |
| Constructors | ✅ Yes | ❌ No |
| Inheritance | extends (single) | implements (multiple) |
| Access modifiers | Any | public only (methods) |
| When to use | "IS-A" with shared code | "CAN-DO" / capability / contract |

### When to Use Which?

```
Use Abstract Class when:
  - Subclasses share common CODE (not just contract)
  - You need constructors
  - You need non-public members
  - You want to provide a partial implementation
  Example: Shape → Circle, Rectangle (share color field, constructor)

Use Interface when:
  - Defining a capability/contract
  - Multiple classes need to implement it
  - Classes from different hierarchies need common behavior
  Example: Flyable → Bird, Plane, Superman (unrelated classes, same capability)
```

---

## 7. Polymorphism

**Polymorphism** = "many forms". The same method call behaves differently depending on the actual object.

```java
// The same variable type (Shape) can hold different objects
Shape s1 = new Circle("Red", 5);
Shape s2 = new Rectangle("Blue", 4, 6);

// The same method call (area()) behaves differently!
System.out.println(s1.area());  // 78.54 (Circle's implementation)
System.out.println(s2.area());  // 24.0  (Rectangle's implementation)

// This is RUNTIME polymorphism (also called dynamic dispatch)
// The JVM decides which method to call at RUNTIME based on the actual object type
```

### Polymorphism in Action

```java
// Method that accepts ANY Shape — works with Circle, Rectangle, Triangle, etc.
public static void printShapeInfo(Shape shape) {
    System.out.println("Color: " + shape.getColor());
    System.out.println("Area: " + shape.area());         // Calls the right version!
    System.out.println("Perimeter: " + shape.perimeter()); // Calls the right version!
}

// Works with any current or FUTURE Shape subclass!
printShapeInfo(new Circle("Red", 5));
printShapeInfo(new Rectangle("Blue", 4, 6));

// Collections with polymorphism
List<Shape> shapes = new ArrayList<>();
shapes.add(new Circle("Red", 5));
shapes.add(new Rectangle("Blue", 4, 6));

for (Shape s : shapes) {
    System.out.println(s.area());  // Each one uses its own implementation!
}
```

### How Polymorphism Works Under the Hood

```
Every class has a "Virtual Method Table" (vtable) — a lookup table of methods.

Shape vtable:                Circle vtable:               Rectangle vtable:
┌──────────────────┐         ┌──────────────────┐         ┌──────────────────┐
│ getColor → Shape │         │ getColor → Shape │         │ getColor → Shape │
│ area → (abstract)│         │ area → Circle    │         │ area → Rectangle │
│ perimeter → (abs)│         │ perimeter → Circ │         │ perimeter → Rect │
└──────────────────┘         └──────────────────┘         └──────────────────┘

When you call shape.area():
1. JVM looks at the ACTUAL object type (not the variable type)
2. Finds the vtable for that class
3. Looks up "area" in the vtable
4. Calls the correct method
```

---

## 8. Method Overloading

**Compile-time polymorphism** — same method name, different parameters.

```java
public class Calculator {
    // Same name, different parameter types
    public int add(int a, int b) {
        return a + b;
    }
    
    public double add(double a, double b) {
        return a + b;
    }
    
    // Same name, different number of parameters
    public int add(int a, int b, int c) {
        return a + b + c;
    }
    
    // ❌ CANNOT overload by return type alone!
    // public double add(int a, int b) { return a + b; }  // ERROR!
    
    // ❌ CANNOT overload by parameter names alone!
    // public int add(int x, int y) { return x + y; }  // ERROR!
}

Calculator calc = new Calculator();
calc.add(1, 2);       // Calls int version → 3
calc.add(1.5, 2.5);   // Calls double version → 4.0
calc.add(1, 2, 3);    // Calls 3-parameter version → 6
```

### Overloading Resolution Rules

The compiler picks the **most specific** matching method:

```java
public void print(int x) { System.out.println("int: " + x); }
public void print(long x) { System.out.println("long: " + x); }
public void print(double x) { System.out.println("double: " + x); }

print(5);     // "int: 5"     (exact match)
print(5L);    // "long: 5"    (exact match)
print(5.0);   // "double: 5.0" (exact match)
print('A');   // "int: 65"    (char widens to int)
```

---

## 9. Method Overriding

**Runtime polymorphism** — a subclass provides its own implementation of a method inherited from the parent.

```java
public class Animal {
    public void makeSound() {
        System.out.println("Some generic sound");
    }
}

public class Dog extends Animal {
    @Override       // Annotation — tells compiler "I intend to override"
    public void makeSound() {
        System.out.println("Woof!");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Meow!");
    }
}

Animal a = new Dog();
a.makeSound();  // "Woof!" — even though variable type is Animal!
```

### Overriding Rules

```java
// 1. Same method signature (name + parameters)
// 2. Return type must be same or COVARIANT (subtype)
// 3. Access modifier must be same or WIDER (not narrower)
// 4. Cannot override static, final, or private methods
// 5. Cannot throw broader checked exceptions

public class Parent {
    protected Number getValue() throws IOException {
        return 42;
    }
}

public class Child extends Parent {
    @Override
    public Integer getValue() throws FileNotFoundException {  // ✅ All valid:
        return 42;                                             // public ≥ protected
    }                                                          // Integer is subtype of Number (covariant)
                                                               // FileNotFoundException is subtype of IOException
}
```

### Overloading vs Overriding

| Feature | Overloading | Overriding |
|---|---|---|
| When | Compile time | Runtime |
| Where | Same class or subclass | Subclass only |
| Parameters | Must be different | Must be same |
| Return type | Can differ | Must be same or covariant |
| Access modifier | Can differ | Must be same or wider |
| `@Override` | Not used | Should be used |
| `static` methods | Can overload | Cannot override (hidden) |

---

## 10. Encapsulation

**Encapsulation** = bundling data (fields) and methods that operate on that data together, and restricting direct access to the data.

```java
// ❌ BAD — No encapsulation
public class BankAccount {
    public double balance;  // Anyone can set this to -1000000!
}

BankAccount acc = new BankAccount();
acc.balance = -1000000;  // Oops! No validation!

// ✅ GOOD — Proper encapsulation
public class BankAccount {
    private double balance;  // Private — can't access directly from outside
    
    // Constructor
    public BankAccount(double initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Initial balance cannot be negative");
        }
        this.balance = initialBalance;
    }
    
    // Getter — controlled read access
    public double getBalance() {
        return balance;
    }
    
    // No setter! Instead, controlled mutation through business methods:
    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Deposit must be positive");
        }
        this.balance += amount;
    }
    
    public void withdraw(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Withdrawal must be positive");
        }
        if (amount > balance) {
            throw new IllegalStateException("Insufficient funds");
        }
        this.balance -= amount;
    }
}

// Now:
BankAccount acc = new BankAccount(1000);
acc.deposit(500);       // ✅ balance = 1500
acc.withdraw(200);      // ✅ balance = 1300
// acc.balance = -1000;  // ❌ COMPILE ERROR! Private field!
// acc.withdraw(5000);   // ❌ RUNTIME ERROR! Insufficient funds!
```

### JavaBeans Convention

```java
// The standard pattern for Java classes:
public class Employee {
    // Private fields
    private String name;
    private int age;
    
    // No-arg constructor
    public Employee() {}
    
    // Parameterized constructor
    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Getter
    public String getName() {
        return name;
    }
    
    // Setter
    public void setName(String name) {
        this.name = name;
    }
    
    // Boolean getter uses "is" prefix
    private boolean active;
    public boolean isActive() {
        return active;
    }
}
```

---

## 11. Abstraction

**Abstraction** = hiding complex implementation details and showing only the essential features.

```java
// You don't need to know HOW a car engine works to DRIVE a car.
// Abstraction provides a simple interface and hides complexity.

// Example: JDBC — You write the same code regardless of whether it's MySQL, Oracle, or PostgreSQL
Connection conn = DriverManager.getConnection(url, user, pass);
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM users");
// You don't know HOW the connection is established, the protocol details, etc.
// That's abstraction!

// Another example: Collections
List<String> list = new ArrayList<>();
list.add("Hello");
list.get(0);
// You don't know that ArrayList uses a dynamic array internally,
// how it resizes, copies elements, etc. You just use the interface.
```

Abstraction is achieved through **abstract classes** and **interfaces** (covered in section 6).

---

## 12. The Object Class

Every class in Java extends `java.lang.Object` (directly or indirectly). Object provides these methods:

```java
public class Object {
    // Returns a string representation
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
    // Default: "ClassName@hexHashCode" (not very useful!)
    // You should OVERRIDE this!
    
    // Checks equality
    public boolean equals(Object obj) {
        return (this == obj);   // Default: reference equality (same object?)
    }
    // You should OVERRIDE this for value equality!
    
    // Returns a hash code
    public int hashCode() {
        // Default: derived from memory address
    }
    // If you override equals(), you MUST override hashCode()!
    
    // Returns the runtime class
    public final Class<?> getClass() { ... }
    
    // Creates a shallow copy
    protected Object clone() throws CloneNotSupportedException { ... }
    
    // Called by garbage collector before reclaiming (deprecated in Java 9+)
    protected void finalize() throws Throwable { ... }
    
    // Thread-related methods
    public final void wait() { ... }
    public final void notify() { ... }
    public final void notifyAll() { ... }
}
```

### Overriding toString()

```java
public class Employee {
    private String name;
    private int age;
    
    @Override
    public String toString() {
        return "Employee{name='" + name + "', age=" + age + "}";
    }
}

Employee emp = new Employee("Alice", 30);
System.out.println(emp);          // Employee{name='Alice', age=30}
// Without override: Employee@1a2b3c4d (useless!)
```

---

## 13. Non-Access Modifiers

### final

```java
// final variable — cannot be reassigned
final int MAX = 100;
// MAX = 200;  // ❌ COMPILE ERROR!

final List<String> list = new ArrayList<>();
list.add("hello");    // ✅ Can modify the object!
// list = new ArrayList<>();  // ❌ Cannot reassign the reference!

// final method — cannot be overridden
public final void criticalMethod() { ... }

// final class — cannot be extended (no subclasses)
public final class String { ... }   // This is why you can't extend String!
```

### static (covered in section 4)

### abstract

```java
// abstract class — cannot be instantiated
public abstract class Shape { ... }

// abstract method — no body, must be overridden
public abstract double area();
```

### synchronized (covered in multithreading notes)

### volatile

```java
// volatile — ensures visibility across threads
// When one thread changes a volatile variable, all other threads see the new value immediately
private volatile boolean running = true;
```

### transient

```java
// transient — excluded from serialization
private transient String password;  // Won't be saved when object is serialized
```

### strictfp

```java
// strictfp — ensures floating-point calculations are consistent across platforms
public strictfp class Calculator { ... }
```

---

## 14. equals(), hashCode(), and Equality

### The Contract

```
If two objects are EQUAL (equals() returns true):
  → They MUST have the SAME hashCode

If two objects have the same hashCode:
  → They might or might not be equal (hash collisions happen)

RULE: If you override equals(), you MUST override hashCode()!
If you don't, HashMaps and HashSets will BREAK.
```

### Implementing equals() and hashCode()

```java
public class Employee {
    private String name;
    private int id;
    
    @Override
    public boolean equals(Object obj) {
        // 1. Same reference?
        if (this == obj) return true;
        
        // 2. Null or different class?
        if (obj == null || getClass() != obj.getClass()) return false;
        
        // 3. Cast and compare fields
        Employee other = (Employee) obj;
        return this.id == other.id && 
               Objects.equals(this.name, other.name);  // Handles null safely
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, id);
        // Uses the same fields as equals()!
    }
}

// Now:
Employee e1 = new Employee("Alice", 1);
Employee e2 = new Employee("Alice", 1);
System.out.println(e1 == e2);      // false (different objects)
System.out.println(e1.equals(e2)); // true  (same content)

// HashMap works correctly:
Map<Employee, String> map = new HashMap<>();
map.put(e1, "Team A");
System.out.println(map.get(e2));   // "Team A" ✅ (because equals + hashCode are correct)
```

### Why hashCode() Matters for HashMap

```
HashMap internal structure:

Bucket 0: [Entry(key=..., val=...)] → [Entry(...)] → null
Bucket 1: null
Bucket 2: [Entry(key=emp1, val="Team A")] → null
Bucket 3: null
...

When you do map.put(emp1, "Team A"):
  1. Compute hashCode() of emp1 → e.g., 12345
  2. Bucket index = 12345 % numBuckets → e.g., bucket 2
  3. Store entry in bucket 2

When you do map.get(emp2):
  1. Compute hashCode() of emp2 → must be 12345 (same fields!)
  2. Bucket index = 12345 % numBuckets → bucket 2
  3. Find entry in bucket 2, check equals() → found!

If hashCode() is wrong:
  emp2's hashCode might be 67890 → bucket 5 → entry not found! 💥
```

---

## 15. Garbage Collection

Java automatically manages memory. When an object has no more references, the **Garbage Collector (GC)** reclaims its memory.

### When Does an Object Become Eligible for GC?

```java
// 1. Setting reference to null
Employee emp = new Employee("Alice");
emp = null;   // The Employee object is now eligible for GC

// 2. Reassigning the reference
Employee emp2 = new Employee("Bob");
emp2 = new Employee("Charlie");  // "Bob" object is eligible for GC

// 3. Object created inside a method (local scope)
public void process() {
    Employee temp = new Employee("Temp");
}  // After method returns, "Temp" object is eligible for GC

// 4. Island of isolation
Employee a = new Employee("A");
Employee b = new Employee("B");
a.friend = b;
b.friend = a;
a = null;
b = null;
// Both objects reference each other but nothing else references them
// They're an "island" — both eligible for GC!
```

### How GC Works Under the Hood

```
Generational Garbage Collection:

┌────────────────────────────────────────────────────────────┐
│  YOUNG GENERATION                                          │
│  ┌──────────────┐ ┌──────────┐ ┌──────────┐               │
│  │    Eden       │ │ Survivor │ │ Survivor │               │
│  │  (new objects │ │   S0     │ │   S1     │               │
│  │   created     │ │          │ │          │               │
│  │   here)       │ │          │ │          │               │
│  └──────────────┘ └──────────┘ └──────────┘               │
│  Minor GC: Fast, frequent, cleans short-lived objects      │
├────────────────────────────────────────────────────────────┤
│  OLD GENERATION (Tenured)                                  │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Objects that survived many Minor GCs live here      │   │
│  │  Major GC: Slower, less frequent                     │   │
│  └─────────────────────────────────────────────────────┘   │
├────────────────────────────────────────────────────────────┤
│  METASPACE (Java 8+, replaces PermGen)                     │
│  │  Class metadata, method bytecode, string pool           │
└────────────────────────────────────────────────────────────┘

Object lifecycle:
1. New object → Eden space
2. Survives Minor GC → moves to Survivor space
3. Survives several Minor GCs → promoted to Old Generation
4. No references left → reclaimed by GC

GC Algorithms:
- Serial GC: Single thread, stop-the-world (small apps)
- Parallel GC: Multiple threads (Java 8 default)
- G1 GC: Region-based, low pause time (Java 9+ default)
- ZGC: Ultra-low latency (<10ms pauses, Java 11+)
- Shenandoah: Low latency alternative (OpenJDK)
```

### You Cannot Force GC!

```java
System.gc();               // "Suggests" GC, but JVM may ignore it!
Runtime.getRuntime().gc(); // Same thing

// finalize() — deprecated! Don't use it. Use try-with-resources instead.
```

---

## 16. Exceptions

### Exception Hierarchy

```
                    Throwable
                   /          \
               Error         Exception
              /    \          /        \
     OutOfMemory  StackOverflow   RuntimeException    IOException
     Error         Error           /      |     \        /      \
                              NullPointer Arithmetic  FileNot   SQL
                              Exception   Exception   Found     Exception
                                                                
     ←── Unchecked ──→      ←── Unchecked ──→     ←── Checked ──→
     (don't need to catch)   (don't need to catch) (MUST handle)
```

### Checked vs Unchecked Exceptions

```java
// CHECKED exceptions — compiler forces you to handle them
// They represent recoverable conditions (file not found, network error)
public void readFile() throws IOException {         // Must declare OR catch
    FileReader reader = new FileReader("test.txt"); // Could throw FileNotFoundException
}

// You MUST either:
// Option 1: Catch it
try {
    readFile();
} catch (IOException e) {
    System.out.println("File error: " + e.getMessage());
}

// Option 2: Declare it (pass responsibility to caller)
public void myMethod() throws IOException {
    readFile();
}

// UNCHECKED exceptions (RuntimeException subclasses) — no compile-time checking
// They represent programming bugs
String s = null;
s.length();           // NullPointerException — YOUR bug, fix your code!
int[] arr = new int[5];
arr[10] = 1;          // ArrayIndexOutOfBoundsException
int x = 10 / 0;       // ArithmeticException
```

### try-catch-finally

```java
try {
    // Code that might throw an exception
    FileReader reader = new FileReader("data.txt");
    int data = reader.read();
} catch (FileNotFoundException e) {
    // Handle specific exception
    System.out.println("File not found: " + e.getMessage());
} catch (IOException e) {
    // Handle broader exception (must come AFTER more specific ones!)
    System.out.println("IO error: " + e.getMessage());
} catch (Exception e) {
    // Catch-all (use sparingly)
    System.out.println("Unexpected error: " + e.getMessage());
    e.printStackTrace();  // Print full stack trace
} finally {
    // ALWAYS runs — whether exception occurred or not
    // Used for cleanup (closing resources)
    System.out.println("This always runs!");
}

// Multi-catch (Java 7+)
try {
    // ...
} catch (FileNotFoundException | ArithmeticException e) {
    // Handle both with the same code
    System.out.println("Error: " + e.getMessage());
}
```

### try-with-resources (Java 7+)

```java
// Resources that implement AutoCloseable are automatically closed!
try (FileReader reader = new FileReader("data.txt");
     BufferedReader buffered = new BufferedReader(reader)) {
    
    String line = buffered.readLine();
    System.out.println(line);
    
} catch (IOException e) {
    System.out.println("Error: " + e.getMessage());
}
// reader and buffered are automatically closed here!
// Even if an exception occurs!
// No need for finally block!
```

### Creating Custom Exceptions

```java
// Checked custom exception
public class InsufficientFundsException extends Exception {
    private double amount;
    
    public InsufficientFundsException(String message, double amount) {
        super(message);     // Pass message to parent Exception class
        this.amount = amount;
    }
    
    public double getAmount() {
        return amount;
    }
}

// Unchecked custom exception
public class InvalidEmployeeIdException extends RuntimeException {
    public InvalidEmployeeIdException(String message) {
        super(message);
    }
    
    public InvalidEmployeeIdException(String message, Throwable cause) {
        super(message, cause);   // Chain the original exception
    }
}

// Usage:
public void withdraw(double amount) throws InsufficientFundsException {
    if (amount > balance) {
        throw new InsufficientFundsException(
            "Cannot withdraw " + amount + ", balance is " + balance,
            amount - balance
        );
    }
    balance -= amount;
}
```

### Reading the Stack Trace

```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.length()" because "str" is null
    at com.myapp.service.UserService.validateName(UserService.java:45)    ← Exception occurred HERE
    at com.myapp.service.UserService.createUser(UserService.java:28)     ← Called from here
    at com.myapp.controller.UserController.handleRequest(UserController.java:15)  ← Called from here
    at com.myapp.Main.main(Main.java:10)                                  ← Entry point

Reading order: TOP = where it happened, BOTTOM = where it started
Look at YOUR code first (ignore framework/library lines initially)
```

---

## 17. Modern Java Features

### Sealed Classes (Java 17)

```java
// A sealed class restricts which classes can extend it
public sealed class Shape permits Circle, Rectangle, Triangle {
    // Only Circle, Rectangle, and Triangle can extend Shape
    // No other class can!
}

public final class Circle extends Shape { ... }      // final = can't be extended further
public sealed class Rectangle extends Shape permits Square { ... }  // allows Square
public non-sealed class Triangle extends Shape { ... }  // open for extension

// Why? Gives you control over the class hierarchy
// Enables exhaustive pattern matching in switch
```

### Pattern Matching (Java 16+)

```java
// Old way:
if (obj instanceof String) {
    String s = (String) obj;     // Redundant cast
    System.out.println(s.length());
}

// New way:
if (obj instanceof String s) {   // Cast and assign in one step!
    System.out.println(s.length());
}

// Pattern matching in switch (Java 21+):
String describe(Object obj) {
    return switch (obj) {
        case Integer i  -> "Integer: " + i;
        case String s   -> "String: " + s;
        case Double d   -> "Double: " + d;
        case null       -> "null";
        default         -> "Unknown: " + obj;
    };
}
```

### Text Blocks (Java 15)

```java
// Old way (ugly string concatenation):
String json = "{\n" +
              "  \"name\": \"Alice\",\n" +
              "  \"age\": 30\n" +
              "}";

// New way — Text Block:
String json = """
        {
            "name": "Alice",
            "age": 30
        }
        """;
// Much cleaner! Preserves formatting, no escape characters needed.
```

### Records (Java 16)

```java
// Old way: 50+ lines of boilerplate for a simple data class
public class Point {
    private final int x;
    private final int y;
    
    public Point(int x, int y) { this.x = x; this.y = y; }
    public int getX() { return x; }
    public int getY() { return y; }
    @Override public boolean equals(Object o) { ... }
    @Override public int hashCode() { ... }
    @Override public String toString() { ... }
}

// New way — Record (1 line!):
public record Point(int x, int y) { }

// Automatically provides:
// - Private final fields (x, y)
// - Constructor
// - Accessor methods: x(), y()  (not getX()!)
// - equals(), hashCode(), toString()
// - Immutable! No setters.

Point p = new Point(3, 4);
System.out.println(p.x());         // 3
System.out.println(p.y());         // 4
System.out.println(p);             // Point[x=3, y=4]
```

### Virtual Threads (Java 21)

```java
// Traditional threads are expensive (each needs ~1MB of stack memory)
// Virtual threads are lightweight (can have MILLIONS of them!)

// Old way:
Thread thread = new Thread(() -> {
    System.out.println("Running in: " + Thread.currentThread());
});
thread.start();

// New way — Virtual Thread:
Thread vThread = Thread.ofVirtual().start(() -> {
    System.out.println("Running in virtual thread!");
});

// Even better — with Executors:
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1_000_000; i++) {
        executor.submit(() -> {
            // Each task gets its own virtual thread!
            // 1 million virtual threads — no problem!
            Thread.sleep(Duration.ofSeconds(1));
            return "Done";
        });
    }
}

// Under the hood:
// Virtual threads are scheduled on a small pool of platform threads
// When a virtual thread blocks (IO, sleep), it's UNMOUNTED from the platform thread
// The platform thread is freed to run other virtual threads
// When the IO completes, the virtual thread is MOUNTED back on any available platform thread
```

---

*Previous: [03-Java-Basics.md](03-Java-Basics.md)*
*Next: [05-Java-Collections.md](05-Java-Collections.md)*
