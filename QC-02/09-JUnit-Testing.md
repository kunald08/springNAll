# JUnit 5 — Unit Testing from Zero to Expert

## Table of Contents
1. [What Is Unit Testing?](#1-what-is-unit-testing)
2. [JUnit 5 Architecture](#2-junit-5-architecture)
3. [Your First Test](#3-your-first-test)
4. [Annotations](#4-annotations)
5. [Assertions](#5-assertions)
6. [Test Lifecycle](#6-test-lifecycle)
7. [Parameterized Tests](#7-parameterized-tests)
8. [Nested Tests](#8-nested-tests)
9. [Dynamic Tests](#9-dynamic-tests)
10. [Assumptions, Timeouts & Exceptions](#10-assumptions-timeouts--exceptions)
11. [Test Suites](#11-test-suites)
12. [TDD — Test-Driven Development](#12-tdd--test-driven-development)
13. [Debugging Techniques](#13-debugging-techniques)

---

## 1. What Is Unit Testing?

A **unit test** tests a single, small piece of code (a method or class) in **isolation**.

```
Why unit test?
┌───────────────────────────────────────────────────┐
│ 1. CATCH BUGS EARLY — find bugs before users do   │
│ 2. CONFIDENCE — refactor without fear             │
│ 3. DOCUMENTATION — tests show how code should work│
│ 4. DESIGN — forces you to write testable code     │
│ 5. REGRESSION — ensure old bugs don't come back   │
└───────────────────────────────────────────────────┘

Testing Pyramid:
        ╱╲
       ╱  ╲      E2E Tests (few, slow, expensive)
      ╱────╲     UI, full system tests
     ╱      ╲
    ╱────────╲   Integration Tests (some)
   ╱          ╲  Database, API, service interaction
  ╱────────────╲
 ╱              ╲ Unit Tests (MANY, fast, cheap)
╱────────────────╲ Individual methods/classes
```

---

## 2. JUnit 5 Architecture

```
JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage

┌───────────────────────────────────────────────┐
│             JUnit Platform                    │
│  Foundation for launching testing frameworks  │
│  (TestEngine API, Console Launcher)           │
├───────────────────────────────────────────────┤
│   JUnit Jupiter          │  JUnit Vintage     │
│   (JUnit 5 API +         │  (backward compat  │
│    new programming       │   for JUnit 3 & 4) │
│    model + annotations)  │                    │
└──────────────────────────┴────────────────────┘
```

### Maven Setup

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
</dependency>
```

### File Structure Convention

```
src/
├── main/
│   └── java/
│       └── com/example/
│           └── Calculator.java         ← Source code
└── test/
    └── java/
        └── com/example/
            └── CalculatorTest.java     ← Test code (mirrors source structure)
```

---

## 3. Your First Test

```java
// Source: src/main/java/com/example/Calculator.java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public int divide(int a, int b) {
        if (b == 0) throw new ArithmeticException("Cannot divide by zero");
        return a / b;
    }
}
```

```java
// Test: src/test/java/com/example/CalculatorTest.java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {    // No need to be public in JUnit 5!
    
    @Test                  // Marks this method as a test
    void testAdd() {
        // Arrange — set up test data
        Calculator calc = new Calculator();
        
        // Act — call the method being tested
        int result = calc.add(2, 3);
        
        // Assert — verify the result
        assertEquals(5, result, "2 + 3 should equal 5");
    }
    
    @Test
    void testDivideByZero() {
        Calculator calc = new Calculator();
        
        // Assert that an exception is thrown
        assertThrows(ArithmeticException.class, () -> {
            calc.divide(10, 0);
        });
    }
}

// Run: Right-click → Run Tests (in IDE) or: mvn test
```

### AAA Pattern

```
Every test follows the AAA pattern:

Arrange → Set up objects, data, conditions
Act     → Call the method being tested
Assert  → Verify the expected outcome

@Test
void testTransfer() {
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

---

## 4. Annotations

```java
import org.junit.jupiter.api.*;

class MyTest {
    
    @BeforeAll       // Runs ONCE before ALL tests in the class (must be static)
    static void setupAll() {
        System.out.println("Before all tests");
    }
    
    @AfterAll        // Runs ONCE after ALL tests (must be static)
    static void teardownAll() {
        System.out.println("After all tests");
    }
    
    @BeforeEach      // Runs before EACH test
    void setup() {
        System.out.println("Before each test");
    }
    
    @AfterEach       // Runs after EACH test
    void teardown() {
        System.out.println("After each test");
    }
    
    @Test            // Marks a test method
    void test1() {
        System.out.println("Test 1");
    }
    
    @Test
    void test2() {
        System.out.println("Test 2");
    }
    
    @Disabled("Bug #123 — fix pending")   // Skip this test
    @Test
    void brokenTest() { }
    
    @DisplayName("Should calculate tax correctly for salary > 50k")
    @Test
    void testTax() { }   // Shows friendly name in test report
    
    @RepeatedTest(5)     // Run this test 5 times
    void flakyTest() { }
    
    @Tag("slow")         // Tag for filtering (mvn test -Dgroups=slow)
    @Test
    void slowTest() { }
}

// Execution order:
// @BeforeAll
//   @BeforeEach → test1() → @AfterEach
//   @BeforeEach → test2() → @AfterEach
// @AfterAll
```

---

## 5. Assertions

```java
import static org.junit.jupiter.api.Assertions.*;

// Basic assertions
assertEquals(expected, actual);              // Objects equal
assertEquals(expected, actual, "message");   // With custom message
assertNotEquals(unexpected, actual);

assertTrue(condition);
assertFalse(condition);

assertNull(object);
assertNotNull(object);

assertSame(expected, actual);               // Same reference (==)
assertNotSame(unexpected, actual);

// Comparing arrays
assertArrayEquals(new int[]{1, 2, 3}, result);

// Comparing iterables (Lists, Sets)
assertIterableEquals(List.of(1, 2, 3), resultList);

// Exception testing
Exception ex = assertThrows(IllegalArgumentException.class, () -> {
    service.process(null);
});
assertEquals("Input cannot be null", ex.getMessage());

// Does NOT throw
assertDoesNotThrow(() -> service.process("valid"));

// Assert multiple conditions at once (reports ALL failures, not just the first)
assertAll("Employee validation",
    () -> assertEquals("Alice", emp.getName()),
    () -> assertEquals(30, emp.getAge()),
    () -> assertEquals("IT", emp.getDepartment()),
    () -> assertNotNull(emp.getEmail())
);
// If name is wrong AND department is wrong, you see BOTH failures!

// Timeout
assertTimeout(Duration.ofSeconds(2), () -> {
    // This test must complete within 2 seconds
    slowMethod();
});

// assertTimeoutPreemptively — kills the test if time exceeded
assertTimeoutPreemptively(Duration.ofMillis(500), () -> {
    Thread.sleep(10000);  // Will be killed after 500ms
});

// Fail explicitly
if (someCondition) {
    fail("Should not reach here");
}
```

---

## 6. Test Lifecycle

```java
@TestInstance(TestInstance.Lifecycle.PER_METHOD)   // DEFAULT — new instance per test
class DefaultLifecycle {
    private int count = 0;
    
    @Test void test1() { count++; assertEquals(1, count); }  // count starts at 0
    @Test void test2() { count++; assertEquals(1, count); }  // count starts at 0 again!
    // Each test gets a FRESH instance of the test class
}

@TestInstance(TestInstance.Lifecycle.PER_CLASS)    // One instance for all tests
class SharedLifecycle {
    private int count = 0;
    
    @Test void test1() { count++; assertEquals(1, count); }  // count = 1
    @Test void test2() { count++; assertEquals(2, count); }  // count = 2 (shared!)
    
    // Benefit: @BeforeAll and @AfterAll don't need to be static!
}

// Test execution order
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
class OrderedTests {
    @Test @Order(1) void createUser() { }
    @Test @Order(2) void loginUser() { }
    @Test @Order(3) void deleteUser() { }
}
```

---

## 7. Parameterized Tests

Run the **same test with different data** — avoid duplicating test methods.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;

class ParameterizedTests {
    
    // @ValueSource — single parameter values
    @ParameterizedTest
    @ValueSource(strings = {"hello", "world", "junit"})
    void testNotEmpty(String word) {
        assertFalse(word.isEmpty());
    }
    
    @ParameterizedTest
    @ValueSource(ints = {2, 4, 6, 8, 100})
    void testEvenNumbers(int number) {
        assertEquals(0, number % 2);
    }
    
    // @NullAndEmptySource — null and empty values
    @ParameterizedTest
    @NullAndEmptySource
    @ValueSource(strings = {"  ", "\t", "\n"})
    void testBlankStrings(String input) {
        assertTrue(input == null || input.isBlank());
    }
    
    // @CsvSource — multiple parameters per test
    @ParameterizedTest
    @CsvSource({
        "1, 2, 3",       // a=1, b=2, expected=3
        "5, 3, 8",
        "-1, 1, 0",
        "0, 0, 0"
    })
    void testAdd(int a, int b, int expected) {
        Calculator calc = new Calculator();
        assertEquals(expected, calc.add(a, b));
    }
    
    // @CsvFileSource — load from CSV file
    @ParameterizedTest
    @CsvFileSource(resources = "/test-data.csv", numLinesToSkip = 1)
    void testFromFile(String name, int age) {
        assertNotNull(name);
        assertTrue(age > 0);
    }
    
    // @EnumSource — test with enum values
    @ParameterizedTest
    @EnumSource(Month.class)
    void testAllMonths(Month month) {
        assertNotNull(month);
    }
    
    @ParameterizedTest
    @EnumSource(value = Month.class, names = {"JANUARY", "FEBRUARY", "MARCH"})
    void testQ1Months(Month month) {
        assertTrue(month.getValue() <= 3);
    }
    
    // @MethodSource — complex test data from a method
    @ParameterizedTest
    @MethodSource("provideStringsForPalindrome")
    void testPalindrome(String input, boolean expected) {
        assertEquals(expected, isPalindrome(input));
    }
    
    static Stream<Arguments> provideStringsForPalindrome() {
        return Stream.of(
            Arguments.of("racecar", true),
            Arguments.of("hello", false),
            Arguments.of("madam", true),
            Arguments.of("", true)
        );
    }
}
```

---

## 8. Nested Tests

**Group related tests** for better organization and readability.

```java
@DisplayName("Calculator Tests")
class CalculatorTest {
    
    Calculator calc;
    
    @BeforeEach
    void setup() {
        calc = new Calculator();
    }
    
    @Nested
    @DisplayName("Addition")
    class AdditionTests {
        
        @Test
        @DisplayName("should add two positive numbers")
        void testPositive() {
            assertEquals(5, calc.add(2, 3));
        }
        
        @Test
        @DisplayName("should add negative numbers")
        void testNegative() {
            assertEquals(-5, calc.add(-2, -3));
        }
        
        @Test
        @DisplayName("should handle zero")
        void testZero() {
            assertEquals(3, calc.add(3, 0));
        }
    }
    
    @Nested
    @DisplayName("Division")
    class DivisionTests {
        
        @Test
        @DisplayName("should divide correctly")
        void testDivide() {
            assertEquals(5, calc.divide(10, 2));
        }
        
        @Test
        @DisplayName("should throw on divide by zero")
        void testDivideByZero() {
            assertThrows(ArithmeticException.class, () -> calc.divide(10, 0));
        }
    }
}

// Output in IDE:
// ✅ Calculator Tests
//    ✅ Addition
//       ✅ should add two positive numbers
//       ✅ should add negative numbers
//       ✅ should handle zero
//    ✅ Division
//       ✅ should divide correctly
//       ✅ should throw on divide by zero
```

---

## 9. Dynamic Tests

Create tests **at runtime** based on data or conditions.

```java
import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.TestFactory;
import static org.junit.jupiter.api.DynamicTest.dynamicTest;

class DynamicTestDemo {
    
    @TestFactory
    Collection<DynamicTest> dynamicTests() {
        return List.of(
            dynamicTest("Test 1 + 1 = 2", () -> assertEquals(2, 1 + 1)),
            dynamicTest("Test 2 * 3 = 6", () -> assertEquals(6, 2 * 3)),
            dynamicTest("Test 10 / 2 = 5", () -> assertEquals(5, 10 / 2))
        );
    }
    
    @TestFactory
    Stream<DynamicTest> dynamicTestsFromStream() {
        // Generate tests from data
        return IntStream.range(1, 10)
            .mapToObj(n -> dynamicTest(
                n + " squared = " + (n * n),
                () -> assertEquals(n * n, Math.pow(n, 2), 0.0001)
            ));
    }
    
    @TestFactory
    Stream<DynamicTest> testFilesInDirectory() throws IOException {
        // Generate tests from files in a directory
        return Files.list(Path.of("test-data/"))
            .filter(p -> p.toString().endsWith(".txt"))
            .map(path -> dynamicTest(
                "Testing file: " + path.getFileName(),
                () -> assertDoesNotThrow(() -> processFile(path))
            ));
    }
}
```

---

## 10. Assumptions, Timeouts & Exceptions

### Assumptions

```java
import static org.junit.jupiter.api.Assumptions.*;

@Test
void onlyOnLinux() {
    // Skip test if assumption fails (test is ABORTED, not FAILED)
    assumeTrue(System.getProperty("os.name").contains("Linux"));
    // This code only runs on Linux
}

@Test
void conditionalTest() {
    assumingThat(
        System.getenv("ENV").equals("PROD"),
        () -> {
            // Only run this assertion in production
            assertEquals(443, getPort());
        }
    );
    // This always runs
    assertNotNull(getConfig());
}
```

### Testing Exceptions

```java
@Test
void shouldThrowWithMessage() {
    IllegalArgumentException ex = assertThrows(
        IllegalArgumentException.class,
        () -> service.validate(null)
    );
    
    // Verify the exception message
    assertEquals("Input cannot be null", ex.getMessage());
    
    // Verify exception type
    assertTrue(ex instanceof RuntimeException);
}

@Test
void shouldNotThrow() {
    assertDoesNotThrow(() -> service.validate("valid input"));
}
```

### Timeout Testing

```java
@Test
@Timeout(value = 500, unit = TimeUnit.MILLISECONDS)  // Annotation-based
void mustFinishQuickly() {
    // Test fails if it takes longer than 500ms
    quickMethod();
}

@Test
void timeoutAssertion() {
    // Assertion-based (more flexible)
    String result = assertTimeout(Duration.ofSeconds(2), () -> {
        Thread.sleep(100);
        return "done";
    });
    assertEquals("done", result);
}
```

---

## 11. Test Suites

Group multiple test classes together.

```java
import org.junit.platform.suite.api.*;

@Suite
@SelectClasses({
    CalculatorTest.class,
    StringUtilsTest.class,
    UserServiceTest.class
})
class AllTests {}

// Or select by package
@Suite
@SelectPackages("com.example.tests")
class AllTests {}

// Include/exclude by tags
@Suite
@SelectPackages("com.example")
@IncludeTags("fast")
@ExcludeTags("slow")
class FastTests {}
```

---

## 12. TDD — Test-Driven Development

### Red-Green-Refactor Cycle

```
   ┌───────────────────┐
   │    1. RED         │  Write a FAILING test first
   │    (write test)   │  (the feature doesn't exist yet)
   └────────┬──────────┘
            │
   ┌────────▼───────────┐
   │    2. GREEN        │  Write the MINIMUM code to make the test pass
   │    (make it pass)  │  (don't worry about elegance)
   └────────┬───────────┘
            │
   ┌────────▼───────────┐
   │    3. REFACTOR     │  Clean up the code while keeping tests green
   │    (improve code)  │  (remove duplication, improve naming)
   └────────┬───────────┘
            │
            └──→ Back to step 1 for the next feature
```

### TDD Example

```java
// STEP 1: RED — Write a failing test
@Test
void shouldReturnFizzForMultiplesOfThree() {
    assertEquals("Fizz", fizzBuzz(3));
    assertEquals("Fizz", fizzBuzz(6));
    assertEquals("Fizz", fizzBuzz(9));
}
// Compile error! fizzBuzz() doesn't exist yet → RED ❌

// STEP 2: GREEN — Write minimum code to pass
public String fizzBuzz(int n) {
    if (n % 3 == 0) return "Fizz";
    return String.valueOf(n);
}
// Test passes → GREEN ✅

// STEP 3: Add another test (RED)
@Test
void shouldReturnBuzzForMultiplesOfFive() {
    assertEquals("Buzz", fizzBuzz(5));
}
// Fails → RED ❌

// STEP 4: Make it pass (GREEN)
public String fizzBuzz(int n) {
    if (n % 3 == 0) return "Fizz";
    if (n % 5 == 0) return "Buzz";
    return String.valueOf(n);
}
// Passes → GREEN ✅

// Continue the cycle... add FizzBuzz for multiples of both, then refactor
```

---

## 13. Debugging Techniques

### IDE Debugging

```
Breakpoints:
- Click left margin in IDE to set a breakpoint (red dot)
- Program pauses at that line during debugging

Step Operations:
- Step Over (F8)  — execute current line, move to next
- Step Into (F7)  — enter the method being called
- Step Out (Shift+F8) — finish current method, return to caller
- Resume (F9)     — continue to next breakpoint

Watch Variables:
- Right-click variable → Add to Watches
- See variable values change as you step through code

Conditional Breakpoints:
- Right-click breakpoint → Condition: "i == 42"
- Only pauses when condition is true

Evaluate Expression:
- While paused, evaluate any expression in the current context
- Alt+F8 in IntelliJ
```

### Debugging Tests

```java
// Add System.out.println temporarily (quick and dirty)
@Test
void debugTest() {
    int result = calc.add(2, 3);
    System.out.println("Result: " + result);  // Quick debug output
    assertEquals(5, result);
}

// Use @DisplayName for clear failure messages
@Test
@DisplayName("Add 2 + 3 should return 5")
void testAdd() {
    assertEquals(5, calc.add(2, 3), 
        "Calculator.add(2,3) returned wrong result");
}

// Use assertAll to see ALL failures at once
@Test
void testMultipleAssertions() {
    assertAll(
        () -> assertEquals(5, calc.add(2, 3)),
        () -> assertEquals(0, calc.add(-1, 1)),
        () -> assertEquals(-3, calc.add(-1, -2))
    );
}
```

---

*Previous: [08-Design-Patterns-SOLID.md](08-Design-Patterns-SOLID.md)*
*Next: [10-Mockito-Mocking.md](10-Mockito-Mocking.md)*
