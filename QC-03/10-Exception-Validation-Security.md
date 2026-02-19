# Exception Handling, Validation & Security - Complete In-Depth Guide

## Table of Contents
- [1. Exception Handling in Spring Boot](#1-exception-handling-in-spring-boot)
- [2. @ExceptionHandler](#2-exceptionhandler)
- [3. @ControllerAdvice / @RestControllerAdvice](#3-controlleradvice--restcontrolleradvice)
- [4. Custom Exception Classes](#4-custom-exception-classes)
- [5. Complete Exception Handling Setup](#5-complete-exception-handling-setup)
- [6. Validation in Spring Boot](#6-validation-in-spring-boot)
- [7. Validation Annotations](#7-validation-annotations)
- [8. Custom Validators](#8-custom-validators)
- [9. Spring Security Basics](#9-spring-security-basics)
- [10. Authentication vs Authorization](#10-authentication-vs-authorization)
- [11. UserDetailsService](#11-userdetailsservice)
- [12. Basic Security Configuration](#12-basic-security-configuration)

---

## 1. Exception Handling in Spring Boot

### The Problem

Without proper exception handling, your API returns ugly errors:

```json
// Default Spring Boot error (not helpful for the client)
{
    "timestamp": "2026-01-15T10:30:00",
    "status": 500,
    "error": "Internal Server Error",
    "message": "",
    "path": "/api/users/999"
}
```

### What We Want

```json
// Clean, informative error response
{
    "status": 404,
    "error": "Not Found",
    "message": "User not found with id: 999",
    "timestamp": "2026-01-15T10:30:00",
    "path": "/api/users/999"
}
```

### How Exception Handling Works

```
1. Client sends request: GET /api/users/999
2. Controller calls: userService.findById(999)
3. Service throws: ResourceNotFoundException("User not found with id: 999")
4. Spring catches the exception
5. @ExceptionHandler or @ControllerAdvice handles it
6. Returns proper error response with correct status code
```

---

## 2. @ExceptionHandler

### What is @ExceptionHandler?

An annotation that handles exceptions thrown by controller methods. You define a method that runs when a specific exception occurs.

### In a Single Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
        // If user not found, service throws RuntimeException
    }
    
    // Handle exception ONLY for this controller
    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<String> handleRuntimeException(RuntimeException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                             .body(ex.getMessage());
    }
}
```

### Problem with @ExceptionHandler in Controller

```
If you have 10 controllers, you'd need to write the same
@ExceptionHandler in ALL 10 controllers!

❌ Code duplication
❌ Hard to maintain
❌ Inconsistent error responses

Solution → @ControllerAdvice (GLOBAL exception handler)
```

---

## 3. @ControllerAdvice / @RestControllerAdvice

### What is @ControllerAdvice?

A **global exception handler** that catches exceptions from ALL controllers in one place.

```
@ControllerAdvice = Handles exceptions for @Controller (returns views)
@RestControllerAdvice = Handles exceptions for @RestController (returns JSON)

@RestControllerAdvice = @ControllerAdvice + @ResponseBody
```

### How It Works Under the Hood

```
1. Exception is thrown in any controller
2. Spring looks for @ExceptionHandler in the same controller
3. If not found → Spring looks for @RestControllerAdvice classes
4. Finds matching @ExceptionHandler method
5. Executes it and returns the response

┌──────────────┐    Exception    ┌────────────────────┐
│  Controller  │ ──────────────→ │ @RestControllerAdvice│
│              │                 │ @ExceptionHandler    │
│ throws ex!   │                 │ catches it!          │
└──────────────┘                 └──────────┬───────────┘
                                            │
                                            ↓
                                    Returns error response
                                    to the client
```

---

## 4. Custom Exception Classes

### Why Custom Exceptions?

```
RuntimeException is too generic.
Custom exceptions tell you exactly WHAT went wrong.

ResourceNotFoundException  → Resource not found (404)
DuplicateResourceException → Duplicate data (409)
BadRequestException        → Invalid input (400)
```

### Creating Custom Exceptions

```java
// 404 — Resource Not Found
public class ResourceNotFoundException extends RuntimeException {
    
    public ResourceNotFoundException(String message) {
        super(message);
    }
    
    public ResourceNotFoundException(String resourceName, String fieldName, Object fieldValue) {
        super(String.format("%s not found with %s: '%s'", resourceName, fieldName, fieldValue));
        // Example: "User not found with id: '999'"
    }
}

// 409 — Duplicate Resource
public class DuplicateResourceException extends RuntimeException {
    
    public DuplicateResourceException(String message) {
        super(message);
    }
}

// 400 — Bad Request
public class BadRequestException extends RuntimeException {
    
    public BadRequestException(String message) {
        super(message);
    }
}
```

---

## 5. Complete Exception Handling Setup

### Step 1: Error Response DTO

```java
// Custom error response structure
public class ErrorResponse {
    
    private int status;
    private String error;
    private String message;
    private LocalDateTime timestamp;
    private String path;
    private Map<String, String> validationErrors;  // For validation errors
    
    public ErrorResponse(int status, String error, String message, String path) {
        this.status = status;
        this.error = error;
        this.message = message;
        this.path = path;
        this.timestamp = LocalDateTime.now();
    }
    
    // Getters and setters...
    public int getStatus() { return status; }
    public String getError() { return error; }
    public String getMessage() { return message; }
    public LocalDateTime getTimestamp() { return timestamp; }
    public String getPath() { return path; }
    public Map<String, String> getValidationErrors() { return validationErrors; }
    public void setValidationErrors(Map<String, String> validationErrors) { 
        this.validationErrors = validationErrors; 
    }
}
```

### Step 2: Global Exception Handler

```java
@RestControllerAdvice  // Handles exceptions from ALL controllers
public class GlobalExceptionHandler {
    
    // Handle 404 — Resource Not Found
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(
            ResourceNotFoundException ex, HttpServletRequest request) {
        
        ErrorResponse error = new ErrorResponse(
            404,
            "Not Found",
            ex.getMessage(),
            request.getRequestURI()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
    
    // Handle 409 — Duplicate Resource
    @ExceptionHandler(DuplicateResourceException.class)
    public ResponseEntity<ErrorResponse> handleDuplicateResource(
            DuplicateResourceException ex, HttpServletRequest request) {
        
        ErrorResponse error = new ErrorResponse(
            409,
            "Conflict",
            ex.getMessage(),
            request.getRequestURI()
        );
        return ResponseEntity.status(HttpStatus.CONFLICT).body(error);
    }
    
    // Handle 400 — Validation Errors
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(
            MethodArgumentNotValidException ex, HttpServletRequest request) {
        
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(fieldError -> {
            errors.put(fieldError.getField(), fieldError.getDefaultMessage());
        });
        
        ErrorResponse error = new ErrorResponse(
            400,
            "Validation Failed",
            "Input validation failed",
            request.getRequestURI()
        );
        error.setValidationErrors(errors);
        
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
    
    // Handle 400 — Bad Request
    @ExceptionHandler(BadRequestException.class)
    public ResponseEntity<ErrorResponse> handleBadRequest(
            BadRequestException ex, HttpServletRequest request) {
        
        ErrorResponse error = new ErrorResponse(
            400,
            "Bad Request",
            ex.getMessage(),
            request.getRequestURI()
        );
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }
    
    // Handle 500 — Generic fallback (catch-all)
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(
            Exception ex, HttpServletRequest request) {
        
        ErrorResponse error = new ErrorResponse(
            500,
            "Internal Server Error",
            "An unexpected error occurred",
            request.getRequestURI()
        );
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

### Step 3: Using Custom Exceptions in Service

```java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User", "id", id));
        // If user not found → throws ResourceNotFoundException
        // GlobalExceptionHandler catches it → returns 404 with message
    }
    
    public User createUser(User user) {
        if (userRepository.existsByEmail(user.getEmail())) {
            throw new DuplicateResourceException("Email already exists: " + user.getEmail());
            // GlobalExceptionHandler catches it → returns 409
        }
        return userRepository.save(user);
    }
}
```

### What the Client Sees

```json
// GET /api/users/999
{
    "status": 404,
    "error": "Not Found",
    "message": "User not found with id: '999'",
    "timestamp": "2026-01-15T10:30:00",
    "path": "/api/users/999",
    "validationErrors": null
}

// POST /api/users with invalid data
{
    "status": 400,
    "error": "Validation Failed",
    "message": "Input validation failed",
    "timestamp": "2026-01-15T10:30:00",
    "path": "/api/users",
    "validationErrors": {
        "name": "Name is required",
        "email": "Must be a valid email address",
        "age": "Age must be between 1 and 120"
    }
}
```

---

## 6. Validation in Spring Boot

### Why Validation?

```
Without validation, you might save this to the database:
{
    "name": "",
    "email": "not-an-email",
    "age": -5
}

That's BAD! We need to validate input BEFORE processing.
```

### Setup

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### How Validation Works

```
1. Add validation annotations to your DTO/Entity fields
2. Add @Valid to the controller parameter
3. Spring validates the input BEFORE calling the method
4. If validation fails → throws MethodArgumentNotValidException
5. Your @ExceptionHandler catches it and returns error details

@PostMapping
public User create(@Valid @RequestBody UserRequest request) {
                    ^^^^^^
                    This triggers validation!
}
```

---

## 7. Validation Annotations

### Common Annotations

```java
public class UserCreateRequest {
    
    @NotNull(message = "Name cannot be null")
    @NotBlank(message = "Name cannot be empty")    // Not null + not empty + not whitespace
    @Size(min = 2, max = 50, message = "Name must be 2-50 characters")
    private String name;
    
    @NotBlank(message = "Email is required")
    @Email(message = "Must be a valid email address")
    private String email;
    
    @NotNull(message = "Age is required")
    @Min(value = 1, message = "Age must be at least 1")
    @Max(value = 120, message = "Age must be at most 120")
    private Integer age;
    
    @Pattern(regexp = "^[0-9]{10}$", message = "Phone must be 10 digits")
    private String phone;
    
    @Positive(message = "Salary must be positive")
    private Double salary;
    
    @Past(message = "Birth date must be in the past")
    private LocalDate birthDate;
    
    @Future(message = "Expiry date must be in the future")
    private LocalDate expiryDate;
    
    // Getters and setters...
}
```

### Complete Annotations Reference

| Annotation | Use For | Example |
|-----------|---------|---------|
| `@NotNull` | Not null | `@NotNull String name` |
| `@NotEmpty` | Not null + not empty | `@NotEmpty List<String> items` |
| `@NotBlank` | Not null + not empty + not whitespace | `@NotBlank String name` |
| `@Size` | String/collection size | `@Size(min=2, max=50)` |
| `@Min` | Minimum number value | `@Min(1)` |
| `@Max` | Maximum number value | `@Max(120)` |
| `@Positive` | Must be > 0 | `@Positive Double price` |
| `@PositiveOrZero` | Must be >= 0 | `@PositiveOrZero Integer count` |
| `@Negative` | Must be < 0 | `@Negative Integer loss` |
| `@Email` | Valid email format | `@Email String email` |
| `@Pattern` | Regex pattern | `@Pattern(regexp="[A-Z]+")` |
| `@Past` | Date in the past | `@Past LocalDate birthDate` |
| `@Future` | Date in the future | `@Future LocalDate expiryDate` |
| `@PastOrPresent` | Date not in future | `@PastOrPresent LocalDate created` |

### Using @Valid in Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    // @Valid triggers validation before the method executes
    @PostMapping
    public ResponseEntity<User> createUser(@Valid @RequestBody UserCreateRequest request) {
        // This code ONLY runs if validation passes
        User user = userService.createUser(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(user);
    }
    
    // @Valid on path variable or request param
    @GetMapping("/{id}")
    public User getUser(@PathVariable @Min(1) Long id) {
        return userService.getUserById(id);
    }
}
```

### Nested Object Validation

```java
public class OrderCreateRequest {
    
    @NotBlank
    private String orderNumber;
    
    @Valid                           // Validate nested object too!
    @NotNull
    private AddressRequest address;
    
    @Valid
    @NotEmpty
    private List<OrderItemRequest> items;
}

public class AddressRequest {
    @NotBlank private String street;
    @NotBlank private String city;
    @Pattern(regexp = "^[0-9]{6}$") private String pincode;
}
```

---

## 8. Custom Validators

### Creating a Custom Annotation

```java
// Step 1: Create the annotation
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = ValidPhoneValidator.class)
public @interface ValidPhone {
    String message() default "Invalid phone number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

// Step 2: Create the validator
public class ValidPhoneValidator implements ConstraintValidator<ValidPhone, String> {
    
    @Override
    public void initialize(ValidPhone constraintAnnotation) {
        // Optional initialization
    }
    
    @Override
    public boolean isValid(String phone, ConstraintValidatorContext context) {
        if (phone == null) return true;  // Use @NotNull for null check
        // Indian phone: starts with 6-9, exactly 10 digits
        return phone.matches("^[6-9][0-9]{9}$");
    }
}

// Step 3: Use it
public class UserRequest {
    @ValidPhone
    private String phone;
}
```

---

## 9. Spring Security Basics

### What is Spring Security?

Spring Security is a framework that provides:
- **Authentication** — Who are you? (Login)
- **Authorization** — What can you do? (Permissions)
- Protection against common attacks (CSRF, XSS, etc.)

### Adding Spring Security

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### What Happens When You Add This Dependency?

```
Spring Boot auto-configures:
1. ALL endpoints are now protected (need login)
2. A default login page at /login
3. A default user: "user"
4. A random password printed in the console:
   "Using generated security password: a1b2c3d4-e5f6-..."
5. Basic HTTP authentication enabled
6. CSRF protection enabled
7. Session management
```

---

## 10. Authentication vs Authorization

### Authentication (AuthN) — "Who are you?"

```
Authentication = Proving your identity

Examples:
  - Username + Password
  - JWT Token
  - OAuth (Google, GitHub login)
  - Fingerprint, Face ID

Process:
  1. User sends credentials (username + password)
  2. Server validates credentials
  3. Server creates a session or token
  4. User sends token with every request
```

### Authorization (AuthZ) — "What can you do?"

```
Authorization = Checking permissions AFTER authentication

Examples:
  - ADMIN can delete users
  - USER can only view their own profile
  - MANAGER can approve requests

Roles and Authorities:
  ROLE_ADMIN → Full access
  ROLE_USER  → Limited access
  ROLE_MANAGER → Specific access
```

### How They Work Together

```
Step 1: Authentication
  User: "I am Kunal, password: 12345"
  Server: "Let me check... Yes, you are Kunal." ✅

Step 2: Authorization
  Kunal: "I want to delete user #10"
  Server: "Kunal has ROLE_USER. Only ADMIN can delete. DENIED!" ❌

  Admin: "I want to delete user #10"
  Server: "Admin has ROLE_ADMIN. Allowed!" ✅
```

---

## 11. UserDetailsService

### What is UserDetailsService?

An interface that Spring Security uses to load user information. You implement it to tell Spring HOW to find users (from database, LDAP, etc.).

```java
// Spring Security's interface
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

### Implementing UserDetailsService

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // Find user from database
        User user = userRepository.findByUsername(username)
            .orElseThrow(() -> new UsernameNotFoundException("User not found: " + username));
        
        // Convert your User entity to Spring Security's UserDetails
        return org.springframework.security.core.userdetails.User.builder()
            .username(user.getUsername())
            .password(user.getPassword())  // Must be encoded (BCrypt)
            .roles(user.getRole())         // e.g., "ADMIN", "USER"
            .build();
    }
}
```

### User Entity for Security

```java
@Entity
@Table(name = "app_users")
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(unique = true, nullable = false)
    private String username;
    
    @Column(nullable = false)
    private String password;  // Stored as BCrypt hash
    
    @Column(nullable = false)
    private String role;      // "ADMIN", "USER", "MANAGER"
    
    private boolean enabled = true;
    
    // Getters and setters...
}
```

### User Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}
```

---

## 12. Basic Security Configuration

### Security Configuration Class

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Autowired
    private CustomUserDetailsService userDetailsService;
    
    // Password encoder — ALWAYS encode passwords!
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
        // Converts "password123" → "$2a$10$N9qo8uLO..."
    }
    
    // Security filter chain — defines security rules
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // Disable CSRF for REST APIs (enable for web forms)
            .csrf(csrf -> csrf.disable())
            
            // Define access rules
            .authorizeHttpRequests(auth -> auth
                // Public endpoints (no login needed)
                .requestMatchers("/api/public/**").permitAll()
                .requestMatchers("/login", "/register").permitAll()
                
                // Admin-only endpoints
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                
                // User endpoints (any logged-in user)
                .requestMatchers("/api/users/**").hasAnyRole("USER", "ADMIN")
                
                // Everything else requires authentication
                .anyRequest().authenticated()
            )
            
            // Use HTTP Basic authentication (for REST APIs)
            .httpBasic(Customizer.withDefaults());
        
        return http.build();
    }
    
    // Authentication manager
    @Bean
    public AuthenticationManager authenticationManager(
            AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

### Understanding the Security Flow

```
Request comes in: GET /api/users/5

1. Security Filter Chain intercepts the request
2. Check: Does this URL match any permitAll() rule? NO
3. Check: Is the user authenticated? 
   → If NO → Return 401 Unauthorized
   → If YES → Continue
4. Check: Does the user have the required role?
   → /api/users/** requires ROLE_USER or ROLE_ADMIN
   → User has ROLE_USER → ALLOWED ✅
   → User has ROLE_GUEST → Return 403 Forbidden ❌
5. Request reaches the controller

┌──────────┐    ┌─────────────────┐    ┌──────────────┐    ┌────────────┐
│  Client  │───→│ Security Filter │───→│ Authorization│───→│ Controller │
│          │    │  (Auth check)   │    │  (Role check) │    │            │
└──────────┘    └─────────────────┘    └──────────────┘    └────────────┘
                    │                       │
                    ↓ 401                   ↓ 403
                "Not logged in!"      "Not allowed!"
```

### Method-Level Security

```java
@EnableMethodSecurity  // Add this to SecurityConfig or main class

@Service
public class UserService {
    
    @PreAuthorize("hasRole('ADMIN')")  // Only ADMIN can call this
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
    
    @PreAuthorize("hasAnyRole('USER', 'ADMIN')")
    public User getUser(Long id) {
        return userRepository.findById(id).orElseThrow();
    }
    
    @PreAuthorize("#username == authentication.name or hasRole('ADMIN')")
    public User getUserByUsername(String username) {
        // User can only access their own data, or admin can access anyone's
        return userRepository.findByUsername(username).orElseThrow();
    }
}
```

### Registering a New User (Password Encoding)

```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    
    @Autowired
    private UserRepository userRepository;
    
    @Autowired
    private PasswordEncoder passwordEncoder;
    
    @PostMapping("/register")
    public ResponseEntity<String> register(@RequestBody RegisterRequest request) {
        
        // Check if username exists
        if (userRepository.findByUsername(request.getUsername()).isPresent()) {
            return ResponseEntity.badRequest().body("Username already exists");
        }
        
        // Create user with ENCODED password
        User user = new User();
        user.setUsername(request.getUsername());
        user.setPassword(passwordEncoder.encode(request.getPassword()));  // ENCODE!
        user.setRole("USER");  // Default role
        
        userRepository.save(user);
        
        return ResponseEntity.status(HttpStatus.CREATED).body("User registered successfully");
    }
}
```

---

## Quick Revision Cheat Sheet

```
EXCEPTION HANDLING:
  @ExceptionHandler        → Handle exception in ONE controller
  @RestControllerAdvice    → Handle exceptions GLOBALLY (all controllers)
  Custom exceptions        → ResourceNotFoundException, DuplicateResourceException
  ErrorResponse DTO        → Standard error format for all APIs

VALIDATION:
  @Valid                   → Trigger validation on @RequestBody
  @NotNull                 → Cannot be null
  @NotBlank                → Cannot be null/empty/whitespace (for Strings)
  @NotEmpty                → Cannot be null/empty (for collections)
  @Size(min, max)          → Length constraint
  @Min / @Max              → Number range
  @Email                   → Valid email format
  @Pattern(regexp)         → Regex validation
  @Past / @Future          → Date constraints
  @Positive                → Must be > 0

  Nested validation: @Valid on nested object field

SPRING SECURITY:
  Authentication (AuthN)   → WHO are you? (Login)
  Authorization (AuthZ)    → WHAT can you do? (Permissions)
  
  UserDetailsService       → Load user from database
  PasswordEncoder          → BCrypt password hashing
  SecurityFilterChain      → Define security rules
  
  .permitAll()             → No login needed
  .authenticated()         → Login required
  .hasRole("ADMIN")        → Specific role needed
  .hasAnyRole("A", "B")   → Any of these roles
  
  @PreAuthorize            → Method-level security
  @EnableMethodSecurity    → Enable method security
  
  NEVER store plain-text passwords! Always use BCrypt!
```

---

**Next: [11-Testing-Spring-Boot.md](11-Testing-Spring-Boot.md) — Testing Spring Boot Applications**
