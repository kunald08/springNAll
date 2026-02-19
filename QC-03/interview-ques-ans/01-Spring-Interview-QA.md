# Spring Core & IoC/DI — Interview Questions & Answers

---

### 1. What is the Spring Framework?
Spring is an open-source Java framework that provides comprehensive infrastructure support for developing enterprise applications. It simplifies Java development by providing features like IoC (Inversion of Control), DI (Dependency Injection), AOP, transaction management, and more.

### 2. What are the main modules of Spring?
- **Spring Core** — IoC container, DI
- **Spring AOP** — Aspect-Oriented Programming
- **Spring Data** — Database access (JDBC, JPA, MongoDB)
- **Spring MVC** — Web framework
- **Spring Security** — Authentication and authorization
- **Spring Boot** — Auto-configuration and rapid development
- **Spring Cloud** — Microservices support

### 3. What is Inversion of Control (IoC)?
IoC means the control of creating and managing objects is **inverted** from the developer to the Spring container. Instead of you creating objects with `new`, the Spring container creates and manages them for you.

```java
// Without IoC (you create)
UserService service = new UserService(new UserRepository());

// With IoC (Spring creates)
@Service
public class UserService {
    @Autowired
    private UserRepository repo;  // Spring injects this
}
```

### 4. What is Dependency Injection (DI)?
DI is a design pattern where an object's dependencies are provided (injected) by an external source (Spring container) rather than the object creating them itself. DI is one way to achieve IoC.

### 5. What are the types of Dependency Injection?
1. **Constructor Injection** — Dependencies passed via constructor (RECOMMENDED)
2. **Setter Injection** — Dependencies set via setter methods
3. **Field Injection** — Dependencies injected directly into fields using `@Autowired`

```java
// Constructor Injection (Best Practice)
@Service
public class UserService {
    private final UserRepository repo;
    
    public UserService(UserRepository repo) {
        this.repo = repo;
    }
}

// Setter Injection
@Service
public class UserService {
    private UserRepository repo;
    
    @Autowired
    public void setRepo(UserRepository repo) {
        this.repo = repo;
    }
}

// Field Injection (Not recommended)
@Service
public class UserService {
    @Autowired
    private UserRepository repo;
}
```

### 6. Why is Constructor Injection preferred?
- **Immutability** — Fields can be `final` (cannot be changed after construction)
- **No null dependencies** — Object cannot be created without required dependencies
- **Testability** — Easy to pass mock dependencies in unit tests
- **No reflection** — Does not use reflection like field injection

### 7. What is the Spring IoC Container?
The Spring IoC Container is responsible for creating objects (beans), wiring them together, configuring them, and managing their lifecycle. There are two types:
- **BeanFactory** — Basic container (lazy loading)
- **ApplicationContext** — Advanced container (eager loading, i18n, events)

### 8. What is the difference between BeanFactory and ApplicationContext?

| Feature | BeanFactory | ApplicationContext |
|---------|------------|-------------------|
| Bean loading | Lazy (on demand) | Eager (at startup) |
| Event handling | No | Yes |
| Internationalization | No | Yes |
| AOP support | No | Yes |
| Annotation support | Limited | Full |

Always use `ApplicationContext` in real projects.

### 9. What is a Spring Bean?
A Spring Bean is an object that is created, managed, and configured by the Spring IoC container. Any class annotated with `@Component`, `@Service`, `@Repository`, or `@Controller` becomes a Spring Bean.

### 10. What are the bean scopes in Spring?

| Scope | Description |
|-------|-------------|
| **singleton** (default) | One instance per Spring container |
| **prototype** | New instance every time it's requested |
| **request** | One instance per HTTP request (web only) |
| **session** | One instance per HTTP session (web only) |
| **application** | One instance per ServletContext (web only) |

```java
@Component
@Scope("prototype")
public class PrototypeBean { }
```

### 11. What is the difference between singleton and prototype scope?
- **Singleton** — One instance shared by all. Same object every time you ask for it.
- **Prototype** — New instance every time you ask. Each request gets a fresh object.

### 12. What is the bean lifecycle in Spring?
1. Container starts → reads configuration
2. Bean is instantiated (created)
3. Dependencies are injected
4. `@PostConstruct` method is called (initialization)
5. Bean is ready to use
6. Container shuts down
7. `@PreDestroy` method is called (cleanup)

