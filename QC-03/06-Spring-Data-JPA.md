# Spring Data JPA - Complete In-Depth Guide

## Table of Contents
- [1. What is Spring Data JPA?](#1-what-is-spring-data-jpa)
- [2. Spring Data Repository](#2-spring-data-repository)
- [3. CrudRepository](#3-crudrepository)
- [4. JpaRepository](#4-jparepository)
- [5. Custom Query Methods (Method Name Queries)](#5-custom-query-methods)
- [6. JPQL (Java Persistence Query Language)](#6-jpql)
- [7. @Query Annotation](#7-query-annotation)
- [8. Named Queries and Native Queries](#8-named-queries-and-native-queries)
- [9. Specifications](#9-specifications)
- [10. Pagination and Sorting](#10-pagination-and-sorting)
- [11. Projections](#11-projections)
- [12. Auditing](#12-auditing)
- [13. Caching](#13-caching)
- [14. Transaction Management](#14-transaction-management)

---

## 1. What is Spring Data JPA?

### Simple Explanation

Spring Data JPA makes working with databases **incredibly easy**. Instead of writing SQL or even JPQL queries, you just define a **Java interface** and Spring generates all the database code for you.

### The Evolution

```
Level 1: Plain JDBC          → Write everything manually (SQL + mapping + connections)
Level 2: JdbcTemplate        → Spring handles connections, you write SQL
Level 3: JPA/Hibernate       → Write entity classes, use EntityManager
Level 4: Spring Data JPA     → Just define an interface — Spring does EVERYTHING!
```

### How It Works

```java
// THIS IS ALL YOU NEED — Spring provides the implementation!
public interface UserRepository extends JpaRepository<User, Long> {
    // No implementation code needed!
    // Spring auto-generates: save, findById, findAll, delete, count, etc.
}
```

**You write 1 line of interface code → Spring generates 20+ database methods!**

---

## 2. Spring Data Repository

### Repository Hierarchy

```
Repository (marker interface — empty)
    │
    ├── CrudRepository (basic CRUD operations)
    │       │
    │       └── PagingAndSortingRepository (adds pagination + sorting)
    │               │
    │               └── JpaRepository (adds JPA-specific methods)
    │
    └── Other Spring Data modules:
        ├── MongoRepository (for MongoDB)
        ├── ElasticsearchRepository (for Elasticsearch)
        └── etc.
```

### Setting Up Spring Data JPA

#### pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Database driver -->
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- For quick testing with in-memory DB -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

#### application.properties

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

#### Entity Class

```java
@Entity
@Table(name = "users")
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    @Column(unique = true, nullable = false)
    private String email;
    
    private int age;
    
    private String city;
    
    @Column(name = "is_active")
    private boolean active;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    // Constructors, getters, setters...
    public User() {}
    
    public User(String name, String email, int age, String city) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.city = city;
        this.active = true;
        this.createdAt = LocalDateTime.now();
    }
}
```

---

## 3. CrudRepository

### What Does CrudRepository Provide?

```java
public interface UserRepository extends CrudRepository<User, Long> {
    // You get ALL of these for FREE:
}
```

| Method | What It Does |
|--------|-------------|
| `save(entity)` | Insert or update |
| `saveAll(entities)` | Save multiple entities |
| `findById(id)` | Find by primary key |
| `existsById(id)` | Check if entity exists |
| `findAll()` | Get all entities |
| `findAllById(ids)` | Get entities by list of IDs |
| `count()` | Count total entities |
| `deleteById(id)` | Delete by ID |
| `delete(entity)` | Delete entity |
| `deleteAll()` | Delete everything |

### Using CrudRepository

```java
@Service
public class UserService {
    
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    // CREATE
    public User createUser(String name, String email, int age, String city) {
        User user = new User(name, email, age, city);
        return userRepository.save(user);  // INSERT INTO users ...
    }
    
    // READ
    public User getUser(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("User not found"));
        // findById returns Optional<User> — handle null safely!
    }
    
    // READ ALL
    public List<User> getAllUsers() {
        List<User> users = new ArrayList<>();
        userRepository.findAll().forEach(users::add);
        return users;
    }
    
    // UPDATE
    public User updateUser(Long id, String newName) {
        User user = getUser(id);
        user.setName(newName);
        return userRepository.save(user);  // UPDATE users SET name = ? WHERE id = ?
        // save() does INSERT if no ID, UPDATE if ID exists
    }
    
    // DELETE
    public void deleteUser(Long id) {
        userRepository.deleteById(id);  // DELETE FROM users WHERE id = ?
    }
    
    // COUNT
    public long countUsers() {
        return userRepository.count();  // SELECT COUNT(*) FROM users
    }
    
    // EXISTS
    public boolean userExists(Long id) {
        return userRepository.existsById(id);  // SELECT COUNT(*) > 0
    }
}
```

---

## 4. JpaRepository

### What Extra Does JpaRepository Add?

`JpaRepository` extends `CrudRepository` + `PagingAndSortingRepository` and adds JPA-specific methods:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    // Everything from CrudRepository PLUS:
}
```

| Extra Method | What It Does |
|-------------|-------------|
| `findAll()` | Returns `List<>` instead of `Iterable<>` |
| `findAll(Sort sort)` | Find all with sorting |
| `findAll(Pageable pageable)` | Find with pagination |
| `saveAndFlush(entity)` | Save and immediately flush to DB |
| `deleteInBatch(entities)` | Batch delete (faster) |
| `deleteAllInBatch()` | Delete all in one query |
| `getById(id)` / `getReferenceById(id)` | Get lazy reference |
| `flush()` | Force pending changes to DB |

### CrudRepository vs JpaRepository

| Feature | CrudRepository | JpaRepository |
|---------|---------------|---------------|
| Return type | `Iterable<T>` | `List<T>` |
| Pagination | No | Yes |
| Sorting | No | Yes |
| Flush/Batch | No | Yes |
| When to use | Simple CRUD | Full JPA features (RECOMMENDED) |

---

## 5. Custom Query Methods

### The Magic of Method Names

Spring Data JPA can **generate queries from method names**! You just name your method following a pattern, and Spring generates the SQL.

```java
public interface UserRepository extends JpaRepository<User, Long> {
    
    // Spring reads the method name and generates the query!
    
    // ===== FIND BY SINGLE FIELD =====
    List<User> findByName(String name);
    // SQL: SELECT * FROM users WHERE name = ?
    
    User findByEmail(String email);
    // SQL: SELECT * FROM users WHERE email = ?
    
    List<User> findByCity(String city);
    // SQL: SELECT * FROM users WHERE city = ?
    
    // ===== FIND BY MULTIPLE FIELDS =====
    List<User> findByNameAndCity(String name, String city);
    // SQL: SELECT * FROM users WHERE name = ? AND city = ?
    
    List<User> findByNameOrEmail(String name, String email);
    // SQL: SELECT * FROM users WHERE name = ? OR email = ?
    
    // ===== COMPARISONS =====
    List<User> findByAgeGreaterThan(int age);
    // SQL: SELECT * FROM users WHERE age > ?
    
    List<User> findByAgeLessThanEqual(int age);
    // SQL: SELECT * FROM users WHERE age <= ?
    
    List<User> findByAgeBetween(int minAge, int maxAge);
    // SQL: SELECT * FROM users WHERE age BETWEEN ? AND ?
    
    // ===== STRING MATCHING =====
    List<User> findByNameContaining(String keyword);
    // SQL: SELECT * FROM users WHERE name LIKE '%keyword%'
    
    List<User> findByNameStartingWith(String prefix);
    // SQL: SELECT * FROM users WHERE name LIKE 'prefix%'
    
    List<User> findByNameEndingWith(String suffix);
    // SQL: SELECT * FROM users WHERE name LIKE '%suffix'
    
    List<User> findByNameIgnoreCase(String name);
    // SQL: SELECT * FROM users WHERE UPPER(name) = UPPER(?)
    
    // ===== NULL CHECKS =====
    List<User> findByEmailIsNull();
    // SQL: SELECT * FROM users WHERE email IS NULL
    
    List<User> findByEmailIsNotNull();
    // SQL: SELECT * FROM users WHERE email IS NOT NULL
    
    // ===== BOOLEAN =====
    List<User> findByActiveTrue();
    // SQL: SELECT * FROM users WHERE is_active = true
    
    List<User> findByActiveFalse();
    // SQL: SELECT * FROM users WHERE is_active = false
    
    // ===== IN =====
    List<User> findByCityIn(List<String> cities);
    // SQL: SELECT * FROM users WHERE city IN (?, ?, ?)
    
    // ===== ORDER BY =====
    List<User> findByOrderByNameAsc();
    // SQL: SELECT * FROM users ORDER BY name ASC
    
    List<User> findByCityOrderByAgeDesc(String city);
    // SQL: SELECT * FROM users WHERE city = ? ORDER BY age DESC
    
    // ===== LIMITING RESULTS =====
    User findFirstByOrderByAgeDesc();
    // SQL: SELECT * FROM users ORDER BY age DESC LIMIT 1
    
    List<User> findTop5ByOrderByCreatedAtDesc();
    // SQL: SELECT * FROM users ORDER BY created_at DESC LIMIT 5
    
    // ===== COUNT AND EXISTS =====
    long countByCity(String city);
    // SQL: SELECT COUNT(*) FROM users WHERE city = ?
    
    boolean existsByEmail(String email);
    // SQL: SELECT COUNT(*) > 0 FROM users WHERE email = ?
    
    // ===== DELETE =====
    void deleteByEmail(String email);
    // SQL: DELETE FROM users WHERE email = ?
    
    long deleteByCity(String city);
    // Returns count of deleted rows
}
```

### Method Name Keywords Reference

| Keyword | SQL | Example |
|---------|-----|---------|
| `And` | AND | `findByNameAndAge` |
| `Or` | OR | `findByNameOrEmail` |
| `Between` | BETWEEN | `findByAgeBetween` |
| `LessThan` | < | `findByAgeLessThan` |
| `GreaterThan` | > | `findByAgeGreaterThan` |
| `LessThanEqual` | <= | `findByAgeLessThanEqual` |
| `GreaterThanEqual` | >= | `findByAgeGreaterThanEqual` |
| `Like` | LIKE | `findByNameLike` |
| `Containing` | LIKE %x% | `findByNameContaining` |
| `StartingWith` | LIKE x% | `findByNameStartingWith` |
| `EndingWith` | LIKE %x | `findByNameEndingWith` |
| `IsNull` | IS NULL | `findByEmailIsNull` |
| `IsNotNull` | IS NOT NULL | `findByEmailIsNotNull` |
| `In` | IN | `findByCityIn` |
| `NotIn` | NOT IN | `findByCityNotIn` |
| `True` | = true | `findByActiveTrue` |
| `False` | = false | `findByActiveFalse` |
| `OrderBy` | ORDER BY | `findByOrderByNameAsc` |
| `Not` | != | `findByNameNot` |
| `IgnoreCase` | UPPER() = UPPER() | `findByNameIgnoreCase` |

---

## 6. JPQL (Java Persistence Query Language)

### What is JPQL?

JPQL is like SQL but works with **entity classes** instead of tables, and **fields** instead of columns.

### SQL vs JPQL

```
SQL:   SELECT * FROM users WHERE name = 'Kunal'     ← Uses TABLE names and COLUMN names
JPQL:  SELECT u FROM User u WHERE u.name = 'Kunal'  ← Uses CLASS names and FIELD names
```

### JPQL Syntax

```java
// Basic SELECT
SELECT u FROM User u                          // All users
SELECT u FROM User u WHERE u.age > 25         // Filter
SELECT u.name FROM User u                     // Select specific field
SELECT u FROM User u WHERE u.name = :name     // Named parameter

// JOIN
SELECT o FROM Order o JOIN o.user u WHERE u.name = :name
SELECT u FROM User u JOIN FETCH u.orders      // Fetch JOIN (avoids N+1)

// Aggregation
SELECT COUNT(u) FROM User u WHERE u.city = :city
SELECT AVG(u.age) FROM User u
SELECT MAX(u.age) FROM User u
SELECT u.city, COUNT(u) FROM User u GROUP BY u.city

// Sorting
SELECT u FROM User u ORDER BY u.name ASC, u.age DESC

// Like
SELECT u FROM User u WHERE u.name LIKE %:keyword%

// IN
SELECT u FROM User u WHERE u.city IN :cities

// Subquery
SELECT u FROM User u WHERE u.age > (SELECT AVG(u2.age) FROM User u2)
```

---

## 7. @Query Annotation

### What is @Query?

When method names get too long or complex, use `@Query` to write JPQL or native SQL directly.

```java
public interface UserRepository extends JpaRepository<User, Long> {
    
    // ===== JPQL QUERIES =====
    
    @Query("SELECT u FROM User u WHERE u.email = :email")
    User findByEmailAddress(@Param("email") String email);
    
    @Query("SELECT u FROM User u WHERE u.age > :age AND u.city = :city")
    List<User> findSeniorUsersInCity(@Param("age") int age, @Param("city") String city);
    
    @Query("SELECT u FROM User u WHERE u.name LIKE %:keyword%")
    List<User> searchByName(@Param("keyword") String keyword);
    
    @Query("SELECT u FROM User u WHERE u.city IN :cities")
    List<User> findUsersInCities(@Param("cities") List<String> cities);
    
    // ===== AGGREGATION =====
    
    @Query("SELECT COUNT(u) FROM User u WHERE u.active = true")
    long countActiveUsers();
    
    @Query("SELECT AVG(u.age) FROM User u")
    Double getAverageAge();
    
    @Query("SELECT u.city, COUNT(u) FROM User u GROUP BY u.city")
    List<Object[]> countUsersByCity();
    
    // ===== JOIN FETCH (Solve N+1 problem) =====
    
    @Query("SELECT u FROM User u JOIN FETCH u.orders WHERE u.id = :id")
    User findUserWithOrders(@Param("id") Long id);
    
    @Query("SELECT DISTINCT u FROM User u JOIN FETCH u.orders")
    List<User> findAllUsersWithOrders();
    
    // ===== UPDATE / DELETE QUERIES =====
    
    @Modifying  // Required for UPDATE/DELETE queries
    @Transactional
    @Query("UPDATE User u SET u.active = false WHERE u.id = :id")
    int deactivateUser(@Param("id") Long id);
    
    @Modifying
    @Transactional
    @Query("DELETE FROM User u WHERE u.active = false")
    int deleteInactiveUsers();
    
    // ===== PAGINATION WITH @Query =====
    
    @Query("SELECT u FROM User u WHERE u.city = :city")
    Page<User> findByCityPaginated(@Param("city") String city, Pageable pageable);
}
```

---

## 8. Named Queries and Native Queries

### Named Queries

Define queries on the entity class and reference them by name:

```java
@Entity
@NamedQueries({
    @NamedQuery(
        name = "User.findByCity",
        query = "SELECT u FROM User u WHERE u.city = :city"
    ),
    @NamedQuery(
        name = "User.findActiveUsers",
        query = "SELECT u FROM User u WHERE u.active = true ORDER BY u.name"
    )
})
public class User {
    // ...
}

// In repository — Spring auto-discovers named queries matching the pattern:
// EntityName.methodName
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByCity(@Param("city") String city);  // Uses named query
    List<User> findActiveUsers();
}
```

### Native Queries (Raw SQL)

When you need database-specific SQL:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    
    // Native SQL query (uses actual table/column names)
    @Query(value = "SELECT * FROM users WHERE email = :email", nativeQuery = true)
    User findByEmailNative(@Param("email") String email);
    
    // Native query with JOIN
    @Query(
        value = "SELECT u.* FROM users u INNER JOIN orders o ON u.id = o.user_id " +
                "WHERE o.amount > :minAmount",
        nativeQuery = true
    )
    List<User> findUsersWithLargeOrders(@Param("minAmount") double minAmount);
    
    // Native query with pagination
    @Query(
        value = "SELECT * FROM users WHERE city = :city",
        countQuery = "SELECT COUNT(*) FROM users WHERE city = :city",
        nativeQuery = true
    )
    Page<User> findByCityNative(@Param("city") String city, Pageable pageable);
}
```

### JPQL vs Native SQL

| Feature | JPQL | Native SQL |
|---------|------|-----------|
| Uses | Entity/field names | Table/column names |
| Portable | Yes (database independent) | No (database specific) |
| Features | Standard JPA | Full database features |
| When to use | Most cases | Complex/optimized queries |

---

## 9. Specifications

### What are Specifications?

Specifications let you build **dynamic queries** at runtime. Instead of writing a new method for every possible filter combination, you build query conditions programmatically.

### The Problem

```java
// Without Specifications — too many methods!
List<User> findByName(String name);
List<User> findByCity(String city);
List<User> findByAge(int age);
List<User> findByNameAndCity(String name, String city);
List<User> findByNameAndAge(String name, int age);
List<User> findByCityAndAge(String city, int age);
List<User> findByNameAndCityAndAge(String name, String city, int age);
// 7 methods for just 3 fields — imagine 10 fields! 😱
```

### The Solution: Specifications

```java
// Step 1: Repository extends JpaSpecificationExecutor
public interface UserRepository extends JpaRepository<User, Long>,
                                        JpaSpecificationExecutor<User> {
}

// Step 2: Create Specification class
public class UserSpecifications {
    
    public static Specification<User> hasName(String name) {
        return (root, query, criteriaBuilder) ->
            name == null ? null : criteriaBuilder.equal(root.get("name"), name);
    }
    
    public static Specification<User> hasCity(String city) {
        return (root, query, criteriaBuilder) ->
            city == null ? null : criteriaBuilder.equal(root.get("city"), city);
    }
    
    public static Specification<User> ageGreaterThan(Integer age) {
        return (root, query, criteriaBuilder) ->
            age == null ? null : criteriaBuilder.greaterThan(root.get("age"), age);
    }
    
    public static Specification<User> isActive() {
        return (root, query, criteriaBuilder) ->
            criteriaBuilder.isTrue(root.get("active"));
    }
    
    public static Specification<User> nameContains(String keyword) {
        return (root, query, criteriaBuilder) ->
            keyword == null ? null : 
            criteriaBuilder.like(root.get("name"), "%" + keyword + "%");
    }
}

// Step 3: Use Specifications — combine ANY filters dynamically!
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public List<User> searchUsers(String name, String city, Integer minAge) {
        Specification<User> spec = Specification.where(null);  // Start with no filter
        
        if (name != null) {
            spec = spec.and(UserSpecifications.hasName(name));
        }
        if (city != null) {
            spec = spec.and(UserSpecifications.hasCity(city));
        }
        if (minAge != null) {
            spec = spec.and(UserSpecifications.ageGreaterThan(minAge));
        }
        
        return userRepository.findAll(spec);
        // Builds: WHERE name = ? AND city = ? AND age > ?
        // Only includes conditions that are NOT null!
    }
}
```

---

## 10. Pagination and Sorting

### Why Pagination?

If you have 1 million records, you DON'T want to load all at once. Pagination loads data in **small pages** (e.g., 20 records at a time).

### Sorting

```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    // Sort by single field
    public List<User> getUsersSortedByName() {
        Sort sort = Sort.by("name").ascending();
        return userRepository.findAll(sort);
        // SQL: SELECT * FROM users ORDER BY name ASC
    }
    
    // Sort by multiple fields
    public List<User> getUsersSorted() {
        Sort sort = Sort.by(
            Sort.Order.asc("city"),
            Sort.Order.desc("age")
        );
        return userRepository.findAll(sort);
        // SQL: SELECT * FROM users ORDER BY city ASC, age DESC
    }
}
```

### Pagination

```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public Page<User> getUsersPage(int pageNumber, int pageSize) {
        // Pages are 0-indexed! Page 0 = first page
        Pageable pageable = PageRequest.of(pageNumber, pageSize);
        return userRepository.findAll(pageable);
        // SQL: SELECT * FROM users LIMIT 10 OFFSET 0  (for page 0, size 10)
    }
    
    // Pagination + Sorting
    public Page<User> getUsersPageSorted(int pageNumber, int pageSize) {
        Pageable pageable = PageRequest.of(
            pageNumber, 
            pageSize, 
            Sort.by("name").ascending()
        );
        return userRepository.findAll(pageable);
        // SQL: SELECT * FROM users ORDER BY name ASC LIMIT 10 OFFSET 20
    }
}
```

### Using Page Object

```java
Page<User> page = userRepository.findAll(PageRequest.of(0, 10));

page.getContent();           // List<User> — the actual data
page.getNumber();            // Current page number (0-based)
page.getSize();              // Page size (10)
page.getTotalElements();     // Total records in DB (e.g., 150)
page.getTotalPages();        // Total pages (150/10 = 15)
page.hasNext();              // Is there a next page?
page.hasPrevious();          // Is there a previous page?
page.isFirst();              // Is this the first page?
page.isLast();               // Is this the last page?
```

### Pagination in Controller (REST API)

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserRepository userRepository;
    
    @GetMapping
    public Page<User> getUsers(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size,
            @RequestParam(defaultValue = "id") String sortBy,
            @RequestParam(defaultValue = "asc") String direction
    ) {
        Sort sort = direction.equalsIgnoreCase("desc") 
            ? Sort.by(sortBy).descending() 
            : Sort.by(sortBy).ascending();
        
        Pageable pageable = PageRequest.of(page, size, sort);
        return userRepository.findAll(pageable);
    }
}

// Usage:
// GET /api/users?page=0&size=10&sortBy=name&direction=asc
// GET /api/users?page=2&size=20&sortBy=age&direction=desc
```

---

## 11. Projections

### What are Projections?

Instead of loading the **entire entity**, projections let you load only **specific fields** you need. This is more efficient.

### Interface-Based Projection (Closed Projection)

```java
// Step 1: Define a projection interface
public interface UserNameEmail {
    String getName();
    String getEmail();
}

// Step 2: Use in repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    List<UserNameEmail> findByCity(String city);
    // SQL: SELECT name, email FROM users WHERE city = ?
    // Only fetches 2 columns instead of all columns!
}

// Step 3: Use it
List<UserNameEmail> users = userRepository.findByCity("Mumbai");
for (UserNameEmail u : users) {
    System.out.println(u.getName() + " - " + u.getEmail());
}
```

### Class-Based Projection (DTO Projection)

```java
// Step 1: Create a DTO (Data Transfer Object)
public class UserSummaryDTO {
    private String name;
    private String email;
    
    // Constructor must match the query result columns
    public UserSummaryDTO(String name, String email) {
        this.name = name;
        this.email = email;
    }
    
    // Getters...
}

// Step 2: Use with @Query
public interface UserRepository extends JpaRepository<User, Long> {
    
    @Query("SELECT new com.example.dto.UserSummaryDTO(u.name, u.email) FROM User u WHERE u.city = :city")
    List<UserSummaryDTO> findUserSummaries(@Param("city") String city);
}
```

### Open Projection (With SpEL)

```java
public interface UserFullName {
    @Value("#{target.firstName + ' ' + target.lastName}")
    String getFullName();
    
    String getEmail();
}
```

---

## 12. Auditing

### What is Auditing?

Auditing automatically records **who** created/modified something and **when**.

### Setup

```java
// Step 1: Enable auditing
@Configuration
@EnableJpaAuditing
public class JpaConfig {
    
    @Bean
    public AuditorAware<String> auditorAware() {
        // Returns the current user (from security context)
        return () -> Optional.of("system-user");
        // In real app: get from SecurityContextHolder
    }
}

// Step 2: Add audit fields to your entity
@Entity
@EntityListeners(AuditingEntityListener.class)
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @CreatedDate
    @Column(name = "created_at", updatable = false)
    private LocalDateTime createdAt;
    
    @LastModifiedDate
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
    
    @CreatedBy
    @Column(name = "created_by", updatable = false)
    private String createdBy;
    
    @LastModifiedBy
    @Column(name = "updated_by")
    private String updatedBy;
}

// Step 3: Or create a reusable base class
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class Auditable {
    
    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;
    
    @LastModifiedDate
    private LocalDateTime updatedAt;
    
    @CreatedBy
    @Column(updatable = false)
    private String createdBy;
    
    @LastModifiedBy
    private String updatedBy;
}

// Now any entity can extend it:
@Entity
public class User extends Auditable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    // createdAt, updatedAt, createdBy, updatedBy are inherited!
}
```

---

## 13. Caching

### What is Caching?

Caching stores frequently accessed data in **memory** so you don't hit the database every time.

### First Level Cache (Enabled by Default)

```java
// First level cache = Persistence Context (per EntityManager session)
@Transactional
public void example() {
    User user1 = userRepository.findById(1L);  // Hits database
    User user2 = userRepository.findById(1L);  // Returns from CACHE (no DB hit!)
    // user1 == user2 → same object!
}
```

### Second Level Cache (Application-Wide)

```java
// Step 1: Add dependency
// <dependency>
//     <groupId>org.hibernate.orm</groupId>
//     <artifactId>hibernate-ehcache</artifactId>
// </dependency>

// Step 2: Configure
// application.properties:
// spring.jpa.properties.hibernate.cache.use_second_level_cache=true
// spring.jpa.properties.hibernate.cache.region.factory_class=org.hibernate.cache.ehcache.EhCacheRegionFactory

// Step 3: Mark entity as cacheable
@Entity
@Cacheable
@org.hibernate.annotations.Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class User {
    // ...
}
```

### Spring Cache Abstraction

```java
// Step 1: Enable caching
@Configuration
@EnableCaching
public class CacheConfig {
}

// Step 2: Use caching annotations
@Service
public class UserService {
    
    @Cacheable(value = "users", key = "#id")
    public User findById(Long id) {
        // First call: queries DB and caches result
        // Subsequent calls with same id: returns from cache (NO DB query!)
        return userRepository.findById(id).orElseThrow();
    }
    
    @CachePut(value = "users", key = "#user.id")
    public User updateUser(User user) {
        // Updates DB AND updates the cache
        return userRepository.save(user);
    }
    
    @CacheEvict(value = "users", key = "#id")
    public void deleteUser(Long id) {
        // Deletes from DB AND removes from cache
        userRepository.deleteById(id);
    }
    
    @CacheEvict(value = "users", allEntries = true)
    public void clearCache() {
        // Removes ALL entries from the "users" cache
    }
}
```

---

## 14. Transaction Management

### Spring Data JPA Default Behavior

```java
// All repository methods are @Transactional by default!

// Read operations → @Transactional(readOnly = true)
userRepository.findAll();      // Read-only transaction
userRepository.findById(1L);   // Read-only transaction

// Write operations → @Transactional
userRepository.save(user);     // Read-write transaction
userRepository.delete(user);   // Read-write transaction
```

### Custom Transactions in Service Layer

```java
@Service
public class OrderService {
    
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private InventoryRepository inventoryRepository;
    
    @Autowired
    private PaymentService paymentService;
    
    @Transactional  // Wraps everything in one transaction
    public Order placeOrder(OrderRequest request) {
        // Step 1: Create order
        Order order = new Order(request.getProduct(), request.getQuantity());
        orderRepository.save(order);
        
        // Step 2: Update inventory
        inventoryRepository.decreaseStock(request.getProduct(), request.getQuantity());
        
        // Step 3: Process payment
        paymentService.charge(request.getPaymentDetails());
        
        // If ANY step fails → ALL steps are ROLLED BACK
        return order;
    }
    
    @Transactional(readOnly = true)  // Optimization for read-only
    public List<Order> getOrderHistory(Long userId) {
        return orderRepository.findByUserId(userId);
    }
}
```

---

## Quick Revision Cheat Sheet

```
Spring Data JPA = Write interface → Get implementation FREE

Repository Hierarchy:
  CrudRepository (basic CRUD)
    → PagingAndSortingRepository (+ pagination, sorting)
      → JpaRepository (+ flush, batch, List return) ← USE THIS

Query Method Names:
  findByName → WHERE name = ?
  findByAgeGreaterThan → WHERE age > ?
  findByNameContaining → WHERE name LIKE '%?%'
  findByActiveTrue → WHERE active = true
  findByNameAndCity → WHERE name = ? AND city = ?
  countByCity → SELECT COUNT(*) WHERE city = ?

@Query:
  @Query("SELECT u FROM User u WHERE u.age > :age")  ← JPQL
  @Query(value = "SELECT * FROM users", nativeQuery = true)  ← Native SQL
  @Modifying + @Transactional → for UPDATE/DELETE queries

Pagination:
  PageRequest.of(page, size, sort)
  Page<User> → getContent(), getTotalPages(), hasNext()

Projections:
  Interface projection → Only selected fields loaded
  DTO projection → new DTO(field1, field2) in @Query

Auditing:
  @CreatedDate, @LastModifiedDate, @CreatedBy, @LastModifiedBy
  @EnableJpaAuditing + AuditorAware

Caching:
  @Cacheable → Read from cache
  @CachePut → Update cache
  @CacheEvict → Remove from cache

Specifications:
  Dynamic queries at runtime
  Combine filters: spec.and(), spec.or()
```

---

**Next: [07-Spring-Boot.md](07-Spring-Boot.md) — Spring Boot Basics, Auto-Configuration, Starters, and Profiles**
