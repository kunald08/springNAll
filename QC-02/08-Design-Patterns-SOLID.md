# Design Patterns & SOLID Principles

## Table of Contents
1. [SOLID Principles](#1-solid-principles)
2. [Introduction to UML](#2-introduction-to-uml)
3. [Creational Patterns — Singleton](#3-singleton-pattern)
4. [Creational Patterns — Factory](#4-factory-pattern)
5. [Structural Patterns](#5-structural-patterns)
6. [Behavioral Patterns](#6-behavioral-patterns)
7. [Pattern Summary & When to Use](#7-pattern-summary)

---

## 1. SOLID Principles

SOLID is five design principles that make code **maintainable, flexible, and understandable**.

### S — Single Responsibility Principle (SRP)

> **A class should have only one reason to change.**

```java
// ❌ BAD — Employee class does EVERYTHING
public class Employee {
    private String name;
    private double salary;
    
    public double calculateTax() { /* tax logic */ }        // Tax responsibility
    public String generateReport() { /* report logic */ }    // Report responsibility
    public void saveToDatabase() { /* DB logic */ }          // Persistence responsibility
}
// If tax rules change → modify Employee
// If report format changes → modify Employee
// If database changes → modify Employee
// THREE reasons to change!

// ✅ GOOD — Each class has ONE responsibility
public class Employee {
    private String name;
    private double salary;
    // Just data — only changes if employee structure changes
}

public class TaxCalculator {
    public double calculateTax(Employee emp) { /* tax logic */ }
    // Only changes if tax rules change
}

public class EmployeeReportGenerator {
    public String generate(Employee emp) { /* report logic */ }
    // Only changes if report format changes
}

public class EmployeeRepository {
    public void save(Employee emp) { /* DB logic */ }
    // Only changes if persistence mechanism changes
}
```

### O — Open/Closed Principle (OCP)

> **Open for extension, closed for modification.**

```java
// ❌ BAD — must modify this method every time we add a new shape
public class AreaCalculator {
    public double calculateArea(Object shape) {
        if (shape instanceof Circle c) {
            return Math.PI * c.radius * c.radius;
        } else if (shape instanceof Rectangle r) {
            return r.width * r.height;
        }
        // Adding Triangle? Must modify this class! ❌
        throw new IllegalArgumentException("Unknown shape");
    }
}

// ✅ GOOD — new shapes extend without modifying existing code
public interface Shape {
    double area();
}

public class Circle implements Shape {
    double radius;
    public double area() { return Math.PI * radius * radius; }
}

public class Rectangle implements Shape {
    double width, height;
    public double area() { return width * height; }
}

// Adding a new shape? Just create a new class! No existing code changes.
public class Triangle implements Shape {
    double base, height;
    public double area() { return 0.5 * base * height; }
}

public class AreaCalculator {
    public double calculateArea(Shape shape) {
        return shape.area();  // Works for ANY shape — never needs modification!
    }
}
```

### L — Liskov Substitution Principle (LSP)

> **Subtypes must be substitutable for their base types without breaking behavior.**

```java
// ❌ BAD — Classic violation: Square extends Rectangle
public class Rectangle {
    protected int width, height;
    
    public void setWidth(int w) { this.width = w; }
    public void setHeight(int h) { this.height = h; }
    public int getArea() { return width * height; }
}

public class Square extends Rectangle {
    @Override
    public void setWidth(int w) { 
        this.width = w; 
        this.height = w;  // Must keep width == height!
    }
    @Override
    public void setHeight(int h) { 
        this.width = h;   // Must keep width == height!
        this.height = h; 
    }
}

// This breaks LSP:
Rectangle r = new Square();    // Substituting Square for Rectangle
r.setWidth(5);
r.setHeight(10);
System.out.println(r.getArea());  // Expected 50 (5*10), but got 100 (10*10)!

// ✅ GOOD — Don't force inheritance. Use a common interface instead.
public interface Shape {
    int getArea();
}

public class Rectangle implements Shape {
    private final int width, height;
    public Rectangle(int w, int h) { this.width = w; this.height = h; }
    public int getArea() { return width * height; }
}

public class Square implements Shape {
    private final int side;
    public Square(int s) { this.side = s; }
    public int getArea() { return side * side; }
}
```

### I — Interface Segregation Principle (ISP)

> **Clients should not be forced to depend on interfaces they don't use.**

```java
// ❌ BAD — one fat interface forces all implementations to include everything
public interface Worker {
    void work();
    void eat();
    void sleep();
}

public class Robot implements Worker {
    public void work() { /* works */ }
    public void eat()  { /* Robots don't eat! */ throw new UnsupportedOperationException(); }
    public void sleep() { /* Robots don't sleep! */ throw new UnsupportedOperationException(); }
}

// ✅ GOOD — split into focused interfaces
public interface Workable {
    void work();
}

public interface Eatable {
    void eat();
}

public interface Sleepable {
    void sleep();
}

public class Human implements Workable, Eatable, Sleepable {
    public void work() { /* works */ }
    public void eat()  { /* eats */ }
    public void sleep() { /* sleeps */ }
}

public class Robot implements Workable {
    public void work() { /* works */ }
    // No eat() or sleep() — doesn't need them!
}
```

### D — Dependency Inversion Principle (DIP)

> **Depend on abstractions, not concrete implementations.**

```java
// ❌ BAD — high-level module depends on low-level module
public class EmailService {
    public void sendEmail(String message) { /* send email */ }
}

public class NotificationManager {
    private EmailService emailService = new EmailService();  // Hardcoded dependency!
    
    public void notify(String message) {
        emailService.sendEmail(message);
        // What if we want to send SMS? Must modify this class!
    }
}

// ✅ GOOD — both depend on an abstraction
public interface NotificationService {
    void send(String message);
}

public class EmailService implements NotificationService {
    public void send(String message) { /* send email */ }
}

public class SMSService implements NotificationService {
    public void send(String message) { /* send SMS */ }
}

public class NotificationManager {
    private final NotificationService service;  // Depends on abstraction!
    
    // Dependency INJECTED from outside
    public NotificationManager(NotificationService service) {
        this.service = service;
    }
    
    public void notify(String message) {
        service.send(message);  // Works with ANY notification service!
    }
}

// Usage:
NotificationManager emailNotifier = new NotificationManager(new EmailService());
NotificationManager smsNotifier = new NotificationManager(new SMSService());
```

---

## 2. Introduction to UML

**UML** (Unified Modeling Language) is a standardized way to visualize system design.

### Common UML Diagram Types

```
Structural Diagrams (what the system IS):
├── Class Diagram        — classes, attributes, methods, relationships
├── Object Diagram       — instances at a point in time
├── Component Diagram    — high-level components and dependencies
└── Package Diagram      — packages and dependencies

Behavioral Diagrams (what the system DOES):
├── Use Case Diagram     — actors and system features
├── Sequence Diagram     — message flow between objects over time
├── Activity Diagram     — workflow / flowchart
└── State Machine Diagram — states and transitions of an object
```

### Class Diagram Elements

```
┌─────────────────────────┐
│      <<interface>>       │   Stereotype
│       Flyable            │   Interface name
├─────────────────────────┤
│                          │   No attributes
├─────────────────────────┤
│ + fly(): void            │   Method
└─────────────────────────┘

┌─────────────────────────┐
│        Employee          │   Class name
├─────────────────────────┤
│ - name: String           │   Private attribute
│ - salary: double         │   Private attribute
│ # id: int                │   Protected attribute
├─────────────────────────┤
│ + getName(): String      │   Public method
│ + setSalary(d: double)   │   Public method
│ - validate(): boolean    │   Private method
└─────────────────────────┘

Visibility:
+ public    - private    # protected    ~ package-private

Relationships:
─────────▶  Association (uses)
─ ─ ─ ─ ─▶ Dependency (depends on)
━━━━━━━━▶  Inheritance (extends)    solid line, hollow arrow
┄ ┄ ┄ ┄ ▶  Implementation (implements)  dashed line, hollow arrow
◆─────────  Composition (owns, can't exist alone)
◇─────────  Aggregation (has, can exist independently)
```

### Example Class Diagram

```
        ┌───────────┐
        │  Animal    │  (abstract)
        │───────────│
        │# name     │
        │───────────│
        │+ speak()  │  (abstract)
        └─────┬─────┘
              │ extends
     ┌────────┴────────┐
     │                 │
┌────▼────┐     ┌──────▼──┐
│   Dog    │     │   Cat    │
│─────────│     │─────────│
│─────────│     │─────────│
│+ speak() │     │+ speak() │
└──────────┘     └──────────┘
```

---

## 3. Singleton Pattern

**Singleton** ensures a class has **exactly one instance** and provides a global access point.

### When to Use
- Database connection pool (one pool shared by all)
- Logger (one logger for the app)
- Configuration manager
- Thread pool

### Basic Singleton (NOT thread-safe)

```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {}  // Private constructor — can't create from outside
    
    public static Singleton getInstance() {
        if (instance == null) {          // Thread A checks: null
            instance = new Singleton();  // Thread A creates
        }                                // Thread B also checks: null (race condition!)
        return instance;                 // Thread B creates ANOTHER instance! ❌
    }
}
```

### Thread-Safe Singleton (Synchronized)

```java
public class Singleton {
    private static Singleton instance;
    
    private Singleton() {}
    
    public static synchronized Singleton getInstance() {  // Lock every call
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
    // ❌ Problem: synchronized is slow, and after first creation it's unnecessary
}
```

### Double-Checked Locking (Best)

```java
public class Singleton {
    // volatile ensures all threads see the updated instance
    private static volatile Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {                // First check (no locking — fast)
            synchronized (Singleton.class) {   // Lock only during creation
                if (instance == null) {        // Second check (inside lock — safe)
                    instance = new Singleton();
                }
            }
        }
        return instance;  // After creation, no locking needed!
    }
}
```

### Enum Singleton (Simplest & Best — recommended by Joshua Bloch)

```java
public enum Singleton {
    INSTANCE;
    
    private int count = 0;
    
    public void doSomething() {
        count++;
        System.out.println("Count: " + count);
    }
}

// Usage:
Singleton.INSTANCE.doSomething();

// Why is this the best?
// 1. Thread-safe (JVM guarantees enum instantiation is thread-safe)
// 2. Serialization-safe (enum prevents creating new instances on deserialization)
// 3. Reflection-safe (can't create enum instances via reflection)
// 4. Super simple!
```

---

## 4. Factory Pattern

**Factory** creates objects without exposing creation logic to the client.

### Simple Factory

```java
// Product interface
public interface Shape {
    void draw();
}

// Concrete products
public class Circle implements Shape {
    public void draw() { System.out.println("Drawing Circle"); }
}

public class Rectangle implements Shape {
    public void draw() { System.out.println("Drawing Rectangle"); }
}

public class Triangle implements Shape {
    public void draw() { System.out.println("Drawing Triangle"); }
}

// Factory — creates objects based on input
public class ShapeFactory {
    public static Shape createShape(String type) {
        return switch (type.toLowerCase()) {
            case "circle" -> new Circle();
            case "rectangle" -> new Rectangle();
            case "triangle" -> new Triangle();
            default -> throw new IllegalArgumentException("Unknown shape: " + type);
        };
    }
}

// Usage — client doesn't know about concrete classes!
Shape s1 = ShapeFactory.createShape("circle");
s1.draw();   // "Drawing Circle"

Shape s2 = ShapeFactory.createShape("triangle");
s2.draw();   // "Drawing Triangle"
```

### Factory Method Pattern

```java
// The factory itself is abstract — subclasses decide what to create
public abstract class Document {
    public abstract Page createPage();  // Factory method
    
    public void addPage() {
        Page page = createPage();       // Subclass decides which Page
        pages.add(page);
    }
}

public class Resume extends Document {
    @Override
    public Page createPage() {
        return new ResumePage();         // Creates resume-specific pages
    }
}

public class Report extends Document {
    @Override
    public Page createPage() {
        return new ReportPage();         // Creates report-specific pages
    }
}
```

### Abstract Factory Pattern

```java
// Family of related objects
public interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

public class WindowsFactory implements GUIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

public class MacFactory implements GUIFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}

// Usage:
GUIFactory factory = isWindows ? new WindowsFactory() : new MacFactory();
Button btn = factory.createButton();     // Platform-specific button!
Checkbox chk = factory.createCheckbox(); // Platform-specific checkbox!
```

---

## 5. Structural Patterns

Structural patterns deal with **composing classes/objects** into larger structures.

### Adapter Pattern

**Converts one interface into another** that a client expects. Like a power adapter!

```java
// Existing interface that client uses
public interface MediaPlayer {
    void play(String filename);
}

// Third-party library with a DIFFERENT interface
public class VLCPlayer {
    public void playVLC(String filename) {
        System.out.println("Playing VLC: " + filename);
    }
}

// Adapter — makes VLCPlayer look like a MediaPlayer
public class VLCAdapter implements MediaPlayer {
    private VLCPlayer vlcPlayer = new VLCPlayer();
    
    @Override
    public void play(String filename) {
        vlcPlayer.playVLC(filename);  // Delegates to the adapted class
    }
}

// Client code — only knows about MediaPlayer
MediaPlayer player = new VLCAdapter();
player.play("movie.vlc");   // Works seamlessly!
```

### Decorator Pattern

**Adds behavior to objects dynamically** without modifying the original class.

```java
// Base interface
public interface Coffee {
    String getDescription();
    double getCost();
}

// Concrete base
public class SimpleCoffee implements Coffee {
    public String getDescription() { return "Simple coffee"; }
    public double getCost() { return 1.00; }
}

// Decorator base
public abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;
    
    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }
}

// Concrete decorators
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) { super(coffee); }
    public String getDescription() { return decoratedCoffee.getDescription() + ", milk"; }
    public double getCost() { return decoratedCoffee.getCost() + 0.50; }
}

public class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) { super(coffee); }
    public String getDescription() { return decoratedCoffee.getDescription() + ", sugar"; }
    public double getCost() { return decoratedCoffee.getCost() + 0.25; }
}

// Usage — stack decorators!
Coffee coffee = new SimpleCoffee();                          // $1.00
coffee = new MilkDecorator(coffee);                          // $1.50
coffee = new SugarDecorator(coffee);                         // $1.75
System.out.println(coffee.getDescription() + " $" + coffee.getCost());
// "Simple coffee, milk, sugar $1.75"
```

### Proxy Pattern

**Controls access to another object.** A placeholder.

```java
public interface Image {
    void display();
}

// Real object (expensive to create)
public class RealImage implements Image {
    private String filename;
    
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();  // SLOW — loads image from disk
    }
    
    private void loadFromDisk() {
        System.out.println("Loading " + filename + " from disk...");
    }
    
    public void display() { System.out.println("Displaying " + filename); }
}

// Proxy — delays creation until needed (lazy loading)
public class ProxyImage implements Image {
    private String filename;
    private RealImage realImage;   // Created only when needed
    
    public ProxyImage(String filename) {
        this.filename = filename;  // No loading yet!
    }
    
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);  // Load only on first display!
        }
        realImage.display();
    }
}
```

---

## 6. Behavioral Patterns

Behavioral patterns deal with **communication between objects**.

### Observer Pattern

**One-to-many dependency**: when one object changes, all dependents are notified.

```java
import java.util.*;

// Subject (Observable)
public class NewsAgency {
    private String news;
    private List<NewsSubscriber> subscribers = new ArrayList<>();
    
    public void addSubscriber(NewsSubscriber sub) {
        subscribers.add(sub);
    }
    
    public void removeSubscriber(NewsSubscriber sub) {
        subscribers.remove(sub);
    }
    
    public void setNews(String news) {
        this.news = news;
        notifyAllSubscribers();  // Tell everyone!
    }
    
    private void notifyAllSubscribers() {
        for (NewsSubscriber sub : subscribers) {
            sub.update(news);
        }
    }
}

// Observer
public interface NewsSubscriber {
    void update(String news);
}

public class EmailAlert implements NewsSubscriber {
    public void update(String news) {
        System.out.println("Email Alert: " + news);
    }
}

public class PhoneNotification implements NewsSubscriber {
    public void update(String news) {
        System.out.println("Phone Notification: " + news);
    }
}

// Usage:
NewsAgency agency = new NewsAgency();
agency.addSubscriber(new EmailAlert());
agency.addSubscriber(new PhoneNotification());
agency.setNews("Breaking: Java 25 released!");
// Output:
// Email Alert: Breaking: Java 25 released!
// Phone Notification: Breaking: Java 25 released!
```

### Strategy Pattern

**Defines a family of algorithms** and makes them interchangeable at runtime.

```java
// Strategy interface
public interface SortStrategy {
    void sort(int[] array);
}

// Concrete strategies
public class BubbleSort implements SortStrategy {
    public void sort(int[] array) {
        System.out.println("Sorting with Bubble Sort");
        // bubble sort implementation
    }
}

public class QuickSort implements SortStrategy {
    public void sort(int[] array) {
        System.out.println("Sorting with Quick Sort");
        // quick sort implementation
    }
}

// Context — uses a strategy
public class Sorter {
    private SortStrategy strategy;
    
    public void setStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void sort(int[] array) {
        strategy.sort(array);
    }
}

// Usage — swap algorithms at runtime!
Sorter sorter = new Sorter();
int[] data = {5, 3, 8, 1, 2};

sorter.setStrategy(new BubbleSort());
sorter.sort(data);   // Uses bubble sort

sorter.setStrategy(new QuickSort());
sorter.sort(data);   // Now uses quick sort — same interface!
```

### Iterator Pattern

Provides a way to access elements of a collection **sequentially** without exposing internals.

```java
// Java's Iterator is exactly this pattern!
List<String> names = List.of("Alice", "Bob", "Charlie");
Iterator<String> it = names.iterator();

while (it.hasNext()) {
    String name = it.next();
    System.out.println(name);
}

// Custom iterator:
public class NumberRange implements Iterable<Integer> {
    private int start, end;
    
    public NumberRange(int start, int end) {
        this.start = start;
        this.end = end;
    }
    
    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<>() {
            private int current = start;
            
            public boolean hasNext() { return current <= end; }
            public Integer next() { return current++; }
        };
    }
}

// Usage:
for (int n : new NumberRange(1, 5)) {
    System.out.println(n);  // 1, 2, 3, 4, 5
}
```

---

## 7. Pattern Summary

| Pattern | Type | Purpose | Real-World Analogy |
|---|---|---|---|
| **Singleton** | Creational | One instance only | President of a country |
| **Factory** | Creational | Create without specifying class | Restaurant — order "burger", kitchen decides how to make it |
| **Abstract Factory** | Creational | Family of related objects | Furniture factory — modern set or Victorian set |
| **Adapter** | Structural | Convert interface | Power adapter (US plug → EU socket) |
| **Decorator** | Structural | Add behavior dynamically | Adding toppings to pizza |
| **Proxy** | Structural | Control access | Security guard at a building |
| **Observer** | Behavioral | One-to-many notification | Newsletter subscription |
| **Strategy** | Behavioral | Swap algorithms | Google Maps — driving, walking, or cycling route |
| **Iterator** | Behavioral | Sequential access | TV remote channel-by-channel |

### When to Use What

```
Need exactly one instance?                        → Singleton
Need to create objects without specifying class?  → Factory
Need to convert one interface to another?         → Adapter
Need to add features without modifying class?     → Decorator
Need to control/delay access to an object?        → Proxy
Need to notify many objects of a change?          → Observer
Need to swap algorithms at runtime?               → Strategy
Need to traverse a collection uniformly?          → Iterator
```

---

*Previous: [07-Java-JDBC-Networking.md](07-Java-JDBC-Networking.md)*
*Next: [09-JUnit-Testing.md](09-JUnit-Testing.md)*
