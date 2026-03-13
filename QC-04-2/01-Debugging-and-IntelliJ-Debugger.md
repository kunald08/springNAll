# Debugging and IntelliJ IDEA Debugger

## Table of Contents
1. [What Is Debugging?](#1-what-is-debugging)
2. [Why Debugging Is Important](#2-why-debugging-is-important)
3. [Types of Errors](#3-types-of-errors)
4. [What Is a Debugger?](#4-what-is-a-debugger)
5. [Getting Started with IntelliJ IDEA Debugger](#5-getting-started-with-intellij-idea-debugger)
6. [Setting Breakpoints](#6-setting-breakpoints)
7. [Step Over, Step Into, Step Out](#7-step-over-step-into-step-out)
8. [Watch Variables and Evaluate Expression](#8-watch-variables-and-evaluate-expression)
9. [Debug Console](#9-debug-console)
10. [Simple Debugging Workflow](#10-simple-debugging-workflow)
11. [Best Practices](#11-best-practices)

---

## 1. What Is Debugging?

Debugging means finding and fixing problems in a program.

A problem in software is often called a **bug**.

When your program gives the wrong output, crashes, or behaves in a strange way, you debug it to understand:

- what happened
- where it happened
- why it happened
- how to fix it

Debugging is not only for experts. It is a basic skill for every developer.

---

## 2. Why Debugging Is Important

Writing code is only one part of programming. Real learning happens when you understand why code fails.

Debugging helps you:

- understand program flow
- inspect variable values
- catch logic mistakes
- fix issues faster
- become confident with large codebases

If you only guess and keep changing code randomly, you waste time. A debugger gives you facts.

---

## 3. Types of Errors

### 3.1 Syntax Errors

These happen when the code does not follow language rules.

Example:

```java
System.out.println("Hello"
```

The compiler usually catches syntax errors before the program runs.

### 3.2 Runtime Errors

These happen while the program is running.

Example:

```java
int a = 10 / 0;
```

This causes an exception.

### 3.3 Logical Errors

These are the most dangerous because the program runs, but gives the wrong result.

Example:

```java
int total = price - quantity;
```

Maybe you meant `price * quantity`.

Debugging is especially useful for logical errors.

---

## 4. What Is a Debugger?

A debugger is a tool that lets you run a program in a controlled way.

Instead of running the full program at normal speed, a debugger lets you:

- pause execution
- run one line at a time
- see current variable values
- check method calls
- test expressions

Think of it like pausing a movie frame by frame to understand what is happening.

---

## 5. Getting Started with IntelliJ IDEA Debugger

IntelliJ IDEA has a built-in debugger for Java and other languages.

### Basic Steps

1. Open your project in IntelliJ IDEA.
2. Open the class with the code you want to test.
3. Click in the left gutter next to a line number to add a breakpoint.
4. Run the program in debug mode.

You can start debug mode by:

- clicking the bug icon
- right-clicking the class and choosing `Debug`
- pressing `Shift + F9`

When the program reaches a breakpoint, IntelliJ pauses execution and shows the debug window.

---

## 6. Setting Breakpoints

A breakpoint tells the debugger: "Pause the program here."

### Why Use Breakpoints?

Breakpoints help you stop at the exact place where you want to inspect the program.

### Example

```java
public class Demo {
    public static void main(String[] args) {
        int x = 10;
        int y = 5;
        int result = x + y;
        System.out.println(result);
    }
}
```

If you put a breakpoint on `int result = x + y;`, the program pauses before that line runs.

At that moment you can inspect:

- `x`
- `y`
- current call stack

### Common Breakpoint Uses

- before a loop
- inside a loop
- before a method call
- before a condition
- near the line where an exception happens

### Conditional Breakpoints

You can tell IntelliJ to stop only when a condition is true.

Example:

- stop only when `i == 50`

This is useful in loops where you do not want to stop on every iteration.

---

## 7. Step Over, Step Into, Step Out

These are the most important debugger actions.

### 7.1 Step Over

Shortcut: `F8`

Runs the current line and moves to the next line in the same method.

If the line calls another method, IntelliJ executes that method without opening it.

Use Step Over when:

- you trust the called method
- you only care about the current method

### 7.2 Step Into

Shortcut: `F7`

Moves into the method being called on the current line.

Use Step Into when:

- you want to inspect method internals
- you think the bug is inside the called method

### 7.3 Step Out

Shortcut: `Shift + F8`

Runs the rest of the current method and returns to the calling method.

Use Step Out when:

- you entered a method by mistake
- you have already checked the method and want to go back

### Example

```java
public class Test {
    public static void main(String[] args) {
        int value = add(2, 3);
        System.out.println(value);
    }

    static int add(int a, int b) {
        return a + b;
    }
}
```

If execution is on `int value = add(2, 3);`

- `Step Over` gives result directly
- `Step Into` opens `add()`
- `Step Out` returns from `add()` to `main()`

---

## 8. Watch Variables and Evaluate Expression

### Watch Variables

A watch lets you track a variable or expression while debugging.

Examples:

- `count`
- `user.getName()`
- `price * quantity`

This helps when values change often.

### Evaluate Expression

In IntelliJ, you can test an expression during debugging without changing code.

Example:

```java
items.size()
```

or

```java
name.toUpperCase()
```

This is useful for checking logic quickly.

---

## 9. Debug Console

The debug console shows output and can also allow expression evaluation depending on the environment.

You may use it to:

- view printed output
- inspect exceptions
- test expressions
- understand flow during runtime

The console is very useful when combined with breakpoints.

Do not confuse debugging with only printing values using `System.out.println()`. Print statements help, but debugger tools give much more control.

---

## 10. Simple Debugging Workflow

Use this basic process:

1. Reproduce the problem.
2. Identify where the problem probably starts.
3. Put breakpoints near that area.
4. Run in debug mode.
5. Check variable values line by line.
6. Follow method calls using Step Into or Step Over.
7. Find where values become wrong.
8. Fix the logic.
9. Run again to verify the fix.

---

## 11. Best Practices

- Do not guess. Inspect facts.
- Use breakpoints near suspicious code, not everywhere.
- Check inputs first, then outputs.
- Debug one issue at a time.
- Understand the root cause before fixing.
- Remove unnecessary breakpoints after use.
- Learn shortcut keys to save time.

---

## Final Summary

Debugging is the process of finding and fixing software problems. IntelliJ IDEA makes debugging easier with breakpoints, step controls, watches, and the debug console.

The main skills to practice are:

- setting breakpoints
- reading variable values
- using Step Over, Step Into, and Step Out
- checking program flow carefully

If you become good at debugging, you become a much stronger developer.
