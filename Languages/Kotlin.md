# 🚀 Kotlin Interview Preparation Guide
> A complete, interview-ready reference covering Kotlin from basics to advanced topics.

---

## 📚 Table of Contents

1. [Kotlin Basics](#1-kotlin-basics)
2. [Variables — var, val, const](#2-variables--var-val-const)
3. [Null Safety](#3-null-safety)
4. [Data Types & Type System](#4-data-types--type-system)
5. [String Operations](#5-string-operations)
6. [Control Flow](#6-control-flow)
7. [Functions](#7-functions)
8. [OOP in Kotlin](#8-oop-in-kotlin)
9. [Visibility Modifiers](#9-visibility-modifiers)
10. [Collections](#10-collections)
11. [Lambdas & Higher-Order Functions](#11-lambdas--higher-order-functions)
12. [Extension Functions](#12-extension-functions)
13. [Scope Functions — let, run, with, also, apply](#13-scope-functions--let-run-with-also-apply)
14. [Coroutines](#14-coroutines)
15. [Flow & Channels](#15-flow--channels)
16. [Annotations & Processing](#16-annotations--processing)
17. [Advanced Kotlin Concepts](#17-advanced-kotlin-concepts)
18. [Kotlin vs Java Interop](#18-kotlin-vs-java-interop)
19. [Top Interview Questions & Answers](#19-top-interview-questions--answers)

---

## 1. Kotlin Basics

### What is Kotlin?
Kotlin is a modern, statically typed programming language from JetBrains. It is designed to be concise, safe, and fully interoperable with Java. Kotlin supports multiple platforms: JVM, Android, JavaScript, and native binaries.

### How does Kotlin work on Android?
Kotlin source code is compiled into Java bytecode, then converted to Dalvik bytecode (DEX) and executed on Android runtime (ART).

```text
Main.kt → Kotlin Compiler → MainKt.class → DEX → Android Runtime
```

Kotlin can also compile to JavaScript or native machine code using Kotlin/JS and Kotlin/Native.

### Why use Kotlin?
| Benefit | What it means |
|---|---|
| **Concise** | Reduces boilerplate and makes code easier to read |
| **Null-safe** | Nullability is part of the type system, reducing runtime crashes |
| **Interoperable** | Can call Java code and be called from Java without extra wrappers |
| **Expressive** | Supports lambdas, extension functions, data classes, and more |
| **Safe** | Encourages immutability and modern language features |
| **Multiplatform** | Share code across Android, backend, web, and native targets |

> Kotlin is often used on Android because it combines modern language features with strong Java interoperability.

---

## 2. Variables — `var`, `val`, `const`

### `var` vs `val`
`var` declares a mutable variable, while `val` declares a read-only reference.

```kotlin
var name = "John"
name = "Jane" // OK

val age = 25
// age = 30   // ❌ Compile-time error
```

A `val` reference cannot be reassigned, but the object it points to may still be mutable.

```kotlin
val numbers = mutableListOf(1, 2, 3)
numbers.add(4)      // OK
// numbers = mutableListOf(5, 6) // ❌ Not allowed
```

### Type inference
Kotlin automatically infers the type from the assigned value.

```kotlin
val title = "Kotlin"   // String
var count = 10          // Int
val pi = 3.14           // Double
```

Explicit typing is useful when declarations are separated from initialization.

```kotlin
val score: Long
score = 1000L
```

### `val` vs `const val`
`const val` is a compile-time constant and can only be used with primitive types and strings.

| Feature | `val` | `const val` |
|---|---|---|
| Runtime or compile time | Runtime | Compile time |
| Allowed types | Any type | Primitive types, String |
| Local declaration | Yes | No |
| Class member | Yes | Only in objects/companion objects |
| Generated as | `final` field | `static final` constant |

```kotlin
const val BASE_URL = "https://api.example.com"
val message = "Welcome, $user"
```

> `const val` is inlined into the bytecode, so it is slightly faster than `val` for constant values.

---

## 3. Null Safety

Kotlin’s type system distinguishes between nullable and non-nullable types to prevent null pointer exceptions at compile time.

### Nullable vs non-nullable types
```kotlin
var name: String = "Raj"
// name = null // ❌ Compile-time error

var nickName: String? = "Reddy"
nickName = null // ✅ Allowed
```

### Safe call operator `?.`
Access members safely when a value may be null.

```kotlin
println(nickName?.length) // Prints null if nickName is null
```

### Elvis operator `?:`
Provide a default when a nullable value is null.

```kotlin
val length = nickName?.length ?: 0
```

### Non-null assertion `!!`
Force a nullable value to be treated as non-null. Use sparingly.

```kotlin
val len = nickName!!.length // Throws if nickName is null
```

### Safe cast `as?`
Try to cast a value and return null on failure.

```kotlin
val anyValue: Any = "123"
val number = anyValue as? Int // null if cast fails
```

### Smart cast
After a successful type check, Kotlin automatically infers the narrowed type.

```kotlin
fun printText(value: Any) {
    if (value is String) {
        println(value.length) // Smart cast to String
    }
}
```

### `let` for nullable receivers
Run a block only when the value is non-null.

```kotlin
nickName?.let { name ->
    println("Name length: ${name.length}")
}
```

### `run` and `also` with null values
Use `run` to compute a result and `also` to perform side effects.

```kotlin
val result = nickName?.run { uppercase() } ?: "UNKNOWN"
nickName?.also { println("Nickname: $it") }
```

> Kotlin’s null safety is built into the type system, not just syntax. That is why `String` and `String?` are different types.

---

## 4. Data Types & Type System

### Built-in numeric and text types
| Kotlin | Example | JVM primitive | Notes |
|---|---|---|---|
| `Byte` | `val b: Byte = 10` | `byte` | 8-bit integer |
| `Short` | `val s: Short = 100` | `short` | 16-bit integer |
| `Int` | `val i = 123` | `int` | 32-bit integer |
| `Long` | `val l = 1_000_000L` | `long` | 64-bit integer |
| `Float` | `val f = 3.14f` | `float` | 32-bit floating point |
| `Double` | `val d = 2.71` | `double` | 64-bit floating point |
| `Char` | `val c = 'A'` | `char` | Single character |
| `Boolean` | `val ok = true` | `boolean` | true/false |
| `String` | `val text = "Hello"` | `String` | Immutable sequence of chars |

### `Any`, `Any?`, `Unit`, `Nothing`
- `Any` is the root of all non-nullable types.
- `Any?` can hold any type including `null`.
- `Unit` means the function returns no meaningful value.
- `Nothing` indicates code that never returns normally.

```kotlin
fun printMessage(): Unit {
    println("Hello")
}

fun fail(message: String): Nothing {
    throw IllegalStateException(message)
}
```

### Type inference
Kotlin usually figures out the type from the assigned value.

```kotlin
val city = "Bangalore" // inferred String
val count = 5           // inferred Int
```

Explicit type declarations are useful for clarity and delayed initialization.

```kotlin
val score: Double
score = 95.5
```

### Smart casting and type checks
Kotlin performs automatic casts after a successful `is` check.

```kotlin
fun describe(value: Any) {
    when (value) {
        is String -> println("String length ${value.length}")
        is Int -> println("Integer: $value")
        else -> println("Unknown type")
    }
}
```

### Type aliases
Give a readable name to a complex or repeated type.

```kotlin
typealias StringMap = Map<String, String>
val headers: StringMap = mapOf("Accept" to "application/json")
```

---

## 5. String Operations

### String Interpolation
Kotlin makes constructing strings easy with `$` placeholders.
```kotlin
val name = "MindOrks"
println("Hello! I am learning from $name")           // Simple variable
println("Name length: ${name.length}")               // Expression inside {}
```

### String Templates
Expressions can be embedded directly inside strings.
```kotlin
val score = 75
val result = "${if (score > 50) "Pass" else "Fail"}"
println(result) // Pass
```

### Multiline Strings
Use triple quotes for raw strings spanning multiple lines.
```kotlin
val text = """
    Line 1
    Line 2
    Line 3
""".trimIndent()
println(text)
```

### Raw Strings with `trimMargin()`
Use `trimMargin()` to strip a leading margin prefix from each line.
```kotlin
val html = """
    |<html>
    |  <body>
    |    <p>Hello</p>
    |  </body>
    |</html>
""".trimMargin()
println(html)
```

### String Builder
For building larger strings efficiently.
```kotlin
val message = buildString {
    append("Hello")
    append(", ")
    append("World")
}
println(message) // Hello, World
```

### Destructuring
Extract multiple values from a data object or tuple-like structure.
```kotlin
data class Developer(val name: String, val age: Int)

val developer = Developer("Raj", 25)
val (name, age) = developer   // Destructuring
println(name)   // Raj
println(age)    // 25
```

---

## 6. Control Flow

### `if` as an Expression
In Kotlin, `if` can be used like an expression that returns a value.
```kotlin
val a = 10
val b = 20
val max = if (a > b) a else b
println(max) // 20
```

> 💡 **Kotlin has NO ternary operator (`? :`).** Use `if-else` or the Elvis operator instead.

### `when` Expression
`when` replaces Java's `switch` and is more expressive.
```kotlin
fun describe(value: Any): String = when (value) {
    1 -> "One"
    2, 3 -> "Two or Three"
    in 4..10 -> "Between 4 and 10"
    is String -> "It's a String"
    else -> "Something else"
}

val status = "SUCCESS"
val result = when (status) {
    "SUCCESS" -> "✅ Done"
    "ERROR" -> "❌ Failed"
    else -> "⏳ Pending"
}
println(result)
```

### Loops
```kotlin
// for loop
for (i in 1..10) { println(i) }          // Inclusive range
for (i in 1 until 10) { println(i) }     // Exclusive upper bound
for (i in 10 downTo 1 step 2) { println(i) }   // Reverse with step
for (item in listOf("A", "B", "C")) { println(item) }

// while loop
var count = 0
while (count < 3) {
    println(count)
    count++
}

// do-while loop — executes at least once
do {
    println("Do at least once")
    count--
} while (count > 0)
```

### `forEach`
```kotlin
val list = listOf("A", "B", "C")
list.forEach { println(it) }
list.forEachIndexed { index, value -> println("$index: $value") }
```

### `break` and `continue` with Labels
```kotlin
outer@ for (i in 1..5) {
    for (j in 1..5) {
        if (j == 3) break@outer    // breaks the outer loop
        if (j == 2) continue@outer // continues the outer loop
    }
}
```

### `return` behavior in lambdas
```kotlin
fun doWork() {
    listOf(1, 2, 3).forEach {
        if (it == 2) return      // returns from doWork(), not just lambda
        println(it)
    }
    println("Done")
}

doWork()
```

Use labeled returns when you want to exit only the lambda.
```kotlin
fun doWorkSafely() {
    listOf(1, 2, 3).forEach {
        if (it == 2) return@forEach
        println(it)
    }
    println("Done")
}

doWorkSafely()
```

---


## 7. Functions

### Regular Functions
```kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}

// Single-expression function
fun add(a: Int, b: Int): Int = a + b
```

### Default Arguments
```kotlin
fun greet(name: String, greeting: String = "Hello") {
    println("$greeting, $name!")
}
greet("Raj")           // Hello, Raj!
greet("Raj", "Hi")     // Hi, Raj!
```

### Named Arguments
```kotlin
greet(greeting = "Hey", name = "Raj")  // Order doesn't matter
```

### Variable Number of Arguments (`vararg`)
```kotlin
fun sum(vararg numbers: Int): Int = numbers.sum()
sum(1, 2, 3, 4)
```

### Lambda Functions
Anonymous functions treated as values.
```kotlin
val add: (Int, Int) -> Int = { a, b -> a + b }
val result = add(9, 10)   // 19

// Single parameter shortcut using `it`
val square: (Int) -> Int = { it * it }
```

### Lambda Structure
```kotlin
{ parameters -> body }
```

### Higher-Order Functions
A function that takes another function as a parameter or returns a function.
```kotlin
fun operate(a: Int, b: Int, action: (Int, Int) -> Int): Int {
    return action(a, b)
}
val result = operate(5, 3) { x, y -> x + y }  // 8
```

### Trailing Lambda Syntax
When a lambda is the last parameter, it can be placed outside the parentheses.
```kotlin
list.filter { it > 10 }     // Trailing lambda
// Same as:
list.filter({ it > 10 })
```

### `it` Keyword
Default name for a single lambda parameter.
```kotlin
val square: (Int) -> Int = { it * it }
```

### Inline Functions
Instructs the compiler to insert the function body at the call site — eliminates lambda overhead.
```kotlin
inline fun execute(block: () -> Unit) {
    block()
}
```

### `noinline`
Prevents a specific lambda parameter from being inlined.
```kotlin
inline fun doSomething(abc: () -> Unit, noinline xyz: () -> Unit) {
    abc()
    xyz()
}
```

### Infix Functions
Called without parentheses or dot notation.
```kotlin
class Operations {
    var x = 10
    infix fun minus(num: Int) {
        this.x = this.x - num
    }
}
val opr = Operations()
opr minus 8   // Infix call — no parentheses needed
```

### Reified Types
Access the actual type parameter inside an inline generic function.
```kotlin
inline fun <reified T> isInstance(value: Any): Boolean {
    return value is T
}
println(isInstance<String>("Hello"))   // true
```

### Pairs and Triples
Return two or three values from a function.
```kotlin
val pair = Pair("Name", 25)
println(pair.first)    // Name
println(pair.second)   // 25

val triple = Triple("A", 1, true)
println(triple.first)  // A
```

---

## 8. OOP in Kotlin

### Class
```kotlin
class Person(val name: String, var age: Int) {
    fun greet() = println("Hi, I'm $name")
}

val person = Person("Raj", 25)   // No `new` keyword needed
person.greet()
```

### `new` Keyword
> ❌ Kotlin does **NOT** use the `new` keyword. Objects are created as: `val obj = ClassName()`

### Constructors

**Primary Constructor** — defined in the class header.
```kotlin
class Person(val name: String, var age: Int)
```

**Secondary Constructor** — declared inside the body, must call primary constructor.
```kotlin
class Person(val name: String) {
    var age: Int = 0

    constructor(name: String, age: Int) : this(name) {
        this.age = age
    }
}
```

> 💡 Default type for constructor arguments is `val`. You can explicitly use `var`.

### `init` Block
Executed right after the primary constructor. Use it when you need to perform logic during construction.
```kotlin
class Person(val name: String) {
    init {
        println("Person created: $name")   // Runs on object creation
    }
}
```
> A class can have **multiple** `init` blocks — they execute in order.

### Data Classes
Designed to hold data. The compiler automatically generates `toString()`, `equals()`, `hashCode()`, and `copy()`.
```kotlin
data class Developer(val name: String, val age: Int)

val dev1 = Developer("Raj", 25)
val dev2 = dev1.copy(age = 26)   // Creates a copy with modified age
```
> Requirements: At least one parameter in the primary constructor.

### Sealed Classes
Restricts class hierarchy — all subclasses must be in the same file. Great for representing states.
```kotlin
sealed class Result {
    data class Success(val data: String) : Result()
    data class Error(val message: String) : Result()
    object Loading : Result()
}

// Exhaustive when — no else needed
when (result) {
    is Result.Success -> println(result.data)
    is Result.Error -> println(result.message)
    is Result.Loading -> println("Loading...")
}
```

### Enum Classes
Represents a fixed set of constants.
```kotlin
enum class Status {
    SUCCESS, ERROR, LOADING
}

enum class Direction(val degree: Int) {
    NORTH(0), EAST(90), SOUTH(180), WEST(270)
}
```

### Sealed Class vs Enum

| Feature | Sealed Class | Enum |
|---|---|---|
| State/data per type | ✅ Different data per subclass | ❌ Limited |
| Multiple instances | ✅ Yes | ❌ Only one per constant |
| `when` exhaustiveness | ✅ Compiler enforced | ✅ Yes |
| Flexibility | ✅ Very flexible | Less flexible |
| Best for | Complex states (API responses, UI states) | Simple constants |

### Object Declaration (Singleton)
Creates a single instance automatically — thread-safe.
```kotlin
object DatabaseManager {
    fun connect() { println("Connected") }
}
DatabaseManager.connect()   // Access directly — no instantiation
```
> **Note:** You cannot use a constructor with `object`, but you can use `init`.

### Companion Object
Allows defining static-like members inside a class.
```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
        const val TAG = "MyClass"
    }
}
MyClass.create()    // Called using class name
MyClass.TAG
```
> Only **one** companion object is allowed per class.

### Equivalent of Java `static` in Kotlin
- `companion object`
- Package-level functions
- `object` declaration

### Inheritance
All Kotlin classes are **final by default** — use `open` to allow inheritance.
```kotlin
open class Animal {
    open fun sound() {
        println("Some sound")
    }
}

class Dog : Animal() {
    override fun sound() {
        println("Bark")
    }
}
```

### Types of Inheritance in Kotlin

```kotlin
// Single Inheritance
open class Animal
class Dog : Animal()

// Multilevel Inheritance
open class Animal
open class Mammal : Animal()
class Dog : Mammal()

// Hierarchical Inheritance
open class Animal
class Dog : Animal()
class Cat : Animal()

// Multiple Inheritance via Interfaces
class Bird : Flyable, Swimmable  // Multiple interfaces allowed
```
> Kotlin does **NOT** support multiple class inheritance. Use interfaces instead.

### Abstract Class
Cannot be instantiated. Can have both abstract and implemented methods.
```kotlin
abstract class Animal {
    abstract fun sound()     // No implementation — must be overridden

    fun sleep() {            // Implemented — inherited as-is
        println("Sleeping...")
    }
}

class Dog : Animal() {
    override fun sound() {
        println("Bark")
    }
}
```

### Interface
A contract that classes must implement. Supports multiple inheritance and can have default implementations.
```kotlin
interface Flyable {
    fun fly()
    fun land() {              // Default implementation
        println("Landing...")
    }
}

class Bird : Flyable {
    override fun fly() {
        println("Flying!")
    }
}
```

### Abstract Class vs Interface

| Feature | Interface | Abstract Class |
|---|---|---|
| Methods | Abstract + default | Abstract + fully implemented |
| State (properties) | ❌ No backing fields | ✅ Can have properties |
| Constructor | ❌ Not allowed | ✅ Allowed |
| Multiple inheritance | ✅ Supported | ❌ Single only |
| Access modifiers | Public by default | All modifiers |
| Instantiation | ❌ No | ❌ No |
| Use case | Define behavior (capabilities) | Share common base logic |

### Encapsulation
Hiding internal data and exposing only what's needed.
```kotlin
class BankAccount(private var balance: Double) {
    fun deposit(amount: Double) {
        if (amount > 0) balance += amount
    }
    fun getBalance() = balance   // Controlled access
}
```

### Polymorphism

**Compile-time (Overloading)** — same function name, different parameters.
```kotlin
fun area(radius: Double): Double = Math.PI * radius * radius
fun area(length: Double, width: Double): Double = length * width
```

**Runtime (Overriding)** — child class provides a specific implementation.
```kotlin
open class Animal { open fun sound() = println("...") }
class Dog : Animal() { override fun sound() = println("Bark") }
class Cat : Animal() { override fun sound() = println("Meow") }
```

### Operator Overloading
Use the same operator with custom types.
```kotlin
data class Pen(val inkColor: String) {
    fun showInkColor() = println(inkColor)
}

operator fun Pen.plus(other: Pen): Pen {
    return Pen("${this.inkColor}, ${other.inkColor}")
}

val bluePen = Pen("Blue")
val blackPen = Pen("Black")
val combined = bluePen + blackPen   // Calls the plus operator
combined.showInkColor()             // Blue, Black
```

### Delegation
Delegate responsibilities to another class using the `by` keyword.
```kotlin
interface Printer {
    fun print()
}

class RealPrinter : Printer {
    override fun print() = println("Printing...")
}

class SmartPrinter(printer: Printer) : Printer by printer   // Delegates to printer
```

### `lateinit`
Promise to initialize a non-null variable later.
```kotlin
class MyActivity {
    lateinit var textView: TextView

    fun setup() {
        textView = TextView(context)   // Initialize later
    }
}
```
- Only for `var` (not `val`)
- Only for non-primitive types
- Throws `UninitializedPropertyAccessException` if accessed before init

#### Checking `lateinit` initialization
```kotlin
if (::textView.isInitialized) {
    textView.text = "Hello"
}
```

### `lazy`
Initialize a value **only when first accessed** — deferred initialization.
```kotlin
val expensiveObject: HeavyObject by lazy {
    HeavyObject()   // Created only when first accessed
}
```
- Only for `val`
- Thread-safe by default
- Computed once and cached

### `lateinit` vs `lazy`

| Feature | `lateinit` | `lazy` |
|---|---|---|
| Variable type | `var` only | `val` only |
| Initialization | From outside / later | On first access |
| Thread safety | Not guaranteed | Thread-safe by default |
| Null check | `isInitialized` | N/A |
| Use case | Dependency injection, setup methods | Expensive computations, deferred init |

---

## 9. Visibility Modifiers

| Modifier | Same Class | Subclass | Same Module | Outside Module |
|---|---|---|---|---|
| `private` | ✅ | ❌ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ❌ | ❌ |
| `internal` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

> 💡 **Default visibility in Kotlin is `public`** (unlike Java where package-private is default).

---

## 10. Collections

### Overview
Collections are data structures used to store and manage groups of objects.

### Three Main Types

| Type | Description | Allows Duplicates | Ordered |
|---|---|---|---|
| `List` | Ordered collection with index access | ✅ Yes | ✅ Yes |
| `Set` | Unique elements only | ❌ No | ❌ Usually no |
| `Map` | Key-value pairs | ❌ No (keys) | ❌ Usually no |

### Mutable vs Immutable

```kotlin
// Immutable — read-only
val names = listOf("Raj", "Sam", "John")
val ids = setOf(1, 2, 3)
val map = mapOf("name" to "Raj", "age" to 25)

// Mutable — can add/remove elements
val names = mutableListOf("Raj", "Sam")
names.add("John")
names.remove("Sam")

val ids = mutableSetOf(1, 2, 3)
ids.add(4)

val map = mutableMapOf<String, Int>()
map["score"] = 100
```

### List
```kotlin
val numbers = listOf(1, 2, 3, 2, 1)  // Allows duplicates
println(numbers[0])                    // Access by index
println(numbers.size)
```

### Set
```kotlin
val numbers = setOf(1, 2, 3, 3, 2)   // Duplicates removed
// Output: [1, 2, 3]
```

### Map
```kotlin
val user = mapOf("name" to "Raj", "age" to 22)
println(user["name"])    // Raj
```

### Collection Operations (Very Important)

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

// filter — returns elements matching condition
val evens = numbers.filter { it % 2 == 0 }   // [2, 4]

// map — transforms each element
val doubled = numbers.map { it * 2 }          // [2, 4, 6, 8, 10]

// find — returns first matching element
val first = numbers.find { it > 3 }           // 4

// any — returns true if any element matches
val hasLarge = numbers.any { it > 4 }         // true

// all — returns true if all elements match
val allPositive = numbers.all { it > 0 }      // true

// count — counts elements matching condition
val evenCount = numbers.count { it % 2 == 0 } // 2

// reduce — aggregates all elements into one
val sum = numbers.reduce { acc, x -> acc + x } // 15

// forEach — loop through each element
numbers.forEach { println(it) }

// sorted / sortedBy
val sorted = numbers.sorted()
val sortedDesc = numbers.sortedDescending()

// groupBy — groups elements by a key
val grouped = numbers.groupBy { if (it % 2 == 0) "even" else "odd" }

// partition — splits into two lists
val (evens2, odds) = numbers.partition { it % 2 == 0 }

// flatMap — flatten nested collections
val nested = listOf(listOf(1, 2), listOf(3, 4))
val flat = nested.flatMap { it }   // [1, 2, 3, 4]

// zip — combine two lists into pairs
val zipped = listOf(1, 2, 3).zip(listOf("A", "B", "C"))  // [(1,A), (2,B), (3,C)]

// distinct — removes duplicates
val distinct = listOf(1, 2, 2, 3).distinct()  // [1, 2, 3]
```

### `map` vs `flatMap`

| Operation | `map` | `flatMap` |
|---|---|---|
| Purpose | Transform each element | Flatten nested collections |
| Input | `List<T>` | `List<List<T>>` |
| Output | `List<R>` | `List<R>` (flat) |

### List vs Array

| Feature | Array | List |
|---|---|---|
| Size | Fixed | Dynamic (MutableList) |
| Flexibility | Less flexible | More flexible |
| Operations | Fewer built-in ops | Rich functional API |

### Sequences (Lazy Collections)
Process data lazily — values are computed one by one, only when needed. More memory-efficient for large datasets.
```kotlin
val result = (1..1_000_000)
    .asSequence()
    .filter { it % 2 == 0 }
    .map { it * 3 }
    .take(10)
    .toList()
```

### Collection Time Complexity

| Operation | List | Set | Map |
|---|---|---|---|
| Access | O(1) | O(1) avg | O(1) avg |
| Search | O(n) | O(1) avg | O(1) avg |
| Insert | O(1) avg | O(1) avg | O(1) avg |

### Best Practices
- ✅ Use **immutable** collections whenever possible
- ✅ Use `Set` for **unique values**
- ✅ Use `Map` for **lookup data**
- ✅ Use `Sequence` for **large datasets**

---

## 11. Lambdas & Higher-Order Functions

### Lambda
An anonymous function used as a value or parameter.
```kotlin
val sum = { a: Int, b: Int -> a + b }
println(sum(5, 3))   // 8

// Single parameter — use `it`
val square: (Int) -> Int = { it * it }
```

### Higher-Order Function
Takes a function as parameter or returns a function.
```kotlin
// Takes function as parameter
fun operate(a: Int, b: Int, action: (Int, Int) -> Int): Int = action(a, b)

// Returns a function
fun getMultiplier(factor: Int): (Int) -> Int = { it * factor }
val double = getMultiplier(2)
println(double(5))   // 10
```

### Function vs Lambda

| Feature | Function | Lambda |
|---|---|---|
| Name | Has a name | Anonymous |
| Declaration | `fun` keyword | `{ }` braces |
| Return type | Explicit | Inferred |
| Usage | Traditional | Functional programming |

### `it` — Default Single Parameter Name
```kotlin
val doubled = listOf(1, 2, 3).map { it * 2 }
```

### Trailing Lambda
When a lambda is the last parameter, write it outside the parentheses.
```kotlin
button.setOnClickListener {
    println("Clicked!")
}
```

---

## 12. Extension Functions

Add new functionality to an existing class **without modifying or inheriting it**.
```kotlin
fun String.addExclamation(): String = this + "!"
fun String.isPalindrome(): Boolean = this == this.reversed()

println("Hello".addExclamation())     // Hello!
println("racecar".isPalindrome())     // true

// Android example
fun View.show() { this.visibility = View.VISIBLE }
fun View.hide() { this.visibility = View.GONE }
toolbar.hide()
```

> 💡 Extension functions are **statically resolved** — they don't actually modify the class.

### Extension Properties
```kotlin
val String.wordCount: Int get() = this.split(" ").size
println("Hello World".wordCount)   // 2
```

---

## 13. Scope Functions — `let`, `run`, `with`, `also`, `apply`

These functions allow executing a block of code in the context of an object.

### Quick Reference Table

| Function | Context object | Return value | Use case |
|---|---|---|---|
| `let` | `it` | Lambda result | Null safety, transform |
| `run` | `this` | Lambda result | Compute using object |
| `with` | `this` | Lambda result | Multiple operations on object |
| `also` | `it` | Same object | Side effects (logging) |
| `apply` | `this` | Same object | Object initialization |

### `let` — Null safety and transformation
```kotlin
val name: String? = "Raj"
name?.let {
    println(it.length)   // Only runs if name is not null
}
val upper = name?.let { it.uppercase() }   // Returns uppercase or null
```

### `run` — Compute result using object context
```kotlin
val result = "Hello".run {
    length + 10   // `this` is the String
}   // result = 15
```

### `with` — Multiple operations on same object
```kotlin
val result = with(StringBuilder()) {
    append("Hello ")
    append("World")
    toString()   // Returns the result
}
```

### `also` — Side effects (logging, debugging)
```kotlin
val list = mutableListOf(1, 2, 3)
    .also { println("Before: $it") }
    .apply { add(4) }
    .also { println("After: $it") }
// also returns the same object, uses `it`
```

### `apply` — Object initialization and configuration
```kotlin
val user = User().apply {
    name = "Raj"    // `this` is the User object
    age = 25
}
// Returns the same object (User)
```

> 💡 **Memory trick:** `apply` & `also` return **same object**. `let`, `run`, `with` return **lambda result**. `apply` & `run` use **`this`**. `let` & `also` use **`it`**.

---

## 14. Coroutines

### What are Coroutines?
Coroutines are a framework to manage **concurrency in a lightweight, non-blocking way**. A coroutine is like a lightweight thread that can **pause (suspend) and resume** without blocking an actual thread.

### Why Coroutines?
- No callback hell
- Cleaner, sequential-looking async code
- Lightweight — thousands of coroutines can run on a few threads
- Built-in cancellation and error handling

### `suspend` Function
A function that can **pause execution** without blocking the thread and resume later.
```kotlin
suspend fun fetchData(): String {
    delay(1000)   // Suspends (not blocks) for 1 second
    return "Data fetched"
}
```
> `suspend` functions can only be called from other `suspend` functions or coroutine builders.

### Coroutine Builders

#### `launch` — Fire and Forget
Starts a coroutine that does not return a result.
```kotlin
val job = CoroutineScope(Dispatchers.Main).launch {
    println("Running task")
}
// Returns: Job
```

#### `async` — Returns a Result
Starts a coroutine and returns a `Deferred<T>` — use `.await()` to get the result.
```kotlin
val deferred = CoroutineScope(Dispatchers.IO).async {
    fetchData()
}
val result = deferred.await()   // Waits for result
```

#### `runBlocking` — Bridges Blocking and Non-blocking Code
Blocks the current thread until all coroutines inside complete. Used mainly in tests or `main()`.
```kotlin
runBlocking {
    delay(1000)
    println("Done")
}
// ⚠️ Avoid on Android UI thread — freezes the app
```

#### `withContext` — Switch Thread and Return Result
Suspends the coroutine, switches to a different dispatcher, executes, and returns.
```kotlin
val result = withContext(Dispatchers.IO) {
    fetchDataFromNetwork()   // Runs on IO thread
}
// result available on original thread
```

#### `coroutineScope` — Wait for All Children
Creates a scope and waits for all launched coroutines to complete.
```kotlin
coroutineScope {
    launch { work1() }
    launch { work2() }
}   // All children must complete before continuing
```

### `launch` vs `async`

| Feature | `launch` | `async` |
|---|---|---|
| Returns | `Job` | `Deferred<T>` |
| Result | ❌ No | ✅ Yes (via `.await()`) |
| Use case | Fire-and-forget | Parallel work with result |

### Dispatchers
Determines **which thread** a coroutine runs on.

| Dispatcher | Thread | Use For |
|---|---|---|
| `Dispatchers.Main` | UI/Main thread | UI updates, View changes |
| `Dispatchers.IO` | Background thread pool | Network calls, DB, File I/O |
| `Dispatchers.Default` | CPU thread pool | Heavy computation, sorting, image processing |
| `Dispatchers.Unconfined` | Current thread (unpredictable) | Testing, advanced use — avoid in production |

```kotlin
viewModelScope.launch {
    val users = withContext(Dispatchers.IO) {
        api.getUsers()          // Fetch on IO thread
    }
    _uiState.value = users      // Update on Main thread
}
```

### Structured Concurrency
Coroutines are organized in a **parent-child hierarchy**. When a parent is cancelled, all children are cancelled automatically.

```kotlin
val job = CoroutineScope(Dispatchers.Main).launch {
    launch { child1() }    // Child 1
    launch { child2() }    // Child 2
}
job.cancel()   // Cancels job + child1 + child2
```

### Job and Cancellation
```kotlin
val job = launch { /* long task */ }
job.cancel()       // Cancel the coroutine
job.join()         // Wait for cancellation to complete

// Check if still active inside a coroutine
while (isActive) {
    // cooperative work
}
```

### Cooperative Cancellation
Coroutines **only stop at suspension points** (like `delay()`, `yield()`, `await()`).

```kotlin
val job = launch {
    while (isActive) {     // Check cancellation manually
        doWork()
        yield()            // Allow cancellation to happen
    }
}
```

### `ensureActive()`
Throws `CancellationException` if the coroutine is cancelled.
```kotlin
ensureActive()   // Stops execution immediately if cancelled
```

### `NonCancellable`
Use to ensure critical cleanup runs even if the coroutine is cancelled.
```kotlin
withContext(NonCancellable) {
    db.saveData()   // Always runs, even during cancellation
}
```

### Timeout
```kotlin
withTimeoutOrNull(3000L) {
    // Automatically cancelled after 3 seconds
    fetchData()
}
```

### Sequential vs Concurrent Execution
```kotlin
// Sequential — total time = time1 + time2
val one = fetchOne()   // Waits for this to complete
val two = fetchTwo()   // Then runs this

// Concurrent — total time = max(time1, time2)
val one = async { fetchOne() }
val two = async { fetchTwo() }
val result = one.await() + two.await()
```

### Coroutine Context
The `CoroutineContext` defines the execution environment of a coroutine.
```kotlin
launch(Dispatchers.Default + CoroutineName("MyTask") + exceptionHandler) {
    // Multiple context elements combined with +
}
```

### Exception Handling
```kotlin
val handler = CoroutineExceptionHandler { _, exception ->
    println("Caught: $exception")
}

CoroutineScope(Dispatchers.Main + handler).launch {
    throw RuntimeException("Error!")
}

// try-catch inside coroutine
launch {
    try {
        riskyOperation()
    } catch (e: Exception) {
        println("Handled: $e")
    }
}
```

### `delay()` vs `Thread.sleep()`

| Feature | `delay()` | `Thread.sleep()` |
|---|---|---|
| Blocking | ❌ Non-blocking | ✅ Blocks the thread |
| Use inside | Coroutines only | Anywhere |
| Performance | ✅ Efficient | ❌ Wastes thread resources |

### Android Coroutine Scopes

| Scope | Lifecycle | Use case |
|---|---|---|
| `viewModelScope` | ViewModel lifecycle | ViewModel operations |
| `lifecycleScope` | Activity/Fragment lifecycle | UI-bound operations |
| `GlobalScope` | App lifetime | ⚠️ Avoid — not lifecycle-aware |

---

## 15. Flow & Channels

### What is Flow?
`Flow` is a **cold, asynchronous stream** that emits multiple values over time. It's part of Kotlin Coroutines.

```kotlin
fun simple(): Flow<Int> = flow {
    emit(1)
    delay(100)
    emit(2)
    emit(3)
}

runBlocking {
    simple().collect { value -> println(value) }
}
// Output: 1, 2, 3
```

### Flow is COLD
Nothing runs until `collect()` is called. Each `collect()` starts a fresh execution.
```kotlin
val flow = simple()   // Nothing happens yet
flow.collect()         // NOW it runs
flow.collect()         // Runs AGAIN — fresh execution
```

### Flow vs List vs Sequence

| Type | Sync/Async | Blocking | Use case |
|---|---|---|---|
| `List` | Sync | No delay | All data at once |
| `Sequence` | Sync | Blocking | Lazy CPU work |
| `Flow` | Async | Non-blocking | Streams, APIs, real-time data |

### Flow Builders
```kotlin
flow { emit(1); emit(2) }      // Custom flow
flowOf(1, 2, 3)                // From values
(1..5).asFlow()                // From range
listOf(1,2,3).asFlow()         // From collection
```

### Flow Operators

**Intermediate operators** — transform data (don't start execution):
```kotlin
flow
    .filter { it % 2 == 0 }         // Filter elements
    .map { it * 2 }                  // Transform elements
    .take(3)                         // Take only 3 elements
    .onEach { println("Got: $it") }  // Side effect without consuming
```

**Terminal operators** — start execution:
```kotlin
flow.collect { println(it) }    // Collect values
flow.toList()                   // Collect as List
flow.reduce { a, b -> a + b }  // Aggregate
flow.first()                    // First value
flow.count()                    // Count values
```

### Thread Context in Flow — `flowOn`
```kotlin
// ❌ Wrong — don't use withContext inside flow
flow {
    withContext(Dispatchers.Default) {
        emit(heavyComputation())   // Crash!
    }
}

// ✅ Correct — use flowOn
flow {
    emit(heavyComputation())
}.flowOn(Dispatchers.Default)   // Changes upstream thread
```

### Performance Operators
```kotlin
// buffer — emitter and collector run concurrently
flow.buffer()

// conflate — skip intermediate values, only latest matters
flow.conflate()

// collectLatest — cancel previous collection if new value arrives
flow.collectLatest { value ->
    delay(300)    // Previous work is cancelled when new value arrives
    process(value)
}
```

### Combining Flows
```kotlin
// zip — pairs values one-to-one
flow1.zip(flow2) { a, b -> "$a - $b" }

// combine — emits whenever either flow changes (uses latest values)
flow1.combine(flow2) { a, b -> "$a - $b" }
```

### Flattening Flows
```kotlin
// flatMapConcat — sequential, waits for previous to complete
// flatMapMerge — parallel, all run at same time
// flatMapLatest — cancels previous when new value arrives (most common in UI)
searchQuery.flatMapLatest { query ->
    searchApi(query)
}
```

### StateFlow & SharedFlow (Android-specific)

| Feature | `StateFlow` | `SharedFlow` |
|---|---|---|
| Cold/Hot | Hot | Hot |
| Replay | Last value (1) | Configurable |
| Initial value | Required | Not required |
| Use case | UI state | Events, one-time actions |

```kotlin
// StateFlow — always has a current value
private val _uiState = MutableStateFlow(UiState.Loading)
val uiState: StateFlow<UiState> = _uiState.asStateFlow()

// SharedFlow — for events
private val _events = MutableSharedFlow<Event>()
val events: SharedFlow<Event> = _events.asSharedFlow()
```

### What is a Channel?
A `Channel` is a **hot** communication mechanism that allows coroutines to send and receive values — like a pipe between coroutines.

```kotlin
val channel = Channel<Int>()

launch { channel.send(1) }      // Producer
launch { println(channel.receive()) }  // Consumer
```

### Channel vs Flow

| Aspect | Flow | Channel |
|---|---|---|
| Level | High-level | Low-level |
| Execution | Cold | Hot |
| Style | Declarative | Imperative |
| Use case | Data streams | Coroutine communication |

### Channel Types
```kotlin
Channel<Int>()                    // Rendezvous (default) — suspends until receiver ready
Channel<Int>(capacity = 4)        // Buffered — can store up to 4 items
Channel<Int>(Channel.UNLIMITED)   // Unlimited buffer
Channel<Int>(Channel.CONFLATED)   // Only latest value kept
```

---

## 16. Annotations & Processing

### What are Annotations?
Metadata added to code that provides instructions to the compiler, framework, or libraries.

```kotlin
@Entity
data class User(@PrimaryKey val id: Int, val name: String)
```

### Common JVM Interop Annotations

| Annotation | Purpose |
|---|---|
| `@JvmStatic` | Makes a method callable as a static method from Java |
| `@JvmOverloads` | Generates overloaded methods for default parameters in Java |
| `@JvmField` | Exposes a Kotlin property as a Java field (no getters/setters) |

```kotlin
class MyClass {
    companion object {
        @JvmStatic fun create() = MyClass()   // Java: MyClass.create()
    }

    @JvmField val name = "Raj"                // Java: obj.name (no getter)

    @JvmOverloads fun greet(name: String = "World") = println("Hello $name")
    // Java gets: greet() and greet(String)
}
```

### Kotlin vs Java Annotation Usage
- Kotlin has simplified syntax — no `@Override` (uses `override` keyword instead)
- Kotlin supports **use-site targets** for precise annotation placement:

```kotlin
@field:Inject lateinit var service: Service     // Targets the backing field
@get:JvmName("getName") val name: String = ""   // Targets the getter
@param:Named("userId") val id: String           // Targets constructor param
```

### KAPT vs KSP

| Feature | KAPT | KSP |
|---|---|---|
| Speed | Slower | Faster |
| Approach | Java-based (converts Kotlin → Java stubs) | Kotlin-native processing |
| Recommended | Older projects | Modern Android projects |

---

## 17. Advanced Kotlin Concepts

### Generics
```kotlin
fun <T> printItem(item: T) = println(item)

class Box<T>(val content: T)
val stringBox = Box("Hello")
val intBox = Box(42)
```

### Variance — `in` and `out`
```kotlin
// out (covariant) — can only produce T (return)
class Producer<out T>(private val value: T) {
    fun get(): T = value
}

// in (contravariant) — can only consume T (parameter)
class Consumer<in T> {
    fun process(value: T) { }
}
```

### Labels
A way to name a loop or expression for use with `break`, `continue`, or `return`.
```kotlin
loop@ for (i in 1..10) {
    for (j in 1..10) {
        if (j == 5) break@loop    // Break out of the outer loop
    }
}
```

### Idioms — Writing Idiomatic Kotlin

Idiomatic Kotlin means following the **recommended patterns** that make code clean, concise, and expressive.

```kotlin
// ✅ Idiomatic — use data class instead of POJO
data class User(val name: String, val age: Int)

// ✅ Idiomatic — use when instead of if-else chains
val message = when (status) {
    200 -> "OK"
    404 -> "Not Found"
    else -> "Unknown"
}

// ✅ Idiomatic — use ?: for null defaults
val name = user?.name ?: "Anonymous"

// ✅ Idiomatic — use apply for object setup
val dialog = AlertDialog.Builder(context).apply {
    setTitle("Confirm")
    setMessage("Are you sure?")
}.create()

// ✅ Idiomatic — use with for multiple method calls
with(sharedPreferences.edit()) {
    putString("key", "value")
    apply()
}
```

### Exception Handling in Kotlin
```kotlin
// try-catch as expression
val result = try {
    parseInt(input)
} catch (e: NumberFormatException) {
    -1   // Returns -1 on error
} finally {
    println("Always runs")
}

// Precondition functions
require(age >= 18) { "Must be 18+" }          // IllegalArgumentException
check(isLoggedIn) { "User not logged in" }    // IllegalStateException
error("Impossible state reached")             // IllegalStateException
```

### Exception Hierarchy
```
Throwable
├── Error (system-level — usually not handled)
│   ├── OutOfMemoryError
│   └── StackOverflowError
└── Exception (recoverable — should be handled)
    ├── IOException
    ├── NullPointerException
    ├── ArithmeticException
    └── IndexOutOfBoundsException
```

---

## 18. Kotlin vs Java Interop

### Calling Kotlin from Java
```kotlin
// Kotlin
class Utils {
    companion object {
        @JvmStatic fun doSomething() { }
    }
}
```
```java
// Java
Utils.doSomething();   // Works because of @JvmStatic
```

### Calling Java from Kotlin
```kotlin
// Kotlin seamlessly calls Java
val list = ArrayList<String>()   // Java class
list.add("Hello")
```

### `==` vs `===`

| Operator | Checks | Primitive types |
|---|---|---|
| `==` | Value equality (`equals()`) | Value |
| `===` | Reference equality | Value (no objects for primitives) |

```kotlin
val str1 = "Hello"
val str2 = "Hello"
println(str1 == str2)    // true — same value
println(str1 === str2)   // true — string interning (same reference)

val obj1 = User("Raj")
val obj2 = User("Raj")
println(obj1 == obj2)    // true — equals() (data class)
println(obj1 === obj2)   // false — different references
```

---

## 19. Top Interview Questions & Answers

### Basics

**Q: How does Kotlin work on Android?**
> Kotlin compiles to Java bytecode, which is executed by the JVM. A `Main.kt` file becomes `MainKt.class`.

**Q: What is the difference between `var` and `val`?**
> `var` is mutable (can be changed), `val` is immutable (read-only after assignment).

**Q: What is the difference between `val` and `const val`?**
> Both are immutable. `val` is assigned at runtime and can hold any type. `const val` is a compile-time constant, only for primitives and String, and is faster because the value is inlined at compile time.

**Q: Does Kotlin have a ternary operator?**
> No. Use `if-else` as an expression: `val result = if (x > 0) "positive" else "negative"` or the Elvis operator `?:` for null checks.

**Q: What is the Elvis operator?**
> `?:` returns the left value if not null, otherwise returns the right value. `val name = user?.name ?: "Unknown"`

**Q: What is the difference between `?.` and `!!`?**
> `?.` (safe call) returns null if the object is null. `!!` (non-null assertion) throws `KotlinNullPointerException` if null — use carefully.

---

### OOP

**Q: Can we use `new` in Kotlin?**
> No. Objects are created without `new`: `val obj = MyClass()`

**Q: What is a data class?**
> A class designed to hold data. The compiler auto-generates `equals()`, `hashCode()`, `toString()`, and `copy()`.

**Q: What is the difference between `object` and `class`?**
> `object` creates a singleton — only one instance exists. `class` can be instantiated multiple times.

**Q: What is a companion object?**
> It allows defining static-like members inside a class. Accessed using the class name: `MyClass.METHOD`.

**Q: What is the equivalent of Java static methods in Kotlin?**
> `companion object`, package-level functions, or `object` declarations.

**Q: Why are classes `final` by default in Kotlin?**
> To promote composition over inheritance and make code safer. Use `open` to allow inheritance.

**Q: Difference between abstract class and interface?**
> Abstract class can have state, constructor, and implemented methods. Interface has no constructor, no backing fields, but supports multiple inheritance and default implementations.

**Q: What is a sealed class?**
> A class with a restricted hierarchy — all subclasses must be in the same file. Used for representing a finite set of states with data (like API responses).

**Q: Difference between sealed class and enum?**
> Sealed class can hold different data per subclass and allows multiple instances. Enum is simpler — each constant is a single instance with no varying state.

---

### Null Safety & Types

**Q: What is the difference between `Any` and `Any?`?**
> `Any` is the root of all non-null types. `Any?` can also be null.

**Q: What is `Nothing` in Kotlin?**
> A type that represents functions that never return (always throw or run forever). Helps the compiler detect unreachable code.

**Q: What is `Unit` in Kotlin?**
> Equivalent to Java's `void` — for functions that return no meaningful value. But unlike `void`, `Unit` is an actual object.

---

### Functions & Lambdas

**Q: What is a higher-order function?**
> A function that takes another function as a parameter or returns a function.

**Q: What is `it` in Kotlin?**
> The implicit name for a single parameter in a lambda: `list.filter { it > 5 }`

**Q: What is an inline function?**
> Inline functions insert the function body at the call site, eliminating lambda object creation overhead.

**Q: What is `noinline`?**
> Prevents a specific lambda parameter from being inlined when the surrounding function is `inline`.

**Q: What is `reified` in Kotlin?**
> Allows accessing the actual type `T` at runtime inside an `inline` generic function.

---

### Coroutines

**Q: What is a coroutine?**
> A lightweight, non-blocking unit of execution that can pause and resume without blocking a thread.

**Q: What is a `suspend` function?**
> A function that can pause execution without blocking its thread and resume later. Must be called from a coroutine or another suspend function.

**Q: Difference between `launch` and `async`?**
> `launch` starts a coroutine and returns a `Job` (no result). `async` starts a coroutine and returns `Deferred<T>` — call `.await()` to get the result.

**Q: What is `withContext`?**
> Switches the coroutine to a different thread (dispatcher), executes the block, and returns the result.

**Q: What is the difference between `Dispatchers.IO` and `Dispatchers.Default`?**
> `IO` is for blocking tasks (network/database). `Default` is for CPU-intensive computation.

**Q: What is structured concurrency?**
> A principle where coroutines are organized in a parent-child hierarchy. When a parent is cancelled, all children are cancelled automatically.

**Q: What is cooperative cancellation?**
> Coroutines only stop at suspension points. You must check `isActive` or use suspension functions for cancellation to work.

**Q: Why avoid `GlobalScope`?**
> It's not tied to any lifecycle — coroutines outlive the component and can cause memory leaks.

---

### Collections

**Q: Difference between `List` and `Set`?**
> `List` allows duplicates and maintains order. `Set` only stores unique elements.

**Q: Difference between mutable and immutable collections?**
> Immutable collections are read-only. Mutable collections allow adding/removing elements.

**Q: Difference between `map()` and `flatMap()`?**
> `map` transforms each element. `flatMap` transforms and then flattens nested collections into a single list.

**Q: Why use `Sequence`?**
> For lazy evaluation — elements are processed one at a time, which is more memory-efficient for large datasets.

---

### Flow

**Q: What is Flow?**
> A cold asynchronous stream that emits multiple values over time. Nothing runs until `collect()` is called.

**Q: Flow vs LiveData?**
> Flow is Kotlin-native, supports operators, is cancellable, and works outside Android. LiveData is lifecycle-aware and simpler for basic UI observation.

**Q: What is `StateFlow`?**
> A hot flow that always holds a current state value and replays it to new collectors. Replaces `LiveData` in modern Android.

**Q: What is `SharedFlow`?**
> A hot flow for broadcasting events to multiple collectors. Used for one-time events like navigation or Snackbar.

**Q: What is `collectLatest`?**
> Cancels the previous collection when a new value arrives. Ideal for search-as-you-type.

**Q: What is `flowOn`?**
> Changes the upstream coroutine context (thread) for the flow. Never use `withContext` inside a flow block.

---

### Annotations

**Q: What is KAPT vs KSP?**
> KAPT is older and slower — converts Kotlin to Java stubs before processing. KSP is faster and Kotlin-native — recommended for modern Android.

**Q: What does `@JvmStatic` do?**
> Makes a companion object method accessible as a static method from Java code.

**Q: What does `@JvmField` do?**
> Exposes a Kotlin property as a Java field — no getter/setter generated.

---

### Advanced

**Q: What is `==` vs `===` in Kotlin?**
> `==` checks value equality (calls `equals()`). `===` checks reference equality.

**Q: What is operator overloading?**
> Redefining standard operators (`+`, `-`, `*`, etc.) for custom types using the `operator` keyword.

**Q: What is a type alias?**
> An alternative name for an existing type for better readability: `typealias StringList = List<String>`

**Q: What are Kotlin idioms?**
> Recommended, idiomatic ways to write clean, concise Kotlin code — things like using `apply` for object setup, `?:` for null defaults, and `when` instead of if-else chains.

---

## 📌 Quick Reference Cheat Sheet

```kotlin
// Variables
var x = 10              // Mutable
val y = 20              // Immutable
const val Z = "API"     // Compile-time constant

// Null Safety
var s: String? = null
val len = s?.length ?: 0    // Safe + fallback
val len2 = s!!.length        // Throws if null

// when expression
val msg = when(x) {
    1 -> "One"
    in 2..5 -> "Two to Five"
    else -> "Other"
}

// Data class
data class User(val name: String, val age: Int)

// Extension function
fun String.isPalindrome() = this == this.reversed()

// Lambda
val add: (Int, Int) -> Int = { a, b -> a + b }

// Coroutine
viewModelScope.launch {
    val data = withContext(Dispatchers.IO) { fetchData() }
    updateUI(data)
}

// Flow
fun getNumbers(): Flow<Int> = flow {
    emit(1); emit(2); emit(3)
}
getNumbers().collect { println(it) }

// Scope functions
val user = User().apply { name = "Raj"; age = 25 }
val result = user?.let { process(it) }
```

---

*Happy Coding & Best of Luck for your Interview! 🎯*