### 13. What is @Autowired?
`@Autowired` is an annotation that tells Spring to automatically inject the required dependency. Spring finds the matching bean by type and injects it.

### 14. What if there are multiple beans of the same type? How does Spring resolve it?
You can use:
- `@Qualifier("beanName")` — Specify which bean to inject
- `@Primary` — Mark one bean as the default choice
- Constructor parameter name matching

```java
@Autowired
@Qualifier("mysqlRepo")
private UserRepository repo;
```

### 15. What is @Component, @Service, @Repository, @Controller?
All are specializations of `@Component` that mark a class as a Spring bean:
- `@Component` — Generic bean
- `@Service` — Business logic layer
- `@Repository` — Data access layer (also adds exception translation)
- `@Controller` — Web controller (handles HTTP requests)

### 16. What is the difference between @Component and @Bean?

| Feature | @Component | @Bean |
|---------|-----------|------|
| Where | On a class | On a method (inside @Configuration) |
| Detection | Auto-detected by component scanning | Explicitly declared |
| Control | Less control over instantiation | Full control over how bean is created |
| Third-party | Cannot use on third-party classes | Can create beans from third-party classes |

### 17. What is @Configuration?
`@Configuration` marks a class as a source of bean definitions. Methods annotated with `@Bean` inside it return objects that are registered as beans in the Spring container.

```java
@Configuration
public class AppConfig {
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

### 18. What is Component Scanning?
Component Scanning is the process where Spring automatically detects classes annotated with `@Component` (and its specializations) and registers them as beans. By default, Spring scans the package where the main class is located and all sub-packages.

### 19. What is @PostConstruct and @PreDestroy?
- `@PostConstruct` — Method runs AFTER the bean is created and dependencies are injected (initialization logic)
- `@PreDestroy` — Method runs BEFORE the bean is destroyed (cleanup logic)

### 20. What is @Value annotation?
`@Value` injects values from property files or SpEL expressions into fields.

```java
@Value("${app.name}")
private String appName;

@Value("${server.port:8080}")  // Default value 8080
private int port;
```

---

# Spring Annotations & Configuration — Interview Questions

### 21. What is SpEL (Spring Expression Language)?
SpEL is a powerful expression language that supports querying and manipulating objects at runtime. You can use it in annotations with `#{expression}`.

```java
@Value("#{2 + 3}")           // 5
@Value("#{systemProperties['user.name']}") // System property
@Value("#{T(java.lang.Math).PI}")          // Static method
```

### 22. What is the difference between @Value("${...}") and @Value("#{...}")?
- `${}` — Property placeholder, reads from application.properties
- `#{}` — SpEL expression, evaluates expressions at runtime

### 23. What is @PropertySource?
`@PropertySource` loads a custom properties file into Spring's Environment.

```java
@Configuration
@PropertySource("classpath:custom.properties")
public class AppConfig { }
```

### 24. What are Spring Profiles?
Profiles allow you to define different configurations for different environments (dev, test, prod). You activate a profile using `spring.profiles.active`.

```properties
# application-dev.properties → Development settings
# application-prod.properties → Production settings
spring.profiles.active=dev
```

### 25. What is @Profile annotation?
`@Profile` marks a bean to be created only when a specific profile is active.

```java
@Bean
@Profile("dev")
public DataSource devDataSource() { ... }

@Bean
@Profile("prod")
public DataSource prodDataSource() { ... }
```

---

# Maven — Interview Questions

### 26. What is Maven?
Maven is a build automation and project management tool for Java. It handles dependency management, project structure, building, testing, and packaging.

### 27. What is POM (pom.xml)?
POM (Project Object Model) is the fundamental unit in Maven. It's an XML file (`pom.xml`) that contains project configuration, dependencies, plugins, and build instructions.

### 28. What are Maven coordinates (GAV)?
- **G** — groupId (organization, e.g., `com.example`)
- **A** — artifactId (project name, e.g., `my-app`)
- **V** — version (e.g., `1.0.0`)

### 29. What is the Maven build lifecycle?
The default lifecycle phases: `validate → compile → test → package → verify → install → deploy`

### 30. What are Maven dependency scopes?
- **compile** (default) — Available everywhere
- **provided** — Available at compile time, NOT at runtime (e.g., Servlet API)
- **runtime** — NOT needed at compile time, needed at runtime (e.g., JDBC driver)
- **test** — Only for testing (e.g., JUnit)

