# Java Interview Answers - Interview Style

## 1. Explain all four OOPs concepts with real-world examples.

If I explain OOP in simple terms, there are four main pillars: encapsulation, abstraction, inheritance, and polymorphism.

Encapsulation means wrapping data and behavior together and controlling access to data. A simple real-world example is a bank account. I cannot directly change the balance from outside. I have to use methods like deposit or withdraw.

Abstraction means hiding internal complexity and showing only what is necessary. For example, when I drive a car, I use the steering, brake, and accelerator. I do not need to know the internal engine logic.

Inheritance means one class can reuse properties and behavior of another class. For example, if I have a parent class `Employee`, then `Manager` and `Developer` can inherit common fields like name, id, and salary.

Polymorphism means the same method or interface can behave differently based on the object. For example, if I have a method `calculateSalary()`, the logic may be different for a full-time employee and a contract employee.

## 2. What is Encapsulation? Give a real-time example.

Encapsulation means protecting data by keeping fields private and giving controlled access through methods. A good real-time example is an ATM. I can withdraw money or check balance through the machine, but I cannot directly access or modify the bank’s internal data. In Java, we do this using private variables with getters, setters, or business methods.

## 3. What is Abstraction? How is it achieved in Java?

Abstraction means hiding implementation details and exposing only the required functionality. In Java, we achieve it mainly using abstract classes and interfaces. For example, in a payment module I may define `makePayment()`, but whether the payment happens through UPI, card, or net banking is hidden inside the implementation.

## 4. What is Polymorphism? What are its types?

Polymorphism means one thing can take many forms. In Java, there are two types. One is compile-time polymorphism, which is method overloading. The second is runtime polymorphism, which is method overriding. Overloading means same method name with different parameters. Overriding means child class provides a different implementation of the parent class method.

## 5. What is Inheritance? What are the types of inheritance in Java?

Inheritance means one class acquires the properties and methods of another class. It helps in code reuse. In Java, class-based inheritance supports single inheritance, multilevel inheritance, and hierarchical inheritance. Java does not support multiple inheritance through classes because it can create ambiguity, but it supports multiple inheritance through interfaces.

## 6. What is the difference between method overloading and method overriding?

Method overloading happens in the same class. The method name is same, but parameters are different. It is decided at compile time.

Method overriding happens when a child class changes the implementation of a parent class method with the same signature. It is decided at runtime.

So in short, overloading is compile-time polymorphism and overriding is runtime polymorphism.

## 7. What is the difference between an abstract class and an interface?

An abstract class is used when I want to provide some common base behavior along with some abstract methods. It can have constructors, instance variables, concrete methods, and abstract methods.

An interface is used when I want to define a contract. A class can implement multiple interfaces, which helps in loose coupling.

So if I want shared code plus a base relationship, I use an abstract class. If I want flexibility and multiple implementations, I use an interface.

## 8. What is the difference between extends and implements?

`extends` is used when one class inherits another class, or one interface inherits another interface. `implements` is used when a class provides the implementation of an interface.

## 9. What is a subclass?

A subclass is a child class that inherits from a parent class. It can use the parent’s properties and methods and can also add its own functionality.

## 10. What is an object? How is it created in Java?

An object is a runtime instance of a class. It represents a real entity with state and behavior. In Java, we usually create it using the `new` keyword, like `Employee emp = new Employee();`.

## 11. Can we override static methods in Java? Why or why not?

No, static methods cannot be overridden in the true sense because they belong to the class, not to the object. They are resolved at compile time. If a child class defines the same static method, that is called method hiding, not overriding.

## 12. What is loose coupling? How is it achieved in Spring?

Loose coupling means one class should not depend heavily on another concrete class. It should depend on abstractions. In Spring, we achieve this using dependency injection. Instead of creating objects manually using `new`, Spring injects the required dependency. This makes the code more maintainable, testable, and flexible.

## 13. What is the difference between encapsulation and abstraction?

Encapsulation is about data hiding and controlled access. Abstraction is about hiding complexity and showing only essential behavior.

So I would say encapsulation answers how to protect data, while abstraction answers how to simplify usage.

## 14. Give a real-time example of encapsulation.

