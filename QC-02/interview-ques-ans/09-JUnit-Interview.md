# JUnit 5 — Interview Questions & Answers

> **How to use this:** Answers are written exactly how you should speak in an interview — clear, confident, and concise.

---

## 1. What is unit testing? Why is it important?

**Answer:**
Unit testing is testing a single, small piece of code — usually a method — in **isolation** from its dependencies. The goal is to verify that each unit of code works correctly on its own.

Why it matters:
- **Catch bugs early** — find issues before deployment, which is cheaper to fix
- **Confidence to refactor** — I can change internal code knowing tests will catch any regressions
- **Living documentation** — tests show exactly how the code is supposed to behave
- **Better design** — writing testable code naturally leads to loosely coupled, well-structured code

In a testing pyramid: many fast unit tests at the base, fewer integration tests in the middle, and very few slow E2E tests at the top.

---

## 2. What is JUnit 5? How is it different from JUnit 4?

**Answer:**
JUnit 5 is the latest version, with a modular architecture:
- **JUnit Platform** — foundation for launching test frameworks
- **JUnit Jupiter** — new programming model with annotations and assertions
- **JUnit Vintage** — backward compatibility for JUnit 3/4 tests

Key differences from JUnit 4:
- No need for `public` on test classes or methods
- `@BeforeEach`/`@AfterEach` replace `@Before`/`@After`
- `@BeforeAll`/`@AfterAll` replace `@BeforeClass`/`@AfterClass`
- `@Disabled` replaces `@Ignore`
- `assertThrows()` instead of `@Test(expected=...)`
- Built-in support for parameterized tests, nested tests, and dynamic tests
- Extension model (`@ExtendWith`) replaces Runners and Rules

---

## 3. Explain the test lifecycle in JUnit 5.

**Answer:**
```
@BeforeAll (once, static) → setup shared resources

    @BeforeEach → setup for each test
    @Test → run test 1
    @AfterEach → cleanup after each test

    @BeforeEach → setup for each test
    @Test → run test 2
    @AfterEach → cleanup after each test

@AfterAll (once, static) → cleanup shared resources
```

By default, JUnit creates a **new instance** of the test class for each test method. This ensures tests are independent. I can change this with `@TestInstance(Lifecycle.PER_CLASS)` to share state across tests.

---

## 4. What are the main JUnit 5 annotations?

**Answer:**
- `@Test` — marks a test method
- `@BeforeEach` / `@AfterEach` — run before/after each test
- `@BeforeAll` / `@AfterAll` — run once before/after all tests (must be static)
- `@DisplayName("...")` — human-readable test name in reports
- `@Disabled("reason")` — skip a test
- `@Tag("category")` — categorize tests for selective execution
- `@RepeatedTest(n)` — run a test n times
- `@ParameterizedTest` — run with different data sets
- `@Nested` — group related tests in inner classes
- `@TestMethodOrder` — control test execution order
- `@Timeout` — fail if test exceeds time limit

---

## 5. What assertions do you commonly use?

**Answer:**
```java
assertEquals(expected, actual);           // Values equal
assertNotEquals(unexpected, actual);
assertTrue(condition);                     // Condition is true
assertFalse(condition);
assertNull(obj);                           // Object is null
assertNotNull(obj);
assertSame(a, b);                          // Same reference (==)
assertArrayEquals(expected, actual);       // Arrays equal
assertThrows(Exception.class, () -> ...);  // Exception is thrown
assertDoesNotThrow(() -> ...);
assertTimeout(Duration.ofSeconds(2), () -> ...);  // Completes within time

// assertAll — checks ALL conditions, reports ALL failures at once
assertAll("user validation",
    () -> assertEquals("Alice", user.getName()),
    () -> assertEquals(30, user.getAge()),
    () -> assertNotNull(user.getEmail())
);
```

`assertAll` is important because regular assertions stop at the first failure. With `assertAll`, I see ALL failures in one run.

---

## 6. What are parameterized tests?

**Answer:**
Parameterized tests run the **same test logic with different data**, avoiding duplicate test methods.

```java
@ParameterizedTest
@CsvSource({"1, 2, 3", "5, 3, 8", "-1, 1, 0"})
void testAdd(int a, int b, int expected) {
    assertEquals(expected, calc.add(a, b));
}
```

Data sources include:
- `@ValueSource` — single values: `@ValueSource(ints = {1, 2, 3})`
- `@CsvSource` — comma-separated tuples
- `@CsvFileSource` — load from a CSV file
- `@EnumSource` — all or selected enum values
- `@MethodSource` — data from a static method returning a Stream
- `@NullAndEmptySource` — null and empty values

---

## 7. What are nested tests?

**Answer:**
Nested tests use `@Nested` inner classes to **group related tests** for better organization and readability.

```java
@DisplayName("Calculator")
class CalculatorTest {
    @Nested @DisplayName("Addition")
    class AddTests {
        @Test void positiveNumbers() { ... }
        @Test void negativeNumbers() { ... }
    }
    @Nested @DisplayName("Division")
    class DivTests {
        @Test void normalDivision() { ... }
        @Test void divideByZero() { ... }
    }
}
```

The output forms a tree structure in test reports, making it clear which area of functionality passed or failed.

---

## 8. What is TDD? Explain the Red-Green-Refactor cycle.

**Answer:**
TDD (Test-Driven Development) is a practice where I write tests **before** writing the actual code:

1. **RED** — Write a failing test for a small piece of functionality
2. **GREEN** — Write the minimum code to make the test pass
3. **REFACTOR** — Clean up the code while keeping all tests green

Then repeat for the next piece of functionality.

Benefits: I get complete test coverage by design, the tests drive a clean API, I build confidence incrementally, and I avoid writing unnecessary code (YAGNI).

---

## 9. What is the AAA pattern?

**Answer:**
AAA stands for **Arrange, Act, Assert** — the standard structure for a test:

- **Arrange** — set up test data and objects
- **Act** — call the method being tested
- **Assert** — verify the expected outcome

```java
@Test void testTransfer() {
    // Arrange
    Account from = new Account(1000);
    Account to = new Account(500);
    // Act
    from.transfer(to, 200);
    // Assert
    assertEquals(800, from.getBalance());
    assertEquals(700, to.getBalance());
}
```

This pattern makes tests readable and consistent.

---

## 10. How do you test exceptions in JUnit 5?

**Answer:**
Using `assertThrows()`:
```java
@Test
void shouldThrowOnNull() {
    IllegalArgumentException ex = assertThrows(
        IllegalArgumentException.class,
        () -> service.validate(null)
    );
    assertEquals("Input cannot be null", ex.getMessage());
}
```

This is better than JUnit 4's `@Test(expected = ...)` because I can:
1. Assert on the specific exception message
2. Test that the exception is thrown by a specific line (not just anywhere in the test)
3. Continue with additional assertions after the exception

---

*Back to: [09-JUnit-Testing.md](../09-JUnit-Testing.md)*