### 31. What is a transitive dependency?
If your project depends on A, and A depends on B, then B is a **transitive dependency** — Maven downloads it automatically.

### 32. What is the difference between Maven's install and deploy?
- `mvn install` — Installs the artifact in your LOCAL Maven repository (~/.m2)
- `mvn deploy` — Uploads the artifact to a REMOTE repository (Nexus, Artifactory)

---

# Spring JDBC & Transactions — Interview Questions

### 33. What is JdbcTemplate?
JdbcTemplate is a Spring class that simplifies JDBC operations. It handles connection management, statement creation, exception handling, and resource cleanup automatically.

### 34. What problems does JdbcTemplate solve compared to plain JDBC?
- No manual connection management
- No try-catch-finally blocks
- Automatic resource cleanup
- Consistent exception handling
- Less boilerplate code

### 35. What is NamedParameterJdbcTemplate?
It's like JdbcTemplate but uses named parameters (:name) instead of positional placeholders (?), making queries more readable.

```java
// JdbcTemplate: 
"SELECT * FROM users WHERE name = ? AND age = ?"

// NamedParameterJdbcTemplate:
"SELECT * FROM users WHERE name = :name AND age = :age"
```

### 36. What is RowMapper?
RowMapper is an interface that maps each row of a ResultSet to a Java object. You implement `mapRow()` to define how columns map to fields.

### 37. What is @Transactional?
`@Transactional` annotation manages database transactions declaratively. It ensures that a group of operations either ALL succeed or ALL fail (rollback).

### 38. How does @Transactional work under the hood?
Spring creates a **proxy** around the annotated class/method. When the method is called:
1. Proxy starts a transaction
2. Real method executes
3. If no exception → commit
4. If RuntimeException → rollback

### 39. What are the important @Transactional attributes?
- `propagation` — How transactions relate to each other
- `isolation` — Concurrency control level
- `readOnly` — Optimization hint for read-only operations
- `timeout` — Maximum time for transaction
- `rollbackFor` — Which exceptions trigger rollback