A real-time example is an employee payroll system. Salary fields are kept private, and updates happen only through approved methods like `updateSalary()` with validation. So direct access is restricted, which is encapsulation.

## 15. What is the difference between final, finally, and finalize?

`final` is a keyword. It is used to make a variable constant, prevent method overriding, or prevent class inheritance.

`finally` is a block used in exception handling. It runs whether an exception occurs or not, so it is mainly used for cleanup.

`finalize()` is a method that was related to garbage collection, but it is deprecated and not recommended now.

## 16. What is the use of the final keyword? Where can it be applied?

The `final` keyword is used to restrict changes. It can be applied to:

- variables, so their value cannot be reassigned
- methods, so they cannot be overridden
- classes, so they cannot be extended
- parameters, so they cannot be modified inside the method

## 17. What is the use of the static keyword? How do you access a static method?

`static` means the member belongs to the class, not to the object. We use it when something should be common for all objects, like utility methods or shared counters. A static method is accessed using the class name, for example `Math.max()`.

## 18. What is the default keyword in interfaces? When is it used?

A default method in an interface allows us to add a method with implementation inside the interface. It is useful when we want to enhance an interface without breaking the existing implementing classes.

## 19. What is the synchronized keyword? When do you use it?

`synchronized` is used to control access to shared resources in multithreading. I use it when multiple threads may access the same data and I want to avoid race conditions or inconsistent results.

## 20. What is the transient keyword in Java?

`transient` is used during serialization. If a field is marked transient, it will not be serialized. This is useful for sensitive or temporary data like passwords, OTP values, or session-specific information.

## 21. How do you restrict a class from being extended other than using sealed classes?

The simplest way is to make the class `final`. Once a class is final, no other class can extend it.

## 22. What are the major features introduced in Java 8?

Java 8 introduced many important features. The major ones are lambda expressions, functional interfaces, Stream API, Optional, method references, default and static methods in interfaces, and the new Date and Time API. These features made Java more concise and functional in style.

## 23. What is a Functional Interface? Give examples.

A functional interface is an interface that contains exactly one abstract method. It is mainly used with lambda expressions. Examples are `Runnable`, `Comparator`, `Predicate`, `Function`, `Consumer`, and `Supplier`.

## 24. What are the types of functional interfaces in Java?

The most common functional interfaces are:

- `Predicate` for condition checking
- `Function` for transforming input to output
- `Consumer` for consuming input without returning anything
- `Supplier` for supplying values
- `UnaryOperator` and `BinaryOperator` for operations on same types

## 25. What is a Lambda expression? Write a simple example.

A lambda expression is a shorter way to write anonymous functions. It reduces boilerplate code.

```java
List<String> names = List.of("Kunal", "Ravi", "Asha");
names.forEach(name -> System.out.println(name));
```

## 26. What is the Stream API? How is it used?

Stream API is used to process collections in a clean and declarative way. I use it for operations like filtering, mapping, sorting, and collecting results. It helps write less code and makes collection processing more readable.

## 27. What is the difference between Stream API and Collections?

Collections are used to store data in memory. Stream API is used to process that data. So collection is the data structure, while stream is the processing pipeline. Also, stream operations usually do not modify the original collection.

## 28. What is the Optional class? Why is it used?

`Optional` is a wrapper object that may or may not contain a value. It is used to avoid null-related problems, especially `NullPointerException`. It forces us to handle missing values more explicitly.

## 29. What are the key features of Java 17?

Java 17 is an LTS version. Key features include sealed classes, records as a standard feature, pattern matching improvements, better performance, better garbage collection support, and stronger JDK internal encapsulation.

## 30. What are the key features of Java 21?

Java 21 is also an LTS version. Key features include virtual threads, pattern matching for switch, record patterns, sequenced collections, and other performance and productivity improvements. Virtual threads are especially important for scalable concurrent applications.

## 31. What is pattern matching in Java? Where did you use it in your project?

Pattern matching reduces boilerplate by combining type checking and casting. Instead of checking type and then casting separately, Java handles both more cleanly.

If I answer from project perspective, I would say I mainly used modern Java features for cleaner conditional logic, but advanced pattern matching usage depends on the project’s Java version and coding style. If a project is on Java 17 or above, it becomes more useful in type-heavy conditional logic.

## 32. What is the difference between ArrayList and LinkedList?

