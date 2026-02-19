# ORM, JPA & Hibernate - Complete In-Depth Guide

## Table of Contents
- [1. What is ORM?](#1-what-is-orm)
- [2. Advantages of ORM](#2-advantages-of-orm)
- [3. Introduction to JPA](#3-introduction-to-jpa)
- [4. JPA vs Hibernate](#4-jpa-vs-hibernate)
- [5. Entity Mapping Annotations](#5-entity-mapping-annotations)
- [6. Relationship Mappings](#6-relationship-mappings)
- [7. Cascade Types](#7-cascade-types)
- [8. Fetch Types (LAZY vs EAGER)](#8-fetch-types-lazy-vs-eager)
- [9. @JoinColumn and @JoinTable](#9-joincolumn-and-jointable)
- [10. Entity Lifecycle (Persistence Context)](#10-entity-lifecycle-persistence-context)

---

## 1. What is ORM?

### Simple Explanation

**ORM (Object-Relational Mapping)** is a technique that lets you work with your **database using Java objects** instead of writing SQL queries manually.

### The Problem Without ORM

In Java, you think in **objects** (classes, fields, methods). In a database, you think in **tables** (rows, columns, SQL). There's a mismatch:

```
Java World (Objects)              Database World (Tables)
┌────────────────────┐            ┌───────────────────────┐
│ class User {       │            │ CREATE TABLE users (  │
│   Long id;         │  ←──?──→   │   id BIGINT,          │
│   String name;     │            │   name VARCHAR(100),  │
│   String email;    │            │   email VARCHAR(200)  │
│ }                  │            │ );                    │
└────────────────────┘            └───────────────────────┘

How to convert between them? That's the "impedance mismatch" problem.
```

### What ORM Does

ORM acts as a **bridge** between Java objects and database tables:

```
Java Object ←────── ORM (the bridge) ──────→ Database Table

User user = new User("Kunal", "kunal@email.com");
entityManager.persist(user);

ORM translates this to:
INSERT INTO users (name, email) VALUES ('Kunal', 'kunal@email.com');
```

### Analogy

Think of ORM as a **translator** between two people speaking different languages:
- **You** speak Java (objects, classes)
- **Database** speaks SQL (tables, queries)
- **ORM** translates between the two

You say: `user.setName("Kunal")` → ORM says to DB: `UPDATE users SET name = 'Kunal'`

---

## 2. Advantages of ORM

| Advantage | Explanation |
|-----------|-------------|
| **Less SQL** | You write Java code, ORM generates SQL |
| **Database independence** | Switch from MySQL to PostgreSQL without changing Java code |
| **Object-oriented** | Work with objects, not ResultSets |
| **Caching** | ORM caches objects (fewer database hits) |
| **Lazy loading** | Load related data only when needed |
| **Automatic schema** | Can generate/update tables from Java classes |
| **Less boilerplate** | No manual ResultSet mapping |
| **Transactions** | Built-in transaction management |

### Without ORM vs With ORM

```java
// ❌ WITHOUT ORM — Manual SQL and mapping
public User findById(Long id) {
    String sql = "SELECT * FROM users WHERE id = ?";
    PreparedStatement stmt = conn.prepareStatement(sql);
    stmt.setLong(1, id);
    ResultSet rs = stmt.executeQuery();
    if (rs.next()) {
        User user = new User();
        user.setId(rs.getLong("id"));
        user.setName(rs.getString("name"));
        user.setEmail(rs.getString("email"));
        return user;
    }
    return null;
}

// ✅ WITH ORM — Just ask for the object
public User findById(Long id) {
    return entityManager.find(User.class, id);
    // ORM handles everything: SQL, mapping, connection, cleanup
}
```

---

## 3. Introduction to JPA

### What is JPA?

**JPA (Java Persistence API)** is a **specification** (set of rules/interfaces) that defines how Java objects should be mapped to database tables. It is NOT an implementation — it's a blueprint.

### Key Point: JPA is a Specification

```
JPA = "Here's WHAT should be done" (interfaces and rules)
Hibernate = "Here's HOW to do it" (actual implementation)

Analogy:
JPA = Recipe (tells you what ingredients and steps)
Hibernate = The actual cooking (follows the recipe)
```

### JPA Interfaces

```java
// JPA defines these interfaces:
EntityManager      // Manages entities (CRUD operations)
EntityManagerFactory // Creates EntityManagers
EntityTransaction  // Manages transactions

// JPA defines these annotations:
@Entity, @Table, @Id, @Column, @OneToMany, etc.

// These are all INTERFACES/ANNOTATIONS — no implementation!
// Hibernate IMPLEMENTS these interfaces.
```

---

## 4. JPA vs Hibernate

### What is Hibernate?

**Hibernate** is the most popular **implementation** of JPA. It's the actual library that does the work.

### JPA vs Hibernate Comparison

```
┌──────────────────────────────────────────┐
│              JPA (Specification)         │
│  Defines: WHAT to do                     │
│  Contains: Interfaces, Annotations       │
│  Package: javax.persistence / jakarta.*  │
│                                          │
│  ┌──────────────────────────────────────┐│
│  │        Hibernate (Implementation)    ││
│  │  Provides: HOW to do it              ││
│  │  Contains: Actual classes, logic     ││
│  │  Package: org.hibernate.*            ││
│  │                                      ││
│  │  Extra features beyond JPA:          ││
│  │  - Second level cache                ││
│  │  - Custom types                      ││
│  │  - HQL (Hibernate Query Language)    ││
│  └──────────────────────────────────────┘│
│                                          │
│  Other implementations:                  │
│  - EclipseLink                           │
│  - OpenJPA                               │
└──────────────────────────────────────────┘
```

| Feature | JPA | Hibernate |
|---------|-----|-----------|
| Type | Specification (rules) | Implementation (code) |
| Can run alone? | No | Yes |
| Package | `jakarta.persistence.*` | `org.hibernate.*` |
| Queries | JPQL | HQL (superset of JPQL) |
| Caching | First level only | First + Second level |
| Vendor lock-in | No (switch implementations) | Yes (Hibernate-specific) |

### Why Use JPA Over Hibernate Directly?

```java
// Using JPA (RECOMMENDED) — portable, can switch implementations
import jakarta.persistence.EntityManager;

@Repository
public class UserRepository {
    @PersistenceContext
    private EntityManager entityManager;  // JPA interface
}

// Using Hibernate directly — locked to Hibernate
import org.hibernate.Session;

@Repository
public class UserRepository {
    private Session session;  // Hibernate specific
}
```

**Best Practice**: Code against JPA interfaces, let Hibernate be the implementation behind the scenes.

---

## 5. Entity Mapping Annotations

### @Entity

Marks a Java class as a **database entity** (maps to a table).

```java
@Entity  // This class maps to a database table
public class User {
    @Id
    private Long id;
    private String name;
    private String email;
}
```

### @Table

Customizes the table name and schema.

```java
@Entity
@Table(
    name = "app_users",           // Table name in database
    schema = "public",            // Database schema
    uniqueConstraints = {
        @UniqueConstraint(columnNames = {"email"})  // email must be unique
    }
)
public class User {
    // ...
}
```

**Without @Table**: JPA uses the class name as the table name (`User` → `user` table).

### @Id

Marks the **primary key** field.

```java
@Entity
public class User {
    @Id  // This field is the primary key
    private Long id;
}
```

### @GeneratedValue

Tells JPA how to **generate primary key values** automatically.

```java
@Entity
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    // Database auto-generates the ID (MySQL AUTO_INCREMENT)
}
```

| Strategy | How It Works | Database Support |
|----------|-------------|-----------------|
| `IDENTITY` | Database auto-increment | MySQL, PostgreSQL, SQL Server |
| `SEQUENCE` | Uses database sequence | PostgreSQL, Oracle |
| `TABLE` | Uses a separate table | All databases |
| `AUTO` | JPA picks the best strategy | All (default) |

```java
// IDENTITY — MySQL AUTO_INCREMENT
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;

// SEQUENCE — PostgreSQL/Oracle
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
@SequenceGenerator(name = "user_seq", sequenceName = "user_sequence", allocationSize = 1)
private Long id;
```

### @Column

Customizes column properties.

```java
@Entity
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(
        name = "user_name",        // Column name in DB
        nullable = false,          // NOT NULL constraint
        length = 100,             // VARCHAR(100)
        unique = true              // UNIQUE constraint
    )
    private String name;
    
    @Column(nullable = false, unique = true, length = 200)
    private String email;
    
    @Column(name = "created_at", updatable = false)  // Cannot be updated after insert
    private LocalDateTime createdAt;
    
    @Column(precision = 10, scale = 2)  // For DECIMAL(10,2)
    private BigDecimal salary;
    
    @Column(columnDefinition = "TEXT")  // Custom SQL type
    private String bio;
    
    @Transient  // NOT mapped to any column — ignored by JPA
    private String temporaryData;
}
```

### Complete Entity Example

```java
@Entity
@Table(name = "employees")
public class Employee {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "first_name", nullable = false, length = 50)
    private String firstName;
    
    @Column(name = "last_name", nullable = false, length = 50)
    private String lastName;
    
    @Column(unique = true, nullable = false)
    private String email;
    
    @Column(precision = 10, scale = 2)
    private BigDecimal salary;
    
    @Column(name = "hire_date")
    private LocalDate hireDate;
    
    @Column(name = "is_active")
    private boolean active;
    
    @Enumerated(EnumType.STRING)  // Store enum as string (not ordinal number)
    private Department department;
    
    // Constructors
    public Employee() {}  // JPA REQUIRES a no-args constructor
    
    public Employee(String firstName, String lastName, String email) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.active = true;
        this.hireDate = LocalDate.now();
    }
    
    // Getters and setters...
}

public enum Department {
    ENGINEERING, MARKETING, SALES, HR, FINANCE
}
```

### What SQL Does JPA Generate?

```sql
-- JPA/Hibernate auto-generates this SQL:
CREATE TABLE employees (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    salary DECIMAL(10,2),
    hire_date DATE,
    is_active BOOLEAN,
    department VARCHAR(255)
);
```

---

## 6. Relationship Mappings

### Understanding Relationships

In a database, tables are related to each other. JPA maps these relationships to Java objects.

```
One User has ONE address      → @OneToOne
One User has MANY orders      → @OneToMany
Many Orders belong to ONE user → @ManyToOne
Many Students have MANY courses → @ManyToMany
```

### 6.1 @OneToOne

One entity has exactly one related entity.

```
┌──────────┐         ┌───────────┐
│  User    │ 1 ── 1  │  Address  │
│──────────│         │───────────│
│ id       │         │ id        │
│ name     │         │ street    │
│ email    │         │ city      │
└──────────┘         │ user_id(FK)│
                     └───────────┘
```

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL)
    private Address address;
}

@Entity
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String street;
    private String city;
    private String zipCode;
    
    @OneToOne
    @JoinColumn(name = "user_id")  // Foreign key column in Address table
    private User user;
}
```

### 6.2 @OneToMany and @ManyToOne

One entity has many related entities (and vice versa).

```
┌──────────┐         ┌────────────┐
│  User    │ 1 ── *  │  Order     │
│──────────│         │────────────│
│ id       │         │ id         │
│ name     │         │ product    │
│          │         │ amount     │
│          │         │ user_id(FK)│
└──────────┘         └────────────┘
```

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    // One user has MANY orders
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Order> orders = new ArrayList<>();
    
    // Helper methods to maintain both sides of the relationship
    public void addOrder(Order order) {
        orders.add(order);
        order.setUser(this);  // Set the back-reference
    }
    
    public void removeOrder(Order order) {
        orders.remove(order);
        order.setUser(null);
    }
}

@Entity
@Table(name = "orders")
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String product;
    private double amount;
    
    // Many orders belong to ONE user
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")  // Foreign key column
    private User user;
}
```

### Understanding `mappedBy`

```
WHO OWNS THE RELATIONSHIP?
The side with the FOREIGN KEY is the OWNER.

In User ↔ Order:
  Order table has user_id (FK) → Order is the OWNER
  User side uses mappedBy = "user" → User is the NON-OWNER (inverse side)

mappedBy = "user"  means:
  "I'm not the owner. The Order entity's 'user' field owns this relationship."
```

### 6.3 @ManyToMany

Many entities relate to many other entities. Requires a **join table**.

```
┌──────────┐         ┌────────────────┐         ┌──────────┐
│ Student  │ * ── *  │ student_course │ * ── *  │  Course  │
│──────────│         │────────────────│         │──────────│
│ id       │         │ student_id(FK) │         │ id       │
│ name     │         │ course_id(FK)  │         │ title    │
└──────────┘         └────────────────┘         └──────────┘
```

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @ManyToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})
    @JoinTable(
        name = "student_course",              // Join table name
        joinColumns = @JoinColumn(name = "student_id"),  // FK to this entity
        inverseJoinColumns = @JoinColumn(name = "course_id")  // FK to other entity
    )
    private Set<Course> courses = new HashSet<>();
    
    public void enrollInCourse(Course course) {
        courses.add(course);
        course.getStudents().add(this);
    }
}

@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    private String instructor;
    
    @ManyToMany(mappedBy = "courses")  // Student owns the relationship
    private Set<Student> students = new HashSet<>();
}
```

---

## 7. Cascade Types

### What is Cascading?

Cascading means: **when you do something to a parent entity, automatically do the same to child entities**.

### Cascade Types Explained

```java
@OneToMany(cascade = CascadeType.ALL)
private List<Order> orders;
```

| Cascade Type | What It Does | Example |
|-------------|-------------|---------|
| `PERSIST` | Save parent → auto-save children | Save User → auto-save Orders |
| `MERGE` | Update parent → auto-update children | Update User → auto-update Orders |
| `REMOVE` | Delete parent → auto-delete children | Delete User → auto-delete Orders |
| `REFRESH` | Refresh parent → auto-refresh children | Refresh User → refresh Orders from DB |
| `DETACH` | Detach parent → auto-detach children | Detach User → Orders also detached |
| `ALL` | All of the above | Everything cascades |

### Code Example

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Order> orders = new ArrayList<>();
}

// ===== CascadeType.PERSIST =====
User user = new User("Kunal");
Order order1 = new Order("Laptop", 999.99);
Order order2 = new Order("Mouse", 29.99);
user.addOrder(order1);
user.addOrder(order2);

entityManager.persist(user);
// ONE persist call saves User AND both Orders!
// SQL generated:
// INSERT INTO users (name) VALUES ('Kunal');
// INSERT INTO orders (product, amount, user_id) VALUES ('Laptop', 999.99, 1);
// INSERT INTO orders (product, amount, user_id) VALUES ('Mouse', 29.99, 1);

// ===== CascadeType.REMOVE =====
entityManager.remove(user);
// Deletes User AND all their Orders!
// SQL: DELETE FROM orders WHERE user_id = 1;
// SQL: DELETE FROM users WHERE id = 1;
```

### orphanRemoval = true

```java
@OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
private List<Order> orders;

// With orphanRemoval = true:
user.getOrders().remove(order1);  // Remove from collection
// JPA automatically DELETES order1 from database!
// Because order1 is now an "orphan" (no parent)

// Without orphanRemoval:
user.getOrders().remove(order1);
// order1 is removed from the list but STILL EXISTS in the database
// user_id is set to NULL (if nullable)
```

---

## 8. Fetch Types (LAZY vs EAGER)

### What is Fetching?

When you load an entity, should its **related entities** be loaded too? Fetch type controls this.

### EAGER Fetching

```java
@ManyToOne(fetch = FetchType.EAGER)
private User user;

// When you load an Order, the User is loaded IMMEDIATELY
Order order = entityManager.find(Order.class, 1L);
// SQL: SELECT o.*, u.* FROM orders o JOIN users u ON o.user_id = u.id WHERE o.id = 1
// Both Order AND User are loaded in ONE query
```

### LAZY Fetching

```java
@OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
private List<Order> orders;

// When you load a User, the Orders are NOT loaded yet
User user = entityManager.find(User.class, 1L);
// SQL: SELECT * FROM users WHERE id = 1   ← Only User loaded

// Orders are loaded ONLY when you ACCESS them
List<Order> orders = user.getOrders();  // NOW the query runs
// SQL: SELECT * FROM orders WHERE user_id = 1
```

### Under the Hood: How LAZY Loading Works

```
When you get user.getOrders():
  1. JPA returns a PROXY object (not the real list)
  2. The proxy looks like a normal List but is actually empty
  3. When you FIRST call a method on the list (like .size() or iterate):
     → The proxy intercepts the call
     → Executes the SQL query
     → Fills the list with real data
     → Returns the result
     
This is called the "proxy pattern"
```

### Default Fetch Types

| Relationship | Default Fetch Type |
|-------------|-------------------|
| `@OneToOne` | EAGER |
| `@ManyToOne` | EAGER |
| `@OneToMany` | LAZY |
| `@ManyToMany` | LAZY |

**Best Practice**: Always use LAZY loading and fetch data when needed.

```java
// GOOD: Explicit LAZY loading
@ManyToOne(fetch = FetchType.LAZY)
private User user;

@OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
private List<Order> orders;
```

### N+1 Problem (LAZY Loading Pitfall)

```java
// N+1 Problem:
List<User> users = userRepository.findAll();  // 1 query for all users
for (User user : users) {
    System.out.println(user.getOrders().size());  // 1 query PER user for orders!
}
// If 100 users → 1 + 100 = 101 queries! 😱

// Solution: Use JOIN FETCH (JPQL)
@Query("SELECT u FROM User u JOIN FETCH u.orders")
List<User> findAllWithOrders();  // 1 query gets everything!
```

---

## 9. @JoinColumn and @JoinTable

### @JoinColumn

Specifies the **foreign key column** in a relationship.

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne
    @JoinColumn(
        name = "user_id",               // Column name in THIS table
        referencedColumnName = "id",     // Column in the OTHER table (default is PK)
        nullable = false,                // NOT NULL constraint
        foreignKey = @ForeignKey(name = "fk_order_user")  // Custom FK name
    )
    private User user;
}

// Generated SQL:
// ALTER TABLE orders ADD CONSTRAINT fk_order_user 
//   FOREIGN KEY (user_id) REFERENCES users(id);
```

### @JoinTable

Used for **@ManyToMany** relationships. Creates a separate join table.

```java
@Entity
public class Student {
    
    @ManyToMany
    @JoinTable(
        name = "enrollment",                              // Join table name
        joinColumns = @JoinColumn(name = "student_id"),    // FK to Student
        inverseJoinColumns = @JoinColumn(name = "course_id"),  // FK to Course
        uniqueConstraints = @UniqueConstraint(
            columnNames = {"student_id", "course_id"}      // Prevent duplicate enrollments
        )
    )
    private Set<Course> courses = new HashSet<>();
}

// Generated SQL:
// CREATE TABLE enrollment (
//     student_id BIGINT NOT NULL,
//     course_id BIGINT NOT NULL,
//     FOREIGN KEY (student_id) REFERENCES students(id),
//     FOREIGN KEY (course_id) REFERENCES courses(id),
//     UNIQUE (student_id, course_id)
// );
```

---

## 10. Entity Lifecycle (Persistence Context)

### What is the Persistence Context?

The Persistence Context is a **first-level cache** managed by the EntityManager. It tracks the state of all entities.

### Entity States

```
┌─────────────────────────────────────────────────────────────┐
│                    ENTITY LIFECYCLE                         │
│                                                             │
│   ┌───────────┐    persist()    ┌────────────┐              │
│   │ TRANSIENT │ ─────────────→  │ PERSISTENT │              │
│   │ (new)     │                 │ (managed)  │              │
│   └───────────┘                 └────────────┘              │
│       ↑                         ↑    │    ↓                 │
│       │ new Object()            │    │  detach()/           │
│       │                  merge()│    │  close()/            │
│       │                         │    │  clear()             │
│       │                         │    ↓                      │
│       │                       ┌──────────────┐              │
│       │                       │  DETACHED    │              │
│       │                       │(disconnected)│              │
│       │                       └──────────────┘              │
│       │                                                     │
│       │                    remove()                         │
│       │                  ┌─────────────┐                    │
│       └────────────────  │  REMOVED    │                    │
│                          │ (deleted)   │                    │
│                          └─────────────┘                    │
└─────────────────────────────────────────────────────────────┘
```

### State 1: Transient (New)

```java
// Object exists in memory but NOT in database and NOT managed by JPA
User user = new User("Kunal", "kunal@email.com");
// This is a regular Java object — JPA knows nothing about it
// No ID assigned, not tracked, not saved
```

### State 2: Persistent (Managed)

```java
// Object is managed by the EntityManager and synced with database
entityManager.persist(user);  // Now it's PERSISTENT

// Any changes to a persistent entity are AUTOMATICALLY saved to DB!
user.setName("Kunal Updated");
// JPA detects this change (dirty checking) and generates:
// UPDATE users SET name = 'Kunal Updated' WHERE id = 1;
// You DON'T need to call save() again!

// Also persistent when loaded from DB:
User loaded = entityManager.find(User.class, 1L);  // PERSISTENT
loaded.setEmail("new@email.com");
// Change is automatically saved at flush/commit time
```

### State 3: Detached

```java
// Object was persistent but is now disconnected from EntityManager
entityManager.detach(user);  // Explicitly detach
// OR
entityManager.close();       // Closing EM detaches all entities
// OR
entityManager.clear();       // Clear persistence context

// Changes to detached objects are NOT automatically saved!
user.setName("Changed");  // This change is LOST unless you merge

// To re-attach:
User managedUser = entityManager.merge(user);  // Re-attach and sync
// managedUser is now persistent again
```

### State 4: Removed

```java
// Object is scheduled for deletion from database
entityManager.remove(user);  // Mark for removal

// The DELETE SQL runs at flush/commit time:
// DELETE FROM users WHERE id = 1;
```

### EntityManager Operations Summary

```java
@Repository
public class UserRepository {
    
    @PersistenceContext
    private EntityManager em;
    
    // PERSIST: Transient → Persistent (INSERT)
    public void save(User user) {
        em.persist(user);  
        // user.getId() is now set (generated by DB)
    }
    
    // FIND: Load from DB → Persistent
    public User findById(Long id) {
        return em.find(User.class, id);
        // Returns null if not found
    }
    
    // MERGE: Detached → Persistent (UPDATE)
    public User update(User detachedUser) {
        return em.merge(detachedUser);
        // Returns a NEW managed instance
        // The original detachedUser is STILL detached!
    }
    
    // REMOVE: Persistent → Removed (DELETE)
    public void delete(Long id) {
        User user = em.find(User.class, id);
        if (user != null) {
            em.remove(user);
        }
    }
    
    // DETACH: Persistent → Detached
    public void detach(User user) {
        em.detach(user);
        // user is no longer tracked
    }
    
    // REFRESH: Reload from database (discard in-memory changes)
    public void refresh(User user) {
        em.refresh(user);
        // Overwrites any in-memory changes with database values
    }
}
```

### Dirty Checking (Under the Hood)

```
When a persistent entity changes, JPA detects it automatically:

1. You load User from DB → JPA stores a SNAPSHOT of all field values
2. You change user.setName("New Name")
3. At flush/commit time:
   → JPA compares CURRENT values with SNAPSHOT
   → Finds: name was "Old Name", now "New Name"
   → Generates: UPDATE users SET name = 'New Name' WHERE id = 1
   → Executes the UPDATE

This is called DIRTY CHECKING — JPA checks if the entity is "dirty" (changed)

user = em.find(User.class, 1L);
// Snapshot: {id:1, name:"Kunal", email:"kunal@email.com"}

user.setName("Updated Kunal");
// Current:  {id:1, name:"Updated Kunal", email:"kunal@email.com"}

// At flush time: compare snapshot vs current
// name changed! → Generate UPDATE SQL
```

---

## Quick Revision Cheat Sheet

```
ORM = Maps Java objects ↔ Database tables (no manual SQL)

JPA = Specification (rules/interfaces)
Hibernate = Implementation (actual code that follows JPA rules)

Entity Annotations:
  @Entity         = "This class is a DB table"
  @Table          = Customize table name
  @Id             = Primary key
  @GeneratedValue = Auto-generate PK (IDENTITY, SEQUENCE, AUTO)
  @Column         = Customize column (name, nullable, unique, length)
  @Transient      = NOT mapped to DB (ignored)

Relationships:
  @OneToOne    → One user has one address
  @OneToMany   → One user has many orders
  @ManyToOne   → Many orders belong to one user
  @ManyToMany  → Many students have many courses

mappedBy = "field" → "I'm NOT the owner, the other side owns it"
@JoinColumn → Defines the foreign key column
@JoinTable → Creates a join table (for @ManyToMany)

Cascade Types:
  PERSIST, MERGE, REMOVE, REFRESH, DETACH, ALL
  CASCADE = "Do to children what you do to parent"

Fetch Types:
  EAGER → Load immediately (careful — can load too much!)
  LAZY → Load only when accessed (PREFERRED)

Entity States:
  Transient  → new Object() — not managed, not in DB
  Persistent → persist() / find() — managed, synced with DB
  Detached   → close() / detach() — was managed, now disconnected
  Removed    → remove() — marked for deletion

Operations: persist, find, merge, remove, detach, refresh
Dirty Checking = JPA auto-detects changes to persistent entities
```

---

**Next: [06-Spring-Data-JPA.md](06-Spring-Data-JPA.md) — Spring Data Repository, CrudRepository, JpaRepository, JPQL, Custom Queries**
