# Java Basics — From Zero to Expert

## Table of Contents
1. [What Is Java?](#1-what-is-java)
2. [JVM, JRE, JDK — The Java Ecosystem](#2-jvm-jre-jdk)
3. [Setup JDK & IntelliJ IDEA](#3-setup)
4. [Your First Java Program](#4-your-first-java-program)
5. [Primitive Data Types](#5-primitive-data-types)
6. [Reference Variables](#6-reference-variables)
7. [Operators](#7-operators)
8. [Control Flow — Conditional Statements](#8-conditional-statements)
9. [Control Flow — Loops](#9-loops)
10. [Arrays](#10-arrays)
11. [Commenting & Documentation](#11-commenting)
12. [Packages & Imports](#12-packages-and-imports)
13. [Methods — Declaration, Parameters, Return Types](#13-methods)
14. [Method Visibility & Scope](#14-method-visibility-and-scope)
15. [The Stack & The Heap](#15-the-stack-and-the-heap)
16. [Method Recursion](#16-recursion)
17. [Casting](#17-casting)
18. [Value Types vs Reference Types](#18-value-vs-reference-types)
19. [Strings](#19-strings)
20. [Wrapper Classes](#20-wrapper-classes)
21. [Debugging in IntelliJ](#21-debugging)

---

## 1. What Is Java?

Java is a **high-level, object-oriented, platform-independent** programming language created by **James Gosling** at Sun Microsystems in **1995** (now owned by Oracle).

### Key Features

```
┌────────────────────────────────────────────────────────────┐
│  "Write Once, Run Anywhere" (WORA)                         │
│                                                            │
│  Java Source (.java)                                       │
│       │                                                    │
│       ▼  javac (compiler)                                  │
│  Bytecode (.class)  ← Platform INDEPENDENT                 │
│       │                                                    │
│       ▼  JVM (Java Virtual Machine)                        │
│  Machine Code  ← Platform SPECIFIC                         │
│                                                            │
│  Same .class file runs on Windows, Mac, Linux!             │
└────────────────────────────────────────────────────────────┘
```

**Why Java?**
- **Platform Independent** — Bytecode runs on any OS with a JVM.
- **Object-Oriented** — Everything is in classes and objects.
- **Strongly Typed** — You must declare variable types; no loose typing.
- **Memory Managed** — Garbage collector handles memory; no manual `malloc`/`free`.
- **Multithreaded** — Built-in support for concurrent programming.
- **Secure** — No pointers, bytecode verification, sandboxing.
- **Massive Ecosystem** — Libraries, frameworks, community.

---

## 2. JVM, JRE, JDK

This is one of the most important concepts. Let's break it down layer by layer.

```
┌──────────────────────────────────────────────────────────┐
│                          JDK                             │
│  (Java Development Kit)                                  │
│  Everything you need to DEVELOP Java programs            │
│                                                          │
│  ┌───────────────────────────────────────────────────┐   │
│  │                      JRE                          │   │
│  │  (Java Runtime Environment)                       │   │
│  │  Everything you need to RUN Java programs         │   │
│  │                                                   │   │
│  │  ┌────────────────────────────────────────────┐   │   │
│  │  │                  JVM                       │   │   │
│  │  │  (Java Virtual Machine)                    │   │   │
│  │  │  The engine that actually executes bytecode│   │   │
│  │  └────────────────────────────────────────────┘   │   │
│  │                                                   │   │
│  │  + Java Class Libraries (rt.jar)                  │   │
│  │  + java command (to run programs)                 │   │
│  └───────────────────────────────────────────────────┘   │
│                                                          │
│  + javac (compiler)                                      │
│  + javadoc (documentation generator)                     │
│  + jdb (debugger)                                        │
│  + jar (archiver)                                        │
│  + jshell (interactive REPL - Java 9+)                   │
└──────────────────────────────────────────────────────────┘
```

### JVM Deep Dive — How It Works Under the Hood

```
                     Your .class bytecode
                            │
                            ▼
┌───────────────────────────────────────────────────────────┐
│                        JVM                                │
│                                                           │
│  1. CLASS LOADER SUBSYSTEM                                │
│     ┌─────────────────────────────────────────────────┐   │
│     │  Loading → Linking → Initialization             │   │
│     │                                                 │   │
│     │  Loading: Reads .class file from disk           │   │
│     │  Linking:                                       │   │
│     │    - Verify: Checks bytecode is valid/safe      │   │
│     │    - Prepare: Allocates memory for static vars  │   │
│     │    - Resolve: Replaces symbolic refs with actual│   │
│     │ Initialization: Runs static blocks & assignments│   │
│     └─────────────────────────────────────────────────┘   │
│                                                           │
│  2. RUNTIME DATA AREAS (Memory)                           │
│     ┌───────────┐ ┌──────────┐ ┌─────────────────────┐    │
│     │  Method   │ │   Heap   │ │  Stack (per thread) │    │
│     │  Area     │ │          │ │  ┌─────────────┐    │    │
│     │ (class    │ │ (objects │ │  │ Frame 3     │    │    │
│     │ metadata, │ │  live    │ │  │ Frame 2     │    │    │
│     │ static    │ │  here)   │ │  │ Frame 1     │    │    │
│     │ vars,     │ │          │ │  └─────────────┘    │    │
│     │ constant  │ │          │ │                     │    │
│     │ pool)     │ │          │ │                     │    │
│     └───────────┘ └──────────┘ └─────────────────────┘    │
│     ┌──────────────────┐  ┌──────────────────────────┐    │
│     │  PC Register     │  │  Native Method Stack     │    │
│     │ (per thread,     │  │  (for native C/C++ code) │    │
│     │  current instr.) │  │                          │    │
│     └──────────────────┘  └──────────────────────────┘    │
│                                                           │
│  3. EXECUTION ENGINE                                      │
│     ┌───────────────────────────────────────────────────┐ │
│     │  Interpreter: Reads and executes bytecode         │ │
│     │               line by line (slow but starts fast) │ │
│     │                                                   │ │
│     │  JIT Compiler: Compiles hot methods to native     │ │
│     │                machine code (fast execution)      │ │
│     │                                                   │ │
│     │  Garbage Collector: Automatically frees memory    │ │
│     │                     of unused objects             │ │
│     └───────────────────────────────────────────────────┘ │
│                                                           │
│  4. NATIVE METHOD INTERFACE (JNI)                         │
│     Lets Java call C/C++ code and vice versa              │
└───────────────────────────────────────────────────────────┘
```

### How Java Code Runs — Step by Step

```
Step 1: You write code
  HelloWorld.java

Step 2: Compile
  $ javac HelloWorld.java
  Creates: HelloWorld.class (bytecode)

Step 3: Run
  $ java HelloWorld
  
  Internally:
  a) JVM starts up
  b) Class Loader loads HelloWorld.class
  c) Bytecode Verifier checks it's safe
  d) Interpreter starts executing main() method
  e) JIT compiler kicks in for frequently-run code (makes it as fast as C++)
  f) Program runs, GC cleans up memory
  g) JVM shuts down
```

---

## 3. Setup

### Installing JDK

```bash
# Check if Java is installed
java -version
javac -version

# On Ubuntu/Debian
sudo apt update
sudo apt install openjdk-21-jdk

# On macOS (using Homebrew)
brew install openjdk@21

# On Windows: Download from https://adoptium.net/ or https://www.oracle.com/java/
# Run installer, add to PATH

# Set JAVA_HOME environment variable
# Linux/Mac: Add to ~/.bashrc or ~/.zshrc
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH

# Verify
java -version
# Output: openjdk version "21.0.x" ...
```

### Setting Up IntelliJ IDEA
1. Download IntelliJ IDEA from https://www.jetbrains.com/idea/ (Community Edition is free).
2. Install and launch.
3. Create New Project → Select JDK → Name your project.
4. IntelliJ creates a project structure:

```
my-project/
├── .idea/              ← IntelliJ project settings
├── src/                ← Your source code goes here
│   └── Main.java
├── out/                ← Compiled .class files
└── my-project.iml      ← Module file
```

---

## 4. Your First Java Program

```java
// File: HelloWorld.java
// Every Java file must have a class with the SAME NAME as the file!

public class HelloWorld {
    // main method — the entry point of every Java program
    // The JVM looks for exactly this signature to start execution
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

### Breaking It Down

```java
public          // Access modifier: visible to all classes
class           // Keyword to define a class
HelloWorld      // Class name (must match filename: HelloWorld.java)
{               // Start of class body

    public      // Accessible from anywhere
    static      // Belongs to the class, not an instance (JVM calls it without creating an object)
    void        // Returns nothing
    main        // Method name (JVM looks for exactly "main")
    (String[] args)  // Parameter: array of command-line arguments
    {           // Start of method body
    
        System          // A class in java.lang package
        .out            // A static field — the standard output stream (PrintStream)
        .println()      // A method that prints a line and adds a newline
        
    }           // End of method body
}               // End of class body
```

### Compile and Run from Command Line

```bash
# Step 1: Write the file
echo 'public class HelloWorld { public static void main(String[] args) { System.out.println("Hello!"); } }' > HelloWorld.java

# Step 2: Compile
javac HelloWorld.java
# Creates HelloWorld.class

# Step 3: Run
java HelloWorld
# Output: Hello!

# With command-line arguments
java HelloWorld arg1 arg2 arg3
# args[0] = "arg1", args[1] = "arg2", args[2] = "arg3"
```

---

## 5. Primitive Data Types

Java has **8 primitive types**. These are NOT objects — they store raw values directly in memory (on the stack).

```java
// Integer types (whole numbers)
byte    b = 127;           // 8 bits  → -128 to 127
short   s = 32767;         // 16 bits → -32,768 to 32,767
int     i = 2147483647;    // 32 bits → -2.1 billion to 2.1 billion (DEFAULT for integers)
long    l = 9223372036854775807L;  // 64 bits → huge range (note the L suffix!)

// Floating point types (decimals)
float   f = 3.14f;         // 32 bits → ~7 decimal digits precision (note the f suffix!)
double  d = 3.141592653589;// 64 bits → ~15 decimal digits precision (DEFAULT for decimals)

// Character type
char    c = 'A';           // 16 bits → single Unicode character (0 to 65,535)
char    c2 = 65;           // Also 'A' (char is secretly a number — ASCII/Unicode value)
char    c3 = '\u0041';     // Also 'A' (Unicode escape)

// Boolean type
boolean flag = true;       // 1 bit conceptually → true or false
```

### Memory Layout

```
Stack Memory (where primitives live):

Variable   │ Value        │ Bits
───────────┼──────────────┼─────
byte b     │ 01111111     │ 8
short s    │ 0111...1111  │ 16
int i      │ 0111...1111  │ 32
long l     │ 0111...1111  │ 64
float f    │ [IEEE 754]   │ 32
double d   │ [IEEE 754]   │ 64
char c     │ 0000...0041  │ 16
boolean f  │ 1            │ JVM dependent (often 32 bits for alignment)
```

### Integer Overflow — What Happens When You Exceed the Range

```java
int max = Integer.MAX_VALUE;      // 2,147,483,647
System.out.println(max);          // 2147483647
System.out.println(max + 1);     // -2147483648  ← OVERFLOW! Wraps around!

// Under the hood (binary):
// 0111 1111 1111 1111 1111 1111 1111 1111  = 2,147,483,647 (max int)
// +                                    1
// = 1000 0000 0000 0000 0000 0000 0000 0000 = -2,147,483,648 (min int!)
// The leading 1 means negative (two's complement representation)
```

### Floating Point Precision Issues

```java
System.out.println(0.1 + 0.2);        // 0.30000000000000004  ← NOT 0.3!
System.out.println(0.1 + 0.2 == 0.3); // false!

// Why? Floating point uses binary fractions (IEEE 754)
// 0.1 in binary = 0.0001100110011... (repeating, like 1/3 = 0.333... in decimal)
// So 0.1 and 0.2 can't be represented exactly in binary

// For money/precise calculations, use BigDecimal:
import java.math.BigDecimal;
BigDecimal a = new BigDecimal("0.1");
BigDecimal b = new BigDecimal("0.2");
System.out.println(a.add(b));          // 0.3 ← exact!
```

### Default Values (Only for Instance/Class Variables, NOT local variables)

```java
public class Defaults {
    static int x;          // default: 0
    static double d;       // default: 0.0
    static boolean b;      // default: false
    static char c;         // default: '\u0000' (null character)
    static String s;       // default: null (reference type)
    
    public static void main(String[] args) {
        System.out.println(x);  // 0
        
        // Local variables have NO default — must initialize before use!
        int local;
        // System.out.println(local);  // ❌ COMPILE ERROR: variable might not have been initialized
        local = 5;
        System.out.println(local);     // ✅ 5
    }
}
```

---

## 6. Reference Variables

Unlike primitives (which store the actual value), **reference variables** store a **memory address** (pointer) to an object on the **heap**.

```java
String name = "Alice";
int[] numbers = {1, 2, 3};
Object obj = new Object();

// 'name' doesn't contain "Alice" — it contains the ADDRESS where "Alice" lives on the heap
```

```
Stack (variables)              Heap (objects)
┌──────────────┐               ┌──────────────────┐
│ name: 0x1A2B │─────────────▶│  "Alice"         │
├──────────────┤               ├──────────────────┤
│ numbers: 0x3C│─────────────▶│  [1, 2, 3]       │
├──────────────┤               ├──────────────────┤
│ obj: 0x5D6E  │─────────────▶│  Object@5d6e     │
└──────────────┘               └──────────────────┘
```

### null — The "No Object" Reference

```java
String name = null;    // name points to nothing
// name.length();      // ❌ NullPointerException! Can't call methods on null!

// Always check for null before using a reference:
if (name != null) {
    System.out.println(name.length());
}
```

---

## 7. Operators

### Arithmetic Operators

```java
int a = 10, b = 3;

System.out.println(a + b);   // 13   (addition)
System.out.println(a - b);   // 7    (subtraction)
System.out.println(a * b);   // 30   (multiplication)
System.out.println(a / b);   // 3    (integer division — truncates decimal!)
System.out.println(a % b);   // 1    (modulus — remainder of 10/3)

// ⚠️ Integer division truncates!
System.out.println(10 / 3);        // 3 (not 3.33!)
System.out.println(10.0 / 3);      // 3.3333...  (if ONE operand is double, result is double)
System.out.println((double)10 / 3); // 3.3333...  (casting to double)

// Increment / Decrement
int x = 5;
x++;        // post-increment: use x, then add 1 → x is now 6
++x;        // pre-increment: add 1, then use x → x is now 7
x--;        // post-decrement: use x, then subtract 1
--x;        // pre-decrement

// The difference matters in expressions:
int a2 = 5;
int b2 = a2++;   // b2 = 5 (used old value), a2 = 6
int c2 = ++a2;   // a2 = 7 (incremented first), c2 = 7

// Compound assignment
x += 5;     // x = x + 5
x -= 3;     // x = x - 3
x *= 2;     // x = x * 2
x /= 4;     // x = x / 4
x %= 3;     // x = x % 3
```

### Comparison Operators

```java
int a = 10, b = 20;

a == b    // false   (equal to)
a != b    // true    (not equal to)
a > b     // false   (greater than)
a < b     // true    (less than)
a >= b    // false   (greater than or equal)
a <= b    // true    (less than or equal)

// ⚠️ For objects, == compares REFERENCES (memory addresses), not values!
String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2);      // false! Different objects in memory!
System.out.println(s1.equals(s2)); // true!  Same content!
```

### Logical Operators

```java
boolean a = true, b = false;

a && b    // false   (AND — both must be true)
a || b    // true    (OR — at least one must be true)
!a        // false   (NOT — flips the value)

// Short-circuit evaluation (VERY important!)
// && stops if first is false (no need to check second)
// || stops if first is true (no need to check second)

String name = null;
if (name != null && name.length() > 0) {  // ✅ Safe! If name is null, length() is never called
    System.out.println(name);
}

// Without short-circuit (& and | — DON'T short-circuit, evaluate both sides):
// if (name != null & name.length() > 0)   // ❌ CRASHES! Evaluates BOTH sides!
```

### Bitwise Operators (Advanced)

```java
int a = 5;   // binary: 0101
int b = 3;   // binary: 0011

a & b    // 1    (AND:  0101 & 0011 = 0001)
a | b    // 7    (OR:   0101 | 0011 = 0111)
a ^ b    // 6    (XOR:  0101 ^ 0011 = 0110)
~a       // -6   (NOT:  flips all bits, including sign bit)
a << 1   // 10   (Left shift:  0101 → 1010)  — effectively multiplies by 2
a >> 1   // 2    (Right shift: 0101 → 0010)  — effectively divides by 2
a >>> 1  // 2    (Unsigned right shift — fills with 0, not sign bit)
```

### Ternary Operator

```java
int age = 20;
String status = (age >= 18) ? "Adult" : "Minor";
// If condition is true → first value, else → second value
```

---

## 8. Conditional Statements

### if-else

```java
int score = 85;

if (score >= 90) {
    System.out.println("Grade: A");
} else if (score >= 80) {
    System.out.println("Grade: B");      // This runs
} else if (score >= 70) {
    System.out.println("Grade: C");
} else {
    System.out.println("Grade: F");
}

// Single-line if (no braces) — works but NOT recommended
if (score > 50) System.out.println("Pass");

// Why not recommended? Easy to make mistakes:
if (score > 50)
    System.out.println("Pass");
    System.out.println("Congrats!");  // This ALWAYS runs! It's NOT inside the if!
```

### switch Statement

```java
int day = 3;

// Traditional switch
switch (day) {
    case 1:
        System.out.println("Monday");
        break;               // Without break, execution "falls through" to next case!
    case 2:
        System.out.println("Tuesday");
        break;
    case 3:
        System.out.println("Wednesday");
        break;
    case 4: case 5:          // Multiple cases, same action
        System.out.println("Thu or Fri");
        break;
    default:
        System.out.println("Weekend");
}

// Enhanced switch (Java 14+) — No break needed, no fall-through!
String dayName = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    case 4, 5 -> "Thursday or Friday";
    default -> "Weekend";
};
System.out.println(dayName);  // Wednesday

// Switch works with: byte, short, int, char, String (Java 7+), enums
```

---

## 9. Loops

### for Loop

```java
// Standard for loop
for (int i = 0; i < 5; i++) {
    System.out.println(i);  // 0, 1, 2, 3, 4
}

// Parts: for(initialization; condition; update)
// 1. initialization: runs ONCE before the loop
// 2. condition: checked BEFORE each iteration (false → stop)
// 3. update: runs AFTER each iteration

// Enhanced for loop (for-each) — for arrays and collections
int[] nums = {10, 20, 30, 40, 50};
for (int num : nums) {
    System.out.println(num);  // 10, 20, 30, 40, 50
}
// Reads as: "for each num in nums"
```

### while Loop

```java
// Checks condition BEFORE each iteration
int count = 0;
while (count < 5) {
    System.out.println(count);  // 0, 1, 2, 3, 4
    count++;
}
// If condition is false initially, body never executes!

// Infinite loop (be careful!)
// while (true) { ... }
```

### do-while Loop

```java
// Checks condition AFTER each iteration — body runs AT LEAST once!
int count = 10;
do {
    System.out.println(count);  // 10 (runs once even though 10 > 5)
    count++;
} while (count < 5);
```

### break and continue

```java
// break — exit the loop entirely
for (int i = 0; i < 10; i++) {
    if (i == 5) break;           // Stop at 5
    System.out.println(i);       // 0, 1, 2, 3, 4
}

// continue — skip current iteration, go to next
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) continue;   // Skip even numbers
    System.out.println(i);       // 1, 3, 5, 7, 9
}

// Labeled break (for nested loops)
outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) break outer;  // Breaks BOTH loops!
        System.out.println(i + "," + j);
    }
}
```

---

## 10. Arrays

An array is a **fixed-size**, **ordered** collection of elements of the **same type**.

### Declaration & Initialization

```java
// Declaration
int[] numbers;           // Preferred style
int numbers2[];          // Also valid (C-style)

// Initialization
numbers = new int[5];    // Create array of 5 integers (all initialized to 0)

// Declaration + initialization
int[] nums = new int[5];                    // [0, 0, 0, 0, 0]
int[] nums2 = {10, 20, 30, 40, 50};        // Array literal
int[] nums3 = new int[]{10, 20, 30};       // Explicit with new

// Access elements (0-indexed!)
System.out.println(nums2[0]);   // 10 (first element)
System.out.println(nums2[4]);   // 50 (last element)
nums2[2] = 99;                  // Modify: [10, 20, 99, 40, 50]

// Length (it's a FIELD, not a method — no parentheses!)
System.out.println(nums2.length);  // 5

// ArrayIndexOutOfBoundsException
// nums2[5] = 100;  // ❌ Index 5 doesn't exist (valid: 0-4)
```

### Arrays Under the Hood

```
Stack                      Heap
┌──────────────┐          ┌───────────────────────────┐
│ nums2: 0xABC │─────────▶│ Array Object               │
└──────────────┘          │ length: 5                  │
                          │ [0]: 10                    │
                          │ [1]: 20                    │
                          │ [2]: 99                    │
                          │ [3]: 40                    │
                          │ [4]: 50                    │
                          └───────────────────────────┘

- Arrays are OBJECTS in Java (live on the heap)
- The variable holds a REFERENCE to the array object
- Array size is FIXED after creation — cannot grow or shrink!
```

### Iterating Arrays

```java
int[] nums = {10, 20, 30, 40, 50};

// Method 1: Classic for loop (when you need the index)
for (int i = 0; i < nums.length; i++) {
    System.out.println("Index " + i + ": " + nums[i]);
}

// Method 2: Enhanced for-each (when you just need values)
for (int num : nums) {
    System.out.println(num);
}

// Method 3: Arrays.toString() for quick printing
import java.util.Arrays;
System.out.println(Arrays.toString(nums));  // [10, 20, 30, 40, 50]
```

### Multi-Dimensional Arrays

```java
// 2D array (matrix)
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

System.out.println(matrix[1][2]);  // 6 (row 1, column 2)
System.out.println(matrix.length);     // 3 (number of rows)
System.out.println(matrix[0].length);  // 3 (number of columns in first row)

// Iterate 2D array
for (int row = 0; row < matrix.length; row++) {
    for (int col = 0; col < matrix[row].length; col++) {
        System.out.print(matrix[row][col] + " ");
    }
    System.out.println();
}

// Jagged array (rows of different lengths)
int[][] jagged = new int[3][];
jagged[0] = new int[]{1, 2};
jagged[1] = new int[]{3, 4, 5};
jagged[2] = new int[]{6};
```

### Useful Array Operations

```java
import java.util.Arrays;

int[] nums = {5, 2, 8, 1, 9, 3};

// Sort
Arrays.sort(nums);  // [1, 2, 3, 5, 8, 9]

// Binary search (array must be sorted first!)
int index = Arrays.binarySearch(nums, 5);  // 3

// Copy
int[] copy = Arrays.copyOf(nums, nums.length);     // Full copy
int[] partial = Arrays.copyOfRange(nums, 1, 4);     // Elements at index 1, 2, 3

// Fill
int[] filled = new int[5];
Arrays.fill(filled, 42);  // [42, 42, 42, 42, 42]

// Compare
boolean equal = Arrays.equals(nums, copy);  // true
```

---

## 11. Commenting

```java
// Single-line comment

/*
 * Multi-line comment
 * Used for longer explanations
 */

/**
 * Javadoc comment — generates HTML documentation
 * Used for classes, methods, and fields
 * 
 * @author Kunal
 * @version 1.0
 * @since 2024-01-01
 */
public class Calculator {
    
    /**
     * Adds two numbers and returns the result.
     * 
     * @param a the first number
     * @param b the second number
     * @return the sum of a and b
     * @throws ArithmeticException if the result overflows
     */
    public int add(int a, int b) {
        return a + b;
    }
}

// Generate HTML docs: javadoc Calculator.java
```

---

## 12. Packages and Imports

Packages organize Java classes into namespaces (like folders for files).

```java
// Declare this class is in the 'com.myapp.models' package
package com.myapp.models;   // MUST be the first statement!

// Import a specific class
import java.util.ArrayList;
import java.util.HashMap;

// Import all classes from a package
import java.util.*;         // Imports ArrayList, HashMap, List, etc.

// Static import (import static methods/fields)
import static java.lang.Math.PI;
import static java.lang.Math.sqrt;
// Now you can use PI and sqrt() directly without Math. prefix

public class Employee {
    double area = PI * sqrt(25);  // Instead of Math.PI * Math.sqrt(25)
}
```

### Package Structure Maps to Folders

```
src/
├── com/
│   └── myapp/
│       ├── models/
│       │   ├── Employee.java    ← package com.myapp.models;
│       │   └── Department.java  ← package com.myapp.models;
│       ├── services/
│       │   └── EmployeeService.java  ← package com.myapp.services;
│       └── Main.java            ← package com.myapp;
```

### java.lang — Automatically Imported

```java
// These classes are in java.lang and are ALWAYS available without import:
String s = "hello";           // java.lang.String
System.out.println();         // java.lang.System
int max = Integer.MAX_VALUE;  // java.lang.Integer
Object obj = new Object();    // java.lang.Object
Math.abs(-5);                 // java.lang.Math
```

---

## 13. Methods

### Method Declaration

```java
public class Calculator {
    
    //  access   static?  return   method      parameters
    //  modifier          type     name
    //  ↓        ↓        ↓        ↓           ↓
        public   static   int      add        (int a, int b) {
            return a + b;
        }
    
    // Method with no return value (void)
    public void printMessage(String msg) {
        System.out.println(msg);
        // No return statement needed (or just "return;" to exit early)
    }
    
    // Method with no parameters
    public double getPI() {
        return 3.14159;
    }
    
    // Varargs — variable number of arguments
    public int sum(int... numbers) {      // numbers is treated as int[] inside
        int total = 0;
        for (int n : numbers) {
            total += n;
        }
        return total;
    }
}

// Usage:
Calculator calc = new Calculator();
calc.sum(1, 2);              // 3
calc.sum(1, 2, 3, 4, 5);     // 15
calc.sum();                   // 0
```

### Method Parameters — Pass by Value

**Java is ALWAYS pass by value.** But the "value" of a reference variable is the memory address.

```java
// Primitive: the VALUE is copied
public static void tryToChange(int x) {
    x = 100;  // Changes the LOCAL copy, not the original!
}

int num = 5;
tryToChange(num);
System.out.println(num);  // Still 5!

// Reference: the ADDRESS is copied (not the object itself)
public static void modifyList(List<String> list) {
    list.add("new item");     // ✅ Modifies the SAME object (we have its address)
    list = new ArrayList<>(); // ❌ Only changes the LOCAL reference, not the caller's
}

List<String> myList = new ArrayList<>();
myList.add("original");
modifyList(myList);
System.out.println(myList);   // [original, new item]  ← modified!
// But myList still points to the original ArrayList, not the new one created inside
```

```
                     Pass by value for reference types:
                     
Before call:
  main:      myList ────────────▶ ArrayList ["original"]
  
During call:
  main:      myList ────────────▶ ArrayList ["original", "new item"]
  method:    list ──────────────▲    (list.add modifies the SAME object)

After list = new ArrayList<>():
  main:      myList ────────────▶ ArrayList ["original", "new item"]
  method:    list ──────────────▶ ArrayList []    (new object, main doesn't see this)
```

---

## 14. Method Visibility and Scope

### Access Modifiers

```java
public class MyClass {
    public int a;       // Accessible from ANYWHERE
    protected int b;    // Accessible from same package + subclasses (even in other packages)
    int c;              // Package-private (default) — accessible from same package only
    private int d;      // Accessible ONLY within this class
}
```

```
                    │ Same Class │ Same Package │ Subclass │ Everywhere │
────────────────────┼────────────┼──────────────┼──────────┼────────────┤
public              │     ✅     │      ✅      │    ✅    │     ✅     │
protected           │     ✅     │      ✅      │    ✅    │     ❌     │
package-private     │     ✅     │      ✅      │    ❌    │     ❌     │
(no modifier)       │            │              │          │            │
private             │     ✅     │      ❌      │    ❌    │     ❌     │
```

### Variable Scope

```java
public class ScopeDemo {
    // Class-level (instance) variable — accessible throughout the class
    private int instanceVar = 10;
    
    // Class-level (static) variable — shared by ALL instances
    private static int staticVar = 20;
    
    public void myMethod() {
        // Local variable — only accessible within this method
        int localVar = 30;
        
        if (true) {
            // Block variable — only accessible within this if block
            int blockVar = 40;
            System.out.println(localVar);  // ✅ Can access outer scope
        }
        // System.out.println(blockVar);   // ❌ Out of scope!
        
        for (int i = 0; i < 5; i++) {
            // i is only accessible inside this for loop
        }
        // System.out.println(i);          // ❌ Out of scope!
    }
}
```

---

## 15. The Stack and The Heap

### The Stack

The **stack** stores:
- Method call frames (one frame per active method call)
- Local variables
- Method parameters
- Return addresses

```
Thread's Stack (LIFO — Last In, First Out):

┌──────────────────────────┐
│  methodC() frame         │  ← TOP (current method)
│    int z = 30            │
├──────────────────────────┤
│  methodB() frame         │
│    int y = 20            │
├──────────────────────────┤
│  methodA() frame         │
│    int x = 10            │
├──────────────────────────┤
│  main() frame            │  ← BOTTOM (entry point)
│    String[] args         │
└──────────────────────────┘

When methodC finishes → its frame is popped (removed)
Control returns to methodB → it's now on top
```

### The Heap

The **heap** stores:
- All objects (created with `new`)
- Instance variables of objects
- Arrays
- Strings

```
Heap (shared by ALL threads):

┌───────────────────────────────────────────┐
│  Employee@1a2b { name: "Alice", age: 30 } │
│  String@3c4d "Hello World"                │
│  int[]@5e6f {1, 2, 3}                     │
│  ArrayList@7g8h [...]                     │
│  (free space for new objects)             │
│  (garbage collector reclaims dead objects)│
└───────────────────────────────────────────┘
```

### Stack vs Heap

| Feature | Stack | Heap |
|---|---|---|
| What's stored | Primitives, references, method frames | Objects |
| Size | Small (typically ~512KB - 1MB per thread) | Large (can be GBs) |
| Speed | Very fast (just move pointer) | Slower (complex allocation) |
| Lifetime | Until method returns | Until no references remain (then GC collects) |
| Thread safety | Each thread has its own stack | Shared among all threads |
| Management | Automatic (LIFO) | Garbage Collector |

### Example: Stack + Heap Together

```java
public static void main(String[] args) {
    int x = 10;                    // x on stack
    String name = "Alice";         // name on stack, "Alice" on heap (String pool)
    Employee emp = new Employee(); // emp on stack, Employee object on heap
    emp.name = "Bob";              // "Bob" on heap, assigned to object's field
    
    process(emp);                  // New stack frame for process()
}

static void process(Employee e) { // e is a COPY of emp's reference (same heap object)
    int y = 20;                   // y on this method's stack frame
    e.name = "Charlie";           // Modifies the SAME object on heap!
}
```

```
STACK                                    HEAP
┌──────────────────────┐
│  process() frame     │                 ┌─────────────────────┐
│    e: 0xABC ─────────┼───────────────▶│  Employee@ABC       │
│    y: 20             │           ┌───▶│  name: "Charlie"    │
├──────────────────────┤           │     └─────────────────────┘
│  main() frame        │           │
│    x: 10             │           │    ┌───────────────────────┐
│    name: 0x123 ──────┼──────────────▶│  "Alice" (String Pool)│
│    emp: 0xABC ───────┼───────────┘    └───────────────────────┘
│    args: 0xDEF       │
└──────────────────────┘
```

---

## 16. Recursion

A method that **calls itself**. Every recursive method needs a **base case** (stopping condition) or you get **StackOverflowError**.

```java
// Factorial: 5! = 5 × 4 × 3 × 2 × 1 = 120
public static int factorial(int n) {
    // Base case — stops the recursion
    if (n <= 1) return 1;
    
    // Recursive case — calls itself with a smaller problem
    return n * factorial(n - 1);
}

// How it works on the stack:
// factorial(5)
//   = 5 * factorial(4)
//     = 4 * factorial(3)
//       = 3 * factorial(2)
//         = 2 * factorial(1)
//           = 1            ← base case hit!
//         = 2 * 1 = 2      ← returning back up
//       = 3 * 2 = 6
//     = 4 * 6 = 24
//   = 5 * 24 = 120

// Stack during deepest call:
// ┌──────────────────┐
// │ factorial(1)     │  ← TOP (base case, about to return 1)
// │ factorial(2)     │
// │ factorial(3)     │
// │ factorial(4)     │
// │ factorial(5)     │
// │ main()           │
// └──────────────────┘
```

```java
// Fibonacci: 0, 1, 1, 2, 3, 5, 8, 13, 21, ...
// Each number = sum of two previous numbers
public static int fibonacci(int n) {
    if (n <= 0) return 0;      // Base case 1
    if (n == 1) return 1;      // Base case 2
    return fibonacci(n - 1) + fibonacci(n - 2);  // Two recursive calls!
}
// Warning: This is O(2^n) — exponentially slow! Use dynamic programming for efficiency.
```

### StackOverflowError

```java
// No base case → infinite recursion → stack fills up → crash!
public static void infinite() {
    infinite();  // StackOverflowError!
}

// Each call adds a frame to the stack. The stack has limited memory (~512KB-1MB).
// Eventually: java.lang.StackOverflowError
```

---

## 17. Casting

### Widening (Implicit) — Automatic, Safe

```java
// Smaller type → larger type (no data loss)
byte b = 42;
int i = b;        // byte → int (automatic)
long l = i;       // int → long (automatic)
double d = l;     // long → double (automatic)

// Widening order:
// byte → short → int → long → float → double
//         char ↗
```

### Narrowing (Explicit) — Manual, Risky

```java
// Larger type → smaller type (possible data loss!)
double d = 3.99;
int i = (int) d;       // 3 (truncated, not rounded!)

int big = 300;
byte b = (byte) big;   // 44 (300 mod 256 = 44, data loss!)

long l = 100L;
int i2 = (int) l;      // 100 (fits, so no loss this time)
```

### Object Casting (Reference Types)

```java
// Upcasting — subclass to superclass (automatic, always safe)
Object obj = "Hello";             // String → Object (automatic)
Number num = Integer.valueOf(42); // Integer → Number (automatic)

// Downcasting — superclass to subclass (manual, can fail!)
Object obj2 = "Hello";
String str = (String) obj2;       // ✅ Works because obj2 IS actually a String

Object obj3 = Integer.valueOf(42);
// String str2 = (String) obj3;   // ❌ ClassCastException! Integer is NOT a String!

// Always check with instanceof before downcasting:
if (obj3 instanceof String) {
    String str3 = (String) obj3;
} else {
    System.out.println("Not a String!");
}

// Pattern matching instanceof (Java 16+):
if (obj2 instanceof String s) {
    // s is already cast and ready to use!
    System.out.println(s.toUpperCase());
}
```

---

## 18. Value vs Reference Types

```java
// VALUE TYPES (primitives) — the variable holds the actual VALUE
int a = 10;
int b = a;       // b gets a COPY of a's value
b = 20;          // Changing b does NOT affect a
System.out.println(a);  // 10 (unchanged!)
System.out.println(b);  // 20

// REFERENCE TYPES (objects) — the variable holds a memory ADDRESS
int[] arr1 = {1, 2, 3};
int[] arr2 = arr1;       // arr2 gets a COPY of arr1's ADDRESS (both point to same array!)
arr2[0] = 99;            // Modifying through arr2 affects arr1 too!
System.out.println(arr1[0]);  // 99 (changed!)

// To make a TRUE copy of an array:
int[] arr3 = arr1.clone();
// or
int[] arr4 = Arrays.copyOf(arr1, arr1.length);
```

---

## 19. Strings

### String Basics

```java
// Strings are IMMUTABLE in Java — once created, cannot be changed!
String s1 = "Hello";          // String literal (stored in String Pool)
String s2 = new String("Hello"); // New object on heap (NOT in pool)
String s3 = "Hello";          // Same literal → same pool object as s1!

// == vs equals()
System.out.println(s1 == s3);      // true  (same object in String Pool)
System.out.println(s1 == s2);      // false (different objects!)
System.out.println(s1.equals(s2)); // true  (same content)
```

### String Pool (Under the Hood)

```
Heap Memory:
┌──────────────────────────────────────────────────────────┐
│                                                          │
│  ┌─────────────────────────────┐                         │
│  │     STRING POOL             │                         │
│  │  ┌───────────────────────┐  │                         │
│  │  │  "Hello"  ◄───── s1   │  │    s2 ──▶ String@9999  │
│  │  │           ◄───── s3   │  │           "Hello"       │
│  │  │  "World"              │  │   (separate object on   │
│  │  │  "Java"               │  │    the heap, NOT in     │
│  │  └───────────────────────┘  │    the pool)            │
│  └─────────────────────────────┘                         │
│                                                          │
└──────────────────────────────────────────────────────────┘

String Pool is a special area in the heap where Java stores unique string literals.
When you write "Hello" twice, Java reuses the same object → saves memory!
```

### String is Immutable — What Does That Mean?

```java
String s = "Hello";
s.toUpperCase();
System.out.println(s);  // "Hello" — NOT "HELLO"! 

// toUpperCase() creates a NEW String, doesn't modify the original!
String upper = s.toUpperCase();
System.out.println(upper);  // "HELLO"

// Even concatenation creates new objects:
String a = "Hello";
a = a + " World";    // Creates a NEW String object "Hello World"
                      // The old "Hello" is abandoned (eligible for GC if no other refs)
```

### Common String Methods

```java
String s = "Hello, World!";

s.length()                    // 13
s.charAt(0)                   // 'H'
s.indexOf("World")            // 7
s.lastIndexOf('l')            // 10
s.substring(7)                // "World!"
s.substring(7, 12)            // "World" (endIndex exclusive)
s.toUpperCase()               // "HELLO, WORLD!"
s.toLowerCase()               // "hello, world!"
s.trim()                      // Removes leading/trailing whitespace
s.strip()                     // Like trim() but handles Unicode spaces (Java 11+)
s.replace("World", "Java")   // "Hello, Java!"
s.contains("World")           // true
s.startsWith("Hello")         // true
s.endsWith("!")                // true
s.isEmpty()                   // false
s.isBlank()                   // false (Java 11+ — true if only whitespace)
s.split(", ")                 // ["Hello", "World!"]
String.join(", ", "a", "b")  // "a, b"
s.chars()                     // IntStream of characters

// Comparison
"abc".equals("abc")            // true
"abc".equalsIgnoreCase("ABC")  // true
"abc".compareTo("abd")         // -1 (lexicographic comparison)
```

### StringBuilder — Mutable Strings (For Performance)

```java
// Problem: String concatenation in a loop creates MANY temporary objects!
String result = "";
for (int i = 0; i < 10000; i++) {
    result += i + ", ";    // Creates 10,000 String objects! Very slow!
}

// Solution: StringBuilder (mutable, fast)
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i).append(", ");   // Modifies the SAME object. Fast!
}
String result2 = sb.toString();

// StringBuilder methods
sb.append("Hello");       // Add to end
sb.insert(5, " World");  // Insert at position
sb.delete(5, 11);         // Delete range
sb.replace(0, 5, "Hi");  // Replace range
sb.reverse();             // Reverse the string
sb.length();              // Length
sb.toString();            // Convert to String
```

```
String concatenation performance:
  String += in loop (10,000 iterations): ~300ms
  StringBuilder (10,000 iterations):     ~1ms

Why? String creates a new object each time → O(n²) copying.
StringBuilder modifies an internal char array → O(n) total.
```

### StringBuffer vs StringBuilder

| Feature | StringBuilder | StringBuffer |
|---|---|---|
| Mutable? | Yes | Yes |
| Thread-safe? | ❌ No | ✅ Yes (synchronized) |
| Performance | Faster | Slower (due to synchronization) |
| Use when | Single-threaded (most cases) | Multi-threaded |

---

## 20. Wrapper Classes

Each primitive type has a corresponding **wrapper class** (object version).

```java
// Primitive → Wrapper
byte    → Byte
short   → Short
int     → Integer        // Note: not "Int"!
long    → Long
float   → Float
double  → Double
char    → Character      // Note: not "Char"!
boolean → Boolean
```

### Why Wrapper Classes?

```java
// 1. Collections can't hold primitives — only objects!
List<int> nums;          // ❌ COMPILE ERROR!
List<Integer> nums;      // ✅ Works!

// 2. Useful utility methods
Integer.parseInt("42")           // String → int
Integer.valueOf("42")            // String → Integer
Integer.toString(42)             // int → String
Integer.MAX_VALUE                // 2,147,483,647
Integer.MIN_VALUE                // -2,147,483,648
Integer.toBinaryString(42)      // "101010"
Integer.toHexString(255)        // "ff"

// 3. Can be null (primitives can't)
Integer x = null;   // ✅ Valid (useful for "no value" scenarios)
// int y = null;    // ❌ COMPILE ERROR!
```

### Autoboxing & Unboxing (Java 5+)

```java
// Autoboxing: primitive → wrapper (automatic)
Integer num = 42;         // int 42 → Integer.valueOf(42) automatically!
List<Integer> list = new ArrayList<>();
list.add(5);              // int 5 → Integer.valueOf(5) automatically!

// Unboxing: wrapper → primitive (automatic)
int x = num;              // Integer → int automatically!
int y = list.get(0);      // Integer → int automatically!

// ⚠️ Unboxing null → NullPointerException!
Integer z = null;
// int w = z;             // ❌ NullPointerException! Can't unbox null!
```

### Integer Caching (Important Under the Hood!)

```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);   // true  ← Wait, why?

Integer c = 128;
Integer d = 128;
System.out.println(c == d);   // false ← And why is THIS different?

// Under the hood: Java caches Integer objects for values -128 to 127
// Integer.valueOf(127) returns a CACHED object (same object every time)
// Integer.valueOf(128) creates a NEW object each time

// ALWAYS use .equals() for wrapper comparison, not ==!
System.out.println(c.equals(d));  // true ✅
```

---

## 21. Debugging in IntelliJ

### Why Debugging Matters

Using `System.out.println()` to find bugs is like trying to find a leak in your house by flooding every room and seeing which one fills up — it works, but it's messy, slow, and you have to clean up afterward (remove all those print statements). A **debugger** lets you freeze your program at any point, look at every variable's value, and step through code line by line — like having X-ray vision into your running program.

### Setting Breakpoints
- Click in the **gutter** (left margin) next to a line number → red dot appears.
- The program pauses BEFORE executing that line.
- You can set as many breakpoints as you want. The program will stop at each one.

### Debug Controls

```
┌──────────────────────────────────────────────────────────┐
│  ▶ Resume (F9)       — Continue until next breakpoint   │
│  ⤓ Step Over (F8)    — Execute current line, go to next  │
│  ⤋ Step Into (F7)    — Go inside the method call         │
│  ⤊ Step Out (Shift+F8) — Finish current method, return   │
│  ⟲ Run to Cursor     — Run to where your cursor is       │
└──────────────────────────────────────────────────────────┘

When to use each:
  Step Over  → When you trust the method works and just want to go to the next line
  Step Into  → When you suspect the bug is INSIDE the method being called
  Step Out   → When you accidentally stepped into a method and want to go back
  Resume     → When you want to skip ahead to the next breakpoint
```

### Debug Panels

```
Variables Panel: Shows all variables in current scope and their values.
                 You can even CHANGE a variable's value while debugging!
                 Right-click a variable → Set Value → type new value.

Watches Panel:   Add custom expressions to monitor (e.g., arr.length, x > 5, 
                 list.get(0).getName()). These update live as you step through.

Call Stack:      Shows the chain of method calls that led to current point.
                 Example: main() → processOrder() → calculateTax() → HERE
                 You can click on any frame to see what variables looked like there.

Console:         Shows program output and lets you evaluate expressions.
```

### Conditional Breakpoints
- Right-click a breakpoint → enter a condition (e.g., `i == 42`).
- Breakpoint only triggers when the condition is true.
- **Super useful in loops!** If a bug happens on iteration 500, you don't want to step through 499 iterations manually.

```java
// Suppose you have this loop and something breaks when i is 42:
for (int i = 0; i < 1000; i++) {
    process(items.get(i));  // ← Set breakpoint with condition: i == 42
                            //    Program runs normally until i hits 42, THEN pauses
}
```

### Evaluate Expression
- During debugging, press **Alt+F8** → type any expression → see its result.
- Useful for testing what-if scenarios without changing code.
- You can call methods, do math, check conditions — anything!

```
Examples of expressions you can evaluate:
  user.getName()                    → "Kunal"
  list.size()                       → 42
  salary * 1.10                     → 66000.0
  name != null && name.length() > 5 → true
  Arrays.toString(myArray)          → "[1, 2, 3, 4, 5]"
```

### Debugging in VS Code

If you're using VS Code instead of IntelliJ, the process is almost identical:
1. Click in the gutter to set a breakpoint (red dot)
2. Press **F5** to start debugging (instead of IntelliJ's debug button)
3. Use the floating debug toolbar for Step Over / Step Into / Step Out / Continue
4. The Variables, Watch, and Call Stack panels are in the left sidebar during debug

### Common Debugging Tips

```
1. START with a breakpoint just BEFORE where you think the bug is
2. Check variable values — is the input what you expected?
3. Step through line by line and watch which path the code takes
4. For NullPointerException: set a breakpoint on the line that throws it,
   then check which variable is null
5. For wrong output: set a breakpoint where the result is calculated,
   then check the inputs going into the calculation
6. Use the Call Stack to trace HOW you got to the current point
7. Don't forget to remove breakpoints when you're done!
```

---

*Previous: [02-Oracle-PLSQL.md](02-Oracle-PLSQL.md)*
*Next: [04-Java-OOP.md](04-Java-OOP.md)*
