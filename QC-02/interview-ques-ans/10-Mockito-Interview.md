# Mockito — Interview Questions & Answers

> **How to use this:** Answers are written exactly how you should speak in an interview — clear, confident, and concise.

---

## 1. What is mocking? Why do we need it?

**Answer:**
Mocking is creating **fake objects** that simulate the behavior of real dependencies. In unit testing, I want to test a class in isolation — but it may depend on a database, a web service, or another class. Instead of using the real dependency (which is slow, fragile, or unavailable), I create a mock that I can control.

For example, if my `OrderService` depends on `PaymentGateway`, I mock `PaymentGateway` so I can test `OrderService` independently — without actually charging a credit card.

---

## 2. What is the difference between mock, stub, and spy?

**Answer:**
- **Mock** — a fully fake object. All methods return defaults (null, 0, false). I program it to return specific values and can verify method calls on it.
- **Stub** — a simplified object with hardcoded return values. Focused on providing test data.
- **Spy** — wraps a **real object**. By default, calls the real methods, but I can override specific methods.

In Mockito:
```java
UserRepo mock = mock(UserRepo.class);       // all methods return null
UserRepo spy = spy(new UserRepoImpl());      // calls real methods unless stubbed
```

**Rule of thumb:** use mocks for dependencies I don't care about internally; use spies when I need partial real behavior.

---

## 3. How do you create and use a mock in Mockito?

**Answer:**
Two ways:

**Inline:**
```java
UserService service = mock(UserService.class);
when(service.findById(1)).thenReturn(new User("Alice"));
```

**Annotation-based (preferred):**
```java
@ExtendWith(MockitoExtension.class)
class OrderTest {
    @Mock UserRepo userRepo;
    @InjectMocks OrderService orderService; // injects mocks into constructor

    @Test void testFind() {
        when(userRepo.findById(1)).thenReturn(new User("Alice"));
        User user = orderService.getUser(1);
        assertEquals("Alice", user.getName());
    }
}
```

`@InjectMocks` creates the real class and injects the `@Mock` objects into it via constructor, setter, or field injection.

---

## 4. Explain when/thenReturn vs doReturn/when.

**Answer:**
Both stub a method, but with a subtle difference:

```java
// Standard — type-safe, preferred
when(repo.findById(1)).thenReturn(user);

// Alternative — required in some special cases
doReturn(user).when(repo).findById(1);
```

I **must** use `doReturn/when` in these situations:
1. **Spies** — `when()` on a spy calls the real method before stubbing; `doReturn()` avoids that
2. **void methods** — `when()` can't wrap void; use `doNothing().when(obj).voidMethod()`
3. **Already stubbed methods** — to change behavior without invoking the old stub

---

## 5. How do you verify method calls?

**Answer:**
```java
// Was it called?
verify(repo).save(user);

// Called exactly n times?
verify(repo, times(2)).findById(anyInt());

// Never called?
verify(repo, never()).delete(any());

// At least / at most
verify(repo, atLeastOnce()).findById(1);
verify(repo, atMost(3)).findById(anyInt());

// No more interactions after verified calls
verifyNoMoreInteractions(repo);
```

Verification is about behavior — I'm confirming that my code **interacted** with the dependency correctly, not just that the result is correct.

---

## 6. What are argument matchers?

**Answer:**
Argument matchers let me stub/verify calls without specifying exact values:

```java
when(repo.findByName(anyString())).thenReturn(user);
when(repo.findById(anyInt())).thenReturn(user);
when(repo.find(any(User.class))).thenReturn(user);
verify(repo).save(argThat(u -> u.getName().equals("Alice")));
```

**Important rule:** if I use a matcher for one argument, I must use matchers for ALL arguments:
```java
// WRONG: when(repo.find("Alice", 30));  — mixing literal and matcher
when(repo.find(eq("Alice"), anyInt()));  // CORRECT
```

Common matchers: `any()`, `anyString()`, `anyInt()`, `anyList()`, `eq(value)`, `isNull()`, `argThat(predicate)`.

---

## 7. What is an ArgumentCaptor?

**Answer:**
An `ArgumentCaptor` captures the actual argument passed to a mocked method, so I can assert on it afterward.

```java
@Captor ArgumentCaptor<User> userCaptor;

@Test void testSave() {
    service.createUser("Alice", 30);

    verify(repo).save(userCaptor.capture());

    User captured = userCaptor.getValue();
    assertEquals("Alice", captured.getName());
    assertEquals(30, captured.getAge());
}
```

This is useful when the method under test internally creates an object and passes it to a dependency. I can't see that object directly, but the captor lets me inspect it.

---

## 8. How do you mock void methods?

**Answer:**
`when()` doesn't work with void methods, so I use `do...()` methods:

```java
// Do nothing (default for mocks, explicit for clarity)
doNothing().when(repo).delete(1);

// Throw exception
doThrow(new RuntimeException("fail")).when(repo).delete(1);

// Custom action
doAnswer(invocation -> {
    int id = invocation.getArgument(0);
    System.out.println("Deleting: " + id);
    return null;
}).when(repo).delete(anyInt());
```

---

## 9. Can you mock static methods and final classes?

**Answer:**
Yes, since Mockito 3.4+, static methods can be mocked using `mockStatic()`:

```java
try (MockedStatic<Utility> mocked = mockStatic(Utility.class)) {
    mocked.when(() -> Utility.generateId()).thenReturn("ABC123");
    assertEquals("ABC123", Utility.generateId());
}
// Outside try-block, static method returns to normal behavior
```

Final classes and methods can also be mocked since Mockito 2+ with the inline mock maker (enabled by default in newer versions).

However, I try to **avoid** mocking statics because it usually means the code design could be improved — dependency injection is better than static calls.

---

## 10. What is BDD-style Mockito?

**Answer:**
BDD (Behavior-Driven Development) style uses `given/when/then` language for more readable tests:

```java
import static org.mockito.BDDMockito.*;

@Test void shouldReturnUser() {
    // Given
    given(repo.findById(1)).willReturn(new User("Alice"));

    // When
    User user = service.getUser(1);

    // Then
    then(repo).should().findById(1);
    assertThat(user.getName()).isEqualTo("Alice");
}
```

`given()` replaces `when()`, and `then().should()` replaces `verify()`. The test reads like a specification.

---

## 11. What are common Mockito mistakes to avoid?

**Answer:**
1. **Mocking value objects** — don't mock simple POJOs, just create real ones
2. **Over-verifying** — don't verify every single call; verify only what matters for the test's purpose
3. **Mocking what you don't own** — don't mock third-party library types; wrap them in your own interface
4. **Ignoring the "strict stubbing" warning** — Mockito warns about unused stubs, which usually means my test setup is wrong
5. **Using `@InjectMocks` with ambiguous injection** — if constructor changes, injection may silently break; prefer explicit construction
6. **Mixing matchers and literals** — must use `eq()` for literals when any other argument uses a matcher

---

*Back to: [10-Mockito.md](../10-Mockito.md)*