`ArrayList` is backed by a dynamic array, so random access is fast. `LinkedList` is based on nodes, so insertion and deletion can be efficient at certain positions, but random access is slower. In most practical applications, I prefer `ArrayList` unless I specifically need frequent insertions and deletions.

## 33. What is the difference between List and Set?

A `List` allows duplicates and maintains insertion order. A `Set` does not allow duplicates. I use `List` when order and repetition matter, and `Set` when uniqueness matters.

## 34. How does HashMap work internally?

`HashMap` stores data in key-value pairs. Internally, it uses the hash code of the key to decide the bucket. If multiple keys fall into the same bucket, collision handling is done using linked structures and balanced trees in newer Java versions. Retrieval depends mainly on `hashCode()` and `equals()`.

## 35. How does HashTable work internally?

`Hashtable` also stores key-value pairs using hashing, similar to `HashMap`, but its methods are synchronized, which makes it thread-safe. It does not allow null keys or null values. Because of synchronization overhead, it is generally slower than `HashMap`.

## 36. What is the difference between Collection and Collections?

`Collection` is an interface and part of the collection hierarchy. `Collections` is a utility class that provides helper methods like sorting, reversing, searching, and creating unmodifiable collections.

## 37. What is the difference between Comparable and Comparator?

`Comparable` is used when a class itself defines its natural ordering using `compareTo()`. `Comparator` is used when I want custom sorting logic outside the class using `compare()`. If I need multiple sorting options, I prefer `Comparator`.

## 38. What is the difference between Array and int Array?

Array is the general concept of a fixed-size indexed data structure. `int[]` specifically means an array of primitive integers. So `int[]` is one type of array.

## 39. What is auto-boxing and unboxing?

Auto-boxing is automatic conversion of primitive types into wrapper objects, like `int` to `Integer`. Unboxing is the reverse, converting `Integer` back to `int`.

## 40. What is an Iterator? What are its methods?

An `Iterator` is used to traverse elements in a collection one by one. The main methods are `hasNext()`, `next()`, and `remove()`.

## 41. What is a Vector? How is it different from ArrayList?

`Vector` is a dynamic array like `ArrayList`, but it is synchronized, which makes it thread-safe. `ArrayList` is not synchronized and is usually faster in normal single-threaded use cases.

## 42. Why do we use ArrayList instead of List interface directly?

Actually, I would answer this carefully: we should usually declare the reference using `List` and instantiate it using `ArrayList`, like `List<String> list = new ArrayList<>();`. We cannot create an object of `List` directly because it is an interface. `ArrayList` is the implementation.

## 43. Write code: create an ArrayList, add items, convert to Stream, filter, collect back to List.

```java
List<String> names = new ArrayList<>();
names.add("Kunal");
names.add("Ravi");
names.add("Anu");

List<String> filtered = names.stream()
        .filter(name -> name.startsWith("R"))
        .toList();
```

## 44. Write a program to sort a list of employees by salary using Comparator.

```java
import java.util.*;

class Employee {
    private String name;
    private double salary;

    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public String getName() { return name; }
    public double getSalary() { return salary; }

    @Override
    public String toString() {
        return name + " - " + salary;
    }
}

public class Main {
    public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("A", 50000));
        employees.add(new Employee("B", 70000));
        employees.add(new Employee("C", 60000));

        employees.sort(Comparator.comparing(Employee::getSalary));
        employees.forEach(System.out::println);
    }
}
```

## 45. Write a program to find duplicate values from a list of employee records.

```java
import java.util.*;

class Employee {
    private int id;
    private String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() { return id; }
}

public class Main {
    public static void main(String[] args) {
        List<Employee> employees = List.of(
                new Employee(1, "A"),
                new Employee(2, "B"),
                new Employee(1, "C")
        );

        Set<Integer> seen = new HashSet<>();
        Set<Integer> duplicates = new HashSet<>();

        for (Employee emp : employees) {
            if (!seen.add(emp.getId())) {
                duplicates.add(emp.getId());
            }
        }

        System.out.println(duplicates);
    }
}
```

## 46. What is a Thread in Java?

A thread is the smallest unit of execution within a process. In Java, multithreading allows multiple tasks to run concurrently, which improves responsiveness and performance.

