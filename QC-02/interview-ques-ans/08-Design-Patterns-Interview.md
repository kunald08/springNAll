# Design Patterns & SOLID — Interview Questions & Answers

> **How to use this:** Answers are written exactly how you should speak in an interview — clear, confident, and concise.

---

## 1. What are SOLID principles? Explain each briefly.

**Answer:**

- **S — Single Responsibility:** A class should have only one reason to change. For example, an `Employee` class shouldn't handle tax calculation, report generation, AND database persistence. Each responsibility should be a separate class.

- **O — Open/Closed:** Classes should be open for extension, closed for modification. Instead of modifying existing code with if-else for each new type, I use polymorphism — new behavior is added by creating new classes that implement an interface.

- **L — Liskov Substitution:** Subclasses must be substitutable for their parent class without breaking behavior. The classic violation is Square extending Rectangle — setting width on a Square also changes height, breaking the Rectangle contract.

- **I — Interface Segregation:** Don't force classes to implement interfaces they don't use. Split fat interfaces into focused ones. A `Robot` shouldn't have to implement `eat()` and `sleep()` from a `Worker` interface.

- **D — Dependency Inversion:** Depend on abstractions, not concrete implementations. High-level modules shouldn't depend on low-level modules. For example, `NotificationManager` should depend on a `NotificationService` interface, not directly on `EmailService`.

---

## 2. What is the Singleton pattern? How do you implement it?

**Answer:**
Singleton ensures a class has **exactly one instance** with a global access point. I use it for shared resources like connection pools, loggers, or configuration managers.

The best implementation is the **enum singleton**:
```java
public enum Singleton {
    INSTANCE;
    public void doSomething() { ... }
}
```

It's thread-safe (JVM guarantees), serialization-safe, and reflection-safe — all for free.

Alternatively, **double-checked locking**:
```java
private static volatile Singleton instance;
public static Singleton getInstance() {
    if (instance == null) {
        synchronized (Singleton.class) {
            if (instance == null) {
                instance = new Singleton();
            }
        }
    }
    return instance;
}
```
The `volatile` keyword is crucial — without it, the JVM might reorder the instance construction, letting another thread see a partially constructed object.

---

## 3. What is the Factory pattern?

**Answer:**
Factory creates objects without exposing the creation logic. The client asks for a product by type, and the factory decides which concrete class to instantiate.

```java
public class ShapeFactory {
    public static Shape createShape(String type) {
        return switch (type) {
            case "circle" -> new Circle();
            case "rectangle" -> new Rectangle();
            default -> throw new IllegalArgumentException("Unknown: " + type);
        };
    }
}

Shape s = ShapeFactory.createShape("circle");  // Client doesn't know about Circle class
```

Benefits: decouples client from concrete classes, centralizes object creation, makes it easy to add new types. Used extensively in frameworks — `Calendar.getInstance()`, `NumberFormat.getInstance()`.

The **Abstract Factory** goes further — it creates families of related objects (e.g., a `GUIFactory` that produces platform-specific Buttons and Checkboxes).

---

## 4. What is the Observer pattern?

**Answer:**
Observer defines a **one-to-many dependency** — when one object (subject) changes state, all its dependents (observers) are notified automatically.

Real-world example: a news agency publishes news, and all subscribers (email alerts, phone notifications, SMS) get notified.

```java
interface Observer { void update(String data); }

class NewsAgency {
    List<Observer> observers = new ArrayList<>();
    void addObserver(Observer o) { observers.add(o); }
    void publish(String news) {
        observers.forEach(o -> o.update(news));
    }
}
```

Used in: event listeners (GUI), publish-subscribe messaging, MVC pattern, reactive programming.

---

## 5. What is the Strategy pattern?

**Answer:**
Strategy defines a family of algorithms, encapsulates each one, and makes them **interchangeable at runtime**.

Example: A payment system that can use CreditCard, PayPal, or Crypto:
```java
interface PaymentStrategy { void pay(double amount); }
class CreditCardPayment implements PaymentStrategy { ... }
class PayPalPayment implements PaymentStrategy { ... }

class ShoppingCart {
    void checkout(PaymentStrategy strategy) {
        strategy.pay(total);  // Algorithm is swapped at runtime
    }
}
```

The client chooses which strategy to use without the cart knowing the implementation details. This follows the Open/Closed principle.

---

## 6. What is the Decorator pattern?

**Answer:**
Decorator **adds behavior to objects dynamically** by wrapping them, without modifying the original class. Each decorator implements the same interface and wraps another instance.

Classic example — I/O streams in Java:
```java
new BufferedReader(new InputStreamReader(new FileInputStream("file.txt")))
```
Each wrapper adds functionality: `FileInputStream` reads bytes, `InputStreamReader` converts to characters, `BufferedReader` adds buffering.

Another example: adding toppings to coffee — each topping wraps the base coffee and adds cost.

---

## 7. What is the Adapter pattern?

**Answer:**
Adapter converts one interface into another that a client expects. It's like a power adapter — a US plug works in a European socket through an adapter.

Use case: I have a third-party library with a different interface than what my code expects. Instead of modifying my code or the library, I create an adapter class that implements my interface and internally delegates to the library.

```java
// My code expects MediaPlayer.play()
// Third-party has VLCPlayer.playVLC()
class VLCAdapter implements MediaPlayer {
    VLCPlayer vlc = new VLCPlayer();
    void play(String file) { vlc.playVLC(file); }  // Delegate
}
```

---

## 8. What is the Proxy pattern?

**Answer:**
Proxy provides a **surrogate or placeholder** for another object to control access to it.

Types:
- **Virtual Proxy** — delays expensive object creation (lazy loading). Load an image only when it's actually displayed.
- **Protection Proxy** — controls access based on permissions.
- **Remote Proxy** — represents an object in a different address space (like RMI).

```java
class ProxyImage implements Image {
    private RealImage realImage;  // Created only when needed
    void display() {
        if (realImage == null) realImage = new RealImage(file);  // Lazy load
        realImage.display();
    }
}
```

---

## 9. What is UML? What diagrams do you know?

**Answer:**
UML (Unified Modeling Language) is a standardized notation for visualizing system design.

Key diagrams I use:
- **Class diagram** — shows classes, their attributes/methods, and relationships (inheritance, composition, association). Most commonly used.
- **Sequence diagram** — shows how objects interact over time (method calls, responses).
- **Use case diagram** — shows actors and system functionality at a high level.
- **Activity diagram** — flowcharts showing workflow.

In class diagrams, `+` means public, `-` means private, `#` means protected. A solid arrow with hollow triangle is inheritance, a dashed arrow is implementation.

---

## 10. When would you use composition over inheritance?

**Answer:**
The principle "**favor composition over inheritance**" means instead of making class B inherit from class A, I make class B CONTAIN an instance of class A.

Reasons:
- Inheritance creates tight coupling — changes in the parent break subclasses
- Java only allows single inheritance
- Composition is more flexible — I can swap components at runtime
- Inheritance represents "IS-A," but many relationships are "HAS-A"

Example: Instead of `ElectricCar extends Car extends Vehicle`, I'd compose: `Car` has an `Engine` (which can be `ElectricEngine` or `GasEngine`). This way, I can change the engine type without changing the Car hierarchy.

---

*Back to: [08-Design-Patterns-SOLID.md](../08-Design-Patterns-SOLID.md)*