### 40. When does @Transactional NOT work?
- Calling a `@Transactional` method from the SAME class (self-invocation)
- Method is `private` (proxy can't override it)
- Only `RuntimeException` triggers rollback by default (not checked exceptions)

### 41. What are transaction propagation types?

| Type | Behavior |
|------|----------|
| REQUIRED (default) | Join existing transaction, or create new |
| REQUIRES_NEW | Always create new (suspend existing) |
| SUPPORTS | Join if exists, or run without transaction |
| NOT_SUPPORTED | Run without transaction (suspend if exists) |
| MANDATORY | Must have existing transaction (else exception) |
| NEVER | Must NOT have transaction (else exception) |

---

# ORM, JPA & Hibernate — Interview Questions

### 42. What is ORM?
Object-Relational Mapping is a technique that maps Java objects to database tables. Instead of writing SQL, you work with Java objects and the ORM framework handles the SQL.

### 43. What is the difference between JPA and Hibernate?
- **JPA** — A specification (set of rules/interfaces). It defines WHAT to do.
- **Hibernate** — An implementation of JPA. It does the actual work.

JPA is like an interface; Hibernate is like the class that implements it.

### 44. What is an Entity?
An Entity is a Java class that is mapped to a database table. It's annotated with `@Entity` and must have an `@Id` field.

### 45. What is the difference between @Entity and @Table?
- `@Entity` — Marks a class as a JPA entity
- `@Table` — Specifies the database table name (optional; defaults to class name)

### 46. What are the JPA relationship types?
- `@OneToOne` — One user has one address
- `@OneToMany` — One user has many orders
- `@ManyToOne` — Many orders belong to one user
- `@ManyToMany` — Many students have many courses

### 47. What is the difference between LAZY and EAGER fetching?
- **EAGER** — Related data is loaded IMMEDIATELY with the main query
- **LAZY** — Related data is loaded ONLY when you actually access it

```
@OneToMany(fetch = FetchType.LAZY)   // Load orders only when getOrders() is called
@ManyToOne(fetch = FetchType.EAGER)  // Load user immediately with the order
```

### 48. What is the N+1 problem?
If you load 10 users and each has orders with LAZY loading, accessing each user's orders results in 1 query for users + 10 queries for orders = 11 queries total. Solution: Use `JOIN FETCH` in JPQL.

### 49. What are Cascade Types?
Cascade propagates operations from parent to child entities:
- `CascadeType.PERSIST` — Save parent → save child too
- `CascadeType.REMOVE` — Delete parent → delete child too
- `CascadeType.ALL` — All operations cascade
- `orphanRemoval = true` — Delete child when removed from parent's collection

### 50. What are the entity states in JPA?
1. **Transient** — New object, not yet managed (`new User()`)
2. **Persistent/Managed** — Saved and tracked by EntityManager
3. **Detached** — Was managed but session closed
4. **Removed** — Marked for deletion

---

# Spring Data JPA — Interview Questions

### 51. What is Spring Data JPA?
Spring Data JPA is a Spring module that simplifies JPA/database access by providing built-in repository methods. You just define an interface, and Spring creates the implementation automatically.

### 52. What is the difference between CrudRepository and JpaRepository?

| Feature | CrudRepository | JpaRepository |
|---------|---------------|---------------|
| CRUD methods | Yes | Yes |
| Pagination | No | Yes |
| Sorting | No | Yes |
| Batch delete | No | Yes |
| flush() | No | Yes |

JpaRepository extends CrudRepository (it has everything CrudRepository has, plus more).

### 53. How do derived query methods work?
Spring Data JPA reads the method name and creates a query automatically:

```java
List<User> findByName(String name);
// → SELECT * FROM users WHERE name = ?

List<User> findByAgeBetween(int min, int max);
// → SELECT * FROM users WHERE age BETWEEN ? AND ?

List<User> findByNameContainingIgnoreCase(String keyword);
// → SELECT * FROM users WHERE LOWER(name) LIKE '%keyword%'
```

### 54. What is @Query annotation?
`@Query` lets you write custom JPQL or native SQL queries.

```java
@Query("SELECT u FROM User u WHERE u.age > :age")
List<User> findUsersOlderThan(@Param("age") int age);

@Query(value = "SELECT * FROM users WHERE city = :city", nativeQuery = true)
List<User> findByCityNative(@Param("city") String city);
```

### 55. What is the difference between JPQL and native SQL?
- **JPQL** — Uses entity/class names (Java-based). Portable across databases.
- **Native SQL** — Uses table/column names (database-specific). Not portable.

### 56. What is Pagination in Spring Data JPA?
Pagination divides large result sets into pages using `Pageable` and `Page`.

```java
Page<User> findAll(Pageable pageable);
// Usage: PageRequest.of(0, 10, Sort.by("name"))
```

### 57. What are Projections in Spring Data JPA?
Projections return only specific fields instead of the entire entity:
- **Interface-based** — Define an interface with getter methods
- **DTO-based** — Use a DTO class with constructor

### 58. What is Spring Data JPA Auditing?
Auditing automatically tracks who created/modified a record and when, using `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, `@LastModifiedBy`.

---

# Spring Boot — Interview Questions

### 59. What is Spring Boot?
Spring Boot is a framework built on top of Spring that simplifies Spring application development by providing auto-configuration, embedded servers, and convention-over-configuration approach.

### 60. What is the difference between Spring and Spring Boot?

| Feature | Spring | Spring Boot |
|---------|--------|-------------|
| Configuration | Manual (XML/Java) | Auto-configuration |
| Server | External (Tomcat WAR) | Embedded (Tomcat JAR) |
| Boilerplate | Lots of configuration | Minimal configuration |
| Startup | Slower to set up | Quick to start |

### 61. What is Auto-Configuration?
Spring Boot automatically configures beans based on the dependencies in your classpath. For example, if `spring-boot-starter-data-jpa` is present, Spring Boot auto-configures DataSource, EntityManagerFactory, and TransactionManager.

### 62. How does Auto-Configuration work under the hood?
1. `@EnableAutoConfiguration` triggers it
2. Spring reads `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`
3. Each auto-configuration class has `@ConditionalOn*` annotations
4. Beans are created only if conditions are met

### 63. What is @SpringBootApplication?
It's a combined annotation:
```java
@SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan
```

### 64. What are Spring Boot Starters?
Starters are pre-packaged dependency sets. Instead of adding 10 individual dependencies, you add one starter.

Example: `spring-boot-starter-web` includes Spring MVC, Tomcat, Jackson, validation, etc.

### 65. What is the embedded server in Spring Boot?
Spring Boot packages an embedded web server (Tomcat by default) inside the JAR. You run the app with `java -jar app.jar` — no need to install Tomcat separately.

### 66. What is application.properties / application.yml?
Configuration files where you set application properties like server port, database URL, logging level, etc. YAML (`.yml`) is an alternative format that uses indentation.

### 67. What is @ConfigurationProperties?
Binds a group of related properties to a Java class with type safety.

```java
@Component
@ConfigurationProperties(prefix = "app.mail")
public class MailProperties {
    private String host;
    private int port;
    // Getters and setters
}
```

### 68. What are Spring Boot Profiles?
Profiles provide environment-specific configurations. You can have `application-dev.properties`, `application-prod.properties`, and activate one with `spring.profiles.active=dev`.

---

# Spring MVC & REST — Interview Questions

### 69. What is the MVC pattern?
MVC separates an application into **Model** (data), **View** (UI), and **Controller** (request handling). The controller receives requests, works with the model, and returns a view.

### 70. What is DispatcherServlet?
DispatcherServlet is the **front controller** of Spring MVC. It receives ALL HTTP requests and dispatches them to the appropriate controller based on URL mapping.

### 71. What is the difference between @Controller and @RestController?
- `@Controller` — Returns VIEW names (HTML pages via Thymeleaf)
- `@RestController` — Returns DATA directly (JSON), equivalent to `@Controller + @ResponseBody`

### 72. What is @RequestMapping?
`@RequestMapping` maps HTTP requests to controller methods. It specifies the URL path and HTTP method. Shortcut annotations: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`.

### 73. What is the difference between @PathVariable and @RequestParam?
- `@PathVariable` — Extracts values from the URL path: `/users/{id}`
- `@RequestParam` — Extracts values from query parameters: `/users?name=Kunal`

### 74. What is @RequestBody and @ResponseBody?
- `@RequestBody` — Converts incoming JSON to Java object (deserialization)
- `@ResponseBody` — Converts Java object to JSON in the response (serialization)

### 75. What is ResponseEntity?
`ResponseEntity` represents the entire HTTP response — status code, headers, and body. It gives you full control over the response.

```java
return ResponseEntity.status(HttpStatus.CREATED).body(user);
return ResponseEntity.notFound().build();
```

### 76. What is REST?
REST (Representational State Transfer) is an architectural style for APIs. It uses standard HTTP methods (GET, POST, PUT, DELETE), resource-based URLs, and is stateless.

### 77. What HTTP status codes should a REST API return?
- 200 OK — Successful GET/PUT
- 201 Created — Successful POST
- 204 No Content — Successful DELETE
- 400 Bad Request — Invalid input
- 401 Unauthorized — Not authenticated
- 403 Forbidden — Not authorized
- 404 Not Found — Resource missing
- 500 Internal Server Error — Server bug

### 78. What is idempotent?
An operation is idempotent if calling it multiple times produces the same result. GET, PUT, DELETE are idempotent. POST is NOT idempotent (each call creates a new resource).

### 79. What is a DTO (Data Transfer Object)?
A DTO is a simple object used to transfer data between layers. It controls what data is exposed to the client, hiding internal fields like passwords.

---

# Exception Handling & Validation — Interview Questions

### 80. What is @ExceptionHandler?
`@ExceptionHandler` handles specific exceptions in a controller. It catches the exception and returns a custom response.

### 81. What is @ControllerAdvice?
`@ControllerAdvice` is a GLOBAL exception handler that applies to ALL controllers. It centralizes exception handling in one class.

### 82. What is the difference between @ControllerAdvice and @RestControllerAdvice?
- `@ControllerAdvice` — For controllers returning views
- `@RestControllerAdvice` — For REST controllers (adds `@ResponseBody` automatically)

### 83. How do you validate request bodies in Spring Boot?
1. Add `spring-boot-starter-validation` dependency
2. Add validation annotations to the DTO (`@NotNull`, `@Email`, etc.)
3. Add `@Valid` before `@RequestBody` in the controller

### 84. What is @Valid annotation?
`@Valid` triggers bean validation. When placed before a parameter, Spring validates the object and throws `MethodArgumentNotValidException` if validation fails.

### 85. Name some common validation annotations.
`@NotNull`, `@NotBlank`, `@NotEmpty`, `@Size`, `@Min`, `@Max`, `@Email`, `@Pattern`, `@Past`, `@Future`, `@Positive`, `@PositiveOrZero`

### 86. What is the difference between @NotNull, @NotEmpty, and @NotBlank?
- `@NotNull` — Value is not null (allows empty string "")
- `@NotEmpty` — Not null AND not empty (allows whitespace "   ")
- `@NotBlank` — Not null AND not empty AND not whitespace (most strict, for strings)

---

# Security — Interview Questions

### 87. What is Spring Security?
Spring Security is a framework for authentication (who are you?) and authorization (what can you do?) in Spring applications.

### 88. What is the difference between Authentication and Authorization?
- **Authentication** — Verifying identity (login with username/password)
- **Authorization** — Checking permissions (can this user access this resource?)

Authentication happens first, then authorization.

### 89. What is UserDetailsService?
An interface you implement to tell Spring Security how to load user data (usually from a database). The `loadUserByUsername()` method returns a `UserDetails` object.

### 90. Why should passwords be encoded?
Plain-text passwords in the database are a security disaster. If the database is breached, all passwords are exposed. BCrypt hashing makes passwords unreadable and irreversible.

### 91. What is the SecurityFilterChain?
`SecurityFilterChain` defines security rules — which URLs are public, which require authentication, which require specific roles.

### 92. What is @PreAuthorize?
Method-level security that checks if the user has the required role before executing the method.

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }
```

---

# Testing — Interview Questions

### 93. What is @SpringBootTest?
`@SpringBootTest` loads the FULL Spring application context for integration testing. It starts the Spring container with all beans.

### 94. What is the difference between @SpringBootTest, @WebMvcTest, and @DataJpaTest?
- `@SpringBootTest` — Full application (all layers)
- `@WebMvcTest` — Only controller/web layer (fast)
- `@DataJpaTest` — Only repository/JPA layer with H2 database

### 95. What is MockMvc?
MockMvc simulates HTTP requests to controllers WITHOUT starting a real web server. It's faster than making actual HTTP calls.

### 96. What is the difference between @Mock and @MockBean?
- `@Mock` (Mockito) — Creates a mock object, no Spring context needed
- `@MockBean` (Spring) — Creates a mock and registers it in the Spring application context

### 97. What is the AAA pattern in testing?
- **Arrange** — Set up test data and mock behavior
- **Act** — Call the method being tested
- **Assert** — Verify the result

---

# Microservices — Interview Questions

### 98. What are Microservices?
Microservices is an architectural style where a large application is built as a collection of small, independent services. Each service focuses on one business function, has its own database, and communicates over APIs.

### 99. What is the difference between Monolithic and Microservices?
- **Monolithic** — Single deployable unit, shared database, one technology stack
- **Microservices** — Multiple independent services, each with own database, can use different technologies

### 100. What is Service Discovery?
Service Discovery is a mechanism where services register themselves with a registry (like Eureka) and find other services by name instead of hardcoded URLs.

### 101. What is an API Gateway?
A single entry point for all client requests that routes them to the correct microservice. It also handles cross-cutting concerns like authentication, rate limiting, and logging.

### 102. What is a Circuit Breaker?
A fault-tolerance pattern that stops calling a failing service after a threshold of failures. It has three states: CLOSED (normal), OPEN (blocking calls), HALF-OPEN (testing recovery).

### 103. How do microservices communicate?
- **Synchronous** — REST calls (RestTemplate, WebClient, OpenFeign)
- **Asynchronous** — Message queues (RabbitMQ, Apache Kafka)

### 104. What is the Database per Service pattern?
Each microservice has its own database. Services cannot directly access another service's database — they must use APIs. This ensures loose coupling and independent deployment.

### 105. What is the Saga pattern?
Saga handles distributed transactions across multiple services by dividing a transaction into a sequence of local transactions. If one fails, compensating transactions undo the previous steps.

---

## Top 10 Most Asked Questions (Quick List)

1. What is Spring Boot and how is it different from Spring?
2. What is Dependency Injection? Types?
3. What is @Transactional and how does it work?
4. Difference between @Controller and @RestController?
5. How does Spring Boot Auto-Configuration work?
6. What are bean scopes? Singleton vs Prototype?
7. How do you handle exceptions globally in Spring Boot?
8. What is the difference between JPA and Hibernate?
9. How does @SpringBootTest differ from @WebMvcTest?
10. What are Microservices? Monolith vs Microservices?
