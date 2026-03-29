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
│      <<interface>>      │   Stereotype
│       Flyable           │   Interface name
├─────────────────────────┤
│                         │   No attributes
├─────────────────────────┤
│ + fly(): void           │   Method
└─────────────────────────┘

┌─────────────────────────┐
│        Employee         │   Class name
├─────────────────────────┤
│ - name: String          │   Private attribute
│ - salary: double        │   Private attribute
│ # id: int               │   Protected attribute
├─────────────────────────┤
│ + getName(): String     │   Public method
│ + setSalary(d: double)  │   Public method
│ - validate(): boolean   │   Private method
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
        │  Animal   │  (abstract)
        │───────────│
        │# name     │
        │───────────│
        │+ speak()  │  (abstract)
        └─────┬─────┘
              │ extends
     ┌────────┴─────────┐
     │                  │
┌────▼─────┐     ┌──────▼───┐
│   Dog    │     │    Cat   │
│──────────│     │──────────│
│──────────│     │──────────│
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

Think of it like subscribing to a YouTube channel. When the YouTuber uploads a new video, ALL subscribers get notified automatically. The YouTuber doesn't need to know who the subscribers are — they just broadcast "new video!" and everyone who subscribed gets the update.

```java
import java.util.*;

// Subject (Observable) — the "YouTuber"
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

// Observer — the "subscriber"
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

Think of Google Maps: you enter a destination, then choose driving, walking, or cycling. The app uses a DIFFERENT algorithm for each, but the interface is the same — you still get directions from A to B. That's the Strategy pattern.

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

## 7. Builder Pattern

### The Problem

Some objects have **many parameters**, some required, some optional. Using a constructor with 10 parameters is confusing and error-prone — which number was the age and which was the zipcode?

```java
// ❌ BAD — "Telescoping Constructor" anti-pattern
// What do these numbers mean?! Which is age, which is zipcode?
User user = new User("Kunal", "kunal@email.com", 25, "123 Main St", 
                      "Mumbai", "MH", 400001, "India", "1234567890", true);
```

### The Solution: Builder Pattern

The Builder pattern lets you construct an object **step by step** with clear, readable method names.

```java
public class User {
    // Required fields
    private final String name;
    private final String email;
    
    // Optional fields
    private final int age;
    private final String address;
    private final String phone;
    private final boolean isActive;

    // Private constructor — can ONLY be called from the Builder
    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.age = builder.age;
        this.address = builder.address;
        this.phone = builder.phone;
        this.isActive = builder.isActive;
    }

    // The Builder — a static inner class
    public static class Builder {
        // Required fields
        private final String name;
        private final String email;
        
        // Optional fields with defaults
        private int age = 0;
        private String address = "";
        private String phone = "";
        private boolean isActive = true;

        // Builder constructor takes REQUIRED fields only
        public Builder(String name, String email) {
            this.name = name;
            this.email = email;
        }

        // Each optional field has a setter that returns the Builder itself
        // This enables "method chaining" (fluent API)
        public Builder age(int age) {
            this.age = age;
            return this;          // ← returns "this" so you can chain!
        }

        public Builder address(String address) {
            this.address = address;
            return this;
        }

        public Builder phone(String phone) {
            this.phone = phone;
            return this;
        }

        public Builder isActive(boolean isActive) {
            this.isActive = isActive;
            return this;
        }

        // The build() method creates the actual User object
        public User build() {
            return new User(this);
        }
    }
}
```

```java
// ✅ GOOD — Builder pattern usage. Clear and readable!
User user = new User.Builder("Kunal", "kunal@email.com")
    .age(25)
    .address("123 Main St, Mumbai")
    .phone("1234567890")
    .isActive(true)
    .build();

// Only required fields? No problem!
User minimalUser = new User.Builder("Priya", "priya@email.com").build();

// You can pick and choose which optional fields to set:
User partialUser = new User.Builder("Amit", "amit@email.com")
    .age(30)
    .build();   // address, phone use defaults
