# Debugging and IntelliJ IDEA Debugger — Interview Questions & Answers

> **How to use this:** Try answering first, then read the answer. These answers are written in an interview-ready style: clear, direct, and professional.

---

## 1. What is debugging?

**Answer:**
Debugging is the process of identifying, analyzing, and fixing problems in a program. These problems may be syntax errors, runtime errors, or logical errors. In practice, debugging is important because it helps us understand exactly where the application is failing and why.

---

## 2. What are the common types of errors in programming?

**Answer:**
The three common types are:

- **Syntax errors**: mistakes in code structure, caught by compiler or interpreter
- **Runtime errors**: errors that occur while the program is running, like divide by zero or null pointer
- **Logical errors**: the program runs, but gives incorrect output

From a debugging perspective, logical errors are often the hardest because the application may not crash, but the behavior is still wrong.

---

## 3. What is a debugger?

**Answer:**
A debugger is a tool that allows us to pause program execution, inspect variable values, and run code step by step. It helps us understand the internal state of the application at a specific point in time instead of guessing based on output alone.

---

## 4. What is a breakpoint?

**Answer:**
A breakpoint is a marker we place on a line of code to pause execution at that point during debugging. Once execution stops, we can inspect variables, check method calls, and verify whether the program state is what we expect.

---

## 5. What is the use of breakpoints in real projects?

**Answer:**
Breakpoints are useful when we want to isolate a problem area. For example, if a calculation gives the wrong result, I can place breakpoints before and after the calculation and inspect the variable values. This helps me identify the exact line where the data becomes incorrect.

---

## 6. What is the difference between Step Over, Step Into, and Step Out?

**Answer:**
- **Step Over** executes the current line and moves to the next line in the same method
- **Step Into** goes inside the called method so we can debug its internal logic
- **Step Out** completes the current method and returns to the caller

In practice, I use Step Over when I trust the called method, Step Into when I suspect the issue is inside that method, and Step Out when I no longer need to debug the current method.

---

## 7. What is the difference between debugging and using print statements?

**Answer:**
Print statements can show values, but debugging is much more powerful. With a debugger, I can pause execution, inspect all variables, evaluate expressions, and move line by line through the code. So print statements are basic troubleshooting, but a debugger gives much better control and visibility.

---

## 8. What are watch variables?

**Answer:**
Watch variables are variables or expressions that we track during a debugging session. They help us monitor how values change as execution moves through the code. This is especially useful in loops or complex business logic.

---

## 9. What is the Debug Console in IntelliJ IDEA?

**Answer:**
The Debug Console in IntelliJ IDEA is the area where we can view runtime output and inspect information during a debug session. It helps us analyze exceptions, printed logs, and sometimes evaluate expressions depending on the context.

---

## 10. How do you usually approach a debugging problem?

**Answer:**
My approach is structured:

1. Reproduce the issue consistently
2. Identify the likely code area
3. Add breakpoints around that logic
4. Run in debug mode
5. Inspect variables and method flow
6. Find the root cause
7. Fix the issue and retest

I try to avoid random code changes. I prefer to confirm the root cause first.

---

## 11. What is a conditional breakpoint?

**Answer:**
A conditional breakpoint is a breakpoint that only pauses execution when a specified condition is true. For example, inside a loop, I may want the debugger to stop only when `i == 50`. This is useful when normal breakpoints would stop too often.

---

## 12. Why is debugging considered an important developer skill?

**Answer:**
Debugging is important because writing code is only part of software development. Real projects always involve unexpected issues, integration problems, and edge cases. A developer who can debug efficiently can identify root causes faster, reduce downtime, and improve code quality.
