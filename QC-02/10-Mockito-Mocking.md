# Mockito — Mocking Framework for Unit Tests

## Table of Contents
1. [What Is Mocking?](#1-what-is-mocking)
2. [Mock vs Stub vs Spy](#2-mock-vs-stub-vs-spy)
3. [Setup & Getting Started](#3-setup--getting-started)
4. [Creating Mocks](#4-creating-mocks)
5. [Stubbing — when().thenReturn()](#5-stubbing)
6. [Verification — verify()](#6-verification)
7. [Argument Matchers](#7-argument-matchers)
8. [Spies — @Spy](#8-spies)
9. [Argument Captors](#9-argument-captors)
10. [Advanced Features](#10-advanced-features)

---

## 1. What Is Mocking?

When unit testing, you want to test **one class in isolation**. But most classes depend on other classes. **Mocking** creates fake versions of those dependencies.

```
Real scenario (integration test):
┌───────────────┐       ┌─────────────────┐       ┌───────────┐
│ UserService   │─────▶│ UserRepository  │─────▶│ Database  │
└───────────────┘       └─────────────────┘       └───────────┘
  (class under test)    (real dependency)       (real DB)

Problem: Slow, needs DB setup, network issues, etc.

Mocked scenario (unit test):
┌───────────────┐        ┌───────────────────┐
│ UserService   │─────▶ │ Mock Repository   │  ← Fake! Returns preset data
└───────────────┘        └───────────────────┘
  (class under test)    (controlled fake)

Benefits:
- FAST (no real DB)
- ISOLATED (test only UserService logic)
- PREDICTABLE (you control what the mock returns)
- NO SETUP (no database, no network)
```

---

## 2. Mock vs Stub vs Spy

```
MOCK:
- Completely fake object
- ALL methods return default values (null, 0, false, empty)
- You define behavior for methods you care about
- Can verify if methods were called

STUB:
- A mock with pre-defined return values
- Mockito combines mock + stub with when().thenReturn()
- "When someone calls this method, return this value"

SPY:
- Wraps a REAL object
- All methods call the REAL implementation by default
- You can override specific methods
- Useful when you want mostly real behavior with a few overrides

Comparison:
┌────────────┬───────────────────┬────────────────────┬─────────────────────┐
│            │     Mock          │      Stub          │       Spy           │
├────────────┼───────────────────┼────────────────────┼─────────────────────┤
│ Base       │ Fake object       │ Fake with returns  │ Real object wrapper │
│ Default    │ null/0/false      │ Preset values      │ Real implementation │
│ Behavior   │ Nothing           │ Defined returns    │ Real + overrides    │
│ Verify?    │ Yes               │ Yes                │ Yes                 │
└────────────┴───────────────────┴────────────────────┴─────────────────────┘
```

---

## 3. Setup & Getting Started

### Maven Dependency

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.11.0</version>
    <scope>test</scope>
</dependency>

<!-- For @ExtendWith(MockitoExtension.class) with JUnit 5 -->
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.11.0</version>
    <scope>test</scope>
</dependency>
```

### Example: Code to Test

```java
// Repository (dependency)
public interface UserRepository {
    User findById(int id);
    List<User> findAll();
    void save(User user);
    void delete(int id);
}

// Service (class under test)
public class UserService {
    private final UserRepository repository;
    
    public UserService(UserRepository repository) {
        this.repository = repository;  // Dependency injection
    }
    
    public User getUser(int id) {
        User user = repository.findById(id);
        if (user == null) {
            throw new UserNotFoundException("User not found: " + id);
        }
        return user;
    }
    
    public List<User> getAllUsers() {
        return repository.findAll();
    }
    
    public void createUser(User user) {
        if (user.getName() == null || user.getName().isBlank()) {
            throw new IllegalArgumentException("Name is required");
        }
        repository.save(user);
    }
}
```

---

## 4. Creating Mocks

### Method 1: Mockito.mock() (Manual)

```java
import org.mockito.Mockito;
import static org.mockito.Mockito.*;

class UserServiceTest {
    
    @Test
    void testGetUser() {
        // Create mock manually
        UserRepository mockRepo = mock(UserRepository.class);
        
        // Inject mock into the class under test
        UserService service = new UserService(mockRepo);
        
        // ...
    }
}
```

### Method 2: @Mock + @ExtendWith (Recommended)

```java
import org.mockito.Mock;
import org.mockito.InjectMocks;
import org.mockito.junit.jupiter.MockitoExtension;
import org.junit.jupiter.api.extension.ExtendWith;

@ExtendWith(MockitoExtension.class)   // Initialize mocks automatically
class UserServiceTest {
    
    @Mock                              // Creates a mock UserRepository
    UserRepository repository;
    
    @InjectMocks                       // Creates UserService and injects the mock
    UserService service;
    
    @Test
    void testGetUser() {
        // repository is already a mock, service already has it injected!
    }
}
```

---

## 5. Stubbing

**Stubbing** = telling the mock what to return when a method is called.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    
    @Mock UserRepository repository;
    @InjectMocks UserService service;
    
    // when().thenReturn() — basic stubbing
    @Test
    void testGetUser() {
        User alice = new User(1, "Alice", "alice@example.com");
        
        // STUB: When findById(1) is called, return alice
        when(repository.findById(1)).thenReturn(alice);
        
        // ACT
        User result = service.getUser(1);
        
        // ASSERT
        assertEquals("Alice", result.getName());
        assertEquals("alice@example.com", result.getEmail());
    }
    
    // thenReturn with different values for consecutive calls
    @Test
    void testConsecutiveCalls() {
        when(repository.findById(1))
            .thenReturn(new User(1, "Alice", "a@x.com"))     // First call
            .thenReturn(new User(1, "Updated Alice", "a@x.com"))  // Second call
            .thenReturn(null);                                 // Third call onwards
        
        assertEquals("Alice", service.getUser(1).getName());
        assertEquals("Updated Alice", service.getUser(1).getName());
        assertThrows(UserNotFoundException.class, () -> service.getUser(1));
    }
    
    // thenThrow — simulate exceptions
    @Test
    void testDatabaseError() {
        when(repository.findById(anyInt()))
            .thenThrow(new RuntimeException("DB connection lost"));
        
        assertThrows(RuntimeException.class, () -> service.getUser(1));
    }
    
    // thenAnswer — dynamic responses based on input
    @Test
    void testDynamicResponse() {
        when(repository.findById(anyInt())).thenAnswer(invocation -> {
            int id = invocation.getArgument(0);  // Get the argument passed
            return new User(id, "User " + id, "user" + id + "@x.com");
        });
        
        assertEquals("User 42", service.getUser(42).getName());
        assertEquals("User 7", service.getUser(7).getName());
    }
    
    // Stubbing void methods (can't use when() with void methods)
    @Test
    void testDeleteThrowsException() {
        doThrow(new RuntimeException("Cannot delete"))
            .when(repository).delete(1);
        
        assertThrows(RuntimeException.class, () -> repository.delete(1));
    }
    
    // doNothing — for void methods (default behavior, but explicit)
    @Test
    void testSave() {
        doNothing().when(repository).save(any(User.class));
        
        service.createUser(new User(1, "Alice", "a@x.com"));
        // No exception → save was called successfully
    }
}
```

---

## 6. Verification

**Verification** = checking that certain methods were called (or not called).

```java
@Test
void testCreateUser() {
    User alice = new User(1, "Alice", "a@x.com");
    
    service.createUser(alice);
    
    // Verify save() was called EXACTLY ONCE with this user
    verify(repository).save(alice);
    
    // Verify save() was called exactly once (explicit)
    verify(repository, times(1)).save(alice);
    
    // Verify save() was called at least once
    verify(repository, atLeastOnce()).save(alice);
    
    // Verify save() was called at least N times
    verify(repository, atLeast(1)).save(alice);
    
    // Verify save() was called at most N times
    verify(repository, atMost(3)).save(alice);
    
    // Verify save() was NEVER called
    verify(repository, never()).delete(anyInt());
    
    // Verify NO OTHER methods were called on the mock
    verifyNoMoreInteractions(repository);
}

@Test
void testValidationPreventseSave() {
    User invalidUser = new User(1, "", "");
    
    assertThrows(IllegalArgumentException.class, () -> {
        service.createUser(invalidUser);
    });
    
    // Verify save() was NEVER called (validation stopped it)
    verify(repository, never()).save(any());
}

// Verify order of method calls
@Test
void testCallOrder() {
    InOrder inOrder = inOrder(repository);
    
    service.getUser(1);
    service.getAllUsers();
    
    inOrder.verify(repository).findById(1);     // Called first
    inOrder.verify(repository).findAll();         // Called second
}
```

---

## 7. Argument Matchers

Use when you don't care about the **exact value** passed to a method.

```java
import static org.mockito.ArgumentMatchers.*;

// any() — match any value
when(repository.findById(anyInt())).thenReturn(alice);

// Type-specific matchers
anyInt()        // any int
anyLong()       // any long
anyString()     // any String
anyBoolean()    // any boolean
anyList()       // any List
anyMap()        // any Map
any()           // any object (including null)
any(User.class) // any User object

// Specific value matchers
eq(42)          // exactly 42
eq("hello")     // exactly "hello"

// String matchers
startsWith("A")
endsWith("z")
contains("hello")
matches("\\d+")   // regex

// Null matchers
isNull()
isNotNull()

// Custom matcher with argThat
when(repository.findById(argThat(id -> id > 0 && id < 100)))
    .thenReturn(alice);

// ⚠️ IMPORTANT RULE: If you use a matcher for one argument,
// you MUST use matchers for ALL arguments!

// ❌ WRONG:
when(service.process("literal", anyInt())).thenReturn(result);

// ✅ CORRECT:
when(service.process(eq("literal"), anyInt())).thenReturn(result);
```

---

## 8. Spies

A **spy** wraps a real object — real methods are called unless you override them.

```java
@ExtendWith(MockitoExtension.class)
class SpyTest {
    
    @Spy
    List<String> spyList = new ArrayList<>();  // Real ArrayList!
    
    @Test
    void testSpy() {
        // Real methods work
        spyList.add("one");
        spyList.add("two");
        assertEquals(2, spyList.size());     // Real size()
        assertEquals("one", spyList.get(0)); // Real get()
        
        // Override specific behavior
        doReturn(100).when(spyList).size();   // Override size() only
        assertEquals(100, spyList.size());    // Stubbed!
        assertEquals("one", spyList.get(0)); // Still real!
    }
    
    // ⚠️ With spies, use doReturn().when() instead of when().thenReturn()
    // Because when().thenReturn() actually CALLS the real method first!
    
    @Spy
    UserRepository realRepo = new InMemoryUserRepository();
    
    @Test
    void testPartialMock() {
        // Real findById works
        realRepo.save(new User(1, "Alice", "a@x.com"));
        assertNotNull(realRepo.findById(1));  // Real behavior
        
        // Override delete to do nothing
        doNothing().when(realRepo).delete(anyInt());
        realRepo.delete(1);  // Does nothing (overridden)
    }
}
```

### When to Use Spy vs Mock

```
Use MOCK when:
- The dependency is an interface (no real implementation)
- You want to control ALL behavior
- Testing the class under test, not the dependency

Use SPY when:
- You need mostly real behavior
- You want to override just 1-2 methods
- Testing legacy code that's hard to refactor
- Verifying calls on a real object
```

---

## 9. Argument Captors

**Capture** the actual argument passed to a mock for inspection.

```java
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;

@ExtendWith(MockitoExtension.class)
class CaptorTest {
    
    @Mock UserRepository repository;
    @InjectMocks UserService service;
    
    @Captor
    ArgumentCaptor<User> userCaptor;   // Captures User arguments
    
    @Test
    void testCaptureArgument() {
        User input = new User(0, "Alice", "alice@example.com");
        
        service.createUser(input);
        
        // Capture the argument passed to save()
        verify(repository).save(userCaptor.capture());
        
        // Inspect the captured argument
        User captured = userCaptor.getValue();
        assertEquals("Alice", captured.getName());
        assertEquals("alice@example.com", captured.getEmail());
    }
    
    @Test
    void testCaptureMultipleCalls() {
        service.createUser(new User(0, "Alice", "a@x.com"));
        service.createUser(new User(0, "Bob", "b@x.com"));
        
        // Capture all calls
        verify(repository, times(2)).save(userCaptor.capture());
        
        List<User> allCaptured = userCaptor.getAllValues();
        assertEquals(2, allCaptured.size());
        assertEquals("Alice", allCaptured.get(0).getName());
        assertEquals("Bob", allCaptured.get(1).getName());
    }
    
    // Manual captor (without annotation)
    @Test
    void testManualCaptor() {
        ArgumentCaptor<Integer> idCaptor = ArgumentCaptor.forClass(Integer.class);
        
        service.getUser(42);
        
        verify(repository).findById(idCaptor.capture());
        assertEquals(42, idCaptor.getValue());
    }
}
```

---

## 10. Advanced Features

### BDD Style (Behavior-Driven Development)

```java
import static org.mockito.BDDMockito.*;

@Test
void testBDDStyle() {
    // GIVEN (instead of when)
    given(repository.findById(1)).willReturn(alice);
    
    // WHEN
    User result = service.getUser(1);
    
    // THEN (instead of verify)
    then(repository).should().findById(1);
    assertEquals("Alice", result.getName());
}
```

### Resetting Mocks

```java
@Test
void testReset() {
    when(repository.findById(1)).thenReturn(alice);
    service.getUser(1);
    
    reset(repository);  // Clear all stubbing and verification
    
    // Now findById(1) returns null again (default)
    assertThrows(UserNotFoundException.class, () -> service.getUser(1));
}
```

### Mocking Static Methods (Mockito 3.4+)

```java
@Test
void testStaticMethod() {
    try (MockedStatic<UUID> mockedUUID = mockStatic(UUID.class)) {
        UUID fixedUUID = UUID.fromString("12345678-1234-1234-1234-123456789012");
        mockedUUID.when(UUID::randomUUID).thenReturn(fixedUUID);
        
        assertEquals(fixedUUID, UUID.randomUUID());
    }
    // Static mock is automatically cleaned up outside the try block
}
```

### Mocking Final Classes/Methods

```java
// Mockito 2+ can mock final classes by default (using inline mock maker)
// Just mock them like any other class:
final class FinalService {
    final String getValue() { return "real"; }
}

@Mock FinalService finalService;

@Test
void testFinal() {
    when(finalService.getValue()).thenReturn("mocked");
    assertEquals("mocked", finalService.getValue());
}
```

---

*Previous: [09-JUnit-Testing.md](09-JUnit-Testing.md)*
*Next: [11-Logging.md](11-Logging.md)*