```

**Real-world Java examples of the Builder pattern:**
- `StringBuilder` — build strings step by step
- `Stream.builder()` — build streams
- `HttpRequest.newBuilder()` — Java 11 HTTP client
- Lombok's `@Builder` annotation — generates all this code for you!

```java
// With Lombok (saves you from writing the Builder class manually):
@Builder
public class User {
    private String name;
    private String email;
    private int age;
    private String address;
}

// Usage is the same:
User user = User.builder()
    .name("Kunal")
    .email("kunal@email.com")
    .age(25)
    .build();
```

---

## 8. Facade Pattern

### What is a Facade?

A **Facade** (pronounced "fuh-SAHD") provides a **simple interface** to a complex system. Think of it like a TV remote — you press "ON" and the TV turns on, selects the right input, adjusts volume, and connects to Wi-Fi. You don't care about all those internal steps; you just want to watch TV. The remote is the facade.

```
Without Facade (client talks to many complex subsystems):

   Your Code
     │
     ├──► SubsystemA.init()
     ├──► SubsystemA.configure(config)
     ├──► SubsystemB.connect()
     ├──► SubsystemB.authenticate(user, pass)
     ├──► SubsystemC.loadData()
     └──► SubsystemC.transform()
     
   You need to know about ALL subsystems and their details. Complex!

With Facade (client talks to ONE simple interface):

   Your Code ──► Facade.doEverything()
                   │
                   ├──► SubsystemA.init()
                   ├──► SubsystemA.configure(config)
                   ├──► SubsystemB.connect()
                   ├──► SubsystemB.authenticate(user, pass)
                   ├──► SubsystemC.loadData()
                   └──► SubsystemC.transform()
                   
   ONE method call. The complexity is hidden behind the Facade.
```

### Example: Home Theater Facade

```java
// Complex subsystems:
class DVDPlayer {
    void on() { System.out.println("DVD Player ON"); }
    void play(String movie) { System.out.println("Playing: " + movie); }
    void off() { System.out.println("DVD Player OFF"); }
}

class Projector {
    void on() { System.out.println("Projector ON"); }
    void setInput(String input) { System.out.println("Input set to: " + input); }
    void off() { System.out.println("Projector OFF"); }
}

class SoundSystem {
    void on() { System.out.println("Sound System ON"); }
    void setVolume(int level) { System.out.println("Volume: " + level); }
    void off() { System.out.println("Sound System OFF"); }
}

class Lights {
    void dim(int level) { System.out.println("Lights dimmed to: " + level + "%"); }
    void on() { System.out.println("Lights ON"); }
}

// ✅ Facade — hides all the complexity behind ONE simple method
class HomeTheaterFacade {
    private DVDPlayer dvd;
    private Projector projector;
    private SoundSystem sound;
    private Lights lights;

    public HomeTheaterFacade(DVDPlayer dvd, Projector projector,
                             SoundSystem sound, Lights lights) {
        this.dvd = dvd;
        this.projector = projector;
        this.sound = sound;
        this.lights = lights;
    }

    // One method to rule them all!
    public void watchMovie(String movie) {
        System.out.println("--- Setting up movie night ---");
        lights.dim(10);
        projector.on();
        projector.setInput("DVD");
        sound.on();
        sound.setVolume(50);
        dvd.on();
        dvd.play(movie);
    }

    public void endMovie() {
        System.out.println("--- Shutting down ---");
        dvd.off();
        sound.off();
        projector.off();
        lights.on();
    }
}

// Usage — SO much simpler!
HomeTheaterFacade theater = new HomeTheaterFacade(dvd, projector, sound, lights);
theater.watchMovie("The Matrix");  // One call does EVERYTHING!
theater.endMovie();                // Clean shutdown in one call!
```

**Where you see Facade in real Java:**
- `javax.faces.context.FacesContext` — in JSF (Java Server Faces)
- Spring's `JdbcTemplate` — facade over raw JDBC (no more Connection/Statement/ResultSet management!)
- SLF4J — facade over multiple logging frameworks

---

## 9. Template Method Pattern

### What is it?

The **Template Method** pattern defines the **skeleton of an algorithm** in a base class, but lets subclasses **fill in specific steps** without changing the overall structure.

Think of making a hot beverage: whether it's tea or coffee, the steps are the same:
1. Boil water
2. Brew the drink (tea leaves or coffee grounds — this step DIFFERS)
3. Pour into cup
4. Add condiments (lemon or milk — this step DIFFERS)

The template (overall recipe) stays the same, but some steps are customized.

```java
// Abstract class with the TEMPLATE METHOD
abstract class HotBeverage {
    