## 47. What are the ways to create a Thread in Java?

There are two traditional ways: extending the `Thread` class and implementing the `Runnable` interface. In modern applications, I usually prefer `Runnable`, `Callable`, and executor framework because they are more flexible and scalable.

## 48. Explain multithreading with an example.

Multithreading means multiple threads run within the same application. For example, in a web application, one thread can process one user request while another thread handles another request at the same time. That improves throughput and responsiveness.

## 49. Write code: create 2 threads — one should sleep and the other should run.

```java
public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            try {
                System.out.println("Thread 1 sleeping");
                Thread.sleep(3000);
                System.out.println("Thread 1 awake");
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread t2 = new Thread(() -> {
            System.out.println("Thread 2 running");
        });

        t1.start();
        t2.start();
    }
}
```

## 50. What is Thread.sleep()? Does it release locks?

`Thread.sleep()` pauses the current thread for a given amount of time. It does not release any lock held by that thread.

## 51. Explain JVM architecture.

JVM architecture mainly includes the class loader subsystem, runtime data areas, execution engine, and native method interface.

The runtime data areas include heap, stack, method area, PC register, and native method stack. The execution engine uses the interpreter and JIT compiler to execute bytecode efficiently.

## 52. What is the difference between JDK, JRE, and JVM?

JVM is the engine that executes Java bytecode.

JRE contains JVM plus the required libraries to run Java applications.

JDK contains JRE plus development tools like compiler and debugger.

So if I want to develop Java, I need JDK. If I only want to run Java, JRE is enough. JVM is the runtime engine inside both.

## 53. How does memory management work in Java (JRE)?

Java manages memory automatically. Objects are created in heap memory, and when they are no longer referenced, garbage collection reclaims that memory. Local variables and method calls are managed using stack memory. This reduces manual memory management issues.

## 54. Where are local variables, object instances, and static variables stored in memory?

Local variables are stored in stack memory. Object instances are stored in heap memory. Static variables are stored in class-level memory, commonly referred to as method area or metaspace-related storage depending on JVM implementation.

## 55. What is the difference between Stack and Heap memory?

Stack memory stores method calls and local variables. It is thread-specific and very fast.

Heap memory stores objects and is shared across threads. It is managed by the garbage collector.

## 56. What are the types of exceptions in Java?

In Java, exceptions are mainly of two types: checked and unchecked exceptions. Apart from that, errors are also there, but errors are more serious system-level issues.

## 57. What is the difference between checked and unchecked exceptions?

Checked exceptions are validated at compile time, so the compiler forces us to handle them or declare them. Example is `IOException`.

Unchecked exceptions occur at runtime and are subclasses of `RuntimeException`, like `NullPointerException` or `ArithmeticException`.

## 58. Write code demonstrating try-catch with a custom exception.

```java
class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}

public class Main {
    static void validateAge(int age) throws InvalidAgeException {
        if (age < 18) {
            throw new InvalidAgeException("Age must be 18 or above");
        }
    }

    public static void main(String[] args) {
        try {
            validateAge(16);
        } catch (InvalidAgeException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## 59. What is try-with-resources? Write an example.

Try-with-resources is used to automatically close resources like files, streams, or database connections.

```java
import java.io.BufferedReader;
import java.io.FileReader;

public class Main {
    public static void main(String[] args) {
        try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            System.out.println(br.readLine());
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## 60. What is a custom exception? When do you create one?

A custom exception is a user-defined exception class created for a specific business case. I create it when the standard Java exceptions are too generic. For example, in a real project I may create exceptions like `UserNotFoundException`, `InvalidResumeException`, or `JobAlreadyAppliedException`.

## 61. How do you handle exceptions in Spring Boot?

In Spring Boot, I usually handle exceptions at three levels. For local cases, I may use try-catch. For business errors, I create custom exceptions. For centralized handling, I use `@ControllerAdvice` or `@RestControllerAdvice` with `@ExceptionHandler`. This keeps controller code clean and gives consistent error responses.

## 62. What is the @ExceptionHandler annotation? Where is it used?

`@ExceptionHandler` is used in Spring to handle specific exceptions. It can be used inside a controller for local handling, or inside a `@ControllerAdvice` class for global exception handling across the application.
