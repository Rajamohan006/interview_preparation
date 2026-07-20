# ☕ Java Complete Reference — Interview Preparation Guide

> A structured, beginner-to-expert reference covering all essential Java concepts, OOP principles, multithreading, collections, exception handling, and interview questions. Designed for technical interview preparation with clean, friendly definitions and real examples.

---

## 📑 Table of Contents

1. [Introduction to Java](#1-introduction-to-java)
2. [JDK, JRE, and JVM](#2-jdk-jre-and-jvm)
3. [JVM Architecture — Deep Dive](#3-jvm-architecture--deep-dive)
4. [Java vs Kotlin](#4-java-vs-kotlin)
5. [Data Types and Variables](#5-data-types-and-variables)
6. [Operators](#6-operators)
7. [Decision Making Statements](#7-decision-making-statements)
8. [Loops in Java](#8-loops-in-java)
9. [Jump Statements](#9-jump-statements)
10. [Arrays](#10-arrays)
11. [Strings in Java](#11-strings-in-java)
12. [StringBuilder and StringBuffer](#12-stringbuilder-and-stringbuffer)
13. [Wrapper Classes](#13-wrapper-classes)
14. [Methods in Java](#14-methods-in-java)
15. [Constructors](#15-constructors)
16. [Access Modifiers](#16-access-modifiers)
17. [Static Keyword](#17-static-keyword)
18. [Final Keyword](#18-final-keyword)
19. [this and super Keywords](#19-this-and-super-keywords)
20. [Object-Oriented Programming (OOP)](#20-object-oriented-programming-oop)
21. [Encapsulation](#21-encapsulation)
22. [Inheritance](#22-inheritance)
23. [Polymorphism](#23-polymorphism)
24. [Abstraction](#24-abstraction)
25. [Interfaces](#25-interfaces)
26. [Abstract Class vs Interface](#26-abstract-class-vs-interface)
27. [Association, Aggregation, and Composition](#27-association-aggregation-and-composition)
28. [Exception Handling](#28-exception-handling)
29. [Collections Framework](#29-collections-framework)
30. [Generics](#30-generics)
31. [Multithreading](#31-multithreading)
32. [Java 8+ Features](#32-java-8-features)
33. [Java Math Class](#33-java-math-class)
34. [Garbage Collection](#34-garbage-collection)
35. [SOLID Principles](#35-solid-principles)
36. [Design Patterns (Basics)](#36-design-patterns-basics)
37. [Common Interview Questions — Java](#37-common-interview-questions--java)

---

## 1. Introduction to Java

### What is Java?
Java is a **high-level, object-oriented, platform-independent programming language** developed by **James Gosling** and his team at **Sun Microsystems**. It was initially started in **1991** as part of the **Green Project** and was officially released in **1995**. Sun Microsystems was later acquired by Oracle Corporation in 2010.

Java follows the principle of **"Write Once, Run Anywhere" (WORA)** — meaning code written on one platform can run on any platform that has a JVM installed, without recompilation.

### Key Features of Java

| Feature | Description |
|---|---|
| **Platform Independent** | Compiled into bytecode, runs on any JVM |
| **Object-Oriented** | Everything is based on classes and objects |
| **Strongly Typed** | Every variable must have a declared type |
| **Robust** | Strong memory management, exception handling |
| **Secure** | No pointers, bytecode verifier, security manager |
| **Multithreaded** | Built-in support for concurrent execution |
| **Distributed** | Supports networking with java.net |
| **Portable** | Platform-independent bytecode |
| **High Performance** | JIT compiler improves runtime speed |
| **Garbage Collected** | Automatic memory management |

### Advantages of Java
```
✅ Platform independence through JVM
✅ Strong object-oriented features
✅ Robust memory management (Garbage Collection)
✅ High security (no pointer arithmetic)
✅ Built-in multithreading support
✅ Rich standard library (Java API)
✅ Large developer community
✅ Widely used in enterprise, Android, financial systems
```

### Disadvantages of Java
```
❌ Slower than native languages like C/C++ (JVM overhead)
❌ Higher memory consumption due to JVM
❌ Verbose syntax — more boilerplate code
❌ Limited support for low-level programming
❌ Slower startup time
```

### Where Java is Used
```
Enterprise Applications   → Banking, Insurance (Spring, Hibernate)
Android Development       → Android SDK (Java/Kotlin)
Web Applications          → Servlets, JSP, Spring Boot
Big Data                  → Hadoop, Spark
Scientific Computing      → Research tools
Embedded Systems          → Smart cards, IoT
Cloud Applications        → Microservices with Spring Boot
```

---

## 2. JDK, JRE, and JVM

### One-Liner for Interview
> **JVM executes Java programs, JRE provides the runtime environment to run Java programs, and JDK provides the complete toolkit to develop Java applications.**

### Core Difference Table

| Component | Full Form | Purpose | Contains |
|---|---|---|---|
| **JVM** | Java Virtual Machine | Executes bytecode | Execution Engine, Memory Areas, ClassLoader |
| **JRE** | Java Runtime Environment | Provides runtime to run Java programs | JVM + Standard Libraries |
| **JDK** | Java Development Kit | Provides tools to develop Java programs | JRE + Compiler + Debugger + Tools |

### Relationship Diagram
```
┌──────────────────────────────────────────┐
│                  JDK                     │
│  ┌────────────────────────────────────┐  │
│  │               JRE                  │  │
│  │  ┌──────────────────────────────┐  │  │
│  │  │           JVM                │  │  │
│  │  │  (Executes bytecode)         │  │  │
│  │  └──────────────────────────────┘  │  │
│  │  + Standard Libraries (java.lang,  │  │
│  │    java.util, java.io...)          │  │
│  └────────────────────────────────────┘  │
│  + javac (compiler)                      │
│  + jdb (debugger)                        │
│  + jar (archiver)                        │
│  + javadoc (doc generator)               │
└──────────────────────────────────────────┘
```

### Interview Q&A — JDK, JRE, JVM

**Q1. What is JVM?**
> JVM (Java Virtual Machine) is a virtual machine that executes Java bytecode. It converts bytecode into machine-specific code and makes Java platform-independent. JVM is platform-dependent (different for Windows, Mac, Linux) but the Java program running on it is platform-independent.

**Q2. What is JRE?**
> JRE (Java Runtime Environment) provides the complete environment required to run Java programs. It includes the JVM and the standard Java class libraries (java.lang, java.util, etc.). If you only want to run a Java program (not develop), you need JRE.

**Q3. What is JDK?**
> JDK (Java Development Kit) is a complete package for developing Java applications. It includes JRE (which has JVM) plus development tools like the Java compiler (javac), debugger (jdb), archiver (jar), and documentation generator (javadoc).

**Q4. Can we run a Java program without JDK?**
> Yes. We can run a Java program using just JRE because JRE contains the JVM needed for execution. However, we cannot compile (.java → .class) without the JDK.

**Q5. Can we run a Java program without JRE?**
> No. JRE provides the runtime environment including the JVM and standard libraries. Without JRE, the program cannot be executed.

**Q6. Can we run a Java program without JVM?**
> No. JVM is mandatory to execute bytecode. Without JVM, the compiled bytecode (.class file) cannot be interpreted and run.

**Q7. Is JVM platform-dependent or independent?**
> JVM is **platform-dependent** — different JVM implementations exist for Windows, Linux, macOS.
> Java programs are **platform-independent** — the same bytecode runs on any platform that has a JVM.

**Q8. What tools are included in JDK?**
```
javac    → Java compiler (source code → bytecode)
java     → JVM launcher (runs .class files)
jdb      → Java debugger
jar      → Java archiver (creates .jar files)
javadoc  → Generates HTML documentation
javap    → Class file disassembler
jconsole → JVM monitoring tool
jstack   → Thread dump utility
jmap     → Heap dump utility
```

**Q9. What is bytecode?**
> Bytecode is the intermediate, platform-independent code generated when Java source code is compiled using `javac`. It is stored in `.class` files and is executed by the JVM. It is NOT machine code — it needs JVM to interpret/compile it further.

**Q10. Why is Java platform-independent?**
> Java source code is compiled into bytecode (not machine-specific code). This bytecode can run on any platform that has a JVM installed. The JVM acts as a translation layer between bytecode and the underlying OS/hardware.

---

## 3. JVM Architecture — Deep Dive

### Full JVM Working Flow

```
Step 1: Write Code
  Hello.java  (Source Code)
       ↓
Step 2: Compile
  javac Hello.java
       ↓
  Hello.class  (Bytecode)
       ↓
Step 3: JVM Loads & Executes
  ┌─────────────────────────────────────┐
  │              JVM                    │
  │                                     │
  │  ① ClassLoader                      │
  │     → Loading                       │
  │     → Linking (Verify+Prepare+Resolve)│
  │     → Initialization                │
  │            ↓                        │
  │  ② Runtime Memory Areas             │
  │     → Method Area                   │
  │     → Heap Memory                   │
  │     → Stack (per thread)            │
  │     → PC Register (per thread)      │
  │     → Native Method Stack           │
  │            ↓                        │
  │  ③ Execution Engine                 │
  │     → Interpreter                   │
  │     → JIT Compiler                  │
  │     → Garbage Collector             │
  │            ↓                        │
  │  ④ Native Method Interface (JNI)    │
  │     → Connects to Native Libraries  │
  └─────────────────────────────────────┘
```

### Step 1 — ClassLoader

ClassLoader is the component that loads `.class` files into JVM memory. It performs three tasks:

```
Loading      → Reads the .class file from disk into memory
Linking      → 3 sub-steps:
                 Verification  → Checks bytecode is valid
                 Preparation   → Allocates memory for static variables
                 Resolution    → Replaces symbolic references with direct ones
Initialization → Executes static blocks, assigns values to static variables
```

**Types of ClassLoaders:**

| ClassLoader | Loads |
|---|---|
| Bootstrap ClassLoader | Core Java libraries (java.lang, java.util) from rt.jar |
| Extension ClassLoader | Extensions from jre/lib/ext |
| Application ClassLoader | Application's own .class files (your code) |

### Step 2 — JVM Runtime Memory Areas

```
┌──────────────────────────────────────────────────────────┐
│                    JVM MEMORY                            │
│                                                          │
│  ┌─────────────────────────────────────────────────┐    │
│  │         Method Area (Metaspace)                  │    │
│  │  Stores: Class metadata, static variables,       │    │
│  │          method code, constant pool              │    │
│  │  Shared across ALL threads                       │    │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  ┌─────────────────────────────────────────────────┐    │
│  │              Heap Memory                         │    │
│  │  Stores: Objects, instance variables             │    │
│  │  Shared across ALL threads                       │    │
│  │  Managed by Garbage Collector                    │    │
│  │  ┌──────────────┐  ┌──────────────────────────┐ │    │
│  │  │  Young Gen   │  │       Old Gen             │ │    │
│  │  │  (Eden +     │  │  (Long-lived objects)     │ │    │
│  │  │   Survivor)  │  │                           │ │    │
│  │  └──────────────┘  └──────────────────────────┘ │    │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  ┌────────────────┐  ┌────────────┐  ┌────────────┐     │
│  │  Stack         │  │ PC Register│  │ Native     │     │
│  │  (Per Thread)  │  │(Per Thread)│  │ Method     │     │
│  │                │  │            │  │ Stack      │     │
│  │  Stores:       │  │ Stores:    │  │(Per Thread)│     │
│  │  Method calls  │  │ Current    │  │            │     │
│  │  Local vars    │  │ instruction│  │ For C/C++  │     │
│  │  Stack frames  │  │ address    │  │ methods    │     │
│  └────────────────┘  └────────────┘  └────────────┘     │
└──────────────────────────────────────────────────────────┘
```

### Step 3 — Execution Engine

```
Execution Engine has two parts:

1. Interpreter
   → Reads and executes bytecode line by line
   → Simple but SLOWER (re-interprets same code each time)

2. JIT Compiler (Just-In-Time)
   → Identifies "hot code" (frequently executed code)
   → Compiles entire method to native machine code
   → Caches the compiled code
   → Next time same code runs → uses cached version
   → Result: MUCH FASTER execution
   → That's why Java gets faster the longer it runs!
```

### Step 4 — Native Method Interface (JNI)
```
JNI = Java Native Interface
→ Bridge between Java code and native C/C++ libraries
→ Used for: hardware access, OS-specific operations
→ Example: System.out.println() internally calls native I/O code
```

### Interview Q&A — JVM

**Q. What are the main components of JVM?**
> ClassLoader, Runtime Memory Areas (Method Area, Heap, Stack, PC Register, Native Method Stack), Execution Engine (Interpreter + JIT Compiler), Garbage Collector, and Native Method Interface (JNI).

**Q. What is JIT Compiler?**
> JIT (Just-In-Time) Compiler is part of the JVM Execution Engine. It improves performance by identifying frequently executed bytecode ("hot code"), compiling it to native machine code at runtime, and caching it for reuse — avoiding repeated interpretation overhead.

**Q. What is ClassLoader?**
> ClassLoader is a component of JVM responsible for loading compiled `.class` files into JVM memory. It performs loading, linking (verification, preparation, resolution), and initialization.

**Q. What is the difference between Stack and Heap memory?**

| Feature | Stack | Heap |
|---|---|---|
| Stores | Method calls, local variables | Objects, instance variables |
| Scope | Per thread | Shared across all threads |
| Size | Smaller | Larger |
| Managed by | JVM automatically | Garbage Collector |
| Access Speed | Faster | Slower |
| Error | StackOverflowError | OutOfMemoryError |

---

## 4. Java vs Kotlin

### Why Kotlin Over Java?
> We choose Kotlin over Java because it improves developer productivity, code safety, and modern app performance with significantly less boilerplate code.

### Comparison Table

| Feature | Java | Kotlin |
|---|---|---|
| **Code Length** | Verbose (more boilerplate) | Concise (less code) |
| **Null Safety** | No built-in null safety (NPE common) | Built-in null safety at compile time |
| **Asynchronous** | Threads, Executors | Coroutines (simpler & efficient) |
| **Interoperability** | — | Fully interoperable with Java |
| **Data Classes** | Manual (getters, setters, equals, hashCode) | Built-in `data class` keyword |
| **Type Inference** | Limited | Strong type inference |
| **Extension Functions** | Not supported | Supported |
| **Smart Casting** | Manual casting required | Automatic smart casting |
| **Checked Exceptions** | Present | No checked exceptions |
| **Android Support** | Older primary language | Preferred by Google (since 2017) |
| **Performance** | Slightly faster (direct JVM) | Comparable (minor overhead) |
| **Release** | 1995 | 2016 |
| **Developer** | Sun Microsystems / Oracle | JetBrains |

---

## 5. Data Types and Variables

### Primitive Data Types

| Type | Size | Default | Range | Example |
|---|---|---|---|---|
| `byte` | 1 byte | 0 | -128 to 127 | `byte b = 10;` |
| `short` | 2 bytes | 0 | -32,768 to 32,767 | `short s = 500;` |
| `int` | 4 bytes | 0 | -2^31 to 2^31-1 | `int i = 1000;` |
| `long` | 8 bytes | 0L | -2^63 to 2^63-1 | `long l = 999L;` |
| `float` | 4 bytes | 0.0f | ~7 decimal digits | `float f = 3.14f;` |
| `double` | 8 bytes | 0.0d | ~15 decimal digits | `double d = 3.14;` |
| `char` | 2 bytes | '\u0000' | 0 to 65,535 | `char c = 'A';` |
| `boolean` | 1 bit | false | true / false | `boolean b = true;` |

### Non-Primitive (Reference) Data Types
```java
String name = "Java";         // String (most used)
int[] arr = {1, 2, 3};        // Array
Student s = new Student();    // Object/Class
```

### Variable Types

```java
// 1. Local Variable — declared inside a method
void show() {
    int x = 10;   // local variable, must be initialized before use
}

// 2. Instance Variable — declared inside class, outside method
class Student {
    String name;  // instance variable (default: null)
    int age;      // instance variable (default: 0)
}

// 3. Static Variable — shared across all objects
class Counter {
    static int count = 0;   // static variable
}
```

### Type Casting
```java
// Widening (automatic) — smaller → larger, no data loss
int i = 100;
long l = i;       // int → long (automatic)
double d = i;     // int → double (automatic)

// Narrowing (manual) — larger → smaller, possible data loss
double pi = 3.14;
int x = (int) pi;  // 3 (decimal part lost)
```

---

## 6. Operators

### 1. Arithmetic Operators
```java
// Used for mathematical calculations
int a = 10, b = 3;
System.out.println(a + b);  // 13 — Addition
System.out.println(a - b);  // 7  — Subtraction
System.out.println(a * b);  // 30 — Multiplication
System.out.println(a / b);  // 3  — Division (integer removes decimal!)
System.out.println(a % b);  // 1  — Modulus (remainder)

// ⚠️ Important: integer division removes decimal part
System.out.println(10 / 3);   // 3 (NOT 3.33)
System.out.println(10.0 / 3); // 3.33 (double division)
```

### 2. Unary Operators
```java
int a = 5;

// Post-increment: use THEN increment
System.out.println(a++);  // prints 5, then a becomes 6

// Pre-increment: increment THEN use
System.out.println(++a);  // a becomes 7, then prints 7

// Post-decrement
System.out.println(a--);  // prints 7, then a becomes 6

// Pre-decrement
System.out.println(--a);  // a becomes 5, then prints 5

// Logical NOT
boolean b = true;
System.out.println(!b);   // false
```

### 3. Assignment Operators
```java
int a = 10;
a += 5;   // a = a + 5  → 15
a -= 3;   // a = a - 3  → 12
a *= 2;   // a = a * 2  → 24
a /= 4;   // a = a / 4  → 6
a %= 4;   // a = a % 4  → 2
```

### 4. Relational Operators
```java
// Always returns boolean (true/false)
int a = 10, b = 20;
System.out.println(a == b);  // false — Equal to
System.out.println(a != b);  // true  — Not equal to
System.out.println(a > b);   // false — Greater than
System.out.println(a < b);   // true  — Less than
System.out.println(a >= b);  // false — Greater than or equal
System.out.println(a <= b);  // true  — Less than or equal
```

### 5. Logical Operators
```java
boolean x = true, y = false;
System.out.println(x && y);  // false — AND (both must be true)
System.out.println(x || y);  // true  — OR (at least one true)
System.out.println(!x);      // false — NOT

// ⚠️ Short-Circuit Evaluation (VERY IMPORTANT)
// && : if first is false → second NOT evaluated
// || : if first is true  → second NOT evaluated
false && someMethod();   // someMethod() never called
true  || someMethod();   // someMethod() never called
```

### 6. Ternary Operator
```java
// Syntax: condition ? valueIfTrue : valueIfFalse
int a = 10, b = 20;
int max = (a > b) ? a : b;   // max = 20

String result = (a % 2 == 0) ? "Even" : "Odd";  // "Even"
```

### 7. Bitwise Operators
```java
int a = 5;  // 0101 in binary
int b = 3;  // 0011 in binary

System.out.println(a & b);   // 1  — AND  (0001)
System.out.println(a | b);   // 7  — OR   (0111)
System.out.println(a ^ b);   // 6  — XOR  (0110)
System.out.println(~a);      // -6 — NOT  (flips all bits)
System.out.println(a << 1);  // 10 — Left shift  (multiply by 2)
System.out.println(a >> 1);  // 2  — Right shift (divide by 2)
System.out.println(a >>> 1); // 2  — Unsigned right shift
```

### 8. instanceof Operator
```java
// Checks if an object is an instance of a class
String s = "Hello";
System.out.println(s instanceof String);  // true

Object obj = new Dog();
System.out.println(obj instanceof Animal);  // true (if Dog extends Animal)
```

### Operator Precedence (High to Low)
```
() [] .                → Highest
++ -- ! ~ (type)
* / %
+ -
<< >> >>>
< <= > >= instanceof
== !=
&
^
|
&&
||
?:
= += -= *= /= %=       → Lowest
```

---

## 7. Decision Making Statements

Decision-making statements (also known as conditional or selection statements) in Java allow the programmer to control the flow of program execution based on the evaluation of boolean conditions.

### 1. if Statement
An `if` statement evaluates a boolean expression. If the expression evaluates to `true`, the statements inside the `if` block are executed. If `false`, the block is skipped.

**Syntax:**
```java
if (condition) {
    // block of code to be executed if condition is true
}
```

**Example:**
```java
int age = 18;
if (age >= 18) {
    System.out.println("You can vote");
}
```

### 2. if-else Statement
An `if-else` statement provides two execution paths. If the condition is `true`, the `if` block is executed; otherwise, the `else` block is executed.

**Syntax:**
```java
if (condition) {
    // block of code to be executed if condition is true
} else {
    // block of code to be executed if condition is false
}
```

**Example:**
```java
int i = 20;
if (i < 15) {
    System.out.println("Small");
} else {
    System.out.println("Large");   // executes this
}
```

### 3. Nested if
A nested `if` is an `if` statement that is the target of another `if` or `else` statement. It is used when a secondary condition must be checked only if the primary condition is true.

**Syntax:**
```java
if (condition1) {
    if (condition2) {
        // block of code to be executed if both condition1 and condition2 are true
    }
}
```

**Example:**
```java
int age = 25;
boolean hasID = true;

if (age >= 18) {
    if (hasID) {
        System.out.println("Entry allowed");
    } else {
        System.out.println("ID required");
    }
}
```

### 4. if-else-if Ladder
An `if-else-if` ladder evaluates multiple conditions sequentially from top to bottom. As soon as one of the conditions is true, its block is executed, and the rest of the ladder is skipped. If none are true, the final `else` block (if present) executes.

**Syntax:**
```java
if (condition1) {
    // statement(s);
} else if (condition2) {
    // statement(s);
} else {
    // statement(s);
}
```

**Example:**
```java
int marks = 75;

if (marks >= 90) {
    System.out.println("Grade A");
} else if (marks >= 75) {
    System.out.println("Grade B");   // executes this
} else if (marks >= 60) {
    System.out.println("Grade C");
} else {
    System.out.println("Grade D");
}
```

### 5. switch Statement
The `switch` statement selects one of many code blocks to execute based on the value of a single variable or expression (called the selector). 

* **Supported Types:** byte, short, char, int, their respective wrapper classes (`Byte`, `Short`, `Character`, `Integer`), `String` (since Java 7), and `enum`s.
* **Fall-Through Behavior:** Without a `break` statement, execution continues into the next `case` block automatically, even if that case doesn't match the condition.

**Syntax:**
```java
switch (expression) {
    case value1:
        // code block
        break;
    case value2:
        // code block
        break;
    default:
        // default code block
}
```

**Example:**
```java
int num = 2;

switch (num) {
    case 1:
        System.out.println("One");
        break;
    case 2:
        System.out.println("Two");    // executes this
        break;
    case 3:
        System.out.println("Three");
        break;
    default:
        System.out.println("Other");  // runs if no case matches
}
```

#### Fall-Through Concept (IMPORTANT)
```java
int x = 1;
switch (x) {
    case 1:
        System.out.println("One");
        // no break → falls through!
    case 2:
        System.out.println("Two");
        // no break → falls through!
    case 3:
        System.out.println("Three");
        break;
}
// Output: One
//         Two
//         Three
// Because: no break after case 1 and case 2!
```

### 6. switch Expressions (Java 12+, Standardized in Java 14)
Java 14 introduced a modern form of `switch` that can be used as an expression (returns a value) and uses the arrow (`->`) syntax. 

* **Arrow syntax (`->`):** Eliminates the need for `break` statements. No fall-through can occur.
* **`yield` Keyword:** Used to return a value from a multi-line code block within a `switch` case.
* **Exhaustiveness:** The compiler forces switch expressions to handle all possible inputs (requiring a `default` case, or complete coverage of enum/sealed types).

**Example:**
```java
String day = "MONDAY";
int numLetters = switch (day) {
    case "MONDAY", "FRIDAY", "SUNDAY" -> 6;
    case "TUESDAY" -> 7;
    case "THURSDAY", "SATURDAY" -> 8;
    case "WEDNESDAY" -> 9;
    default -> throw new IllegalStateException("Invalid day: " + day);
};
// numLetters = 6

// Example using yield for multi-line block
int size = switch (day) {
    case "MONDAY" -> {
        System.out.println("Beginning of work week");
        yield 6;
    }
    default -> day.length();
};
```

### 7. Pattern Matching for switch (Java 17+, Finalized in Java 21)
Java 21 finalized Pattern Matching for `switch`, allowing case labels to check the **type** of an object and automatically cast it.

* **Type Patterns:** Simplifies `instanceof` checks and casts.
* **Guarded Patterns (`when`):** Allows adding arbitrary boolean conditions to cases.
* **Null Handling:** `null` can be matched directly as a case, avoiding a manual null check before the switch.

**Example:**
```java
static String formatterPattern(Object obj) {
    return switch (obj) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s when s.length() > 5 -> String.format("long string: %s", s);
        case String s  -> String.format("short string: %s", s);
        case null      -> "null value";
        default        -> obj.toString();
    };
}
```

### 8. Ternary Operator (Shorthand if-else)
The ternary operator (`?:`) is a conditional operator that provides a shorthand way to write simple `if-else` statements. It evaluates a condition and returns one of two values.

**Syntax:**
```java
variable = (condition) ? value_if_true : value_if_false;
```

**Example:**
```java
int a = 10, b = 20;
int max = (a > b) ? a : b;   // max = 20
System.out.println("Max: " + max);
```

---

### if-else vs switch vs switch Expressions

| Feature | if-else | Traditional switch | switch Expression (Java 14+) |
|---|---|---|---|
| **Syntax Style** | Statement block | Statement block, requires `break` | Expression returning value, uses `->` or `yield` |
| **Condition Type** | Boolean expression (complex comparison) | Constant exact matches only | Constant matches, type patterns (Java 21+) |
| **Fall-through** | N/A | Yes (if `break` is omitted) | No |
| **Performance** | O(N) sequential check | O(1) jump table (compiled optimization) | O(1) jump table |
| **Exhaustiveness Check** | No (compiler doesn't check coverage) | No | Yes (compiler ensures all paths covered) |
| **Supported Types** | Any boolean expression | byte, short, char, int, wrappers, String, enum | Any type (using Pattern Matching in Java 21+) |

---

### Common Interview Questions

**Q. What is fall-through in switch?**
> Fall-through occurs when a `switch` case does not have a `break` statement. Execution continues into the subsequent case blocks regardless of whether they match the switch expression. This can be used intentionally to share execution logic, or it can be a source of bugs if omitted accidentally.

**Q. Can switch work with String?**
> Yes, from Java 7 onwards, `switch` supports `String` values. Internally, the compiler compares the String's `hashCode()` and then verifies equality using `.equals()`.

**Q. Which is faster — if-else or switch?**
> `switch` is generally faster when there are many conditions because the compiler can compile it into a jump table (`lookupswitch` or `tableswitch` JVM instructions) which operates in O(1) time complexity, whereas `if-else` executes conditions sequentially in O(N) time.

**Q. What is the dangling else problem?**
> The **dangling else** problem is a confusion that happens when we write nested `if` statements without using curly braces `{ }`. Because there are no braces, it is hard to tell which `if` statement the `else` belongs to.
> 
> In Java, the rule is simple: **an `else` always pairs with the closest preceding `if` that does not already have an `else`.**
> 
> **Example:**
> ```java
> if (x > 2)
>     if (x > 4)
>         System.out.println("A");
>     else
>         System.out.println("B");  // Belongs to the INNER if (x > 4), NOT the outer one!
> ```
> **Best Practice:** Always use curly braces `{ }` to make it clear and avoid this issue.

**Q. What is the difference between a switch statement and a switch expression?**
> - **Statement vs Expression:** A switch statement executes a block of statements but does not return a value. A switch expression evaluates to a value that can be assigned to a variable or passed as an argument.
> - **Syntax:** Switch expressions typically use arrow (`->`) syntax instead of colons (`:`) and do not require `break` statements.
> - **Exhaustiveness:** Switch expressions must cover all possible values (exhaustive), whereas switch statements do not have this requirement.

**Q. How does pattern matching for switch (Java 21) handle null values?**
> In traditional `switch`, passing a `null` value results in a `NullPointerException`. In Java 21's pattern matching, you can explicitly add `case null -> ...` to handle null inputs safely. If no `null` case is present and the value is null, the switch expression will still throw a `NullPointerException`.

**Q. What are Guarded Patterns in switch?**
> Guarded patterns allow case labels to refine a type match with an additional boolean condition using the `when` keyword (e.g., `case String s when s.length() > 5 -> ...`). The case matches only if both the type matches and the `when` condition evaluates to `true`.

---

## 8. Loops in Java

### Types of Loops

| Loop | Type | Use When |
|---|---|---|
| `for` | Entry-controlled | Number of iterations is KNOWN |
| `while` | Entry-controlled | Number of iterations is NOT known |
| `do-while` | Exit-controlled | Must execute AT LEAST ONCE |
| `for-each` | Enhanced | Iterating arrays or collections |

### 1. for Loop
```java
// Entry-controlled: condition checked BEFORE execution
// Best when: number of iterations is known

for (int i = 1; i <= 5; i++) {
    System.out.println("Count: " + i);
}

// Array traversal
int[] arr = {10, 20, 30, 40};
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
```

### 2. while Loop
```java
// Entry-controlled: condition checked BEFORE execution
// Best when: number of iterations depends on a condition

int i = 1;
while (i <= 5) {
    System.out.println("Count: " + i);
    i++;
}

// Useful for: reading input until condition met
Scanner sc = new Scanner(System.in);
int num = sc.nextInt();
while (num != 0) {
    System.out.println("Number: " + num);
    num = sc.nextInt();
}
```

### 3. do-while Loop
```java
// Exit-controlled: condition checked AFTER execution
// Guarantees at least ONE execution

int i = 1;
do {
    System.out.println("Count: " + i);
    i++;
} while (i <= 5);

// Even if condition is false from start, it executes once:
int j = 10;
do {
    System.out.println("Runs at least once: " + j);  // prints this
} while (j < 5);   // false, but still ran once!
```

### 4. Enhanced for Loop (for-each)
```java
// Used to iterate over arrays and collections
// Cannot use index, cannot modify array

int[] arr = {10, 20, 30, 40, 50};
for (int num : arr) {
    System.out.println(num);
}

// With ArrayList
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
for (String name : names) {
    System.out.println(name);
}
```

### Interview Questions

**Q. Difference between for and while loop?**
> Use `for` when the number of iterations is known beforehand. Use `while` when iterations depend on a runtime condition. Both are entry-controlled loops.

**Q. When is do-while preferred over while?**
> `do-while` is preferred when the loop body must execute at least once regardless of the condition — like menu-driven programs where you show the menu, take input, then check if user wants to continue.

**Q. What is an infinite loop?**
```java
while (true) {
    // runs forever unless break is used
}
for (;;) {
    // also infinite
}
```

---

## 9. Jump Statements

| Statement | Purpose | Used In |
|---|---|---|
| `break` | Exit loop or switch immediately | Loops, switch |
| `continue` | Skip current iteration, move to next | Loops only |
| `return` | Exit current method (with or without value) | Methods |

### break Example
```java
for (int i = 1; i <= 10; i++) {
    if (i == 5) break;    // exits loop when i == 5
    System.out.println(i);
}
// Output: 1 2 3 4
```

### continue Example
```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) continue;  // skips iteration when i == 3
    System.out.println(i);
}
// Output: 1 2 4 5
```

### return Example
```java
int add(int a, int b) {
    return a + b;       // exits method and returns value
}

void greet(String name) {
    if (name == null) return;  // early exit
    System.out.println("Hello, " + name);
}
```

---

## 10. Arrays

### What is an Array?
An array is a **collection of same-type elements stored in contiguous memory locations**. It allows storing multiple values under a single variable name, accessed using an index starting from 0. The size of an array is **fixed** once created.

```java
// Declaration
int[] arr;        // preferred style
int arr[];        // valid but less common

// Initialization with new
int[] arr = new int[5];         // creates array of 5 ints (default: 0)

// Initialization with values
int[] arr = {10, 20, 30, 40, 50};

// Access
System.out.println(arr[0]);    // 10 (first element)
System.out.println(arr[4]);    // 50 (last element)
System.out.println(arr.length); // 5 (total size)
```

### Types of Arrays

#### 1. One-Dimensional Array (1D)
```java
int[] arr = {10, 20, 30, 40};

// Traverse
for (int i = 0; i < arr.length; i++) {
    System.out.print(arr[i] + " ");
}
// Output: 10 20 30 40
```

#### 2. Two-Dimensional Array (2D)
```java
// Like a matrix/table with rows and columns
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

System.out.println(matrix[1][2]);  // 6 (row 1, column 2)

// Traverse 2D array
for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[i].length; j++) {
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}
```

#### 3. Jagged Array (Ragged Array)
```java
// Each row has a DIFFERENT number of columns
int[][] jagged = {
    {1, 2, 3},
    {4, 5},
    {6}
};
System.out.println(jagged[0].length);  // 3
System.out.println(jagged[1].length);  // 2
System.out.println(jagged[2].length);  // 1
```

#### 4. Multi-Dimensional Array
```java
int[][][] cube = new int[2][2][2];  // 3D array
cube[0][0][0] = 1;
```

#### 5. String Array
```java
String[] names = {"Alice", "Bob", "Charlie"};
for (String name : names) {
    System.out.println(name);
}
```

### Array Limitations
```
❌ Fixed size — cannot grow or shrink
❌ Can store only same type
❌ No built-in methods for add/remove
→ Use ArrayList for dynamic resizing
```

### Important Array Operations
```java
import java.util.Arrays;

int[] arr = {5, 2, 8, 1, 9};

// Sort
Arrays.sort(arr);                      // {1, 2, 5, 8, 9}

// Search (array must be sorted first)
int idx = Arrays.binarySearch(arr, 5); // returns index of 5

// Copy
int[] copy = Arrays.copyOf(arr, arr.length);

// Fill
Arrays.fill(arr, 0);                   // all elements set to 0

// Compare
boolean equal = Arrays.equals(arr, copy);

// Convert to String
System.out.println(Arrays.toString(arr));
```

### Common Interview Questions

**Q. What is ArrayIndexOutOfBoundsException?**
> It occurs when you try to access an array element with an index that is either negative or greater than or equal to the array's length. Example: `arr[5]` when array has only 5 elements (valid indices: 0-4).

**Q. Difference between Array and ArrayList?**

| Feature | Array | ArrayList |
|---|---|---|
| Size | Fixed | Dynamic |
| Type | Primitive + Object | Objects only |
| Performance | Faster | Slightly slower |
| Methods | Limited | Rich API (add, remove, etc.) |
| Syntax | `int[] arr` | `ArrayList<Integer> list` |

---

## 11. Strings in Java

### What is a String?
In Java, a `String` is **not a primitive type — it is an object** that represents a sequence of characters. Internally, Java uses a character array (`char[]`) to store string content. The most important characteristic of Strings in Java is **immutability** — once a String object is created, its value **cannot be changed**. Any modification creates a new String object in memory.

```java
String s = "Hello";     // String literal (stored in String Pool)
String s2 = new String("Hello");  // Object in heap
```

### String Pool (String Intern Pool)
```
String Pool is a special memory area inside Heap where
Java stores String literals to reuse them.

String a = "Hello";
String b = "Hello";
→ Both point to SAME object in String Pool
→ a == b is TRUE (same reference)

String c = new String("Hello");
→ Creates NEW object in Heap (outside pool)
→ a == c is FALSE (different references)
→ a.equals(c) is TRUE (same value)
```

### Important String Methods
```java
String s = "Hello World";

// Length
s.length()             // 11

// Character access
s.charAt(0)            // 'H'

// Substring
s.substring(6)         // "World"
s.substring(0, 5)      // "Hello"

// Case conversion
s.toUpperCase()        // "HELLO WORLD"
s.toLowerCase()        // "hello world"

// Search
s.indexOf("o")         // 4 (first occurrence)
s.lastIndexOf("o")     // 7 (last occurrence)
s.contains("World")    // true

// Comparison
s.equals("Hello World")          // true (case-sensitive)
s.equalsIgnoreCase("hello world") // true (case-insensitive)
s.compareTo("Hello")              // positive number (s > "Hello")

// Trim and Replace
"  hello  ".trim()          // "hello"
s.replace("World", "Java")  // "Hello Java"
s.replaceAll("[aeiou]", "*") // replace using regex

// Split
String[] parts = "a,b,c".split(",");  // ["a", "b", "c"]

// Join
String joined = String.join("-", "a", "b", "c");  // "a-b-c"

// Check start/end
s.startsWith("Hello")  // true
s.endsWith("World")    // true

// Empty/Blank check
"".isEmpty()           // true
"  ".isBlank()         // true (Java 11+)

// Convert to char array
char[] chars = s.toCharArray();

// Convert number to String
String num = String.valueOf(42);    // "42"
String num2 = Integer.toString(42); // "42"

// String to number
int n = Integer.parseInt("42");
```

### String Immutability — Why It Matters
```java
String s = "Hello";
s = s + " World";  // Does NOT modify "Hello"
                   // Creates NEW object "Hello World"
                   // "Hello" becomes eligible for GC

// This is why for repeated concatenation → use StringBuilder!
```

### Interview Questions

**Q. Why are Strings immutable in Java?**
> Strings are immutable for security (cannot be modified after creation, safe for passwords/network connections), thread safety (can be shared across threads without synchronization), and performance (String Pool caching only works because strings don't change).

**Q. Difference between == and .equals() for Strings?**
> `==` compares **references** (memory addresses). `.equals()` compares **actual content** (character values). Always use `.equals()` to compare String values.

**Q. What is String interning?**
> String interning is the process of storing only one copy of each distinct String value in the String Pool. When you use `String.intern()`, Java checks the pool and returns the existing reference if found, or adds it to the pool if not.

---

## 12. StringBuilder and StringBuffer

### Why StringBuilder and StringBuffer Exist
```
Problem with String (immutable):
  String s = "hello";
  s = s + " world";   // creates NEW object each time!

  In a loop with 1000 iterations → 1000 new String objects
  → High memory usage
  → Excessive Garbage Collection
  → Poor performance

Solution: Mutable string classes
  StringBuffer → Thread-safe (older, Java 1.0)
  StringBuilder → Not thread-safe but FASTER (Java 5+)
```

### StringBuffer
```java
// Thread-safe, mutable string — all methods are synchronized
StringBuffer sb = new StringBuffer("Hello");

sb.append(" World");      // Hello World
sb.insert(5, ",");        // Hello, World
sb.delete(5, 6);          // Hello World
sb.replace(6, 11, "Java");// Hello Java
sb.reverse();             // avaJ olleH
sb.toString();            // convert to String

System.out.println(sb.length());    // current length
System.out.println(sb.capacity()); // default: 16 + initial length
```

### StringBuilder
```java
// NOT thread-safe, but FASTER — preferred for single-threaded use
StringBuilder sb = new StringBuilder("Hello");

sb.append(" World");
sb.insert(0, "Say: ");
sb.delete(0, 5);
sb.reverse();
sb.replace(0, 5, "Hi");

String result = sb.toString();
```

### String vs StringBuilder vs StringBuffer

| Feature | String | StringBuilder | StringBuffer |
|---|---|---|---|
| **Mutability** | Immutable (new object on change) | Mutable (modifies in place) | Mutable (modifies in place) |
| **Thread-Safe** | Yes (immutability makes it safe) | ❌ No | ✅ Yes (synchronized) |
| **Performance** | Slow (creates new objects) | ✅ Fastest | Slower (sync overhead) |
| **Use Case** | Fixed, unchanging strings | Single-threaded manipulation | Multi-threaded manipulation |
| **Memory** | High (many objects) | Efficient | Efficient |

### When to Use What?
```
String         → Use when value won't change (constants, keys)
StringBuilder  → Use in single-threaded scenarios (loops, building strings)
StringBuffer   → Use in multi-threaded environments (thread-safe required)
```

---

## 13. Wrapper Classes

### What are Wrapper Classes?
Wrapper classes in Java are used to **convert primitive data types into objects**. Each primitive type has a corresponding wrapper class that wraps (encapsulates) the primitive value inside an object.

### Why Wrapper Classes Are Needed
> Wrapper classes are required because Java Collections (ArrayList, HashMap, etc.) and many APIs work **only with objects, not primitives**. Wrapper classes enable primitives to participate in object-oriented features like generics, collections, and APIs.

```
Key Reasons:
✅ Collections only store objects: ArrayList<Integer> (not ArrayList<int>)
✅ Required for Generics
✅ Allows null values (primitives cannot be null)
✅ Provides utility methods (parse, compare, convert)
✅ Enables autoboxing/unboxing
```

### Primitive to Wrapper Mapping

| Primitive | Wrapper Class |
|---|---|
| `int` | `Integer` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |
| `byte` | `Byte` |
| `short` | `Short` |
| `long` | `Long` |

### Autoboxing and Unboxing

#### Autoboxing — Primitive → Object (Automatic)
```java
int a = 10;
Integer obj = a;   // autoboxing: int → Integer (automatic)

// Internally: Integer obj = Integer.valueOf(a);
```

#### Unboxing — Object → Primitive (Automatic)
```java
Integer obj = 10;
int a = obj;       // unboxing: Integer → int (automatic)

// Internally: int a = obj.intValue();
```

### Important Wrapper Methods
```java
// parseInt() — String → primitive int
int a = Integer.parseInt("100");     // 100

// valueOf() — primitive/String → wrapper Object
Integer obj = Integer.valueOf(10);   // Integer object

// xxxValue() — wrapper → primitive
Integer obj = 42;
int i = obj.intValue();              // 42
double d = obj.doubleValue();        // 42.0

// toString() — wrapper → String
String s = Integer.toString(42);     // "42"

// compareTo() — compare two wrapper objects
Integer a = 10, b = 20;
a.compareTo(b);  // negative (a < b)

// equals() — compare values (NOT references)
Integer x = 100, y = 100;
x.equals(y);     // true

// MIN_VALUE / MAX_VALUE constants
Integer.MAX_VALUE;  // 2147483647
Integer.MIN_VALUE;  // -2147483648

// toBinaryString, toHexString, toOctalString
Integer.toBinaryString(10);   // "1010"
Integer.toHexString(255);     // "ff"
Integer.toOctalString(8);     // "10"
```

### Important Wrapper Concepts

**parseInt() vs valueOf():**
```java
int a = Integer.parseInt("100");       // returns primitive int
Integer b = Integer.valueOf("100");    // returns Integer object
```

**Wrapper classes are immutable:**
```java
Integer a = 10;
// a = 10 cannot be "changed"
// reassigning creates a new object
```

**Null vs Primitive:**
```java
Integer a = null;  // ✅ valid — object can be null
int b = null;      // ❌ compile error — primitive cannot be null
```

**Integer Caching Trap (VERY COMMON INTERVIEW TRAP):**
```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);   // true (cached range: -128 to 127)

Integer c = 128;
Integer d = 128;
System.out.println(c == d);   // false (outside cache range → new objects)
System.out.println(c.equals(d)); // true (always use .equals()!)
```

### Common Interview Questions

**Q. Why wrapper classes are needed?**
> Because Java Collections and APIs work with objects, not primitives. Wrapper classes enable primitives to be used in collections, generics, and provide utility methods.

**Q. What is autoboxing?**
> Autoboxing is the automatic conversion of a primitive type into its corresponding wrapper object by the Java compiler. Example: `Integer obj = 10;` — the compiler automatically converts `int 10` to `Integer`.

**Q. Difference between parseInt() and valueOf()?**
> `parseInt()` returns a **primitive int**, while `valueOf()` returns a **wrapper Integer object**.

**Q. Are wrapper classes mutable?**
> No, wrapper classes are **immutable**. Once created, their value cannot be changed.

**Q. Can wrapper classes hold null?**
> Yes. Since they are objects, they can hold null. Primitives cannot hold null.

---

## 14. Methods in Java

### What is a Method?
A method is a **reusable block of code defined inside a class** that performs a specific operation. Methods improve code reusability, readability, and maintainability by avoiding repetition.

```java
// Method Structure:
access_modifier return_type method_name(parameters) {
    // method body
    return value;  // if return type is not void
}

// Example:
public int add(int a, int b) {
    return a + b;
}
```

### Components of a Method

| Component | Example | Meaning |
|---|---|---|
| Access Modifier | `public` | Who can access it |
| Return Type | `int` | Type of value returned (or `void`) |
| Method Name | `add` | Name following camelCase |
| Parameters | `(int a, int b)` | Input values |
| Method Body | `{ return a + b; }` | Code to execute |

### Types of Methods

#### 1. Predefined Methods
```java
// Built-in methods provided by Java libraries
Math.sqrt(16);          // 4.0
"hello".toUpperCase();  // "HELLO"
System.out.println("Hi");
```

#### 2. User-Defined Methods
```java
// Created by developers based on requirements
class Calculator {
    int multiply(int a, int b) {
        return a * b;
    }
}
```

### Instance vs Static Methods

#### Instance Method — Requires an Object
```java
class Demo {
    String name = "Java";

    void show() {                 // instance method
        System.out.println(name); // can access instance variables
    }
}

// Must create object to call
Demo d = new Demo();
d.show();
```

#### Static Method — No Object Needed
```java
class Demo {
    static void greet() {         // static method
        System.out.println("Hello from static!");
    }
}

// Call directly using class name
Demo.greet();
// or just greet(); inside same class
```

### Static vs Instance Methods — Key Differences

| Feature | Static Method | Instance Method |
|---|---|---|
| Belongs to | Class | Object |
| Object needed? | ❌ No | ✅ Yes |
| Access | Only static members | Static + Instance members |
| `this` keyword | ❌ Not allowed | ✅ Allowed |
| Memory | One copy per class | One per object |
| Polymorphism | ❌ Cannot be overridden | ✅ Can be overridden |

### Method Signature
```java
// Signature = method name + parameter list (NOT return type!)
add(int a, int b)         // signature
int add(int a, int b)     // method (signature + return type)

// Signatures must be unique within a class for overloading
```

### Method Call Stack (LIFO)
```
When methods call each other:

main() calls add()
add() calls multiply()

Stack state:
[ multiply() ] ← top (currently running)
[ add()      ]
[ main()     ] ← bottom

multiply() finishes → removed from stack
add() resumes
add() finishes → removed
main() resumes
```

### Variable Arguments (Varargs)
```java
// Method that accepts variable number of arguments
int sum(int... numbers) {
    int total = 0;
    for (int n : numbers) total += n;
    return total;
}

sum(1, 2);           // 3
sum(1, 2, 3, 4, 5);  // 15
```

---

## 15. Constructors

### What is a Constructor?
A constructor is a **special method used to initialize objects** when they are created. It is automatically invoked by the JVM when a `new` keyword is used. Constructors have the **same name as the class** and **no return type** (not even `void`).

### Key Characteristics
```
✅ Same name as class
✅ No return type
✅ Called automatically when object is created
✅ Can be overloaded
✅ Can throw exceptions
❌ Cannot be static, abstract, final, or synchronized
❌ Cannot be inherited
❌ Cannot be overridden
```

### Types of Constructors

#### 1. Default Constructor (Compiler-Provided)
```java
class Test {
    int id;
    String name;
    // No constructor written → compiler adds:
    // Test() { super(); }
}

Test t = new Test();
System.out.println(t.id);    // 0 (default int value)
System.out.println(t.name);  // null (default String value)
```

#### 2. No-Argument Constructor
```java
class Employee {
    Employee() {
        System.out.println("Employee created!");
    }
}

Employee e = new Employee();  // "Employee created!"
```

#### 3. Parameterized Constructor
```java
class Student {
    int id;
    String name;

    Student(int i, String n) {
        id = i;
        name = n;
    }
}

Student s = new Student(101, "Alice");
System.out.println(s.id + " " + s.name);  // 101 Alice
```

#### 4. Constructor Overloading
```java
class Product {
    int id;
    String name;
    double price;

    Product() {
        System.out.println("Default product created");
    }

    Product(int id) {
        this.id = id;
    }

    Product(int id, String name) {
        this.id = id;
        this.name = name;
    }

    Product(int id, String name, double price) {
        this.id = id;
        this.name = name;
        this.price = price;
    }
}

Product p1 = new Product();
Product p2 = new Product(1);
Product p3 = new Product(2, "Laptop");
Product p4 = new Product(3, "Phone", 999.99);
```

#### 5. Copy Constructor
```java
class Student {
    int id;
    String name;

    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    // Copy constructor — copies values from another object
    Student(Student s) {
        this.id = s.id;
        this.name = s.name;
    }
}

Student s1 = new Student(1, "Alice");
Student s2 = new Student(s1);  // copy of s1
```

#### 6. Private Constructor
```java
// Prevents object creation from outside the class
// Used in: Singleton pattern, utility classes

class Singleton {
    private static Singleton instance;

    private Singleton() { }   // cannot be called from outside

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

### Constructor Chaining

#### Using this() — Same Class
```java
class Employee {
    int id;
    String name;
    String role;

    Employee() {
        this(0, "Unknown", "None");  // must be FIRST statement
        System.out.println("Default constructor");
    }

    Employee(int id, String name, String role) {
        this.id = id;
        this.name = name;
        this.role = role;
    }
}
```

#### Using super() — Parent Class
```java
class Animal {
    String type;

    Animal(String type) {
        this.type = type;
        System.out.println("Animal constructor: " + type);
    }
}

class Dog extends Animal {
    String breed;

    Dog(String breed) {
        super("Mammal");   // calls Animal constructor — must be FIRST
        this.breed = breed;
        System.out.println("Dog constructor: " + breed);
    }
}

Dog d = new Dog("Labrador");
// Output:
// Animal constructor: Mammal
// Dog constructor: Labrador
```

### Constructor vs Method

| Feature | Constructor | Method |
|---|---|---|
| Purpose | Initialize object | Perform operation |
| Name | Same as class | Any valid name |
| Return Type | No return type | Must have return type |
| Invocation | Automatic (on `new`) | Explicit call |
| Inheritance | ❌ Not inherited | ✅ Inherited |
| Overriding | ❌ Cannot be overridden | ✅ Can be overridden |

### Constructor Execution Order (Inheritance)
```java
class A {
    A() { System.out.println("A constructor"); }
}
class B extends A {
    B() { System.out.println("B constructor"); }
}
class C extends B {
    C() { System.out.println("C constructor"); }
}

C c = new C();
// Output:
// A constructor   ← parent first
// B constructor
// C constructor   ← child last
```

### Common Constructor Errors
```java
// ❌ This is NOT a constructor (has return type!)
class Test {
    void Test() {    // this is a METHOD, not constructor!
        System.out.println("This is a method");
    }
}

// ❌ Recursive constructor call — compile error!
class Test {
    Test() {
        this();   // infinite recursion → error!
    }
}
```

---

## 16. Access Modifiers

### What are Access Modifiers?
Access modifiers are keywords that **control the visibility and accessibility** of classes, methods, variables, and constructors in Java. They are the foundation of **encapsulation**.

### The Four Access Modifiers

| Modifier | Same Class | Same Package | Subclass (diff pkg) | Other Classes |
|---|---|---|---|---|
| `private` | ✅ | ❌ | ❌ | ❌ |
| `default` (no keyword) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

### private
```java
class BankAccount {
    private double balance;     // only accessible within BankAccount class

    private void deductFee() {  // private method
        balance -= 10;
    }

    public double getBalance() {
        return balance;         // controlled access via public method
    }
}

BankAccount acc = new BankAccount();
acc.balance;      // ❌ COMPILE ERROR — cannot access private
acc.getBalance(); // ✅ works — public method
```

### default (package-private)
```java
class Helper {
    void help() {    // no modifier = default
        System.out.println("Helping...");
    }
}
// Accessible only within the same package
// Not accessible from outside the package
```

### protected
```java
class Animal {
    protected String type = "Mammal";  // accessible in subclasses

    protected void eat() {
        System.out.println("Eating...");
    }
}

class Dog extends Animal {       // Dog in a DIFFERENT package
    void show() {
        System.out.println(type);  // ✅ accessible via inheritance
        eat();                     // ✅ accessible
    }
}
```

### public
```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
// Accessible from ANYWHERE — any class, any package
```

---

## 17. Static Keyword

### What is static?
The `static` keyword means the member **belongs to the class itself**, not to any particular instance (object). Static members are **shared across all objects** of the class.

### Types of Static

#### 1. Static Variable
```java
class Student {
    int id;              // instance variable — each object has its own
    String name;
    static String school = "ABC School";  // shared across ALL students

    static int count = 0;

    Student(int id, String name) {
        this.id = id;
        this.name = name;
        count++;         // counts how many students created
    }
}

Student s1 = new Student(1, "Alice");
Student s2 = new Student(2, "Bob");

System.out.println(Student.count);    // 2 — accessed via class name
System.out.println(Student.school);   // "ABC School"
```

#### 2. Static Method
```java
class MathUtils {
    static int square(int n) {
        return n * n;
    }

    static double circleArea(double r) {
        return Math.PI * r * r;
    }
}

// No object needed — call directly
int result = MathUtils.square(5);        // 25
double area = MathUtils.circleArea(3.0); // 28.27...
```

#### 3. Static Block
```java
class Database {
    static String url;
    static String driver;

    static {
        // runs ONCE when class is loaded
        url = "jdbc:mysql://localhost/mydb";
        driver = "com.mysql.jdbc.Driver";
        System.out.println("Database initialized");
    }
}
// Static block executes before any constructor or main method
```

#### 4. Static Nested Class
```java
class Outer {
    static class Inner {
        void show() {
            System.out.println("Static nested class");
        }
    }
}

// Create instance of static nested class
Outer.Inner inner = new Outer.Inner();
inner.show();
```

### Static Block vs Constructor

| Feature | Static Block | Constructor |
|---|---|---|
| Runs | Once (class loading) | Every object creation |
| Purpose | Static variable initialization | Object initialization |
| Timing | Before any object created | When `new` is used |

### Important Rules for static
```
✅ Static methods can access static variables/methods
❌ Static methods CANNOT access instance variables/methods directly
❌ Static methods CANNOT use 'this' keyword
❌ Static methods CANNOT be overridden (can be hidden)
✅ Static members are shared — changing one affects all objects
```

---

## 18. Final Keyword

### Types of final

#### 1. Final Variable — Value Cannot Change
```java
final int MAX_SIZE = 100;
MAX_SIZE = 200;  // ❌ COMPILE ERROR — cannot reassign final variable

// Blank final: declared but initialized in constructor
class Circle {
    final double PI;

    Circle() {
        PI = 3.14159;  // initialized in constructor
    }
}
```

#### 2. Final Method — Cannot be Overridden
```java
class Parent {
    final void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    void display() {    // ❌ COMPILE ERROR — cannot override final method
        System.out.println("Child display");
    }
}
```

#### 3. Final Class — Cannot be Inherited
```java
final class ImmutableClass {
    int value;
}

class SubClass extends ImmutableClass {  // ❌ COMPILE ERROR
}

// Example: String class is final in Java
// You cannot extend String!
```

### final vs static vs static final
```java
int normalVar = 10;     // can change, per object
static int s = 10;      // shared, can change
final int f = 10;       // per object, cannot change
static final int SF = 10; // shared constant (like a constant)
```

---

## 19. this and super Keywords

### this Keyword
`this` refers to the **current object** of the class. It is used to differentiate instance variables from local/parameter variables.

#### Uses of this
```java
class Student {
    int id;
    String name;

    // 1. Differentiate instance variable from parameter
    Student(int id, String name) {
        this.id = id;      // this.id = instance var, id = parameter
        this.name = name;
    }

    // 2. Call current class method
    void display() {
        this.show();       // calls show() of current object
    }

    void show() {
        System.out.println(id + " " + name);
    }

    // 3. Return current object
    Student getStudent() {
        return this;
    }

    // 4. Pass current object to another method
    void print() {
        printDetails(this);
    }

    void printDetails(Student s) {
        System.out.println(s.id);
    }

    // 5. Constructor chaining (must be first statement)
    Student() {
        this(0, "Unknown");  // calls parameterized constructor
    }
}
```

### super Keyword
`super` refers to the **immediate parent class object**. Used to access parent's variables, methods, and constructor.

#### Uses of super
```java
class Animal {
    String type = "Animal";

    void sound() {
        System.out.println("Some sound");
    }

    Animal(String type) {
        System.out.println("Animal created: " + type);
    }
}

class Dog extends Animal {
    String type = "Dog";    // hides parent's type

    Dog() {
        super("Mammal");    // 1. Call parent constructor (must be first)
    }

    void showType() {
        System.out.println(super.type);  // 2. Access parent variable → "Animal"
        System.out.println(this.type);   // "Dog"
    }

    void sound() {
        super.sound();       // 3. Call parent method
        System.out.println("Dog barks");
    }
}
```

---

## 20. Object-Oriented Programming (OOP)

### What is OOP?
Object-Oriented Programming (OOP) is a **programming paradigm that organizes software around objects** rather than functions and logic. An object represents a real-world entity containing both **data (attributes)** and **behavior (methods)**.

### Class vs Object
```java
// Class = Blueprint/Template (no memory allocated)
class Car {
    String model;
    int year;

    void start() {
        System.out.println(model + " started");
    }
}

// Object = Real instance (memory allocated in heap)
Car myCar = new Car();       // object creation
myCar.model = "Toyota";
myCar.year = 2024;
myCar.start();               // "Toyota started"
```

### Four Pillars of OOP

```
      ┌─────────────────────────────────┐
      │         4 Pillars of OOP        │
      ├──────────┬──────────┬───────────┤
      │Encap-    │Inher-    │Poly-      │
      │sulation  │itance    │morphism   │
      │          │          │           │
      │Data      │Code      │Many       │
      │Hiding    │Reuse     │Forms      │
      ├──────────┴──────────┴───────────┤
      │         Abstraction             │
      │     (Hide Complexity)           │
      └─────────────────────────────────┘
```

---

## 21. Encapsulation

### What is Encapsulation?
Encapsulation is the process of **binding data (variables) and methods together** into a single unit (class) and **restricting direct access** to the data. It is achieved by making variables `private` and providing `public` getter/setter methods.

```java
class Employee {
    private int id;
    private String name;
    private double salary;

    // Getter methods — read-only access
    public int getId() { return id; }
    public String getName() { return name; }
    public double getSalary() { return salary; }

    // Setter methods — controlled write access
    public void setId(int id) {
        if (id > 0) this.id = id;  // validation!
    }
    public void setName(String name) {
        if (name != null && !name.isEmpty()) this.name = name;
    }
    public void setSalary(double salary) {
        if (salary >= 0) this.salary = salary;  // cannot set negative salary
    }
}

Employee e = new Employee();
e.setSalary(50000);
System.out.println(e.getSalary());   // 50000.0
e.setSalary(-100);                   // rejected by validation
System.out.println(e.getSalary());   // still 50000.0
```

### Advantages of Encapsulation
```
✅ Data hiding — internal state protected from outside misuse
✅ Validation — setters can validate input before changing state
✅ Flexibility — internal implementation can change without affecting callers
✅ Security — sensitive data not directly accessible
✅ Maintainability — changes in one class don't break others
```

---

## 22. Inheritance

### What is Inheritance?
Inheritance is the mechanism where **one class acquires the properties and behaviors of another class**. The existing class is called the **parent/superclass**, and the new class is called the **child/subclass**. It promotes **code reusability**.

```java
class Animal {
    String name;

    void eat() {
        System.out.println(name + " is eating");
    }

    void breathe() {
        System.out.println("Breathing...");
    }
}

class Dog extends Animal {  // Dog inherits Animal
    void bark() {
        System.out.println("Woof!");
    }
}

Dog d = new Dog();
d.name = "Bruno";
d.eat();      // ✅ inherited from Animal
d.breathe();  // ✅ inherited from Animal
d.bark();     // ✅ Dog's own method
```

### Types of Inheritance

#### 1. Single Inheritance
```java
class A { }
class B extends A { }   // B inherits from A only
```

#### 2. Multilevel Inheritance
```java
class A { }
class B extends A { }
class C extends B { }   // C → B → A (chain)
```

#### 3. Hierarchical Inheritance
```java
class A { }
class B extends A { }   // B inherits A
class C extends A { }   // C also inherits A
class D extends A { }   // D also inherits A
```

#### 4. Multiple Inheritance (via Interface ONLY)
```java
// Java does NOT support multiple class inheritance
// class C extends A, B { }  ← ❌ COMPILE ERROR

// But supported through interfaces:
interface A { void methodA(); }
interface B { void methodB(); }
class C implements A, B {
    public void methodA() { }
    public void methodB() { }
}
```

#### 5. Hybrid Inheritance
```java
// Combination of multiple types
// Achieved through interfaces in Java
```

### Why Java Doesn't Support Multiple Inheritance with Classes (Diamond Problem)
```java
class A {
    void show() { System.out.println("A"); }
}
class B extends A {
    void show() { System.out.println("B"); }
}
class C extends A {
    void show() { System.out.println("C"); }
}
// class D extends B, C { }  ← ❌ Diamond Problem!
// Which show() would D.show() call? B's or C's?
// Java avoids this ambiguity by not allowing multiple class inheritance
```

### Important Inheritance Rules
```
✅ Child inherits all public and protected members
❌ Child does NOT inherit private members (they exist but are inaccessible)
❌ Constructors are NOT inherited
✅ super() can be used to call parent constructor
✅ Child can override parent methods
✅ Child can add new methods and variables
```

---

## 23. Polymorphism

### What is Polymorphism?
Polymorphism means **"many forms"**. It allows the same method, object, or interface to **behave differently depending on the context**. It increases flexibility, reusability, and dynamic behavior.

```
Poly = Many
Morph = Forms
→ One interface, multiple implementations
```

### Types of Polymorphism

| Type | Also Called | Achieved By | Resolved At |
|---|---|---|---|
| Compile-Time | Static Polymorphism | Method Overloading | Compile time |
| Runtime | Dynamic Polymorphism | Method Overriding | Runtime (JVM) |

### 1. Compile-Time Polymorphism — Method Overloading
```java
// Same method name, DIFFERENT parameters (type/count/order)
// Compiler decides which to call based on arguments

class Calculator {
    // Different number of parameters
    int add(int a, int b) {
        return a + b;
    }

    // Different parameter types
    double add(double a, double b) {
        return a + b;
    }

    // Different number of parameters
    int add(int a, int b, int c) {
        return a + b + c;
    }

    // Different order of types
    String add(String s, int n) {
        return s + n;
    }
}

Calculator c = new Calculator();
c.add(5, 10);          // calls int add(int, int)
c.add(5.0, 10.0);      // calls double add(double, double)
c.add(1, 2, 3);        // calls int add(int, int, int)
c.add("Value: ", 42);  // calls String add(String, int)
```

### 2. Runtime Polymorphism — Method Overriding
```java
// Child class provides its OWN implementation of parent's method
// JVM decides which to call at runtime based on actual object type

class Payment {
    void process() {
        System.out.println("Processing payment...");
    }
}

class UPIPayment extends Payment {
    @Override
    void process() {
        System.out.println("Processing via UPI");
    }
}

class CreditCardPayment extends Payment {
    @Override
    void process() {
        System.out.println("Processing via Credit Card");
    }
}

class NetBankingPayment extends Payment {
    @Override
    void process() {
        System.out.println("Processing via Net Banking");
    }
}

// Polymorphic reference — parent reference, child object
Payment p;

p = new UPIPayment();
p.process();            // "Processing via UPI"

p = new CreditCardPayment();
p.process();            // "Processing via Credit Card"

p = new NetBankingPayment();
p.process();            // "Processing via Net Banking"
```

### Method Overloading vs Method Overriding

| Feature | Method Overloading | Method Overriding |
|---|---|---|
| Definition | Same name, different parameters | Redefine parent method in child |
| Inheritance | Not required | Required |
| Parameters | Must be different | Must be same |
| Return Type | Can be different | Must be same or covariant |
| Polymorphism | Compile-time | Runtime |
| Binding | Static binding | Dynamic binding |
| Access Modifier | Any | Cannot reduce visibility |
| static methods | Can be overloaded | Cannot be overridden |
| final methods | Can be overloaded | Cannot be overridden |

### @Override Annotation
```java
class Animal {
    void sound() { System.out.println("Some sound"); }
}

class Cat extends Animal {
    @Override               // tells compiler: this is intentional override
    void sound() {          // if method name is wrong → compile error
        System.out.println("Meow");
    }
}
// @Override is optional but RECOMMENDED — catches typos at compile time
```

---

## 24. Abstraction

### What is Abstraction?
Abstraction is the process of **hiding internal implementation details and showing only essential functionalities** to the user. It reduces complexity and improves security by exposing only what is necessary.

```
Real-world example:
  ATM Machine
    → You see: Insert Card, Enter PIN, Withdraw Cash
    → Hidden: Bank network, encryption, database transactions
    → You use the functionality without knowing HOW it works
```

### Ways to Achieve Abstraction in Java

| Method | Abstraction Level |
|---|---|
| Abstract Class | Partial abstraction (0% - 100%) |
| Interface | Full abstraction (100%) — before Java 8 |

### Abstract Class
```java
// abstract keyword — cannot be instantiated
abstract class Shape {
    String color;

    // Abstract method — no body, MUST be implemented by subclass
    abstract double area();       // no { } !
    abstract double perimeter();

    // Concrete method — has implementation
    void setColor(String color) {
        this.color = color;
    }

    void display() {
        System.out.println("Color: " + color + ", Area: " + area());
    }
}

class Circle extends Shape {
    double radius;

    Circle(double r) { this.radius = r; }

    @Override
    double area() { return Math.PI * radius * radius; }

    @Override
    double perimeter() { return 2 * Math.PI * radius; }
}

class Rectangle extends Shape {
    double l, w;

    Rectangle(double l, double w) { this.l = l; this.w = w; }

    @Override
    double area() { return l * w; }

    @Override
    double perimeter() { return 2 * (l + w); }
}

// Shape s = new Shape();  ← ❌ Cannot instantiate abstract class!
Shape s = new Circle(5);   // ✅ abstract reference, concrete object
s.display();
```

### Abstract Class Rules
```
✅ Can have abstract AND concrete methods
✅ Can have constructors (used by subclass via super())
✅ Can have instance variables
✅ Can have static methods
❌ Cannot be instantiated (cannot use new AbstractClass())
❌ Subclass MUST implement all abstract methods (or be abstract itself)
```

---

## 25. Interfaces

### What is an Interface?
An interface is a **blueprint/contract** that defines a set of abstract methods that a class must implement. It is used to achieve **full abstraction** and **multiple inheritance** in Java.

```java
// Interface declaration
interface Drawable {
    // Variables: automatically public static final
    int VERSION = 1;  // same as: public static final int VERSION = 1;

    // Methods: automatically public abstract (before Java 8)
    void draw();
    void resize(int factor);
}

// Implementing the interface
class Circle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing circle");
    }

    @Override
    public void resize(int factor) {
        System.out.println("Resizing circle by " + factor);
    }
}
```

### Multiple Interface Implementation
```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {
    @Override
    public void fly() { System.out.println("Duck flying"); }

    @Override
    public void swim() { System.out.println("Duck swimming"); }
}
```

### Interface Features (Java 8+)
```java
interface Greetable {
    // Abstract method (as before)
    void greet(String name);

    // Default method (Java 8) — provides default implementation
    default void sayBye(String name) {
        System.out.println("Goodbye, " + name + "!");
    }

    // Static method (Java 8) — called on interface itself
    static void info() {
        System.out.println("This is Greetable interface");
    }

    // Private method (Java 9) — helper for default methods
    private void helper() {
        System.out.println("Private helper");
    }
}
```

### Types of Interfaces

#### 1. Normal Interface
```java
interface Vehicle {
    void start();
    void stop();
    void refuel();
}
```

#### 2. Functional Interface (Single Abstract Method)
```java
// Exactly ONE abstract method → can be used with Lambda expressions
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);  // only one abstract method
}

// Lambda usage
Calculator add = (a, b) -> a + b;
Calculator mul = (a, b) -> a * b;
System.out.println(add.calculate(5, 3));  // 8
```

#### 3. Marker Interface
```java
// Empty interface — marks a class with metadata
interface Serializable { }    // marks class as serializable
interface Cloneable { }       // marks class as cloneable
interface Remote { }          // marks class as remote object
```

---

## 26. Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---|---|---|
| Methods | Abstract + Concrete | Abstract + Default + Static (Java 8+) |
| Variables | Any type | public static final only |
| Constructor | ✅ Has constructor | ❌ No constructor |
| Inheritance | Single (extends one) | Multiple (implements many) |
| Access Modifiers | Any | public (by default) |
| Speed | Slightly faster | Slightly slower |
| Use When | "IS-A" relationship with shared code | "CAN-DO" capability contract |
| Example | `class Dog extends Animal` | `class Duck implements Flyable` |

### When to Use Which?
```
Abstract Class → When:
  ✅ Classes share common code/state
  ✅ You want to provide partial implementation
  ✅ You want non-public methods
  Example: Template Method design pattern

Interface → When:
  ✅ Unrelated classes need same behavior
  ✅ Multiple inheritance of type is needed
  ✅ You want to define a contract (API)
  Example: Comparable, Runnable, Serializable
```

---

## 27. Association, Aggregation, and Composition

### Association
```
Association represents a relationship between two independent classes.
Both objects can exist independently of each other.

Example: Teacher and Student
  → A teacher can have many students
  → A student can have many teachers
  → Both exist independently
```

```java
class Teacher {
    String name;
    List<Student> students;
}

class Student {
    String name;
    List<Teacher> teachers;
}
```

### Aggregation (HAS-A — Weak Relationship)
```
Aggregation is a weak association where one object CONTAINS another object.
The child object CAN EXIST independently of the parent.

Example: Department HAS Employees
  → Department is deleted → Employees still exist
  → Employee can exist without Department
```

```java
class Employee {
    String name;
}

class Department {
    String deptName;
    List<Employee> employees;   // Department HAS Employees
    // If Department is deleted, Employee objects still exist
}
```

### Composition (HAS-A — Strong Relationship)
```
Composition is a strong association where one object OWNS another.
The child object CANNOT EXIST without the parent.

Example: House HAS Rooms
  → House is destroyed → Rooms are also destroyed
  → Rooms cannot exist without the House
```

```java
class Room {
    String type;
    Room(String type) { this.type = type; }
}

class House {
    String address;
    Room[] rooms;  // House OWNS Rooms

    House(String address) {
        this.address = address;
        rooms = new Room[]{    // rooms created WITH house
            new Room("Bedroom"),
            new Room("Kitchen"),
            new Room("Hall")
        };
    }
    // When House object is garbage collected, Rooms go with it
}
```

### Summary

| Concept | Relationship | Object Lifetime |
|---|---|---|
| Association | Uses/knows | Independent |
| Aggregation | Has-A (weak) | Child can survive without parent |
| Composition | Has-A (strong) | Child destroyed with parent |

---

## 28. Exception Handling

### What is an Exception?
An exception is an **unexpected event that disrupts normal program flow** during runtime. Exception handling allows programs to catch and handle these events gracefully without crashing.

### Exception Hierarchy
```
Throwable
├── Error (serious system errors — don't catch these)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
│
└── Exception
    ├── Checked Exception (must handle at compile time)
    │   ├── IOException
    │   ├── FileNotFoundException
    │   ├── SQLException
    │   └── ClassNotFoundException
    │
    └── RuntimeException (Unchecked — detected at runtime)
        ├── NullPointerException
        ├── ArrayIndexOutOfBoundsException
        ├── ClassCastException
        ├── ArithmeticException (divide by zero)
        ├── NumberFormatException
        └── IllegalArgumentException
```

### try-catch-finally
```java
try {
    // Code that might throw exception
    int result = 10 / 0;           // ArithmeticException!
    System.out.println(result);    // never reached

} catch (ArithmeticException e) {
    // Handle specific exception
    System.out.println("Cannot divide by zero: " + e.getMessage());

} catch (Exception e) {
    // Handle any other exception (must be AFTER specific ones)
    System.out.println("Some error: " + e.getMessage());

} finally {
    // ALWAYS executes — whether exception occurs or not
    // Used for cleanup: close files, DB connections
    System.out.println("Finally block executed");
}
```

### Multiple Catch Blocks
```java
try {
    int[] arr = new int[5];
    arr[10] = 100;    // ArrayIndexOutOfBoundsException

} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Array error: " + e.getMessage());
} catch (NullPointerException e) {
    System.out.println("Null error: " + e.getMessage());
} catch (Exception e) {
    System.out.println("General error: " + e.getMessage());
}
```

### throw vs throws
```java
// throw: manually throw an exception
void validateAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}

// throws: declare that method MIGHT throw a checked exception
void readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);
    // ...
}

// Caller must handle it:
try {
    readFile("data.txt");
} catch (IOException e) {
    System.out.println("File not found");
}
```

### Custom Exception
```java
// Create your own exception
class InsufficientFundsException extends Exception {
    double amount;

    InsufficientFundsException(double amount) {
        super("Insufficient funds. Short by: " + amount);
        this.amount = amount;
    }
}

class BankAccount {
    double balance = 1000;

    void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException(amount - balance);
        }
        balance -= amount;
    }
}

// Usage
BankAccount acc = new BankAccount();
try {
    acc.withdraw(1500);
} catch (InsufficientFundsException e) {
    System.out.println(e.getMessage());
}
```

### try-with-resources (Java 7+)
```java
// Auto-closes resources (implements AutoCloseable)
try (FileReader fr = new FileReader("data.txt");
     BufferedReader br = new BufferedReader(fr)) {

    String line = br.readLine();
    System.out.println(line);
    // fr and br automatically closed — no need for finally!

} catch (IOException e) {
    System.out.println("Error: " + e.getMessage());
}
```

### Checked vs Unchecked Exceptions

| Feature | Checked Exception | Unchecked Exception |
|---|---|---|
| Also called | Compile-time exception | Runtime exception |
| Detected at | Compile time | Runtime |
| Must handle? | ✅ Yes (try-catch or throws) | ❌ No (optional) |
| Examples | IOException, SQLException | NullPointerException, ArrayIndexOutOfBoundsException |
| Extends | Exception (not RuntimeException) | RuntimeException |

---

## 29. Collections Framework

### What is the Collections Framework?
The Java Collections Framework is a **set of classes and interfaces** that provide ready-made data structures (like lists, sets, maps, queues) to store, retrieve, and manipulate groups of objects.

### Collections Hierarchy
```
java.util.Collection (Interface)
├── List (ordered, allows duplicates)
│   ├── ArrayList     → dynamic array, fast random access
│   ├── LinkedList    → doubly linked list, fast insert/delete
│   └── Vector        → thread-safe ArrayList (legacy)
│       └── Stack     → LIFO stack
│
├── Set (unordered, NO duplicates)
│   ├── HashSet       → hash table, no order
│   ├── LinkedHashSet → hash table + linked list, insertion order
│   └── TreeSet       → red-black tree, sorted order
│
└── Queue (FIFO order)
    ├── LinkedList    → also implements Queue
    ├── PriorityQueue → elements ordered by priority
    └── ArrayDeque    → double-ended queue

java.util.Map (Key-Value pairs, separate hierarchy)
├── HashMap          → hash table, no order, allows null key
├── LinkedHashMap    → insertion order maintained
├── TreeMap          → sorted by key
└── Hashtable        → thread-safe HashMap (legacy)
```

### ArrayList
```java
import java.util.ArrayList;

ArrayList<String> list = new ArrayList<>();

list.add("Alice");           // add at end
list.add("Bob");
list.add(0, "Charlie");      // add at index

list.get(0);                 // "Charlie"
list.set(1, "Dave");         // replace at index 1
list.remove("Alice");        // remove by value
list.remove(0);              // remove by index
list.size();                 // number of elements
list.contains("Bob");        // true/false
list.indexOf("Bob");         // index of element

// Iterate
for (String s : list) System.out.println(s);
list.forEach(System.out::println);  // Java 8+
```

### HashMap
```java
import java.util.HashMap;

HashMap<String, Integer> map = new HashMap<>();

map.put("Alice", 95);          // add entry
map.put("Bob", 87);
map.put("Alice", 99);          // overwrites previous value!

map.get("Alice");              // 99
map.getOrDefault("Carol", 0);  // 0 (not found)
map.containsKey("Bob");        // true
map.containsValue(87);         // true
map.remove("Bob");             // remove entry
map.size();                    // number of entries

// Iterate entries
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " → " + entry.getValue());
}

// Java 8+
map.forEach((key, value) -> System.out.println(key + " → " + value));
```

### List vs Set vs Map

| Feature | List | Set | Map |
|---|---|---|---|
| Duplicates | ✅ Allowed | ❌ Not allowed | Keys: unique, Values: allowed |
| Order | Maintains insertion order | No guaranteed order | Depends on implementation |
| Index access | ✅ Yes (get(i)) | ❌ No | By key |
| Null values | ✅ Allowed | One null allowed (HashSet) | One null key (HashMap) |
| Common Use | Ordered collection | Unique elements | Key-value pairs |

### Collections Utility Methods
```java
import java.util.Collections;

List<Integer> nums = new ArrayList<>(Arrays.asList(3, 1, 4, 1, 5, 9));

Collections.sort(nums);                    // ascending sort
Collections.sort(nums, Collections.reverseOrder()); // descending
Collections.shuffle(nums);                 // random shuffle
Collections.reverse(nums);                 // reverse order
Collections.max(nums);                     // maximum element
Collections.min(nums);                     // minimum element
Collections.frequency(nums, 1);            // count occurrences of 1
Collections.unmodifiableList(nums);        // immutable view
```

---

## 30. Generics

### What are Generics?
Generics allow you to write **type-safe, reusable code** by parameterizing classes, interfaces, and methods with types. They eliminate the need for casting and catch type errors at compile time.

```java
// Without generics — unsafe (runtime ClassCastException risk)
List list = new ArrayList();
list.add("Hello");
list.add(42);           // accidentally added Integer
String s = (String) list.get(1);  // ClassCastException at runtime!

// With generics — type-safe
List<String> list = new ArrayList<String>();
list.add("Hello");
list.add(42);           // ❌ COMPILE ERROR — caught early!
String s = list.get(0); // no cast needed
```

### Generic Class
```java
class Pair<T, U> {
    T first;
    U second;

    Pair(T first, U second) {
        this.first = first;
        this.second = second;
    }
}

Pair<String, Integer> p = new Pair<>("Alice", 95);
System.out.println(p.first + ": " + p.second);  // Alice: 95
```

### Generic Method
```java
public <T> void printArray(T[] arr) {
    for (T item : arr) System.out.print(item + " ");
}

printArray(new Integer[]{1, 2, 3});
printArray(new String[]{"a", "b", "c"});
```

### Bounded Wildcards
```java
// Upper bound: ? extends T — accepts T or subtype
List<? extends Number> nums;  // List<Integer> or List<Double>

// Lower bound: ? super T — accepts T or supertype
List<? super Integer> ints;  // List<Integer> or List<Number> or List<Object>

// Unbounded: ? — any type
List<?> anything;
```

---

## 31. Multithreading

### What is Multithreading?
Multithreading is a process where **multiple threads execute simultaneously** within a single program to perform multiple tasks concurrently. A thread is the **smallest unit of execution** inside a process.

### Process vs Thread

| Feature | Process | Thread |
|---|---|---|
| Definition | Independent program | Small execution unit inside a process |
| Memory | Separate memory space | Shared memory within process |
| Weight | Heavyweight | Lightweight |
| Communication | Inter-Process Communication (complex) | Shared memory (simple) |
| Creation | Expensive | Cheap |

### Thread Life Cycle
```
New        → Thread object created (Thread t = new Thread())
    ↓
Runnable   → t.start() called → ready to run
    ↓
Running    → Thread actually executing (CPU allocated)
    ↓          ↑
Waiting/   → t.wait(), t.sleep(), t.join() called
Blocked    
    ↓
Terminated → run() method completed, or exception occurred
```

### Ways to Create Threads

#### 1. Extending Thread Class
```java
class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 1; i <= 3; i++) {
            System.out.println(getName() + " → " + i);
        }
    }
}

MyThread t1 = new MyThread();
MyThread t2 = new MyThread();
t1.start();   // starts new thread, calls run() internally
t2.start();   // another thread
// ⚠️ NEVER call run() directly — that runs in current thread, not new one!
```

#### 2. Implementing Runnable Interface (Preferred)
```java
class MyTask implements Runnable {
    String name;

    MyTask(String name) { this.name = name; }

    @Override
    public void run() {
        for (int i = 1; i <= 3; i++) {
            System.out.println(name + " → " + i);
        }
    }
}

Thread t1 = new Thread(new MyTask("Task-1"));
Thread t2 = new Thread(new MyTask("Task-2"));
t1.start();
t2.start();

// Using Lambda (Java 8+)
Thread t3 = new Thread(() -> System.out.println("Lambda thread"));
t3.start();
```

### start() vs run()

| Method | Description |
|---|---|
| `start()` | Creates a new thread and calls `run()` in that new thread |
| `run()` | Executes in the CURRENT thread (no new thread created) |

### Important Thread Methods
```java
Thread t = new Thread(() -> {
    System.out.println("Running: " + Thread.currentThread().getName());
});

t.start();                  // Start the thread
t.setName("Worker-1");      // Set thread name
t.getName();                // Get thread name
t.getPriority();            // Get priority (1-10)
t.setPriority(8);           // Set priority
t.isAlive();                // Is thread still running?
t.isDaemon();               // Is it a daemon thread?
t.setDaemon(true);          // Set as daemon (before start!)

Thread.sleep(1000);         // Pause current thread for 1 second
Thread.currentThread();     // Reference to currently running thread

t.join();                   // Wait for t to finish before continuing
t.join(2000);               // Wait for t, but max 2 seconds
```

### sleep() Example
```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
    try {
        Thread.sleep(1000);   // pause 1 second between each print
    } catch (InterruptedException e) {
        System.out.println("Thread interrupted!");
    }
}
```

### join() Example
```java
Thread t1 = new Thread(() -> {
    for (int i = 1; i <= 5; i++)
        System.out.println("t1: " + i);
});

t1.start();
t1.join();   // main thread waits for t1 to finish
System.out.println("Main thread continues after t1 finishes");
```

### Thread Priority

| Constant | Value |
|---|---|
| `Thread.MIN_PRIORITY` | 1 |
| `Thread.NORM_PRIORITY` | 5 (default) |
| `Thread.MAX_PRIORITY` | 10 |

```java
Thread t1 = new Thread(() -> System.out.println("High priority"));
Thread t2 = new Thread(() -> System.out.println("Low priority"));
t1.setPriority(Thread.MAX_PRIORITY);  // 10
t2.setPriority(Thread.MIN_PRIORITY);  // 1
t1.start();
t2.start();
// Higher priority thread gets CPU preference (not guaranteed!)
```

### Daemon Thread
```java
// Background thread that supports main/user threads
// Automatically terminates when all user threads finish

Thread daemonThread = new Thread(() -> {
    while (true) {
        System.out.println("Daemon running...");
        try { Thread.sleep(500); } catch (InterruptedException e) { }
    }
});

daemonThread.setDaemon(true);  // must set BEFORE start()
daemonThread.start();

// When main thread ends, daemon thread stops automatically
// Examples: Garbage Collector, Log writers, Auto-save
```

### Synchronization
```java
// Problem: Race condition — multiple threads modify shared data
class Counter {
    int count = 0;

    // Without sync: count may be wrong due to race condition
    void increment() {
        count++;   // read-modify-write is NOT atomic!
    }
}

// Solution 1: synchronized method
class Counter {
    int count = 0;

    synchronized void increment() {  // only one thread at a time
        count++;
    }
}

// Solution 2: synchronized block (more fine-grained)
class Counter {
    int count = 0;

    void increment() {
        synchronized (this) {   // lock on 'this' object
            count++;
        }
    }
}
```

### Inter-Thread Communication
```java
// wait(), notify(), notifyAll() — must be called inside synchronized block

class Buffer {
    int item;
    boolean available = false;

    synchronized void produce(int item) throws InterruptedException {
        while (available) wait();   // wait if buffer is full
        this.item = item;
        available = true;
        notify();                   // wake up consumer
    }

    synchronized int consume() throws InterruptedException {
        while (!available) wait();  // wait if buffer is empty
        available = false;
        notify();                   // wake up producer
        return item;
    }
}
```

### Deadlock
```java
// Deadlock: two threads wait for each other's locked resources → neither proceeds

Object lockA = new Object();
Object lockB = new Object();

Thread t1 = new Thread(() -> {
    synchronized (lockA) {
        System.out.println("T1 holding A, waiting for B");
        synchronized (lockB) {      // waiting for t2 to release B
            System.out.println("T1 acquired both");
        }
    }
});

Thread t2 = new Thread(() -> {
    synchronized (lockB) {
        System.out.println("T2 holding B, waiting for A");
        synchronized (lockA) {      // waiting for t1 to release A
            System.out.println("T2 acquired both");
        }
    }
});

// Deadlock avoidance: always acquire locks in SAME order!
```

### Thread Pool (ExecutorService)
```java
import java.util.concurrent.*;

// Fixed thread pool — reuses 3 threads for all tasks
ExecutorService executor = Executors.newFixedThreadPool(3);

for (int i = 1; i <= 10; i++) {
    final int taskId = i;
    executor.execute(() -> {
        System.out.println("Task " + taskId + " by " + Thread.currentThread().getName());
    });
}

executor.shutdown();   // stop accepting new tasks
executor.awaitTermination(1, TimeUnit.MINUTES);  // wait for all to finish
```

### Callable and Future
```java
// Callable = like Runnable but returns a result
Callable<Integer> task = () -> {
    Thread.sleep(1000);
    return 42;   // returns value!
};

ExecutorService executor = Executors.newSingleThreadExecutor();
Future<Integer> future = executor.submit(task);

System.out.println("Doing other work...");
int result = future.get();   // blocks until result is ready
System.out.println("Result: " + result);  // 42

executor.shutdown();
```

### Runnable vs Callable

| Feature | Runnable | Callable |
|---|---|---|
| Return value | ❌ No (void) | ✅ Yes |
| Exception | Cannot throw checked exceptions | Can throw exceptions |
| Method | `run()` | `call()` |
| Use with | Thread, ExecutorService | ExecutorService only |

---

## 32. Java 8+ Features

### Lambda Expressions
```java
// Before Java 8: Anonymous class
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
};

// Java 8 Lambda: concise, readable
Runnable r = () -> System.out.println("Running");

// Lambda with parameters
Comparator<Integer> comp = (a, b) -> a - b;

// Lambda with body
Calculator add = (a, b) -> {
    int result = a + b;
    return result;
};
```

### Stream API
```java
import java.util.stream.*;

List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Filter even numbers, square them, collect to list
List<Integer> result = numbers.stream()
    .filter(n -> n % 2 == 0)          // keep only even
    .map(n -> n * n)                   // square each
    .collect(Collectors.toList());     // collect results
// [4, 16, 36, 64, 100]

// Sum using reduce
int sum = numbers.stream()
    .reduce(0, Integer::sum);          // 55

// Count with filter
long count = numbers.stream()
    .filter(n -> n > 5)
    .count();                          // 5

// Find max
Optional<Integer> max = numbers.stream()
    .max(Integer::compareTo);          // Optional[10]

// Sorted and collected
List<String> names = Arrays.asList("Charlie", "Alice", "Bob");
List<String> sorted = names.stream()
    .sorted()
    .collect(Collectors.toList());
// [Alice, Bob, Charlie]
```

### Optional
```java
// Prevents NullPointerException
Optional<String> name = Optional.of("Alice");
Optional<String> empty = Optional.empty();
Optional<String> nullable = Optional.ofNullable(null);

name.isPresent();          // true
name.get();                // "Alice"
empty.isPresent();         // false
name.orElse("Unknown");    // "Alice"
empty.orElse("Unknown");   // "Unknown"
name.ifPresent(System.out::println);  // prints "Alice"
name.map(String::toUpperCase).get();  // "ALICE"
```

### Method References
```java
// Shorthand for lambda when lambda just calls a method

// Static method reference
Function<String, Integer> parse = Integer::parseInt;
parse.apply("42");  // 42

// Instance method reference
String s = "hello";
Supplier<String> upper = s::toUpperCase;
upper.get();  // "HELLO"

// Constructor reference
Supplier<ArrayList> listFactory = ArrayList::new;
ArrayList list = listFactory.get();

// Used in stream operations
List<String> names = Arrays.asList("alice", "bob");
names.stream().forEach(System.out::println);  // method reference
names.stream().map(String::toUpperCase).forEach(System.out::println);
```

### Default and Static Interface Methods
```java
interface Greeter {
    void greet();  // abstract

    default void bye() {      // default — provides implementation
        System.out.println("Goodbye!");
    }

    static Greeter create() { // static — utility factory method
        return () -> System.out.println("Hello!");
    }
}
```

### CompletableFuture (Java 8)
```java
import java.util.concurrent.CompletableFuture;

// Async execution
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    // runs in a separate thread
    return "Hello from async!";
});

future.thenAccept(result -> System.out.println(result));

// Chaining
CompletableFuture.supplyAsync(() -> "World")
    .thenApply(s -> "Hello, " + s)
    .thenAccept(System.out::println);   // Hello, World
```

### var — Local Variable Type Inference (Java 10)
```java
var name = "Alice";       // String inferred
var age = 25;             // int inferred
var list = new ArrayList<String>();  // ArrayList<String> inferred

// Useful for complex types
var map = new HashMap<String, List<Integer>>();
```

### Records (Java 16)
```java
// Concise immutable data classes
record Point(int x, int y) { }
// Automatically generates: constructor, getters, equals, hashCode, toString

Point p = new Point(3, 4);
System.out.println(p.x());      // 3
System.out.println(p.y());      // 4
System.out.println(p);          // Point[x=3, y=4]
```

### Virtual Threads (Java 21)
```java
// Lightweight threads managed by JVM (not OS threads)
// Can create millions of virtual threads without performance issues

Thread.startVirtualThread(() -> {
    System.out.println("Virtual Thread running");
});

// Or using executor
var executor = Executors.newVirtualThreadPerTaskExecutor();
executor.execute(() -> System.out.println("Task 1"));
```

---

## 33. Java Math Class

### Overview
The `Math` class (`java.lang.Math`) provides mathematical methods. Since `java.lang` is auto-imported, no import is needed. All methods are **static** — call directly without object.

### Complete Method Reference

| Method | Purpose | Example |
|---|---|---|
| `Math.abs(x)` | Absolute value | `abs(-5)` → 5 |
| `Math.max(a,b)` | Maximum | `max(3,7)` → 7 |
| `Math.min(a,b)` | Minimum | `min(3,7)` → 3 |
| `Math.pow(a,b)` | a to the power b | `pow(2,8)` → 256.0 |
| `Math.sqrt(x)` | Square root | `sqrt(16)` → 4.0 |
| `Math.cbrt(x)` | Cube root | `cbrt(27)` → 3.0 |
| `Math.ceil(x)` | Round up | `ceil(3.2)` → 4.0 |
| `Math.floor(x)` | Round down | `floor(3.9)` → 3.0 |
| `Math.round(x)` | Round nearest | `round(3.5)` → 4 |
| `Math.random()` | Random 0.0-1.0 | `random()` → 0.7342... |
| `Math.log(x)` | Natural log | `log(Math.E)` → 1.0 |
| `Math.log10(x)` | Base-10 log | `log10(100)` → 2.0 |
| `Math.exp(x)` | e^x | `exp(1)` → 2.718... |
| `Math.sin(x)` | Sine (radians) | `sin(Math.PI/2)` → 1.0 |
| `Math.cos(x)` | Cosine (radians) | `cos(0)` → 1.0 |
| `Math.tan(x)` | Tangent (radians) | `tan(Math.PI/4)` → 1.0 |
| `Math.toRadians(x)` | Degrees → radians | `toRadians(180)` → π |
| `Math.toDegrees(x)` | Radians → degrees | `toDegrees(Math.PI)` → 180.0 |
| `Math.signum(x)` | Sign of number | `signum(-5)` → -1.0 |
| `Math.hypot(a,b)` | √(a²+b²) | `hypot(3,4)` → 5.0 |

### Random Number Generation
```java
// Random double between 0.0 and 1.0
Math.random();

// Random int between 0 and 99
int num = (int)(Math.random() * 100);

// Random int between min and max (inclusive)
int min = 5, max = 15;
int rand = min + (int)(Math.random() * (max - min + 1));

// Using java.util.Random (more control)
Random r = new Random();
r.nextInt(100);      // 0 to 99
r.nextDouble();      // 0.0 to 1.0
r.nextBoolean();     // true or false
```

---

## 34. Garbage Collection

### What is Garbage Collection?
Garbage Collection (GC) is the **automatic memory management mechanism** in Java that identifies and removes objects that are no longer referenced by the program, freeing heap memory and preventing memory leaks.

```java
// Creating objects
Student s1 = new Student("Alice");
Student s2 = new Student("Bob");

s1 = null;   // "Alice" object now unreachable → eligible for GC
s1 = s2;     // previous s1 object eligible for GC
```

### How GC Identifies Objects to Collect
```
Reference Counting: (Java uses enhanced version)
  → Count how many references point to each object
  → Count reaches 0 → eligible for GC

Mark and Sweep:
  → Phase 1 (Mark): Start from "roots" (stack vars, static vars)
              follow all references, mark reachable objects
  → Phase 2 (Sweep): Collect all unmarked (unreachable) objects
```

### Heap Memory Structure
```
Heap
├── Young Generation (new objects created here)
│   ├── Eden Space      → all new objects start here
│   ├── Survivor S0     → survived one GC cycle
│   └── Survivor S1     → survived multiple GC cycles
│
└── Old Generation (Tenured)
    → Long-lived objects that survived multiple young GC cycles
    → Major GC (Full GC) happens here — more expensive
```

### Types of Garbage Collectors

| GC | Best For | Characteristics |
|---|---|---|
| Serial GC | Single-threaded apps | Simple, stop-the-world |
| Parallel GC | Multi-core, throughput | Multiple GC threads |
| G1 GC | Balanced (default Java 9+) | Low pause times |
| ZGC | Low latency apps | Sub-millisecond pauses |
| Shenandoah GC | Ultra-low latency | Concurrent compaction |

### System.gc() and finalize()
```java
// Request GC — not guaranteed to run immediately
System.gc();

// finalize() — called before GC collects object (DEPRECATED Java 9+)
@Override
protected void finalize() throws Throwable {
    System.out.println("Object being collected");
}
```

---

## 35. SOLID Principles

### S — Single Responsibility Principle
> A class should have **only ONE reason to change**. Each class should do exactly one job.

```java
// ❌ BAD — doing too many things
class Employee {
    void calculateSalary() { }    // business logic
    void saveToDB() { }           // database concern
    void generateReport() { }    // reporting concern
}

// ✅ GOOD — each class has ONE responsibility
class Employee { String name; double salary; }
class SalaryCalculator { void calculate(Employee e) { } }
class EmployeeRepository { void save(Employee e) { } }
class SalaryReportGenerator { void generate(Employee e) { } }
```

### O — Open/Closed Principle
> Software should be **open for extension but closed for modification**. Add new features by adding new code, not by changing existing code.

```java
// ✅ GOOD
interface Shape { double area(); }
class Circle implements Shape { public double area() { return Math.PI*r*r; } }
class Rectangle implements Shape { public double area() { return l*w; } }

// Adding Triangle doesn't require modifying existing code
class Triangle implements Shape { public double area() { return 0.5*b*h; } }
```

### L — Liskov Substitution Principle
> Objects of a subclass should be **replaceable with objects of the parent class** without breaking the program.

```java
// ✅ GOOD
class Bird { void fly() { } }
class Eagle extends Bird { void fly() { System.out.println("Eagle flies high"); } }
class Sparrow extends Bird { void fly() { System.out.println("Sparrow flies low"); } }

// Any Bird reference should work with Eagle or Sparrow
Bird b = new Eagle();
b.fly();  // works correctly — LSP followed
```

### I — Interface Segregation Principle
> **Don't force classes to implement interfaces they don't use**. Have many smaller, specific interfaces rather than one large, general interface.

```java
// ❌ BAD — forces Penguin to implement fly()
interface Animal {
    void eat();
    void fly();   // Penguin can't fly!
    void swim();
}

// ✅ GOOD — segregated interfaces
interface Eatable { void eat(); }
interface Flyable { void fly(); }
interface Swimmable { void swim(); }

class Eagle implements Eatable, Flyable { }      // only what Eagle can do
class Penguin implements Eatable, Swimmable { }  // only what Penguin can do
```

### D — Dependency Inversion Principle
> **Depend on abstractions, not concrete implementations**. High-level modules should not depend on low-level modules.

```java
// ❌ BAD — tightly coupled
class EmailNotification {
    void send(String msg) { System.out.println("Email: " + msg); }
}
class OrderService {
    EmailNotification notifier = new EmailNotification(); // hard dependency!
    void placeOrder() { notifier.send("Order placed"); }
}

// ✅ GOOD — depends on abstraction
interface Notifier { void send(String msg); }
class EmailNotification implements Notifier { public void send(String msg) { } }
class SMSNotification implements Notifier { public void send(String msg) { } }

class OrderService {
    Notifier notifier;  // depends on interface, not concrete class
    OrderService(Notifier notifier) { this.notifier = notifier; }
    void placeOrder() { notifier.send("Order placed"); }
}

// Can swap notification type without changing OrderService!
new OrderService(new EmailNotification());
new OrderService(new SMSNotification());
```

---

## 36. Design Patterns (Basics)

### Singleton Pattern
```java
// Ensures only ONE instance of a class exists globally
public class Singleton {
    private static Singleton instance;

    private Singleton() { }  // private constructor

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

// Thread-safe Singleton
public class ThreadSafeSingleton {
    private static volatile ThreadSafeSingleton instance;

    private ThreadSafeSingleton() { }

    public static ThreadSafeSingleton getInstance() {
        if (instance == null) {
            synchronized (ThreadSafeSingleton.class) {
                if (instance == null) {
                    instance = new ThreadSafeSingleton();
                }
            }
        }
        return instance;
    }
}
```

### Factory Pattern
```java
interface Animal { void speak(); }
class Dog implements Animal { public void speak() { System.out.println("Woof"); } }
class Cat implements Animal { public void speak() { System.out.println("Meow"); } }

class AnimalFactory {
    static Animal create(String type) {
        switch (type) {
            case "dog": return new Dog();
            case "cat": return new Cat();
            default: throw new IllegalArgumentException("Unknown: " + type);
        }
    }
}

Animal a = AnimalFactory.create("dog");
a.speak();  // Woof
```

### Builder Pattern
```java
class Person {
    private String name;
    private int age;
    private String email;

    private Person() { }

    static class Builder {
        private Person person = new Person();

        Builder name(String name) { person.name = name; return this; }
        Builder age(int age) { person.age = age; return this; }
        Builder email(String email) { person.email = email; return this; }
        Person build() { return person; }
    }
}

Person p = new Person.Builder()
    .name("Alice")
    .age(30)
    .email("alice@example.com")
    .build();
```

---

## 37. Common Interview Questions — Java

### Core Java

**Q1. What is the difference between == and .equals() in Java?**
```
== (reference comparison):
  → Compares memory addresses (references)
  → For objects: are they the SAME object in memory?
  → For primitives: are the VALUES equal?

.equals() (content comparison):
  → Compares actual content/values
  → Overridden in String, Integer, etc.
  → Should always use for String comparison

Example:
  String a = new String("hello");
  String b = new String("hello");
  a == b      → false (different objects in heap)
  a.equals(b) → true  (same content)
```

**Q2. What is the difference between ArrayList and LinkedList?**

| Feature | ArrayList | LinkedList |
|---|---|---|
| Storage | Dynamic array | Doubly linked list |
| Random access | O(1) — fast | O(n) — slow |
| Insert at middle | O(n) — slow (shifting) | O(1) — fast (pointer update) |
| Memory | Less (only data) | More (data + 2 pointers) |
| Best for | Read-heavy operations | Insert/delete-heavy operations |

**Q3. What is the difference between HashMap and Hashtable?**

| Feature | HashMap | Hashtable |
|---|---|---|
| Thread-safe | ❌ No | ✅ Yes (synchronized) |
| Null key | ✅ One allowed | ❌ Not allowed |
| Performance | Faster | Slower (sync overhead) |
| Introduced | Java 2 | Java 1 (legacy) |
| Recommended | ✅ Yes | ❌ Use ConcurrentHashMap instead |

**Q4. What is the difference between throw and throws?**
```
throw:
  → Used to MANUALLY throw an exception inside a method
  → Followed by an exception object
  → Example: throw new IllegalArgumentException("Invalid input");

throws:
  → Declares that a method MIGHT throw a checked exception
  → Written in method signature
  → Caller must handle it
  → Example: void readFile() throws IOException { }
```

**Q5. What is the difference between final, finally, and finalize?**
```
final:
  → Keyword to restrict modification
  → final variable → value cannot change
  → final method → cannot be overridden
  → final class → cannot be inherited

finally:
  → Block in exception handling
  → Always executes (whether exception occurs or not)
  → Used for cleanup code (close files, DB connections)

finalize():
  → Method called by GC before collecting an object
  → Deprecated since Java 9
  → Not guaranteed to run
```

**Q6. What is the difference between Comparable and Comparator?**

| Feature | Comparable | Comparator |
|---|---|---|
| Package | `java.lang` | `java.util` |
| Method | `compareTo(Object o)` | `compare(Object o1, Object o2)` |
| Purpose | Natural ordering | Custom/multiple orderings |
| Modifies class | Yes | No |
| Use | `Collections.sort(list)` | `Collections.sort(list, comparator)` |

```java
// Comparable — class defines its own ordering
class Student implements Comparable<Student> {
    int marks;
    public int compareTo(Student other) {
        return this.marks - other.marks;  // ascending by marks
    }
}

// Comparator — external, reusable comparison logic
Comparator<Student> byName = (s1, s2) -> s1.name.compareTo(s2.name);
Comparator<Student> byMarks = (s1, s2) -> s1.marks - s2.marks;
```

**Q7. What is the difference between interface and abstract class?**
> Refer to [Section 26](#26-abstract-class-vs-interface)

**Q8. What is a NullPointerException and how to avoid it?**
```java
// NPE occurs when you try to use a null reference
String s = null;
s.length();  // NullPointerException!

// Prevention:
// 1. Null check
if (s != null) { s.length(); }

// 2. Optional (Java 8+)
Optional.ofNullable(s).ifPresent(str -> str.length());

// 3. Objects.requireNonNull
Objects.requireNonNull(s, "s cannot be null");

// 4. Ternary
int len = (s != null) ? s.length() : 0;
```

**Q9. What is the String Pool?**
> The String Pool (also called String Intern Pool) is a special memory area inside the Heap where Java caches String literals. When you write `String s = "hello"`, Java first checks if "hello" already exists in the pool — if yes, it returns the existing reference instead of creating a new object. This saves memory. `new String("hello")` bypasses the pool and creates a new heap object.

**Q10. What is method hiding vs method overriding?**
```java
class Parent {
    static void staticMethod() { System.out.println("Parent static"); }
    void instanceMethod() { System.out.println("Parent instance"); }
}

class Child extends Parent {
    // Static methods are HIDDEN (not overridden)
    static void staticMethod() { System.out.println("Child static"); }

    // Instance methods are OVERRIDDEN
    @Override
    void instanceMethod() { System.out.println("Child instance"); }
}

Parent p = new Child();
p.staticMethod();   // "Parent static" — method hiding, resolved by reference type
p.instanceMethod(); // "Child instance" — overriding, resolved by object type
```

**Q11. What is the difference between Stack and Queue?**

| Feature | Stack | Queue |
|---|---|---|
| Order | LIFO (Last In, First Out) | FIFO (First In, First Out) |
| Add operation | push() | offer() / add() |
| Remove operation | pop() | poll() / remove() |
| Peek operation | peek() | peek() |
| Java class | `Stack<E>` / `Deque<E>` | `Queue<E>` / `LinkedList<E>` |

**Q12. What is the difference between checked and unchecked exceptions?**
> Refer to [Section 28](#28-exception-handling)

**Q13. What is object cloning in Java?**
```java
class Student implements Cloneable {
    int id;
    String name;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();  // shallow copy
    }
}

Student s1 = new Student(1, "Alice");
Student s2 = (Student) s1.clone();  // creates copy
```

**Q14. What is a static nested class vs inner class?**
```java
class Outer {
    // Inner class — requires Outer instance
    class Inner {
        void show() { System.out.println("Inner class"); }
    }

    // Static nested class — does NOT require Outer instance
    static class StaticNested {
        void show() { System.out.println("Static nested class"); }
    }
}

// Usage
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();          // needs outer object

Outer.StaticNested nested = new Outer.StaticNested();  // no outer needed
```

**Q15. What is the main method signature and why each keyword?**
```java
public static void main(String[] args)
```
```
public  → JVM must access it from anywhere — so public
static  → JVM calls it without creating an object — so static
void    → Returns nothing to JVM — so void
main    → JVM looks specifically for method named "main"
String[] args → Accepts command-line arguments as String array
```

**Q16. Can we overload the main method?**
> Yes, we can overload `main()` by changing its parameters, but the JVM will only recognize `public static void main(String[] args)` as the entry point. The other overloaded versions must be explicitly called.

**Q17. What happens when we call super() and this() in constructor?**
```
Rules:
  → super() and this() must be the FIRST statement in constructor
  → They CANNOT both appear in the same constructor
  → If neither is written, compiler inserts super() automatically
  → this() calls another constructor in SAME class
  → super() calls constructor in PARENT class
```

**Q18. What is covariant return type?**
```java
class Animal {
    Animal get() { return new Animal(); }
}

class Dog extends Animal {
    @Override
    Dog get() { return new Dog(); }  // return type is Dog (subtype of Animal)
    // This is covariant return type — allowed since Java 5
}
```

**Q19. Difference between HashSet, LinkedHashSet, and TreeSet?**

| Feature | HashSet | LinkedHashSet | TreeSet |
|---|---|---|---|
| Order | No order | Insertion order | Sorted (natural/comparator) |
| Performance | O(1) | O(1) | O(log n) |
| Null | One null allowed | One null allowed | No null (causes NPE) |
| Internal | HashMap | LinkedHashMap | Red-Black Tree |

**Q20. What is Serialization in Java?**
```java
// Serialization: Converting object → byte stream (save to file/network)
// Deserialization: byte stream → object (restore from file/network)

import java.io.*;

class Student implements Serializable {  // must implement Serializable
    int id;
    String name;
    transient String password;  // transient fields NOT serialized
}

// Serialize
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("student.ser"));
oos.writeObject(student);

// Deserialize
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("student.ser"));
Student s = (Student) ois.readObject();
```

---

## 🔧 Quick Reference Cheat Sheet

```java
// ── DATA TYPES ────────────────────────────────────
byte b = 10;   short s = 100;   int i = 1000;   long l = 9999L;
float f = 3.14f;   double d = 3.14;   char c = 'A';   boolean flag = true;

// ── STRING METHODS ───────────────────────────────
s.length()   s.charAt(0)   s.substring(1,3)   s.indexOf("a")
s.toUpperCase()   s.toLowerCase()   s.trim()   s.replace("a","b")
s.split(",")   s.contains("x")   s.equals("abc")   s.isEmpty()
String.valueOf(42)   Integer.parseInt("42")

// ── ARRAY ────────────────────────────────────────
int[] arr = new int[5];
int[] arr = {1,2,3,4,5};
Arrays.sort(arr);   Arrays.toString(arr);   Arrays.copyOf(arr,3);

// ── COLLECTIONS ──────────────────────────────────
ArrayList<String> list = new ArrayList<>();
list.add("a");   list.get(0);   list.remove(0);   list.size();

HashMap<String,Integer> map = new HashMap<>();
map.put("a",1);   map.get("a");   map.containsKey("a");   map.remove("a");

// ── EXCEPTION ────────────────────────────────────
try { } catch (Exception e) { } finally { }
throw new IllegalArgumentException("message");
void method() throws IOException { }

// ── THREAD ───────────────────────────────────────
Thread t = new Thread(() -> System.out.println("Running"));
t.start();   t.join();   Thread.sleep(1000);
synchronized void method() { }

// ── STREAM (JAVA 8) ──────────────────────────────
list.stream().filter(x -> x > 0).map(x -> x*2).collect(Collectors.toList());
list.stream().sorted().distinct().limit(5).forEach(System.out::println);

// ── OOP KEYWORDS ─────────────────────────────────
extends     → inheritance
implements  → interface
abstract    → abstract class/method
interface   → interface declaration
super       → parent class reference
this        → current class reference
final       → constant/no-override/no-inherit
static      → class-level member
instanceof  → type check
@Override   → marks overriding method
```

---

*📌 This guide covers Java from basics to expert level for interview preparation.*
*Last Updated: May 2026*