    // THIS is the template method — defines the algorithm's structure
    // It's "final" so subclasses can't change the overall order!
    public final void prepare() {
        boilWater();          // Step 1 — same for everyone
        brew();               // Step 2 — DIFFERENT for each drink
        pourInCup();          // Step 3 — same for everyone
        addCondiments();      // Step 4 — DIFFERENT for each drink
    }
    
    private void boilWater() {
        System.out.println("Boiling water...");
    }
    
    private void pourInCup() {
        System.out.println("Pouring into cup...");
    }
    
    // Abstract methods — subclasses MUST implement these
    protected abstract void brew();
    protected abstract void addCondiments();
}

// Concrete class — Tea
class Tea extends HotBeverage {
    protected void brew() {
        System.out.println("Steeping tea bag for 3 minutes");
    }
    
    protected void addCondiments() {
        System.out.println("Adding lemon");
    }
}

// Concrete class — Coffee
class Coffee extends HotBeverage {
    protected void brew() {
        System.out.println("Dripping coffee through filter");
    }
    
    protected void addCondiments() {
        System.out.println("Adding sugar and milk");
    }
}

// Usage:
HotBeverage myDrink = new Tea();
myDrink.prepare();
// Output:
// Boiling water...
// Steeping tea bag for 3 minutes
// Pouring into cup...
// Adding lemon

HotBeverage myOtherDrink = new Coffee();
myOtherDrink.prepare();
// Output:
// Boiling water...
// Dripping coffee through filter
// Pouring into cup...
// Adding sugar and milk
```

**Where you see Template Method in real Java:**
- `HttpServlet` — you override `doGet()`, `doPost()`, but the servlet container calls `service()` which routes to them
- `JUnit` — the test runner calls `@BeforeEach` → test method → `@AfterEach` (you just fill in the test)
- Spring's `JdbcTemplate` — the template handles connection/exception/closing, you just provide the SQL and row mapping

---

## 10. Pattern Summary

| Pattern | Type | Purpose | Real-World Analogy |
|---|---|---|---|
| **Singleton** | Creational | One instance only | President of a country |
| **Factory** | Creational | Create without specifying class | Restaurant — order "burger", kitchen decides how to make it |
| **Abstract Factory** | Creational | Family of related objects | Furniture factory — modern set or Victorian set |
| **Builder** | Creational | Construct complex objects step by step | Subway sandwich — choose bread, meat, veggies, sauce |
| **Adapter** | Structural | Convert interface | Power adapter (US plug → EU socket) |
| **Decorator** | Structural | Add behavior dynamically | Adding toppings to pizza |
| **Facade** | Structural | Simple interface to complex system | TV remote — one button does many things |
| **Proxy** | Structural | Control access | Security guard at a building |
| **Observer** | Behavioral | One-to-many notification | YouTube subscription |
| **Strategy** | Behavioral | Swap algorithms | Google Maps — driving, walking, or cycling route |
| **Template Method** | Behavioral | Define algorithm skeleton, fill in steps | Recipe — same steps, different ingredients |
| **Iterator** | Behavioral | Sequential access | TV remote channel-by-channel |

### When to Use What

```
Need exactly one instance?                        → Singleton
Need to create objects without specifying class?  → Factory
Need to build objects step by step?               → Builder
Need to convert one interface to another?         → Adapter
Need to add features without modifying class?     → Decorator
Need a simple interface to complex subsystem?     → Facade
Need to control/delay access to an object?        → Proxy
Need to notify many objects of a change?          → Observer
Need to swap algorithms at runtime?               → Strategy
Need same algorithm structure, different steps?   → Template Method
Need to traverse a collection uniformly?          → Iterator
```

---

*Previous: [07-Java-JDBC-Networking.md](07-Java-JDBC-Networking.md)*
*Next: [09-JUnit-Testing.md](09-JUnit-Testing.md)*
