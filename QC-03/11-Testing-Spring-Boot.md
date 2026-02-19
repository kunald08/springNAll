# Testing Spring Boot Applications - Complete In-Depth Guide

## Table of Contents
- [1. Why Testing?](#1-why-testing)
- [2. Types of Tests](#2-types-of-tests)
- [3. Testing Dependencies](#3-testing-dependencies)
- [4. @SpringBootTest](#4-springboottest)
- [5. Unit Testing Service Layer](#5-unit-testing-service-layer)
- [6. Unit Testing with Mockito](#6-unit-testing-with-mockito)
- [7. Testing Repository Layer](#7-testing-repository-layer)
- [8. Testing Controller Layer (MockMvc)](#8-testing-controller-layer-mockmvc)
- [9. Testing REST APIs (@WebMvcTest)](#9-testing-rest-apis-webmvctest)
- [10. Integration Testing](#10-integration-testing)
- [11. Test Annotations Reference](#11-test-annotations-reference)

---

## 1. Why Testing?

### The Problem Without Tests

```
Without testing:
  - You change one thing → something else breaks
  - You don't know if your code works until you manually test
  - Bugs reach production
  - Fear of making changes
  - "It works on my machine" situations

With testing:
  - You change something → tests tell you immediately if it broke
  - Automated verification
  - Confidence to refactor
  - Documentation of expected behavior
  - Catch bugs early (cheaper to fix)
```

---

## 2. Types of Tests

```
┌─────────────────────────────────────┐
│          END-TO-END TESTS           │  ← Slowest, most realistic
│   (Full application, database, UI)   │
├─────────────────────────────────────┤
│       INTEGRATION TESTS              │  ← Medium speed
│  (Multiple layers working together)  │
├─────────────────────────────────────┤
│          UNIT TESTS                  │  ← Fastest, most isolated
│     (Single class/method)            │
└─────────────────────────────────────┘
```

| Type | What It Tests | Speed | Dependencies |
|------|--------------|-------|--------------|
| **Unit Test** | Single method/class in isolation | Very fast | Mocked |
| **Integration Test** | Multiple components together | Medium | Real (or embedded) |
| **End-to-End Test** | Full application flow | Slow | Everything real |

---

## 3. Testing Dependencies

Spring Boot Starter Test includes everything:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

```
This includes:
  ✅ JUnit 5 (Jupiter) — Test framework
  ✅ Mockito — Mocking framework
  ✅ AssertJ — Fluent assertions
  ✅ Spring Test — Spring testing support
  ✅ MockMvc — Test controllers without HTTP
  ✅ Hamcrest — Matcher library
  ✅ JSONassert — JSON testing
```

### Test File Location

```
src/
├── main/java/com/example/demo/
│   ├── controller/UserController.java
│   ├── service/UserService.java
│   └── repository/UserRepository.java
└── test/java/com/example/demo/           ← Tests go here (mirror structure)
    ├── controller/UserControllerTest.java
    ├── service/UserServiceTest.java
    └── repository/UserRepositoryTest.java
```

---

## 4. @SpringBootTest

### What is @SpringBootTest?

It loads the **FULL Spring application context** for testing. It starts up the Spring container just like your real application.

```java
@SpringBootTest  // Loads full application context
class DemoApplicationTests {
    
    @Test
    void contextLoads() {
        // Just checking if the application starts without errors
    }
}
```

### When to Use @SpringBootTest

```
Use @SpringBootTest when:
  ✅ Testing the full application
  ✅ Integration tests (multiple layers)
  ✅ Testing with real database (H2 in-memory)
  ✅ Testing configuration

Don't use for:
  ❌ Unit tests (too heavy, starts entire context)
  ❌ Testing a single class (use Mockito instead)
```

### @SpringBootTest Options

```java
// Default — loads full context, no web server
@SpringBootTest
class MyTest { }

// With embedded web server on random port
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class MyTest {
    @LocalServerPort
    private int port;  // Gets the random port number
}

// With specific properties
@SpringBootTest(properties = {
    "spring.datasource.url=jdbc:h2:mem:testdb",
    "spring.jpa.hibernate.ddl-auto=create-drop"
})
class MyTest { }

// With specific profile
@ActiveProfiles("test")
@SpringBootTest
class MyTest { }
```

---

## 5. Unit Testing Service Layer

### Basic Unit Test (No Spring, No Mocking)

```java
// A simple class to test
public class Calculator {
    public int add(int a, int b) { return a + b; }
    public int divide(int a, int b) {
        if (b == 0) throw new ArithmeticException("Cannot divide by zero");
        return a / b;
    }
}

// Test class
class CalculatorTest {
    
    private Calculator calculator;
    
    @BeforeEach  // Runs BEFORE each test method
    void setUp() {
        calculator = new Calculator();
    }
    
    @Test
    void testAdd() {
        int result = calculator.add(2, 3);
        assertEquals(5, result);
    }
    
    @Test
    void testDivide() {
        assertEquals(5, calculator.divide(10, 2));
    }
    
    @Test
    void testDivideByZero() {
        assertThrows(ArithmeticException.class, () -> {
            calculator.divide(10, 0);
        });
    }
}
```

### JUnit 5 Assertions

```java
// Common assertions
assertEquals(expected, actual);           // Check equality
assertNotEquals(unexpected, actual);      // Check inequality
assertTrue(condition);                     // Check true
assertFalse(condition);                    // Check false
assertNull(object);                        // Check null
assertNotNull(object);                     // Check not null
assertThrows(Exception.class, () -> {});  // Check exception thrown
assertDoesNotThrow(() -> {});              // Check no exception

// With messages
assertEquals(5, result, "Addition should return 5");

// AssertJ (more readable — recommended)
import static org.assertj.core.api.Assertions.*;

assertThat(result).isEqualTo(5);
assertThat(name).isNotBlank();
assertThat(list).hasSize(3);
assertThat(list).contains("Kunal");
assertThat(list).isEmpty();
assertThat(user.getAge()).isGreaterThan(18);
assertThat(exception).isInstanceOf(RuntimeException.class);
```

---

## 6. Unit Testing with Mockito

### Why Mockito?

```
Problem: UserService depends on UserRepository.
In a UNIT test, you want to test UserService ALONE.
You don't want to connect to a real database!

Solution: MOCK the repository (create a fake version)

Real App:    UserService → UserRepository → Database
Unit Test:   UserService → MockRepository (fake, no database)
```

### Basic Mockito Example

```java
@ExtendWith(MockitoExtension.class)  // Enable Mockito
class UserServiceTest {
    
    @Mock  // Create a fake/mock UserRepository
    private UserRepository userRepository;
    
    @InjectMocks  // Create real UserService, inject mock repository into it
    private UserService userService;
    
    @Test
    void testGetUserById_Success() {
        // ARRANGE — Set up test data and mock behavior
        User mockUser = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        mockUser.setId(1L);
        
        // Tell the mock: "When findById(1) is called, return this user"
        when(userRepository.findById(1L)).thenReturn(Optional.of(mockUser));
        
        // ACT — Call the method being tested
        User result = userService.getUserById(1L);
        
        // ASSERT — Verify the result
        assertThat(result).isNotNull();
        assertThat(result.getName()).isEqualTo("Kunal");
        assertThat(result.getEmail()).isEqualTo("kunal@email.com");
        
        // VERIFY — Ensure the repository was actually called
        verify(userRepository, times(1)).findById(1L);
    }
    
    @Test
    void testGetUserById_NotFound() {
        // Mock returns empty Optional
        when(userRepository.findById(999L)).thenReturn(Optional.empty());
        
        // Assert that exception is thrown
        assertThrows(ResourceNotFoundException.class, () -> {
            userService.getUserById(999L);
        });
        
        verify(userRepository).findById(999L);
    }
    
    @Test
    void testCreateUser_Success() {
        User newUser = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        User savedUser = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        savedUser.setId(1L);
        
        when(userRepository.existsByEmail("kunal@email.com")).thenReturn(false);
        when(userRepository.save(newUser)).thenReturn(savedUser);
        
        User result = userService.createUser(newUser);
        
        assertThat(result.getId()).isEqualTo(1L);
        verify(userRepository).existsByEmail("kunal@email.com");
        verify(userRepository).save(newUser);
    }
    
    @Test
    void testCreateUser_DuplicateEmail() {
        User newUser = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        
        when(userRepository.existsByEmail("kunal@email.com")).thenReturn(true);
        
        assertThrows(DuplicateResourceException.class, () -> {
            userService.createUser(newUser);
        });
        
        // save() should NEVER be called if email is duplicate
        verify(userRepository, never()).save(any());
    }
}
```

### Mockito Key Methods

```java
// STUBBING (defining mock behavior)
when(mock.method()).thenReturn(value);      // Return a value
when(mock.method()).thenThrow(exception);   // Throw exception
when(mock.method()).thenAnswer(invocation -> ...); // Custom logic

// VERIFICATION (checking if methods were called)
verify(mock).method();                     // Called exactly once
verify(mock, times(2)).method();           // Called exactly 2 times
verify(mock, never()).method();            // Never called
verify(mock, atLeast(1)).method();         // Called at least once
verify(mock, atMost(3)).method();          // Called at most 3 times

// ARGUMENT MATCHERS
when(repo.findById(any())).thenReturn(...);         // Any argument
when(repo.findByName(anyString())).thenReturn(...);  // Any string
when(repo.findByAge(anyInt())).thenReturn(...);      // Any int
when(repo.findById(eq(1L))).thenReturn(...);         // Specific value
```

---

## 7. Testing Repository Layer

### @DataJpaTest

Tests ONLY the JPA/Repository layer. It configures an in-memory H2 database automatically.

```java
@DataJpaTest  // Only loads JPA components (faster than @SpringBootTest)
class UserRepositoryTest {
    
    @Autowired
    private UserRepository userRepository;
    
    @Autowired
    private TestEntityManager entityManager;  // For setting up test data
    
    @BeforeEach
    void setUp() {
        // Insert test data
        User user1 = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        User user2 = new User("Priya", "priya@email.com", 23, "Delhi");
        entityManager.persist(user1);
        entityManager.persist(user2);
        entityManager.flush();
    }
    
    @Test
    void testFindByEmail() {
        Optional<User> found = userRepository.findByEmail("kunal@email.com");
        
        assertThat(found).isPresent();
        assertThat(found.get().getName()).isEqualTo("Kunal");
    }
    
    @Test
    void testFindByEmail_NotFound() {
        Optional<User> found = userRepository.findByEmail("unknown@email.com");
        
        assertThat(found).isEmpty();
    }
    
    @Test
    void testFindByCity() {
        List<User> mumbaiUsers = userRepository.findByCity("Mumbai");
        
        assertThat(mumbaiUsers).hasSize(1);
        assertThat(mumbaiUsers.get(0).getName()).isEqualTo("Kunal");
    }
    
    @Test
    void testExistsByEmail() {
        assertThat(userRepository.existsByEmail("kunal@email.com")).isTrue();
        assertThat(userRepository.existsByEmail("unknown@email.com")).isFalse();
    }
    
    @Test
    void testSaveUser() {
        User newUser = new User("Ravi", "ravi@email.com", 28, "Chennai");
        User savedUser = userRepository.save(newUser);
        
        assertThat(savedUser.getId()).isNotNull();
        assertThat(savedUser.getName()).isEqualTo("Ravi");
    }
}
```

---

## 8. Testing Controller Layer (MockMvc)

### What is MockMvc?

MockMvc lets you test controllers **without starting a real HTTP server**. It simulates HTTP requests.

```
Real request:   Browser → Tomcat → DispatcherServlet → Controller
MockMvc:        Test → MockMvc → DispatcherServlet → Controller

Same processing, no real server needed! Much faster.
```

### @WebMvcTest

Loads ONLY the web/controller layer (not service, not repository).

```java
@WebMvcTest(UserController.class)  // Only load UserController
class UserControllerTest {
    
    @Autowired
    private MockMvc mockMvc;  // Simulates HTTP calls
    
    @MockBean  // Create a mock and put it in the Spring context
    private UserService userService;
    
    @Autowired
    private ObjectMapper objectMapper;  // JSON conversion
    
    @Test
    void testGetAllUsers() throws Exception {
        // Arrange
        List<User> users = List.of(
            new User("Kunal", "kunal@email.com", 25, "Mumbai"),
            new User("Priya", "priya@email.com", 23, "Delhi")
        );
        when(userService.getAllUsers()).thenReturn(users);
        
        // Act & Assert
        mockMvc.perform(get("/api/users"))                    // Send GET request
            .andExpect(status().isOk())                       // Expect 200
            .andExpect(jsonPath("$.size()").value(2))          // Expect 2 users
            .andExpect(jsonPath("$[0].name").value("Kunal"))   // First user name
            .andExpect(jsonPath("$[1].name").value("Priya"));  // Second user name
    }
    
    @Test
    void testGetUserById_Success() throws Exception {
        User user = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        user.setId(1L);
        when(userService.getUserById(1L)).thenReturn(user);
        
        mockMvc.perform(get("/api/users/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Kunal"))
            .andExpect(jsonPath("$.email").value("kunal@email.com"));
    }
    
    @Test
    void testGetUserById_NotFound() throws Exception {
        when(userService.getUserById(999L))
            .thenThrow(new ResourceNotFoundException("User", "id", 999));
        
        mockMvc.perform(get("/api/users/999"))
            .andExpect(status().isNotFound());
    }
    
    @Test
    void testCreateUser() throws Exception {
        User newUser = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        User savedUser = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        savedUser.setId(1L);
        
        when(userService.createUser(any(User.class))).thenReturn(savedUser);
        
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)       // Set content type
                .content(objectMapper.writeValueAsString(newUser)))  // Send JSON body
            .andExpect(status().isCreated())                    // Expect 201
            .andExpect(jsonPath("$.id").value(1))
            .andExpect(jsonPath("$.name").value("Kunal"));
    }
    
    @Test
    void testDeleteUser() throws Exception {
        doNothing().when(userService).deleteUser(1L);
        
        mockMvc.perform(delete("/api/users/1"))
            .andExpect(status().isNoContent());  // Expect 204
        
        verify(userService).deleteUser(1L);
    }
}
```

---

## 9. Testing REST APIs (@WebMvcTest)

### Testing Validation

```java
@Test
void testCreateUser_ValidationFails() throws Exception {
    // Send invalid data (empty name, invalid email)
    String invalidJson = """
        {
            "name": "",
            "email": "not-an-email",
            "age": -5
        }
        """;
    
    mockMvc.perform(post("/api/users")
            .contentType(MediaType.APPLICATION_JSON)
            .content(invalidJson))
        .andExpect(status().isBadRequest())              // Expect 400
        .andExpect(jsonPath("$.validationErrors.name").exists())   // Name error
        .andExpect(jsonPath("$.validationErrors.email").exists()); // Email error
}
```

### Testing with JsonPath

```java
// JsonPath expressions:
// $           → root
// $.name      → root.name field
// $[0]        → first array element
// $[0].name   → name of first element
// $.size()    → array size
// $..name     → all "name" fields (recursive)
// $[?(@.age > 20)]  → filter elements where age > 20

mockMvc.perform(get("/api/users"))
    .andExpect(jsonPath("$").isArray())
    .andExpect(jsonPath("$.length()").value(2))
    .andExpect(jsonPath("$[0].name").value("Kunal"))
    .andExpect(jsonPath("$[*].email").isNotEmpty());
```

---

## 10. Integration Testing

### Full Integration Test

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
class UserIntegrationTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Autowired
    private UserRepository userRepository;
    
    @Autowired
    private ObjectMapper objectMapper;
    
    @BeforeEach
    void setUp() {
        userRepository.deleteAll();  // Clean database before each test
    }
    
    @Test
    void testFullCrudFlow() throws Exception {
        // 1. CREATE — POST /api/users
        String userJson = """
            {
                "name": "Kunal",
                "email": "kunal@email.com",
                "age": 25,
                "city": "Mumbai"
            }
            """;
        
        MvcResult createResult = mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(userJson))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").isNotEmpty())
            .andExpect(jsonPath("$.name").value("Kunal"))
            .andReturn();
        
        // Extract the created user's ID
        String response = createResult.getResponse().getContentAsString();
        Long userId = objectMapper.readTree(response).get("id").asLong();
        
        // 2. READ — GET /api/users/{id}
        mockMvc.perform(get("/api/users/" + userId))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Kunal"));
        
        // 3. UPDATE — PUT /api/users/{id}
        String updatedJson = """
            {
                "name": "Kunal Updated",
                "email": "kunal@email.com",
                "age": 26,
                "city": "Delhi"
            }
            """;
        
        mockMvc.perform(put("/api/users/" + userId)
                .contentType(MediaType.APPLICATION_JSON)
                .content(updatedJson))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Kunal Updated"))
            .andExpect(jsonPath("$.city").value("Delhi"));
        
        // 4. DELETE — DELETE /api/users/{id}
        mockMvc.perform(delete("/api/users/" + userId))
            .andExpect(status().isNoContent());
        
        // 5. VERIFY — GET should return 404
        mockMvc.perform(get("/api/users/" + userId))
            .andExpect(status().isNotFound());
    }
}
```

### Using TestRestTemplate (Real HTTP Calls)

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserApiIntegrationTest {
    
    @Autowired
    private TestRestTemplate restTemplate;
    
    @LocalServerPort
    private int port;
    
    @Test
    void testCreateAndGetUser() {
        // Create user
        User newUser = new User("Kunal", "kunal@email.com", 25, "Mumbai");
        ResponseEntity<User> createResponse = restTemplate
            .postForEntity("/api/users", newUser, User.class);
        
        assertThat(createResponse.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        assertThat(createResponse.getBody().getId()).isNotNull();
        
        Long userId = createResponse.getBody().getId();
        
        // Get user
        ResponseEntity<User> getResponse = restTemplate
            .getForEntity("/api/users/" + userId, User.class);
        
        assertThat(getResponse.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(getResponse.getBody().getName()).isEqualTo("Kunal");
    }
}
```

---

## 11. Test Annotations Reference

| Annotation | Purpose |
|-----------|---------|
| `@Test` | Marks a test method |
| `@BeforeEach` | Runs before EACH test method |
| `@AfterEach` | Runs after EACH test method |
| `@BeforeAll` | Runs once before ALL tests (static) |
| `@AfterAll` | Runs once after ALL tests (static) |
| `@DisplayName` | Custom test name |
| `@Disabled` | Skip this test |
| `@SpringBootTest` | Full application context |
| `@WebMvcTest` | Only web/controller layer |
| `@DataJpaTest` | Only JPA/repository layer |
| `@MockBean` | Mock bean in Spring context |
| `@Mock` | Mockito mock (no Spring) |
| `@InjectMocks` | Inject mocks into class |
| `@ActiveProfiles` | Use specific profile |
| `@Sql` | Run SQL before test |

---

## Quick Revision Cheat Sheet

```
TEST TYPES:
  Unit Test        → Test single class (use @Mock)
  Integration Test → Test multiple layers (use @SpringBootTest)
  Controller Test  → Test HTTP layer (use @WebMvcTest + MockMvc)
  Repository Test  → Test data layer (use @DataJpaTest)

MOCKITO:
  @Mock             → Create fake object
  @InjectMocks      → Inject mocks into real object
  @MockBean         → Create mock in Spring context
  when(...).thenReturn(...)  → Define mock behavior
  verify(mock).method()      → Assert method was called

MOCKMVC:
  mockMvc.perform(get("/url"))      → Send GET request
  .andExpect(status().isOk())       → Check status code
  .andExpect(jsonPath("$.name").value("X")) → Check JSON

ASSERTIONS:
  assertEquals(expected, actual)    → JUnit
  assertThat(actual).isEqualTo(expected) → AssertJ (recommended)

TEST STRUCTURE (AAA Pattern):
  ARRANGE → Set up test data and mocks
  ACT     → Call the method being tested
  ASSERT  → Verify the result
```

---

**Next: [12-Microservices-Basics.md](12-Microservices-Basics.md) — Microservices Architecture Fundamentals**
