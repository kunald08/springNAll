# Code Quality & Static Analysis — Interview Questions & Answers

> **How to use this:** Answers are written exactly how you should speak in an interview — clear, confident, and concise.

---

## 1. What is static code analysis? Why is it important?

**Answer:**
Static code analysis examines source code **without running it** — tools scan the code for bugs, vulnerabilities, style violations, and code smells automatically.

Why it matters:
- **Catches bugs early** — finds null pointer risks, resource leaks, and security issues before testing
- **Enforces consistency** — every developer follows the same style and patterns
- **Reduces code review effort** — automated checks handle the repetitive stuff so reviewers focus on logic
- **Improves maintainability** — prevents technical debt from accumulating

Common tools: **PMD** (bad practices), **Checkstyle** (formatting/style), **SpotBugs** (actual bugs), **SonarQube** (all-in-one dashboard).

---

## 2. What is SonarQube?

**Answer:**
SonarQube is a **continuous code quality platform** that aggregates results from multiple static analysis tools into a single dashboard.

It tracks:
- **Bugs** — code that is likely wrong
- **Vulnerabilities** — security risks (SQL injection, XSS, etc.)
- **Code Smells** — maintainability issues (long methods, duplicated code)
- **Coverage** — percentage of code covered by tests
- **Duplications** — copy-pasted code blocks
- **Technical Debt** — estimated time to fix all issues

SonarQube integrates with CI/CD pipelines through a **Quality Gate** — if code doesn't meet the threshold (e.g., 80% coverage, zero critical bugs), the build fails. This prevents bad code from reaching production.

---

## 3. What is PMD? What does it check?

**Answer:**
PMD is a static analysis tool that finds **bad coding practices and potential bugs**:

- **Empty catch blocks** — silently swallowing exceptions
- **Unused variables and imports** — dead code
- **Empty if statements** — logic that does nothing
- **Overly complex methods** — high cyclomatic complexity
- **God classes** — classes that do too much
- **Unnecessary object creation** — performance waste

PMD uses configurable **rulesets**, so I can enable/disable specific checks. It also has **CPD** (Copy-Paste Detector) for finding duplicated code blocks across the codebase.

---

## 4. What is Checkstyle?

**Answer:**
Checkstyle enforces **coding standards and formatting rules** — it's about style and consistency, not bugs.

It checks:
- Naming conventions (camelCase for variables, PascalCase for classes)
- Indentation and whitespace
- Javadoc presence and completeness
- Line length limits
- Import ordering
- Brace placement (same line vs next line)
- File header/copyright comments

Popular presets: **Sun Code Conventions** and **Google Java Style Guide**. Teams typically start with a preset and customize it. The key benefit is that every developer's code looks the same, making code reviews faster.

---

## 5. What is SpotBugs?

**Answer:**
SpotBugs (successor to FindBugs) analyzes **compiled bytecode** to find real bugs:

- **Null pointer dereferences** — using a variable that could be null
- **Infinite recursive loops**
- **Resource leaks** — streams/connections not closed
- **Incorrect equals/hashCode** — violating the contract
- **Synchronization issues** — race conditions, deadlocks
- **SQL injection** risks

Since it analyzes bytecode (not source), it catches issues that source-level tools miss. Bugs are categorized by severity: **Critical**, **Major**, **Normal**, **Minor**.

---

## 6. What is code coverage? What tools measure it?

**Answer:**
Code coverage measures what **percentage of code** is executed during tests:

- **Line coverage** — which lines ran
- **Branch coverage** — which if/else branches ran
- **Method coverage** — which methods were called

**JaCoCo** is the standard Java coverage tool. It instruments bytecode and generates reports:
```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <executions>
        <execution><goals><goal>prepare-agent</goal></goals></execution>
        <execution><id>report</id>
            <phase>verify</phase>
            <goals><goal>report</goal></goals>
        </execution>
    </executions>
</plugin>
```

A typical target is **80% line coverage**. But coverage alone doesn't guarantee quality — I can have 100% coverage with meaningless assertions. Good tests verify behavior, not just touch lines.

---

## 7. What are code smells?

**Answer:**
Code smells are **not bugs** — the code works — but they indicate poor design that makes maintenance harder:

| Code Smell | What It Means |
|-----------|---------------|
| **Long Method** | Method does too much; should be split |
| **God Class** | Class has too many responsibilities |
| **Duplicate Code** | Same logic in multiple places |
| **Feature Envy** | Method uses another class's data more than its own |
| **Primitive Obsession** | Using primitives instead of small objects (e.g., `String email` vs `Email email`) |
| **Long Parameter List** | Too many method parameters; use a parameter object |
| **Dead Code** | Unused methods, variables, or imports |
| **Magic Numbers** | Hardcoded values without explanation |

I address code smells through **refactoring** — restructuring code without changing its behavior.

---

## 8. What is technical debt?

**Answer:**
Technical debt is the **accumulated cost of shortcuts** in code — quick fixes, skipped tests, poor design decisions that make future changes harder and slower.

Types:
- **Intentional** — "We'll ship fast now and refactor later" (sometimes acceptable with a plan)
- **Unintentional** — bad design that wasn't recognized at the time

SonarQube measures technical debt in **estimated time to fix** (e.g., "3 days of debt"). The key is managing it — some debt is acceptable, but if it grows unchecked, development slows to a crawl.

I address it by: allocating sprint time for refactoring, fixing debt alongside feature work, and using quality gates in CI to prevent new debt.

---

## 9. How do these tools fit into CI/CD?

**Answer:**
In a typical CI pipeline:

```
Code Push → Build → Unit Tests → JaCoCo Coverage Report
         → PMD / Checkstyle / SpotBugs static analysis
         → SonarQube analysis (aggregates everything)
         → Quality Gate check: PASS or FAIL
         → Deploy (only if passed)
```

All these tools run as **Maven/Gradle plugins** and report to SonarQube. If the quality gate fails (e.g., coverage drops below 80%, or new critical bugs are introduced), the pipeline stops and the developer gets immediate feedback.

This is "shift left" — catching quality issues as early as possible in the development cycle.

---

## 10. What is a code review? What do you look for?

**Answer:**
A code review is when another developer examines my code before it's merged. I look for:

1. **Correctness** — does the logic actually solve the problem?
2. **Edge cases** — null inputs, empty collections, boundary values
3. **Readability** — clear naming, small methods, no magic numbers
4. **Test coverage** — are there tests? Do they test the right things?
5. **Security** — input validation, no hardcoded credentials, proper auth checks
6. **Performance** — unnecessary loops, N+1 queries, large object creation
7. **SOLID principles** — single responsibility, proper abstractions
8. **Error handling** — are exceptions handled appropriately?

Good code reviews are respectful, focused on the code (not the person), and aim to share knowledge across the team.

---

*Back to: [12-Code-Quality.md](../12-Code-Quality.md)*
