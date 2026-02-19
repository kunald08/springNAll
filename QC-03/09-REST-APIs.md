# REST APIs with Spring Boot - Complete In-Depth Guide

## Table of Contents
- [1. What is REST?](#1-what-is-rest)
- [2. REST Principles](#2-rest-principles)
- [3. HTTP Methods](#3-http-methods)
- [4. HTTP Status Codes](#4-http-status-codes)
- [5. Building REST APIs with Spring Boot](#5-building-rest-apis-with-spring-boot)
- [6. ResponseEntity](#6-responseentity)
- [7. Complete CRUD REST API Example](#7-complete-crud-rest-api-example)
- [8. Data Access with Spring Boot (Putting It All Together)](#8-data-access-with-spring-boot-putting-it-all-together)
- [9. Request and Response Customization](#9-request-and-response-customization)
- [10. API Best Practices](#10-api-best-practices)

---

## 1. What is REST?

### Definition

**REST (Representational State Transfer)** is an architectural style for designing APIs (Application Programming Interfaces) that allows different applications to communicate over HTTP.

### Simple Analogy

```
Think of REST like a RESTAURANT:

You (Client/Browser) → Waiter (API) → Kitchen (Server/Database)

1. You look at the MENU (API documentation)
2. You TELL the waiter what you want (HTTP Request)
3. The waiter goes to the kitchen (Server processes)
4. The waiter brings your food (HTTP Response with data)

You don't go to the kitchen yourself!
You communicate through a standard protocol (the waiter/API).
```

### What is an API?

```
API = Application Programming Interface

It's a CONTRACT between two systems:
"If you send me THIS request, I'll give you THAT response."

Example:
  Request:  GET /api/users/5
  Response: {"id": 5, "name": "Kunal", "email": "kunal@email.com"}
```

---

## 2. REST Principles

### 1. Client-Server Separation

```
CLIENT (Frontend)              SERVER (Backend)
- React, Angular, Mobile       - Spring Boot
- Sends requests               - Processes requests
- Displays data                - Returns data

They are INDEPENDENT. You can change the frontend without
touching the backend, and vice versa.
```

### 2. Stateless

```
Each request contains ALL the information the server needs.
The server does NOT remember previous requests.

❌ Bad (Stateful):
  Request 1: "Login as Kunal" → Server remembers
  Request 2: "Show my orders" → Server knows it's Kunal

✅ Good (Stateless):
  Request 1: "Login as Kunal" → Server returns token
  Request 2: "Show orders" + token → Server identifies Kunal from token
```

### 3. Uniform Interface — Resource-Based URLs

```
Resources are identified by URLs (URIs):

✅ Good REST URLs:
  GET    /api/users          → Get all users
  GET    /api/users/5        → Get user with ID 5
  POST   /api/users          → Create a new user
  PUT    /api/users/5        → Update user 5
  DELETE /api/users/5        → Delete user 5
  GET    /api/users/5/orders → Get orders of user 5

❌ Bad URLs (not RESTful):
  GET /api/getAllUsers
  POST /api/createUser
  GET /api/deleteUser?id=5
  POST /api/updateUserById
```

### 4. Use of Standard HTTP Methods

| Method | Action | Safe? | Idempotent? |
|--------|--------|-------|-------------|
| GET | Read data | Yes | Yes |
| POST | Create new | No | No |
| PUT | Update (full) | No | Yes |
| PATCH | Update (partial) | No | No |
| DELETE | Remove | No | Yes |

```
Safe = Does NOT change data on the server
Idempotent = Calling it multiple times gives the SAME result

GET /users/5 → Always returns user 5 (safe + idempotent)
DELETE /users/5 → First call deletes, next calls still result in "deleted" state (idempotent)
POST /users → Each call creates a NEW user (NOT idempotent)
```

---

## 3. HTTP Methods

### GET — Retrieve Data

```java
// Get all users
@GetMapping("/api/users")
public List<User> getAllUsers() {
    return userService.findAll();
}

// Get one user
@GetMapping("/api/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}
```

```
Request:  GET /api/users/5
Response: 200 OK
{
    "id": 5,
    "name": "Kunal",
    "email": "kunal@email.com"
}
```

### POST — Create New Resource

```java
@PostMapping("/api/users")
public User createUser(@RequestBody User user) {
    return userService.save(user);
}
```

```
Request:  POST /api/users
Body:     {"name": "Kunal", "email": "kunal@email.com"}
Response: 201 Created
{
    "id": 10,
    "name": "Kunal",
    "email": "kunal@email.com"
}
```

### PUT — Update Entire Resource

```java
@PutMapping("/api/users/{id}")
public User updateUser(@PathVariable Long id, @RequestBody User user) {
    user.setId(id);
    return userService.save(user);
}
```

```
Request:  PUT /api/users/5
Body:     {"name": "Kunal Updated", "email": "kunal.new@email.com", "age": 26}
Response: 200 OK
→ Replaces ALL fields. If you omit "age", it becomes null!
```

### PATCH — Partial Update

```java
@PatchMapping("/api/users/{id}")
public User patchUser(@PathVariable Long id, @RequestBody Map<String, Object> updates) {
    return userService.partialUpdate(id, updates);
}
```

```
Request:  PATCH /api/users/5
Body:     {"email": "new@email.com"}
Response: 200 OK
→ Only updates the email. Other fields stay the same.
```

### DELETE — Remove Resource

```java
@DeleteMapping("/api/users/{id}")
public void deleteUser(@PathVariable Long id) {
    userService.deleteById(id);
}
```

```
Request:  DELETE /api/users/5
Response: 204 No Content (or 200 OK)
```

---

## 4. HTTP Status Codes

### Categories

```
1xx → Informational (rarely used in APIs)
2xx → SUCCESS ✅
3xx → Redirection ↩️
4xx → CLIENT Error (your fault) ❌
5xx → SERVER Error (our fault) 💥
```

### Most Important Status Codes

| Code | Name | When to Use |
|------|------|-------------|
| **200** | OK | Successful GET, PUT, PATCH |
| **201** | Created | Successful POST (new resource created) |
| **204** | No Content | Successful DELETE (nothing to return) |
| **400** | Bad Request | Invalid data from client |
| **401** | Unauthorized | Not logged in / no credentials |
| **403** | Forbidden | Logged in but not allowed |
| **404** | Not Found | Resource doesn't exist |
| **405** | Method Not Allowed | Wrong HTTP method |
| **409** | Conflict | Duplicate data (e.g., email already exists) |
| **500** | Internal Server Error | Server crashed / unhandled exception |

### How to Remember

```
200 = "Here's your data" ✅
201 = "I created it for you" ✅
204 = "Done, nothing to say" ✅
400 = "You sent bad data" ❌
401 = "Who are you? Login first!" 🔑
403 = "I know who you are, but NO" 🚫
404 = "That doesn't exist" 🔍
500 = "Something broke on my end" 💥
```

---

## 5. Building REST APIs with Spring Boot

### Project Setup

```xml
<!-- pom.xml dependencies -->
<dependencies>
    <!-- REST API support -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Database access -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    
    <!-- In-memory database for learning -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
    
    <!-- Validation -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
</dependencies>
```

### Layered Architecture

```
┌──────────────────────────────────────────┐
│       CLIENT (Browser, Postman, React)    │
│          Sends HTTP requests              │
└────────────────┬─────────────────────────┘
                 │
                 ↓
┌──────────────────────────────────────────┐
│  CONTROLLER Layer (@RestController)       │
│  - Receives HTTP requests                 │
│  - Validates input                        │
│  - Calls service layer                    │
│  - Returns HTTP responses                 │
└────────────────┬─────────────────────────┘
                 │
                 ↓
┌──────────────────────────────────────────┐
│  SERVICE Layer (@Service)                 │
│  - Contains business logic                │
│  - Applies rules and validations          │
│  - Calls repository layer                 │
└────────────────┬─────────────────────────┘
                 │
                 ↓
┌──────────────────────────────────────────┐
│  REPOSITORY Layer (@Repository)           │
│  - Talks to database                      │
│  - Uses Spring Data JPA                   │
│  - Returns entity objects                 │
└────────────────┬─────────────────────────┘
                 │
                 ↓
┌──────────────────────────────────────────┐
│           DATABASE (H2, MySQL, etc.)      │
└──────────────────────────────────────────┘
```

---

## 6. ResponseEntity

### What is ResponseEntity?

`ResponseEntity<T>` lets you control the complete HTTP response — status code, headers, and body.

```java
// Without ResponseEntity (limited control)
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);  // Always returns 200 OK
}

// With ResponseEntity (full control)
@GetMapping("/users/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    if (user == null) {
        return ResponseEntity.notFound().build();  // 404
    }
    return ResponseEntity.ok(user);  // 200 with body
}
```

### ResponseEntity Builder Methods

```java
// 200 OK with body
ResponseEntity.ok(user);
ResponseEntity.ok().body(user);

// 201 Created with body and location header
ResponseEntity.created(URI.create("/api/users/" + user.getId()))
              .body(user);

// 204 No Content
ResponseEntity.noContent().build();

// 400 Bad Request
ResponseEntity.badRequest().body(errorMessage);

// 404 Not Found
ResponseEntity.notFound().build();

// Custom status
ResponseEntity.status(HttpStatus.CONFLICT).body("Email already exists");

// With custom headers
ResponseEntity.ok()
    .header("X-Custom-Header", "value")
    .header("Cache-Control", "no-cache")
    .body(data);
```

---

## 7. Complete CRUD REST API Example

### Entity

```java
@Entity
@Table(name = "users")
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    @Column(nullable = false, unique = true)
    private String email;
    
    private int age;
    
    private String city;
    
    // Default constructor (required by JPA)
    public User() {}
    
    // Parameterized constructor
    public User(String name, String email, int age, String city) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.city = city;
    }
    
    // Getters and setters...
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
    public String getCity() { return city; }
    public void setCity(String city) { this.city = city; }
}
```

### Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    Optional<User> findByEmail(String email);
    List<User> findByCity(String city);
    boolean existsByEmail(String email);
}
```

### Service

```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    // Get all users
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    // Get user by ID
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User not found with id: " + id));
    }
    
    // Create user
    public User createUser(User user) {
        if (userRepository.existsByEmail(user.getEmail())) {
            throw new DuplicateResourceException("Email already exists: " + user.getEmail());
        }
        return userRepository.save(user);
    }
    
    // Update user
    public User updateUser(Long id, User updatedUser) {
        User existingUser = getUserById(id);
        existingUser.setName(updatedUser.getName());
        existingUser.setEmail(updatedUser.getEmail());
        existingUser.setAge(updatedUser.getAge());
        existingUser.setCity(updatedUser.getCity());
        return userRepository.save(existingUser);
    }
    
    // Delete user
    public void deleteUser(Long id) {
        User user = getUserById(id);
        userRepository.delete(user);
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    // GET /api/users — Get all users
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        List<User> users = userService.getAllUsers();
        return ResponseEntity.ok(users);  // 200 OK
    }
    
    // GET /api/users/5 — Get one user
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(user);  // 200 OK
    }
    
    // POST /api/users — Create new user
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User createdUser = userService.createUser(user);
        URI location = URI.create("/api/users/" + createdUser.getId());
        return ResponseEntity.created(location).body(createdUser);  // 201 Created
    }
    
    // PUT /api/users/5 — Update user
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
        User updatedUser = userService.updateUser(id, user);
        return ResponseEntity.ok(updatedUser);  // 200 OK
    }
    
    // DELETE /api/users/5 — Delete user
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();  // 204 No Content
    }
}
```

### Testing with cURL or Postman

```bash
# Create a user (POST)
curl -X POST http://localhost:8080/api/users \
     -H "Content-Type: application/json" \
     -d '{"name":"Kunal","email":"kunal@email.com","age":25,"city":"Mumbai"}'

# Get all users (GET)
curl http://localhost:8080/api/users

# Get one user (GET)
curl http://localhost:8080/api/users/1

# Update a user (PUT)
curl -X PUT http://localhost:8080/api/users/1 \
     -H "Content-Type: application/json" \
     -d '{"name":"Kunal Updated","email":"kunal@email.com","age":26,"city":"Delhi"}'

# Delete a user (DELETE)
curl -X DELETE http://localhost:8080/api/users/1
```

---

## 8. Data Access with Spring Boot (Putting It All Together)

### application.properties (H2 Database for Learning)

```properties
# H2 Database Configuration
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# H2 Console (access at /h2-console)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

### application.properties (MySQL for Production)

```properties
# MySQL Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/myapp
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

### Full Project Structure

```
src/
├── main/
│   ├── java/com/example/demo/
│   │   ├── DemoApplication.java           ← Main class
│   │   ├── controller/
│   │   │   └── UserController.java        ← REST Controller
│   │   ├── service/
│   │   │   └── UserService.java           ← Business Logic
│   │   ├── repository/
│   │   │   └── UserRepository.java        ← Data Access
│   │   ├── model/
│   │   │   └── User.java                  ← Entity
│   │   └── exception/
│   │       ├── ResourceNotFoundException.java
│   │       ├── DuplicateResourceException.java
│   │       └── GlobalExceptionHandler.java
│   └── resources/
│       └── application.properties
└── test/
    └── java/com/example/demo/
        └── controller/
            └── UserControllerTest.java
```

---

## 9. Request and Response Customization

### Using DTOs (Data Transfer Objects)

```
WHY DTOs?
  Entity (User) has fields like: password, role, internalNotes
  You DON'T want to expose all fields to the client!
  DTO = Only the fields the client needs to see
```

```java
// Request DTO — What the client SENDS
public class UserCreateRequest {
    private String name;
    private String email;
    private int age;
    private String city;
    // Getters and setters
}

// Response DTO — What the client RECEIVES
public class UserResponse {
    private Long id;
    private String name;
    private String email;
    private String city;
    // No password! No internal notes!
    // Getters and setters
}

// Controller using DTOs
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @PostMapping
    public ResponseEntity<UserResponse> createUser(@RequestBody UserCreateRequest request) {
        // Convert request DTO to entity
        User user = new User();
        user.setName(request.getName());
        user.setEmail(request.getEmail());
        user.setAge(request.getAge());
        user.setCity(request.getCity());
        
        User savedUser = userService.createUser(user);
        
        // Convert entity to response DTO
        UserResponse response = new UserResponse();
        response.setId(savedUser.getId());
        response.setName(savedUser.getName());
        response.setEmail(savedUser.getEmail());
        response.setCity(savedUser.getCity());
        
        return ResponseEntity.created(URI.create("/api/users/" + response.getId()))
                             .body(response);
    }
}
```

### Standard API Response Wrapper

```java
// A standard wrapper for all API responses
public class ApiResponse<T> {
    private boolean success;
    private String message;
    private T data;
    private LocalDateTime timestamp;
    
    public ApiResponse(boolean success, String message, T data) {
        this.success = success;
        this.message = message;
        this.data = data;
        this.timestamp = LocalDateTime.now();
    }
    
    // Static factory methods
    public static <T> ApiResponse<T> success(T data) {
        return new ApiResponse<>(true, "Success", data);
    }
    
    public static <T> ApiResponse<T> success(String message, T data) {
        return new ApiResponse<>(true, message, data);
    }
    
    public static <T> ApiResponse<T> error(String message) {
        return new ApiResponse<>(false, message, null);
    }
    
    // Getters and setters...
}

// Usage in controller
@GetMapping("/{id}")
public ResponseEntity<ApiResponse<User>> getUser(@PathVariable Long id) {
    User user = userService.getUserById(id);
    return ResponseEntity.ok(ApiResponse.success(user));
}

// Response JSON:
// {
//     "success": true,
//     "message": "Success",
//     "data": {
//         "id": 1,
//         "name": "Kunal",
//         "email": "kunal@email.com"
//     },
//     "timestamp": "2026-01-15T10:30:00"
// }
```

---

## 10. API Best Practices

### URL Naming Conventions

```
✅ Use nouns (not verbs):      /api/users, /api/orders
❌ Avoid verbs:               /api/getUsers, /api/createOrder

✅ Use plural nouns:           /api/users, /api/products
❌ Avoid singular:             /api/user, /api/product

✅ Use kebab-case:             /api/order-items
❌ Avoid camelCase:            /api/orderItems

✅ Use hierarchical URLs:      /api/users/5/orders
❌ Avoid flat URLs:            /api/getUserOrders?userId=5

✅ Version your API:           /api/v1/users, /api/v2/users
```

### JSON Naming Conventions

```java
// Use camelCase for JSON fields
{
    "id": 1,
    "firstName": "Kunal",          // ✅ camelCase
    "email": "kunal@email.com",
    "createdAt": "2026-01-15"
}

// Jackson annotations for customization
public class User {
    @JsonProperty("full_name")       // Custom JSON field name
    private String name;
    
    @JsonIgnore                       // Don't include in JSON
    private String password;
    
    @JsonFormat(pattern = "dd-MM-yyyy")  // Date format
    private LocalDate birthDate;
}
```

### Pagination for Large Data

```java
@GetMapping
public ResponseEntity<Page<User>> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(defaultValue = "id") String sortBy
) {
    Pageable pageable = PageRequest.of(page, size, Sort.by(sortBy));
    Page<User> users = userRepository.findAll(pageable);
    return ResponseEntity.ok(users);
}

// GET /api/users?page=0&size=10&sortBy=name

// Response includes pagination metadata:
// {
//     "content": [...],           ← actual data
//     "totalElements": 100,       ← total records
//     "totalPages": 10,           ← total pages
//     "size": 10,                 ← page size
//     "number": 0,                ← current page (0-based)
//     "first": true,              ← is first page?
//     "last": false               ← is last page?
// }
```

---

## Quick Revision Cheat Sheet

```
REST = Representational State Transfer
  - Client-Server separation
  - Stateless (each request is independent)
  - Resource-based URLs (/api/users, /api/users/5)
  - Standard HTTP methods (GET, POST, PUT, DELETE, PATCH)

HTTP Methods:
  GET    → Read (safe, idempotent)
  POST   → Create (NOT idempotent)
  PUT    → Full update (idempotent)
  PATCH  → Partial update
  DELETE → Remove (idempotent)

Status Codes:
  200 OK          → Success
  201 Created     → Resource created (POST)
  204 No Content  → Deleted successfully
  400 Bad Request → Invalid input
  401 Unauthorized→ Not authenticated
  403 Forbidden   → Not authorized
  404 Not Found   → Resource missing
  500 Server Error→ Bug on server

ResponseEntity:
  ResponseEntity.ok(data)           → 200
  ResponseEntity.created(uri).body(data) → 201
  ResponseEntity.noContent().build() → 204
  ResponseEntity.notFound().build()  → 404
  ResponseEntity.badRequest().body(error) → 400

Architecture:
  Controller → Service → Repository → Database
  @RestController (JSON) vs @Controller (HTML)
  Use DTOs to control what data is exposed

Best Practices:
  - Use plural nouns in URLs: /api/users
  - Version your API: /api/v1/users
  - Use proper status codes
  - Use DTOs (don't expose entities directly)
  - Add pagination for list endpoints
  - Use a standard response wrapper (ApiResponse)
```

---

**Next: [10-Exception-Validation-Security.md](10-Exception-Validation-Security.md) — Exception Handling, Validation, and Security Basics**
