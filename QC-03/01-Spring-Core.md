# Spring Core - Complete In-Depth Guide

## Table of Contents
- [1. Introduction to Spring Framework](#1-introduction-to-spring-framework)
- [2. Why Spring? The Problem It Solves](#2-why-spring-the-problem-it-solves)
- [3. Spring Modules Overview](#3-spring-modules-overview)
- [4. Spring Container](#4-spring-container)
- [5. Inversion of Control (IoC)](#5-inversion-of-control-ioc)
- [6. Dependency Injection (DI)](#6-dependency-injection-di)
- [7. Constructor Injection](#7-constructor-injection)
- [8. Setter Injection](#8-setter-injection)
- [9. Field Injection](#9-field-injection)
- [10. @Autowired Annotation](#10-autowired-annotation)
- [11. @Qualifier Annotation](#11-qualifier-annotation)
- [12. @Primary Annotation](#12-primary-annotation)
- [13. @Component and @Service](#13-component-and-service)
- [14. @Repository and @Controller](#14-repository-and-controller)
- [15. Bean Scopes](#15-bean-scopes)
- [16. Bean Lifecycle](#16-bean-lifecycle)

---

## 1. Introduction to Spring Framework

### What is Spring?

Spring is a **Java framework** that makes it easy to build Java applications. Think of it as a **helpful assistant** that manages your Java objects and their relationships so you don't have to do it manually.

### Simple Analogy

Imagine you are building a car:
- **Without Spring**: You manually create the engine, wheels, seats, steering — and you connect them all yourself. If the engine type changes, you have to go everywhere in your code and change it.
- **With Spring**: You tell Spring "I need a car with an engine, 4 wheels, and a steering." Spring creates all the parts and assembles them for you. If you want a different engine, you just change one configuration, and Spring handles the rest.

### History
- Created by **Rod Johnson** in **2003**
- Built as an alternative to the heavy and complex **Java EE (Enterprise Edition)** / EJB (Enterprise JavaBeans)
- It is **open source** and **lightweight**

### Key Features
| Feature | What It Means |
|---------|--------------|
| Lightweight | Does not force you to use everything, use only what you need |
| IoC Container | Manages object creation and wiring for you |
| AOP Support | Separates cross-cutting concerns (logging, security) |
| Modular | Pick the modules you need |
| Testable | Easy to write unit tests |

---

## 2. Why Spring? The Problem It Solves

### The Problem: Tight Coupling

In normal Java, when one class needs another class, you create it directly:

```java
public class Car {
    // Car is TIGHTLY COUPLED to PetrolEngine
    private PetrolEngine engine = new PetrolEngine();
    
    public void drive() {
        engine.start();
        System.out.println("Car is moving...");
    }
}
```

**Problems with this approach:**
1. If you want to switch to `DieselEngine`, you have to modify the `Car` class
2. You cannot test `Car` without a real `PetrolEngine`
3. `Car` is **tightly coupled** to `PetrolEngine`

### The Solution: Loose Coupling with Spring

```java
public class Car {
    // Car only knows about the Engine INTERFACE, not a specific implementation
    private Engine engine;
    
    // Spring will INJECT the right engine for you
    public Car(Engine engine) {
        this.engine = engine;
    }
    
    public void drive() {
        engine.start();
        System.out.println("Car is moving...");
    }
}
```

Now `Car` does not care which engine it gets. Spring decides and provides it. This is **loose coupling**.

---

## 3. Spring Modules Overview

Spring is not one big thing. It is divided into **modules**. You pick what you need.

```
┌──────────────────────────────────────────────────────┐
│                   SPRING FRAMEWORK                   │
├──────────┬──────────┬──────────┬──────────┬──────────┤
│   Core   │   AOP    │   Data   │   MVC    │ Security │
│Container │ (Aspect  │ (JDBC,   │ (Web     │ (Auth,   │
│  IoC,DI  │ Oriented │  JPA,    │ Layer,   │ Access   │
│  Beans   │ Prog.)   │  ORM)    │ REST)    │ Control) │
└──────────┴──────────┴──────────┴──────────┴──────────┘
```

### 3.1 Core Module
- **Heart of Spring**
- Provides IoC (Inversion of Control) and DI (Dependency Injection)
- Manages the lifecycle of beans (objects)
- Contains: `BeanFactory`, `ApplicationContext`

### 3.2 AOP Module (Aspect-Oriented Programming)
- Handles **cross-cutting concerns** — things that apply across many classes
- Examples: Logging, Security checks, Transaction management
- Instead of writing logging code in every method, you write it once and apply it everywhere

### 3.3 Data Module
- Simplifies working with databases
- Includes: **JDBC Template**, **JPA support**, **ORM integration**
- Handles database connections, error handling, and resource cleanup for you

### 3.4 MVC Module (Model-View-Controller)
- For building **web applications**
- Handles HTTP requests, routing, form processing
- Works with view technologies like Thymeleaf, JSP

### 3.5 Security Module
- Handles **authentication** (who are you?) and **authorization** (what can you do?)
- Protects web applications, REST APIs
- Handles login, logout, role-based access

---

## 4. Spring Container

### What is the Spring Container?

The Spring Container is the **core** of the Spring Framework. It is responsible for:
1. **Creating** objects (called beans)
2. **Configuring** them
3. **Managing** their complete lifecycle (birth to death)
4. **Wiring** them together (connecting dependencies)

### Behind the Scenes: How It Works

```
┌──────────────────────────────────────────────────────┐
│              SPRING CONTAINER                        │
│                                                      │
│  1. Read Configuration (XML / Annotations / Java)    │
│  2. Create Bean Definitions (metadata)               │
│  3. Create Bean Instances (actual objects)           │
│  4. Inject Dependencies (wire beans together)        │
│  5. Manage Lifecycle (init → use → destroy)          │
│                                                      │
│  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐                  │
│  │Bean1│  │Bean2│  │Bean3│  │Bean4│                  │
│  └─────┘  └─────┘  └─────┘  └─────┘                  │
└──────────────────────────────────────────────────────┘
```

### Two Types of Spring Container

#### 4.1 BeanFactory (Basic Container)
- **Lightweight** container
- Creates beans **lazily** (only when you ask for them)
- Uses less memory
- Basic features only

```java
// Using BeanFactory (rarely used directly)
BeanFactory factory = new XmlBeanFactory(new ClassPathResource("beans.xml"));
Car car = (Car) factory.getBean("car");
```

#### 4.2 ApplicationContext (Advanced Container) — MOST USED
- **Extension** of BeanFactory with extra features
- Creates beans **eagerly** (at startup, by default)
- Additional features:
  - Event publishing
  - Internationalization (i18n)
  - AOP support
  - Annotation-based configuration

```java
// Using ApplicationContext (PREFERRED)
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
Car car = context.getBean(Car.class);
```

### Types of ApplicationContext

| Type | When to Use |
|------|-------------|
| `ClassPathXmlApplicationContext` | Load XML config from classpath |
| `FileSystemXmlApplicationContext` | Load XML config from file system |
| `AnnotationConfigApplicationContext` | Load Java-based configuration (MODERN) |
| `WebApplicationContext` | For web applications |

### Under the Hood: Container Startup Process

```
Step 1: You create ApplicationContext
        ↓
Step 2: Spring reads your configuration (annotations/@Component scans)
        ↓
Step 3: Spring creates BeanDefinition objects (metadata about each bean)
        → Bean name, class type, scope, dependencies, init/destroy methods
        ↓
Step 4: Spring creates actual bean instances
        → Calls constructors
        ↓
Step 5: Spring injects dependencies
        → Sets fields, calls setters, or uses constructor args
        ↓
Step 6: Spring calls initialization methods
        → @PostConstruct, afterPropertiesSet(), custom init methods
        ↓
Step 7: Beans are READY to use
        ↓
Step 8: When container shuts down → calls destroy methods
        → @PreDestroy, destroy(), custom destroy methods
```

---

## 5. Inversion of Control (IoC)

### What is IoC?

**Inversion of Control** means: **You do NOT create objects yourself. The Spring Container creates them for you.**

In normal programming, YOUR code controls everything:
```java
// YOU are in control — YOU create objects
Engine engine = new PetrolEngine();
Car car = new Car(engine);
```

In Spring (IoC), the **container is in control**:
```java
// SPRING is in control — SPRING creates objects and gives them to you
@Component
public class Car {
    @Autowired
    private Engine engine;  // Spring provides this automatically
}
```

### Analogy

**Without IoC (You cook at home):**
- You go to the market
- You buy ingredients
- You cook the food
- You serve it
- You clean up

**With IoC (You go to a restaurant):**
- You just order food (tell Spring what you need)
- The restaurant (Spring Container) handles everything else
- You just eat (use the objects)

The **control is inverted** — from YOU to the FRAMEWORK.

### Under the Hood: How IoC Works

```
┌─────────────────────┐     ┌─────────────────────┐
│   Traditional Way   │     │      IoC Way        │
│                     │     │                     │
│  Your Code          │     │  Spring Container   │
│    ↓                │     │    ↓                │
│  Creates Objects    │     │  Creates Objects    │
│    ↓                │     │    ↓                │
│  Manages Lifecycle  │     │  Manages Lifecycle  │
│    ↓                │     │    ↓                │
│  Wires Dependencies │     │  Wires Dependencies │
│                     │     │    ↓                │
│                     │     │  Gives to Your Code │
└─────────────────────┘     └─────────────────────┘
```

---

## 6. Dependency Injection (DI)

### What is Dependency Injection?

**Dependency Injection is HOW Spring implements IoC.** It is a technique where Spring provides (injects) the objects that a class needs, instead of the class creating them itself.

### What is a Dependency?

If class A needs class B to work, then B is a **dependency** of A.

```java
public class Car {
    private Engine engine;  // Engine is a DEPENDENCY of Car
}
```

### Three Ways to Inject Dependencies

```
┌──────────────────────────────────────────────┐
│       Dependency Injection Methods           │
├──────────────┬──────────────┬────────────────┤
│ Constructor  │   Setter     │    Field       │
│ Injection    │  Injection   │  Injection     │
│ (RECOMMENDED)│  (Optional)  │  (Avoid)       │
└──────────────┴──────────────┴────────────────┘
```

---

## 7. Constructor Injection

### What is Constructor Injection?

Dependencies are provided through the **constructor** of the class. Spring calls the constructor with the required arguments.

### Why is it RECOMMENDED?

1. **Immutability**: You can make dependencies `final`
2. **Required dependencies**: If a dependency is missing, the app won't start — you catch errors early
3. **Testability**: Easy to test — just pass mock objects in the constructor
4. **Clear contract**: Looking at the constructor tells you exactly what the class needs

### Code Example

```java
// Step 1: Define an interface
public interface Engine {
    void start();
}

// Step 2: Create an implementation
@Component
public class PetrolEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Petrol Engine started! Vroom!");
    }
}

// Step 3: Inject via constructor
@Component
public class Car {
    
    private final Engine engine;  // 'final' because we set it once in constructor
    
    // Spring sees this constructor, finds a bean of type Engine,
    // and passes it here automatically
    @Autowired  // Optional for single constructor (Spring 4.3+)
    public Car(Engine engine) {
        this.engine = engine;
        System.out.println("Car created with engine: " + engine.getClass().getSimpleName());
    }
    
    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

### Under the Hood: What Spring Does

```
1. Spring scans for @Component classes
2. Finds PetrolEngine → creates an instance → stores in container
3. Finds Car → sees constructor needs Engine
4. Looks in container for a bean of type Engine
5. Finds PetrolEngine → passes it to Car's constructor
6. Car object is created and stored in container
```

### Main Class to Run

```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(MyApp.class, args);
        
        Car car = context.getBean(Car.class);
        car.drive();
        // Output:
        // Car created with engine: PetrolEngine
        // Petrol Engine started! Vroom!
        // Car is driving...
    }
}
```

---

## 8. Setter Injection

### What is Setter Injection?

Dependencies are provided through **setter methods** after the object is created. The object is first created with a no-args constructor, then Spring calls the setter methods.

### When to Use?

- For **optional** dependencies (the class can work without them)
- When you want to **change** the dependency later at runtime

### Code Example

```java
@Component
public class Car {
    
    private Engine engine;  // NOT final — can be changed later
    
    // Default constructor — Spring creates Car first with this
    public Car() {
        System.out.println("Car created (no engine yet)");
    }
    
    // Then Spring calls this setter to inject the dependency
    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
        System.out.println("Engine injected via setter: " + engine.getClass().getSimpleName());
    }
    
    public void drive() {
        if (engine != null) {
            engine.start();
            System.out.println("Car is driving...");
        } else {
            System.out.println("No engine! Can't drive.");
        }
    }
}
```

### Under the Hood: Step by Step

```
1. Spring creates Car using no-args constructor
   → Car object exists but engine is NULL
2. Spring finds setEngine() method with @Autowired
3. Spring looks for a bean of type Engine in the container
4. Spring calls car.setEngine(petrolEngine)
   → Now car has an engine
```

### Constructor vs Setter Injection Comparison

| Feature | Constructor Injection | Setter Injection |
|---------|----------------------|------------------|
| When to use | Required dependencies | Optional dependencies |
| Immutability | Yes (use `final`) | No |
| Object state | Fully initialized after creation | Partially initialized, completed later |
| Circular dependency | Throws error (good!) | Can handle it (risky!) |
| Recommended | YES (Spring team recommends) | For optional stuff only |

---

## 9. Field Injection

### What is Field Injection?

Dependencies are injected directly into the **field** (variable) using `@Autowired`, without any constructor or setter.

### Code Example

```java
@Component
public class Car {
    
    @Autowired  // Spring directly sets this field using reflection
    private Engine engine;
    
    public void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}
```

### Why You Should AVOID Field Injection

```
❌ Problems with Field Injection:

1. Cannot make fields 'final' → No immutability
2. Hard to test → Cannot easily pass mock objects
3. Hidden dependencies → Looking at the class, you don't know what it needs
4. Uses Java Reflection (slower, bypasses access control)
5. Cannot detect missing dependencies at startup
6. Spring team DOES NOT recommend this
```

### Under the Hood: How Field Injection Works

```
1. Spring creates Car using default constructor
2. Spring uses Java Reflection to access the private field 'engine'
   → Field.setAccessible(true)  // bypasses 'private' access
3. Spring finds a bean of type Engine
4. Spring directly sets the field value
   → field.set(carInstance, engineBean)
```

### All Three Injections Compared

```java
// ✅ BEST: Constructor Injection
@Component
public class Car {
    private final Engine engine;
    
    public Car(Engine engine) {   // Clear, testable, immutable
        this.engine = engine;
    }
}

// ⚠️ OK: Setter Injection (for optional dependencies)
@Component
public class Car {
    private Engine engine;
    
    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}

// ❌ AVOID: Field Injection
@Component
public class Car {
    @Autowired
    private Engine engine;  // Hidden, hard to test, uses reflection
}
```

---

## 10. @Autowired Annotation

### What is @Autowired?

`@Autowired` tells Spring: **"Please find the right bean and inject it here."**

It can be placed on:
- Constructor (recommended)
- Setter method
- Field (avoid)

### How @Autowired Works Under the Hood

```
When Spring sees @Autowired:

1. It looks at the TYPE of the dependency (e.g., Engine)
2. It searches the container for a bean of that type
3. Three scenarios:
   a. EXACTLY ONE bean found → Injects it ✅
   b. NO bean found → Throws NoSuchBeanDefinitionException ❌
   c. MULTIPLE beans found → Throws NoUniqueBeanDefinitionException ❌
      (Use @Qualifier or @Primary to resolve)
```

### @Autowired with required = false

```java
@Component
public class Car {
    
    private Engine engine;
    
    @Autowired(required = false)  // Won't throw error if no Engine bean exists
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
    
    public void drive() {
        if (engine != null) {
            engine.start();
        } else {
            System.out.println("No engine available, cannot drive!");
        }
    }
}
```

### @Autowired on Constructor (Since Spring 4.3)

```java
@Component
public class Car {
    
    private final Engine engine;
    
    // If there is ONLY ONE constructor, @Autowired is OPTIONAL
    // Spring automatically uses this constructor
    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

---

## 11. @Qualifier Annotation

### The Problem: Multiple Beans of Same Type

```java
public interface Engine {
    void start();
}

@Component
public class PetrolEngine implements Engine {
    public void start() { System.out.println("Petrol engine started"); }
}

@Component
public class DieselEngine implements Engine {
    public void start() { System.out.println("Diesel engine started"); }
}

@Component
public class Car {
    @Autowired
    private Engine engine;  // ❌ ERROR! Spring finds TWO Engine beans!
    // Which one should it inject? PetrolEngine or DieselEngine?
}
```

### The Solution: @Qualifier

`@Qualifier` tells Spring **exactly which bean** to inject when there are multiple candidates.

```java
@Component
public class Car {
    
    private final Engine engine;
    
    @Autowired
    public Car(@Qualifier("dieselEngine") Engine engine) {
        this.engine = engine;  // Now Spring knows to inject DieselEngine
    }
}
```

### How Bean Names Work

By default, Spring names beans using the **class name with the first letter lowercase**:
- `PetrolEngine` → bean name is `petrolEngine`
- `DieselEngine` → bean name is `dieselEngine`
- `MyService` → bean name is `myService`

You can also give a custom name:
```java
@Component("myCustomEngine")
public class PetrolEngine implements Engine { ... }

// Now use:
@Qualifier("myCustomEngine")
```

---

## 12. @Primary Annotation

### What is @Primary?

`@Primary` marks a bean as the **default choice** when multiple beans of the same type exist. If no `@Qualifier` is specified, Spring picks the `@Primary` bean.

### Code Example

```java
@Component
@Primary  // This is the DEFAULT engine
public class PetrolEngine implements Engine {
    public void start() { System.out.println("Petrol engine started"); }
}

@Component
public class DieselEngine implements Engine {
    public void start() { System.out.println("Diesel engine started"); }
}

@Component
public class Car {
    
    @Autowired
    private Engine engine;  // PetrolEngine will be injected (it has @Primary)
}
```

### @Primary vs @Qualifier

| Feature | @Primary | @Qualifier |
|---------|----------|------------|
| Purpose | Sets a DEFAULT bean | Selects a SPECIFIC bean |
| Where to place | On the bean class | On the injection point |
| Priority | Lower | Higher (overrides @Primary) |
| When to use | When one bean should be default | When you need a specific bean |

```java
// @Qualifier overrides @Primary
@Component
public class Truck {
    
    @Autowired
    @Qualifier("dieselEngine")  // Even though PetrolEngine is @Primary,
    private Engine engine;       // DieselEngine is injected because of @Qualifier
}
```

---

## 13. @Component and @Service

### @Component

`@Component` is a **generic annotation** that tells Spring: "This class is a Spring bean. Please manage it."

```java
@Component  // Spring will create and manage this object
public class EmailValidator {
    public boolean isValid(String email) {
        return email.contains("@");
    }
}
```

### Under the Hood: Component Scanning

```
When your application starts:

1. Spring looks at @SpringBootApplication (or @ComponentScan)
2. It finds the base package (e.g., com.example)
3. It scans ALL classes in that package and sub-packages
4. Any class with @Component (or its specializations) gets registered
5. Spring creates instances and stores them in the container

com.example/
├── MyApp.java              ← @SpringBootApplication (starts scanning HERE)
├── service/
│   └── UserService.java    ← @Service → FOUND ✅
├── controller/
│   └── UserController.java ← @Controller → FOUND ✅
├── repository/
│   └── UserRepo.java       ← @Repository → FOUND ✅
└── util/
    └── Helper.java         ← @Component → FOUND ✅
```

### @Service

`@Service` is a **specialization of @Component**. It marks a class as a **business logic** layer.

```java
@Service  // Same as @Component, but tells developers "this is business logic"
public class UserService {
    
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public User findUser(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("User not found"));
    }
    
    public void registerUser(User user) {
        // Business logic: validate, hash password, save
        if (user.getEmail() == null) {
            throw new IllegalArgumentException("Email is required");
        }
        userRepository.save(user);
    }
}
```

### @Component vs @Service — Are They Different?

**Technically, no.** `@Service` is just `@Component` with a different name. But they signal **intent**:

```java
// Looking at Spring source code:
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component  // ← @Service IS a @Component internally!
public @interface Service {
    String value() default "";
}
```

**Use @Service to say**: "This class contains business logic"
**Use @Component to say**: "This is a generic Spring-managed bean"

---

## 14. @Repository and @Controller

### @Repository

Marks a class as a **data access layer** (talks to the database).

**Special feature**: It automatically translates database-specific exceptions into Spring's `DataAccessException`. This means you don't have to catch `SQLException` — Spring converts it for you.

```java
@Repository  // Data access layer — talks to database
public class UserRepository {
    
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    public User findById(Long id) {
        return jdbcTemplate.queryForObject(
            "SELECT * FROM users WHERE id = ?",
            new Object[]{id},
            (rs, rowNum) -> new User(
                rs.getLong("id"),
                rs.getString("name"),
                rs.getString("email")
            )
        );
    }
}
```

### @Controller

Marks a class as a **web controller** that handles HTTP requests.

```java
@Controller  // Web layer — handles HTTP requests
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/users/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        User user = userService.findUser(id);
        model.addAttribute("user", user);
        return "user-profile";  // Returns a VIEW name (e.g., Thymeleaf template)
    }
}
```

### The Stereotype Annotations Family

```
                    @Component (Generic)
                   /     |      \
                  /      |       \
          @Service  @Repository  @Controller
          (Business) (Data)      (Web)
                                    \
                                 @RestController
                                 (REST API)
```

All four are detected by component scanning. The specializations provide extra behavior and clarity:

| Annotation | Layer | Extra Feature |
|-----------|-------|---------------|
| `@Component` | Generic | None — just registers as bean |
| `@Service` | Business logic | None — semantic clarity |
| `@Repository` | Data access | Exception translation |
| `@Controller` | Web/HTTP | Handles web requests |
| `@RestController` | REST API | @Controller + @ResponseBody |

---

## 15. Bean Scopes

### What is a Bean Scope?

Scope defines **how many instances** of a bean Spring creates and **how long they live**.

### 15.1 Singleton Scope (DEFAULT)

```java
@Component
@Scope("singleton")  // This is the DEFAULT, you don't even need to write this
public class UserService {
    // Only ONE instance of UserService exists in the entire application
    // Every class that asks for UserService gets the SAME object
}
```

**Under the Hood:**
```
Container creates:  UserService@abc123

Class A asks for UserService → gets UserService@abc123
Class B asks for UserService → gets UserService@abc123  (SAME object!)
Class C asks for UserService → gets UserService@abc123  (SAME object!)
```

**When to use**: Services, repositories, utilities — objects that don't hold user-specific state.

### 15.2 Prototype Scope

```java
@Component
@Scope("prototype")
public class ShoppingCart {
    private List<Item> items = new ArrayList<>();
    
    public void addItem(Item item) {
        items.add(item);
    }
}
```

**Under the Hood:**
```
Class A asks for ShoppingCart → Spring creates NEW ShoppingCart@111
Class B asks for ShoppingCart → Spring creates NEW ShoppingCart@222
Class C asks for ShoppingCart → Spring creates NEW ShoppingCart@333

Each one is a DIFFERENT object!
```

**When to use**: Objects that hold different state for each use.

**Important**: Spring does NOT manage the full lifecycle of prototype beans. It creates them but does NOT call `@PreDestroy` on them.

### 15.3 Request Scope (Web Applications Only)

```java
@Component
@Scope("request")  // or @RequestScope
public class RequestLogger {
    private String requestId = UUID.randomUUID().toString();
    // New instance for EVERY HTTP request
}
```

**Under the Hood:**
```
HTTP Request 1 comes in → Spring creates RequestLogger@111
HTTP Request 2 comes in → Spring creates RequestLogger@222
Request 1 ends → RequestLogger@111 is destroyed
```

### 15.4 Session Scope (Web Applications Only)

```java
@Component
@Scope("session")  // or @SessionScope
public class UserSession {
    private String username;
    private String role;
    // One instance per USER SESSION (persists across multiple requests from same user)
}
```

### 15.5 Application Scope (Web Applications Only)

```java
@Component
@Scope("application")  // or @ApplicationScope
public class AppConfig {
    private int totalVisitors;
    // One instance per ServletContext (shared across entire web application)
    // Similar to Singleton but specifically for web context
}
```

### Scope Comparison

| Scope | Instances | Lifetime | Use Case |
|-------|-----------|----------|----------|
| singleton | 1 | Entire app | Services, repos, configs |
| prototype | Many | Until garbage collected | Shopping cart, form data |
| request | 1 per HTTP request | Single request | Request logging |
| session | 1 per user session | User's session | User preferences |
| application | 1 per web app | Entire web app | App-wide counters |

---

## 16. Bean Lifecycle

### Overview

Every Spring bean goes through a lifecycle — from creation to destruction.

```
┌───────────────────────────────────────────────────────────┐
│                  BEAN LIFECYCLE                           │
│                                                           │
│  1. Instantiation (Constructor called)                    │
│     ↓                                                     │
│  2. Populate Properties (Dependencies injected)           │
│     ↓                                                     │
│  3. BeanNameAware.setBeanName()                           │
│     ↓                                                     │
│  4. BeanFactoryAware.setBeanFactory()                     │
│     ↓                                                     │
│  5. ApplicationContextAware.setApplicationContext()       │
│     ↓                                                     │
│  6. BeanPostProcessor.postProcessBeforeInitialization()   │
│     ↓                                                     │
│  7. @PostConstruct method                                 │
│     ↓                                                     │
│  8. InitializingBean.afterPropertiesSet()                 │
│     ↓                                                     │
│  9. Custom init-method                                    │
│     ↓                                                     │
│  10. BeanPostProcessor.postProcessAfterInitialization()   │
│     ↓                                                     │
│  11. BEAN IS READY TO USE ✅                              │
│     ↓                                                     │
│  (Application runs...)                                    │
│     ↓                                                     │
│  12. @PreDestroy method                                   │
│     ↓                                                     │
│  13. DisposableBean.destroy()                             │
│     ↓                                                     │
│  14. Custom destroy-method                                │
│     ↓                                                     │
│  15. BEAN IS DESTROYED 💀                                 │
└───────────────────────────────────────────────────────────┘
```

### @PostConstruct and @PreDestroy

These are the most commonly used lifecycle callbacks.

```java
@Component
public class DatabaseConnectionPool {
    
    private List<Connection> connections;
    
    public DatabaseConnectionPool() {
        System.out.println("1. Constructor called — object created");
    }
    
    @PostConstruct  // Called AFTER constructor + dependency injection
    public void initialize() {
        System.out.println("2. @PostConstruct — Initializing connection pool...");
        connections = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            connections.add(createConnection());
        }
        System.out.println("   Pool initialized with " + connections.size() + " connections");
    }
    
    @PreDestroy  // Called BEFORE the bean is removed from the container
    public void cleanup() {
        System.out.println("3. @PreDestroy — Closing all connections...");
        for (Connection conn : connections) {
            conn.close();
        }
        System.out.println("   All connections closed. Cleanup done!");
    }
    
    public Connection getConnection() {
        return connections.remove(0);
    }
    
    private Connection createConnection() {
        // Simulating connection creation
        return new Connection();
    }
}
```

### When Each Method Is Called

```
Application starts:
→ "1. Constructor called — object created"
→ "2. @PostConstruct — Initializing connection pool..."
→ "   Pool initialized with 10 connections"
→ Application is running...

Application shuts down (Ctrl+C or context.close()):
→ "3. @PreDestroy — Closing all connections..."
→ "   All connections closed. Cleanup done!"
```

### Common Use Cases

| Callback | Use Case |
|----------|----------|
| `@PostConstruct` | Open connections, load cache, validate config, start schedulers |
| `@PreDestroy` | Close connections, flush cache, release resources, stop schedulers |

### Complete Example: Putting It All Together

```java
// Interface
public interface NotificationService {
    void sendNotification(String message);
}

// Implementation 1
@Component
@Primary
public class EmailNotificationService implements NotificationService {
    
    @PostConstruct
    public void init() {
        System.out.println("Email service initialized — SMTP connection ready");
    }
    
    @Override
    public void sendNotification(String message) {
        System.out.println("📧 Email sent: " + message);
    }
    
    @PreDestroy
    public void destroy() {
        System.out.println("Email service shutting down — SMTP connection closed");
    }
}

// Implementation 2
@Component
public class SmsNotificationService implements NotificationService {
    
    @PostConstruct
    public void init() {
        System.out.println("SMS service initialized — SMS gateway connected");
    }
    
    @Override
    public void sendNotification(String message) {
        System.out.println("📱 SMS sent: " + message);
    }
    
    @PreDestroy
    public void destroy() {
        System.out.println("SMS service shutting down — SMS gateway disconnected");
    }
}

// Service that uses notification
@Service
public class OrderService {
    
    private final NotificationService notificationService;  // Gets EmailNotificationService (@Primary)
    
    public OrderService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
    
    public void placeOrder(String item) {
        System.out.println("Order placed for: " + item);
        notificationService.sendNotification("Your order for " + item + " has been placed!");
    }
}

// Main Application
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(MyApp.class, args);
        
        OrderService orderService = context.getBean(OrderService.class);
        orderService.placeOrder("Laptop");
        
        context.close();  // Triggers @PreDestroy methods
    }
}
```

**Output:**
```
Email service initialized — SMTP connection ready
SMS service initialized — SMS gateway connected
Order placed for: Laptop
📧 Email sent: Your order for Laptop has been placed!
SMS service shutting down — SMS gateway disconnected
Email service shutting down — SMTP connection closed
```

---

## Quick Revision Cheat Sheet

```
Spring = Java framework for building applications
IoC = Framework controls object creation (not you)
DI = Spring injects dependencies into your classes

Three ways to inject:
  ✅ Constructor Injection (BEST — use final, immutable)
  ⚠️ Setter Injection (for optional dependencies)
  ❌ Field Injection (avoid — hard to test)

Annotations:
  @Component = Generic bean
  @Service = Business logic bean
  @Repository = Data access bean (+ exception translation)
  @Controller = Web request handler
  @Autowired = "Inject dependency here"
  @Qualifier = "Inject THIS specific bean"
  @Primary = "This is the default bean"

Scopes:
  singleton (default) = One instance for entire app
  prototype = New instance every time
  request/session/application = Web-specific

Lifecycle:
  Constructor → DI → @PostConstruct → [READY] → @PreDestroy → [DESTROYED]

Container:
  BeanFactory = Basic (lazy)
  ApplicationContext = Advanced (eager, PREFERRED)
```

---

**Next: [02-Spring-Annotations-Configuration.md](02-Spring-Annotations-Configuration.md) — Annotation Configuration, Java Configuration, Properties, and SpEL**
