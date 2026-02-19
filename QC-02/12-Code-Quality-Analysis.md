# Code Quality & Analysis — PMD, Checkstyle, SonarQube, JaCoCo, SpotBugs

## Table of Contents
1. [Why Code Quality Matters](#1-why-code-quality-matters)
2. [Static Code Analysis](#2-static-code-analysis)
3. [PMD — Bug & Bad Practice Finder](#3-pmd)
4. [Checkstyle — Code Style Enforcer](#4-checkstyle)
5. [SpotBugs — Bug Detection](#5-spotbugs)
6. [JaCoCo — Code Coverage](#6-jacoco)
7. [SonarQube — All-in-One Quality Platform](#7-sonarqube)
8. [Code Smells & Technical Debt](#8-code-smells--technical-debt)
9. [Code Review & Pull Requests](#9-code-review--pull-requests)
10. [CI — Continuous Integration](#10-ci--continuous-integration)

---

## 1. Why Code Quality Matters

```
Good Code:                          Bad Code:
✅ Easy to read and understand      ❌ Confusing, no one wants to touch it
✅ Easy to change and extend        ❌ Change one thing, break three others
✅ Few bugs                         ❌ Constant bug reports
✅ New team members ramp up fast    ❌ Takes months to understand
✅ Automated tests catch regressions❌ "Works on my machine" 🤷

Code Quality Metrics:
┌────────────────────────────────────────────────────┐
│ RELIABILITY   — Does it work correctly?            │
│ SECURITY      — Is it safe from vulnerabilities?   │
│ MAINTAINABILITY — Can it be changed easily?        │
│ COVERAGE      — Is it tested?                      │ 
│ DUPLICATIONS  — Is code copy-pasted?               │
│ COMPLEXITY    — Is it too complicated?             │
└────────────────────────────────────────────────────┘
```

---

## 2. Static Code Analysis

**Static analysis** examines code **without running it** — finds bugs, style issues, and vulnerabilities by reading the source code or bytecode.

```
Types of Analysis:

Static Analysis (compile time):
- PMD        → finds bad practices, unused code, complexity
- Checkstyle → enforces coding standards (formatting, naming)
- SpotBugs   → finds real bugs in bytecode
- SonarQube  → combines all of the above + more

Dynamic Analysis (runtime):
- JaCoCo     → code coverage (which lines are tested?)
- Profilers  → performance analysis
- Debuggers  → step-through execution
```

---

## 3. PMD

**PMD** scans Java source code for potential problems: unused variables, empty catch blocks, unnecessary object creation, complexity, and more.

### What PMD Catches

```java
// 1. Empty catch block
try {
    riskyOperation();
} catch (Exception e) {
    // PMD: "Empty catch block"  ← Swallowing exceptions silently!
}

// 2. Unused variable
public void process() {
    int x = 42;         // PMD: "Unused local variable 'x'"
    doSomethingElse();
}

// 3. Unnecessary object creation
Boolean b = new Boolean(true);   // PMD: "Use Boolean.valueOf(true)"

// 4. God class (too many responsibilities)
public class AppManager {       // PMD: "GodClass — too many methods/fields"
    // 50 methods, 30 fields...
}

// 5. Cyclomatic complexity too high
public void process(int type) {  // PMD: "CyclomaticComplexity > 10"
    if (type == 1) { ... }
    else if (type == 2) { ... }
    else if (type == 3) { ... }
    // ... 15 more branches
}
```

### PMD Maven Configuration

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-pmd-plugin</artifactId>
    <version>3.21.2</version>
    <configuration>
        <rulesets>
            <ruleset>/category/java/bestpractices.xml</ruleset>
            <ruleset>/category/java/codestyle.xml</ruleset>
            <ruleset>/category/java/design.xml</ruleset>
            <ruleset>/category/java/errorprone.xml</ruleset>
            <ruleset>/category/java/performance.xml</ruleset>
        </rulesets>
        <failOnViolation>true</failOnViolation>
    </configuration>
</plugin>

<!-- Run: mvn pmd:check -->
```

### PMD Rule Categories

```
bestpractices  — avoid common anti-patterns
codestyle      — naming conventions, code formatting
design         — complexity, coupling, cohesion
errorprone     — likely bugs, null pointers, empty blocks
multithreading — thread safety issues
performance    — unnecessary object creation, inefficient code
security       — hard-coded passwords, SQL injection
```

---

## 4. Checkstyle

**Checkstyle** enforces **coding standards** — consistent formatting, naming conventions, Javadoc requirements.

### What Checkstyle Checks

```java
// 1. Naming conventions
int MyVariable = 5;           // Checkstyle: "Variable name must match '^[a-z][a-zA-Z0-9]*$'"
// Should be: int myVariable = 5;

class my_class { }            // Checkstyle: "Class name must match '^[A-Z][a-zA-Z0-9]*$'"
// Should be: class MyClass { }

// 2. Missing Javadoc
public void process() { }     // Checkstyle: "Missing Javadoc comment"

// 3. Line length
String s = "This is a very long string that goes way beyond 120 characters which is usually the maximum allowed by checkstyle configuration"; // Too long!

// 4. Whitespace
if(x==5){                     // Checkstyle: "WhitespaceAround: '==' is not preceded/followed by whitespace"
// Should be: if (x == 5) {

// 5. Braces
if (x == 5)
    doSomething();             // Checkstyle: "NeedBraces: 'if' construct must use braces"
// Should be:
if (x == 5) {
    doSomething();
}
```

### Checkstyle Maven Configuration

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-checkstyle-plugin</artifactId>
    <version>3.3.1</version>
    <configuration>
        <configLocation>google_checks.xml</configLocation>  <!-- or sun_checks.xml -->
        <failOnViolation>true</failOnViolation>
    </configuration>
</plugin>

<!-- Run: mvn checkstyle:check -->
```

### Built-in Configurations

```
google_checks.xml  — Google's Java style guide
sun_checks.xml     — Sun/Oracle's Java style guide
custom.xml         — Your own rules

You can also create a custom checkstyle.xml with only the rules you want.
```

---

## 5. SpotBugs

**SpotBugs** (successor of FindBugs) analyzes **compiled bytecode** to find real bugs.

### What SpotBugs Catches

```java
// 1. Null pointer dereference
public void process(String input) {
    System.out.println(input.length());   // SpotBugs: "NP_NULL_ON_SOME_PATH"
    // input could be null!
}

// 2. Infinite recursive loop
public int factorial(int n) {
    return n * factorial(n - 1);   // SpotBugs: "IL_INFINITE_RECURSIVE_LOOP"
    // Missing base case!
}

// 3. Equals without hashCode
public class User {
    @Override
    public boolean equals(Object o) { ... }
    // SpotBugs: "HE_EQUALS_NO_HASHCODE"
    // Missing hashCode override!
}

// 4. Resource leak
public void readFile() {
    FileInputStream fis = new FileInputStream("data.txt");
    // SpotBugs: "OBL_UNSATISFIED_OBLIGATION" — stream never closed!
}

// 5. Synchronization issues
public class Counter {
    private int count;
    public void increment() { count++; }       // Not synchronized!
    public synchronized int getCount() { return count; }
    // SpotBugs: "IS2_INCONSISTENT_SYNC" — inconsistent synchronization
}
```

### SpotBugs Maven Configuration

```xml
<plugin>
    <groupId>com.github.spotbugs</groupId>
    <artifactId>spotbugs-maven-plugin</artifactId>
    <version>4.8.3.0</version>
</plugin>

<!-- Run: mvn spotbugs:check -->
```

### SpotBugs Annotations

```java
import edu.umd.cs.findbugs.annotations.NonNull;
import edu.umd.cs.findbugs.annotations.Nullable;

// Help SpotBugs understand your intent
public User findUser(@NonNull String username) {   // username must not be null
    // ...
}

public @Nullable User findById(int id) {           // May return null
    // ...
}
```

---

## 6. JaCoCo

**JaCoCo** (Java Code Coverage) measures **how much of your code is tested**.

### Coverage Metrics

```
Line Coverage:    What % of lines are executed by tests?
Branch Coverage:  What % of if/else branches are tested?
Method Coverage:  What % of methods are called by tests?
Class Coverage:   What % of classes are tested?

Example:
public int calculate(int x) {        // Line 1: ✅ covered
    if (x > 0) {                     // Line 2: ✅ covered (branch: true ✅, false ❌)
        return x * 2;                // Line 3: ✅ covered
    } else {
        return x * -1;               // Line 4: ❌ NOT covered
    }
}

Line Coverage: 3/4 = 75%
Branch Coverage: 1/2 = 50%     (only tested positive numbers)
```

### JaCoCo Maven Configuration

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <!-- Prepare agent for test execution -->
        <execution>
            <goals><goal>prepare-agent</goal></goals>
        </execution>
        <!-- Generate report after tests -->
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals><goal>report</goal></goals>
        </execution>
        <!-- Enforce minimum coverage -->
        <execution>
            <id>check</id>
            <goals><goal>check</goal></goals>
            <configuration>
                <rules>
                    <rule>
                        <limits>
                            <limit>
                                <counter>LINE</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.80</minimum>  <!-- 80% line coverage required -->
                            </limit>
                            <limit>
                                <counter>BRANCH</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.70</minimum>  <!-- 70% branch coverage required -->
                            </limit>
                        </limits>
                    </rule>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>

<!-- Run: mvn test jacoco:report -->
<!-- Report: target/site/jacoco/index.html -->
```

### Understanding Coverage Reports

```
JaCoCo generates an HTML report with color coding:

🟢 GREEN — Line/branch fully covered by tests
🟡 YELLOW — Branch partially covered (e.g., only if, not else)
🔴 RED — Not covered at all

Good coverage targets:
- 80%+ line coverage (industry standard)
- 70%+ branch coverage
- 100% coverage is usually NOT worth it (diminishing returns)
- Focus on testing BUSINESS LOGIC, not getters/setters
```

---

## 7. SonarQube

**SonarQube** is a platform that combines PMD, Checkstyle, SpotBugs, and more into one dashboard.

### What SonarQube Provides

```
┌──────────────────────────────────────────────────────┐
│                  SonarQube Dashboard                 │
├──────────┬──────────┬──────────┬──────────┬──────────┤
│ Bugs     │ Vulner-  │ Code     │ Coverage │ Dupli-   │
│          │ abilities│ Smells   │          │ cations  │
│ 3 🔴     │ 1 🟡     │ 42 🟢    | 78% 🟡   │ 2.3%     |
├──────────┴──────────┴──────────┴──────────┴──────────┤
│                                                      │
│ Quality Gate: ✅ PASSED                              │
│                                                      │
│ Rules: PMD + Checkstyle + SpotBugs + Security rules  │
│ Languages: Java, JavaScript, Python, C#, etc.        │
│ Integration: Maven, Gradle, Jenkins, GitHub Actions  │
└──────────────────────────────────────────────────────┘
```

### Quality Gates

```
A Quality Gate is a set of conditions that code must meet:

Default Quality Gate:
┌───────────────────────────────────────────────┐
│ New code coverage ≥ 80%                       │
│ New code duplications ≤ 3%                    │
│ New bugs = 0 (reliability rating A)           │
│ New vulnerabilities = 0 (security rating A)   │
│ New code smells ≤ X (maintainability rating A)│
└───────────────────────────────────────────────┘

If any condition fails → Quality Gate = FAILED → Build can be blocked!
```

### Running SonarQube Analysis

```xml
<!-- Maven plugin -->
<plugin>
    <groupId>org.sonarsource.scanner.maven</groupId>
    <artifactId>sonar-maven-plugin</artifactId>
    <version>3.10.0.2594</version>
</plugin>
```

```bash
# Run analysis (SonarQube server must be running)
mvn sonar:sonar \
    -Dsonar.host.url=http://localhost:9000 \
    -Dsonar.token=your-auth-token
```

### SonarQube Issue Types

```
BUG 🐛
- Definitely wrong code that will cause incorrect behavior
- Example: null pointer dereference, resource leak

VULNERABILITY 🔓
- Security flaw that could be exploited
- Example: SQL injection, hard-coded credentials

CODE SMELL 🦨
- Not a bug, but makes code harder to maintain
- Example: too-long methods, duplicated code, god classes

SECURITY HOTSPOT 🔥
- Code that needs manual security review
- Example: crypto usage, regex patterns, cookie settings
```

---

## 8. Code Smells & Technical Debt

### Common Code Smells

```java
// 1. Long Method — method does too many things
public void processOrder(Order order) {
    // 200 lines of code... validate, calculate, save, email, log
    // Solution: extract into smaller methods
}

// 2. God Class — class with too many responsibilities
public class AppManager {
    // handles users, orders, payments, emails, logging...
    // Solution: Split into UserService, OrderService, etc.
}

// 3. Duplicated Code — copy-paste programming
// Same 20 lines in 5 different classes
// Solution: Extract to a shared method/class

// 4. Long Parameter List
public void createUser(String name, String email, int age, 
    String address, String city, String state, String zip, String phone) { }
// Solution: Use a parameter object
public void createUser(UserDTO dto) { }

// 5. Feature Envy — method uses another class's data more than its own
public class OrderPrinter {
    public void print(Order order) {
        // Uses order.getCustomer().getName(), order.getCustomer().getAddress()...
        // This method should probably be in the Order or Customer class
    }
}

// 6. Magic Numbers
if (status == 3) { ... }          // What does 3 mean?!
// Solution:
if (status == Status.APPROVED) { ... }

// 7. Dead Code — unreachable or unused code
public void oldMethod() { }       // Never called anywhere
// Solution: Delete it! Version control has the history.
```

### Technical Debt

```
Technical Debt = the cost of maintaining bad code

Like financial debt:
- Taking shortcuts NOW (borrowing)
- Paying interest LATER (bugs, slow development, confusion)

SonarQube measures it:
"Technical Debt: 5 days"
= It would take ~5 days of developer time to fix all code smells

Types:
1. Deliberate — "We know this is messy, but we need to ship"
2. Accidental — "We didn't know better at the time"
3. Bit rot — Code that was good but degraded over time

Managing technical debt:
- Track it (SonarQube dashboard)
- Budget time to pay it down (e.g., 20% of each sprint)
- Don't let new code add more debt (Quality Gates)
- Refactor as you go ("Boy Scout Rule" — leave code cleaner than you found it)
```

---

## 9. Code Review & Pull Requests

### Code Review Checklist

```
✅ Functionality
  □ Does the code do what it's supposed to?
  □ Are edge cases handled?
  □ Are error scenarios handled?

✅ Readability
  □ Are variable/method names clear and descriptive?
  □ Is the code easy to follow?
  □ Are complex parts commented?

✅ Design
  □ Does it follow SOLID principles?
  □ Is there unnecessary duplication?
  □ Are classes/methods single-purpose?

✅ Testing
  □ Are there unit tests?
  □ Do tests cover happy path AND edge cases?
  □ Is coverage adequate?

✅ Security
  □ No hard-coded secrets/passwords?
  □ Input validation present?
  □ No SQL injection vulnerabilities?

✅ Performance
  □ No unnecessary database calls in loops?
  □ Appropriate data structures used?
  □ No memory leaks?
```

### Pull Request Best Practices

```
Writing a Good PR:
1. SMALL — 200-400 lines max. Large PRs don't get good reviews.
2. SINGLE PURPOSE — one feature or bug fix per PR
3. DESCRIPTIVE TITLE — "Add user email validation" not "Fix stuff"
4. DESCRIPTION — What, Why, How, and any concerns
5. SCREENSHOTS — for UI changes
6. LINKED ISSUE — reference the ticket/issue number

PR Template Example:
─────────────────────────────────────
## What
Added email validation to user registration

## Why
Users could register with invalid emails (Bug #123)

## How
- Added EmailValidator utility class
- Added validation in UserService.createUser()
- Added unit tests for edge cases

## Testing
- 15 unit tests added (100% coverage on new code)
- Manually tested with invalid emails

## Checklist
- [x] Tests pass
- [x] No new warnings
- [x] Documentation updated
─────────────────────────────────────

Reviewing a PR:
1. Be KIND — critique the code, not the person
2. Be SPECIFIC — "line 42: this could NPE if user is null" not "fix this"
3. SUGGEST — offer alternatives, not just criticism
4. PRAISE — acknowledge good solutions
5. ASK — "Why did you choose X over Y?" instead of "X is wrong"
```

---

## 10. CI — Continuous Integration

### What Is CI?

```
Continuous Integration = automatically build and test code on every commit

Developer workflow WITHOUT CI:
1. Write code
2. Push to Git
3. Hope it works
4. Find out days later it broke something
5. 😱

Developer workflow WITH CI:
1. Write code
2. Push to Git
3. CI server automatically:
   a. Pulls the code
   b. Compiles it
   c. Runs all tests
   d. Runs code quality checks (PMD, Checkstyle, SpotBugs)
   e. Measures code coverage (JaCoCo)
   f. Reports to SonarQube
   g. ✅ Passes or ❌ Fails
4. You know in minutes if something broke!
```

### CI Pipeline Example (GitHub Actions)

```yaml
# .github/workflows/ci.yml
name: Java CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Set up JDK 21
      uses: actions/setup-java@v4
      with:
        java-version: '21'
        distribution: 'temurin'

    - name: Build with Maven
      run: mvn clean verify

    - name: Run Tests
      run: mvn test

    - name: Check Code Style
      run: mvn checkstyle:check

    - name: Run PMD
      run: mvn pmd:check

    - name: Run SpotBugs
      run: mvn spotbugs:check

    - name: Generate Coverage Report
      run: mvn jacoco:report

    - name: SonarQube Analysis
      run: mvn sonar:sonar -Dsonar.token=${{ secrets.SONAR_TOKEN }}
```

### CI Best Practices

```
1. Run CI on EVERY push and pull request
2. Keep builds FAST (< 10 minutes)
3. Fix broken builds IMMEDIATELY (top priority!)
4. Never merge a PR with failing CI
5. Automate everything: tests, quality checks, deployment
6. Use Quality Gates — block merges if quality drops
7. Track metrics over time — coverage trends, debt trends
```

---

### Tool Comparison Summary

| Tool | Analyzes | Finds | Input |
|---|---|---|---|
| **PMD** | Source code | Bad practices, complexity, unused code | .java files |
| **Checkstyle** | Source code | Style violations, formatting | .java files |
| **SpotBugs** | Bytecode | Real bugs, null pointers, concurrency | .class files |
| **JaCoCo** | Runtime | Code coverage metrics | Test execution |
| **SonarQube** | All of above | Everything + dashboard + trends | All |

```
Recommended setup for a project:
1. Checkstyle — consistent code style across team
2. PMD — catch bad practices early
3. SpotBugs — find real bugs
4. JaCoCo — ensure adequate test coverage
5. SonarQube — unified dashboard + quality gates
6. CI pipeline — run everything automatically on every commit
```

---

## SonarLint — Get SonarQube Feedback Right in Your IDE

### What is SonarLint?

**SonarLint** is a free IDE plugin (available for IntelliJ, VS Code, Eclipse) that gives you **real-time code quality feedback** as you type — just like how a spell-checker underlines typos. It catches bugs, code smells, security vulnerabilities, and more, BEFORE you even commit your code.

Think of SonarQube as the "server-side report card" and SonarLint as the "in-class tutor" — SonarLint catches issues while you code, SonarQube catches them in the CI pipeline.

### How to Set It Up

```
IntelliJ IDEA:
  1. File → Settings → Plugins → Search "SonarLint" → Install
  2. Restart IntelliJ
  3. SonarLint automatically starts analyzing your code!
  4. You'll see issues highlighted with yellow/red underlines

VS Code:
  1. Extensions → Search "SonarLint" → Install (publisher: SonarSource)
  2. It works immediately for Java, JavaScript, Python, etc.

Eclipse:
  1. Help → Eclipse Marketplace → Search "SonarLint" → Install
```

### Connecting SonarLint to SonarQube (Connected Mode)

By default, SonarLint uses its own built-in rules. But if your team uses a SonarQube server with **custom quality profiles**, you can connect them:

```
Connected Mode means:
  - SonarLint uses the SAME rules as your SonarQube server
  - If the team disables a rule on SonarQube, SonarLint won't show it either
  - Issues match between your IDE and the CI pipeline
  - No surprises when you push code!

Steps (IntelliJ):
  1. Settings → Tools → SonarLint → + Add SonarQube Connection
  2. Enter your SonarQube server URL and authentication token
  3. Bind your project to the SonarQube project
  4. SonarLint downloads the team's quality profile and uses those rules
```

### What SonarLint Catches (Examples)

```java
// ❌ SonarLint says: "Cognitive Complexity of this method is 18, higher than 15 allowed"
// Translation: Your method is too complicated. Break it into smaller methods.

// ❌ SonarLint says: "Remove this unused private method"
private void helperMethod() { ... }  // Never called anywhere!

// ❌ SonarLint says: "Use try-with-resources instead"
FileInputStream fis = new FileInputStream("file.txt");
// ... use fis ...
fis.close();  // What if an exception occurs before this line? Resource leak!

// ✅ Fix:
try (FileInputStream fis = new FileInputStream("file.txt")) {
    // ... use fis ...
}  // Automatically closed, even if exception occurs!
```

---

## How the Tools Work Together

In a real project, these tools form a **layered defense** against bad code. Here's how they fit together: 

```
Developer writes code
        │
        ▼
  ┌─────────────────┐
  │   SonarLint      │  ← Catches issues IN YOUR IDE as you type
  │   (IDE Plugin)   │     Instant feedback. Before you even save!
  └────────┬────────┘
           │
     git commit + push
           │
           ▼
  ┌─────────────────────────────────────────────────┐
  │   CI Pipeline (GitHub Actions / Jenkins)         │
  │                                                  │
  │   Step 1: mvn checkstyle:check                  │  ← Style check
  │   Step 2: mvn pmd:check                         │  ← Bad practices
  │   Step 3: mvn spotbugs:check                    │  ← Real bugs
  │   Step 4: mvn test                              │  ← Run tests
  │   Step 5: mvn jacoco:report                     │  ← Coverage report
  │   Step 6: mvn sonar:sonar                       │  ← Send ALL results to SonarQube
  │                                                  │
  └────────┬────────────────────────────────────────┘
           │
           ▼
  ┌─────────────────┐
  │   SonarQube      │  ← Aggregates everything into a dashboard
  │   (Server)       │     Quality Gate: PASS or FAIL
  │                  │     Shows trends over time
  │   If FAIL ───────┼───► Block the merge/deployment!
  └─────────────────┘

The key: JaCoCo generates coverage data → SonarQube reads it
         PMD, Checkstyle, SpotBugs find issues → SonarQube aggregates them
         SonarLint in IDE uses same rules → No surprises in CI
```

### Maven Config for All Tools Together

```xml
<build>
    <plugins>
        <!-- JaCoCo — must run BEFORE tests to instrument code -->
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <executions>
                <execution>
                    <goals><goal>prepare-agent</goal></goals>
                </execution>
                <execution>
                    <id>report</id>
                    <phase>test</phase>
                    <goals><goal>report</goal></goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>

<!-- Then in CI, run: mvn clean verify sonar:sonar -->
<!-- This runs tests → generates JaCoCo report → sends everything to SonarQube -->
```

---

## Reverse Engineering — Understanding Existing Code

### What is Reverse Engineering?

**Reverse engineering** in software means taking an existing system (that you didn't write) and figuring out how it works — its structure, designs, and purpose. Think of it like being handed a finished puzzle and needing to understand the picture AND how the pieces fit together.

This is a crucial skill because in the real world, you'll spend more time reading and understanding existing code than writing new code.

### Why Do We Need It?

```
Common scenarios:
  1. You join a new team → need to understand a 500,000-line codebase
  2. Legacy system with no documentation → need to figure out how it works
  3. Third-party library → need to understand its internals to debug an issue
  4. System migration → need to understand the old system to build the new one
  5. Security analysis → need to find vulnerabilities in existing software
```

### Steps for Reverse Engineering a Java Codebase

```
Step 1: GET THE BIG PICTURE
  - Read the README, wiki, architecture docs (if any exist)
  - Look at the project structure (module names, package names)
  - Find the main() method or entry point
  - Identify the major frameworks used (Spring Boot? Hibernate? etc.)

Step 2: UNDERSTAND THE ARCHITECTURE
  - Draw a diagram of the main modules/packages and their dependencies
  - Identify the layers: Controller → Service → Repository → Database
  - Find configuration files (application.properties, pom.xml)
  - Map out the database schema (tables, relationships)

Step 3: TRACE THE CODE FLOW
  - Pick a feature (e.g., "user login") and trace it end-to-end
  - Start from the API endpoint/controller → follow the method calls
  - Use your IDE's "Go to Definition" (Ctrl+Click) and "Find Usages"
  - Use the debugger to step through the code with real data

Step 4: DOCUMENT WHAT YOU FIND
  - Create or update architecture diagrams
  - Add comments to confusing code sections
  - Write down assumptions and verify them with the team
```

### Tools for Reverse Engineering

```
IDE Features:
  - "Find Usages" (Alt+F7 in IntelliJ) — who calls this method?
  - "Call Hierarchy" (Ctrl+Alt+H) — what's the call chain?
  - "Type Hierarchy" (Ctrl+H) — class inheritance tree
  - "Structure" view — overview of a class's methods and fields
  - Debugger — step through code to see actual runtime behavior

Diagram Generation:
  - IntelliJ UML Diagrams — auto-generate class diagrams from code
  - PlantUML — write text, get diagrams
  - draw.io — manual diagramming (free)

Database Reverse Engineering:
  - DBeaver — free tool to visualize database schema, generate ER diagrams
  - Oracle SQL Developer — view tables, relationships, generate DDL
  - SchemaSpy — auto-generates database documentation from a live database

Code Analysis Tools:
  - SonarQube — shows complexity hotspots, code smells, dependencies
  - JDepend — analyzes package dependencies, finds circular dependencies
  - Structure101 — visualizes architecture and finds violations
  
Decompilers (for .class files when you don't have source):
  - JD-GUI — open .jar files and see decompiled Java source
  - IntelliJ built-in decompiler — just open any .class file!
  - CFR — command-line decompiler with modern Java support
```

### Reverse Engineering a Database

```sql
-- Get all tables in a schema:
SELECT table_name FROM user_tables ORDER BY table_name;

-- Get columns for a specific table:
DESCRIBE employees;

-- Find all foreign key relationships:
SELECT a.table_name, a.column_name, 
       c.table_name AS referenced_table,
       c.column_name AS referenced_column
FROM user_cons_columns a
JOIN user_constraints b ON a.constraint_name = b.constraint_name
JOIN user_cons_columns c ON b.r_constraint_name = c.constraint_name
WHERE b.constraint_type = 'R'
ORDER BY a.table_name;

-- This gives you the relationships between tables!
-- Now you can draw an ER diagram.
```

```
Tips for Reverse Engineering:
  ✅ Start with the entry point and follow the happy path first
  ✅ Use the debugger — it's faster than reading code
  ✅ Draw diagrams as you go (even rough ones on paper)
  ✅ Ask the team! (if possible) — save hours of guessing
  ✅ Look at tests — they often reveal how the code is meant to be used
  ❌ Don't try to understand EVERYTHING at once — focus on one feature at a time
  ❌ Don't skip reading config files (they explain a LOT about the system)
```

---

*Previous: [11-Logging.md](11-Logging.md)*
*This is the final file in the series!*

---

## 📚 Full Series Index

1. [01-Oracle-SQL-Fundamentals.md](01-Oracle-SQL-Fundamentals.md)
2. [02-Oracle-PLSQL.md](02-Oracle-PLSQL.md)
3. [03-Java-Basics.md](03-Java-Basics.md)
4. [04-Java-OOP.md](04-Java-OOP.md)
5. [05-Java-Collections.md](05-Java-Collections.md)
6. [06-Java-Advanced.md](06-Java-Advanced.md)
7. [07-Java-JDBC-Networking.md](07-Java-JDBC-Networking.md)
8. [08-Design-Patterns-SOLID.md](08-Design-Patterns-SOLID.md)
9. [09-JUnit-Testing.md](09-JUnit-Testing.md)
10. [10-Mockito-Mocking.md](10-Mockito-Mocking.md)
11. [11-Logging.md](11-Logging.md)
12. [12-Code-Quality-Analysis.md](12-Code-Quality-Analysis.md) ← You are here
