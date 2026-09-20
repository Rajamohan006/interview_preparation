# 📚 Java Collections — Arrays & Strings DSA Guide

> A unified reference for Java Arrays and Strings covering fundamentals, memory models, all built-in methods, DSA-critical patterns, and interview cheat sheets.

---

## 📑 Contents

- [Part 1 — Arrays](#part-1--arrays-in-java)
- [Part 2 — Strings](#part-2--strings-in-java)
- [Part 3 — Java Collection Framework](#part-3--java-collection-framework)

---

# Part 1 — Arrays in Java
# ðŸ“¦ Arrays in Java â€” Complete DSA Interview Guide

> A comprehensive guide covering Java array fundamentals, built-in methods, memory models, time complexity, and interview-critical operations.

---

## ðŸ“‘ Table of Contents

- [What is an Array?](#what-is-an-array)
- [How Java Arrays Work in Memory](#how-java-arrays-work-in-memory)
- [Advantages of Java Arrays](#advantages-of-java-arrays)
- [Limitations of Java Arrays](#limitations-of-java-arrays)
- [Array Operations & Methods](#array-operations--methods)
  - [1. Declaration](#1-declaration)
  - [2. Creating an Array](#2-creating-an-array)
  - [3. Initialize an Array](#3-initialize-an-array)
  - [4. Access an Element](#4-access-an-element)
  - [5. Update an Element](#5-update-an-element)
  - [6. Get Array Length](#6-get-array-length)
  - [7. Traverse Using `for`](#7-traverse-using-for)
  - [8. Traverse Using Enhanced `for`](#8-traverse-using-enhanced-for)
- [`java.util.Arrays` Methods](#javautilarrays-methods)
  - [9. `Arrays.toString()`](#9-arraystostring)
  - [10. `Arrays.sort()`](#10-arrayssort)
  - [11. `Arrays.binarySearch()`](#11-arraysbinarysearch)
  - [12. `Arrays.equals()`](#12-arraysequals)
  - [13. `Arrays.copyOf()`](#13-arrayscopyof)
  - [14. `Arrays.copyOfRange()`](#14-arrayscopyofrange)
  - [15. `Arrays.fill()`](#15-arraysfill)
  - [16. `Arrays.compare()`](#16-arrayscompare)
  - [17. `Arrays.mismatch()`](#17-arraysmismatch)
  - [18. `Arrays.deepToString()`](#18-arraysdeeptostring)
  - [19. `Arrays.deepEquals()`](#19-arraysdeepequals)
  - [20. Convert Array to Stream](#20-convert-array-to-stream)
- [Important Array Operations for DSA](#important-array-operations-for-dsa)
- [Most Important Methods to Remember](#most-important-methods-to-remember)
- [Array vs. ArrayList â€” Key Interview Distinction](#array-vs-arraylist--key-interview-distinction)
- [Arrays Class in Java (`java.util.Arrays`)](#-arrays-class-in-java-javautilarrays)
  - [Class Declaration](#class-declaration)
  - [Why Do We Need the Arrays Class?](#why-do-we-need-the-arrays-class)
  - [Methods With Examples](#methods-in-java-arrays-class--with-examples)
  - [Complete Methods Reference Table](#complete-methods-reference-table)
  - [`sort()` vs `parallelSort()`](#key-differences-sort-vs-parallelsort)
  - [`equals()` vs `deepEquals()`](#key-differences-equals-vs-deepequals)
  - [`toString()` vs `deepToString()`](#key-differences-tostring-vs-deeptostring)

---

## What is an Array?

> **An array is a collection of elements of the same data type stored in contiguous memory locations. It allows multiple values to be stored under a single name and accessed using an index.**

### Key Characteristics

- Java arrays can hold both **primitive types** (like `int`, `char`, `boolean`, etc.) and **objects** (like `String`, `Integer`, etc.).
- For primitive type arrays, elements are stored **directly in contiguous locations**.
- For non-primitive type arrays, **references** to items are stored at contiguous locations (the actual objects live elsewhere on the heap).
- After creating an array, its **size is fixed** â€” it cannot be changed.

### Code Example

```java
public class Geeks {

    public static void main(String[] args) {

        // Primitive array
        int[] arr = {10, 20, 30, 40};
        int n = arr.length;

        System.out.print("Primitive Array -> ");
        for (int i = 0; i < n; i++)
            System.out.print(arr[i] + " ");

        System.out.println();

        // Non-primitive array (String objects)
        String[] names = {"Lakshit", "Rahul", "Pankaj"};

        System.out.print("Non-Primitive Array -> ");
        for (int i = 0; i < names.length; i++)
            System.out.print(names[i] + " ");
    }
}
```

**Output:**

```text
Primitive Array -> 10 20 30 40 
Non-Primitive Array -> Lakshit Rahul Pankaj 
```

**Explanation:**

- The primitive array stores integer values and is traversed using a loop.
- The non-primitive array stores `String` objects and is printed using its `length` property.

---

## How Java Arrays Work in Memory

### Primitive Array

```text
int[] arr = {10, 20, 30, 40};

Memory (contiguous):
+----+----+----+----+
| 10 | 20 | 30 | 40 |
+----+----+----+----+
  [0]  [1]  [2]  [3]
```

### Non-Primitive (Object) Array

```text
String[] names = {"Lakshit", "Rahul", "Pankaj"};

Memory (contiguous references -> objects on heap):
+------+------+------+
| ref1 | ref2 | ref3 |   <- Contiguous references
+------+------+------+
    |       |       |
"Lakshit" "Rahul" "Pankaj"   <- Objects on heap
```

> **Senior Insight:** Because primitive array elements are stored contiguously in memory, iterating over them achieves excellent **CPU cache locality** â€” the CPU prefetches entire cache lines, reducing memory stalls. This is why `int[]` is often faster than `ArrayList<Integer>` in tight numerical loops.

---

## Advantages of Java Arrays

| Advantage | Detail |
|---|---|
| **Efficient Access** | Accessing an element by index is `O(1)` â€” memory address computed directly: `Base + (index Ã— elementSize)` |
| **Memory Management** | Fixed size makes memory allocation predictable and compact |
| **Data Organization** | Groups related elements under a single name with ordered indexing |
| **Cache Locality** | Contiguous storage maximizes CPU cache hits (especially for primitive arrays) |

---

## Limitations of Java Arrays

| Limitation | Better Alternative |
|---|---|
| **Fixed Size** â€” Cannot be changed after creation | Use `ArrayList` for dynamic resizing |
| **Type Homogeneity** â€” Only one data type per array | Use `Object[]`, Collections, or custom classes for mixed types |
| **Costly Insertion & Deletion** â€” Requires shifting elements | Use `LinkedList` for frequent insert/delete in the middle |

---

## Array Operations & Methods

> **Important:** A Java array (`int[]`, `String[]`, etc.) has very few built-in members. Most useful operations come from `java.util.Arrays`.

---

### 1. Declaration

```java
int[] numbers;       // Preferred style
```

or

```java
int numbers[];       // C-style (valid but not preferred)
```

---

### 2. Creating an Array

```java
int[] numbers = new int[5];
```

Creates an array with 5 elements. Default values are automatically assigned:

```text
[0, 0, 0, 0, 0]
```

| Type | Default Value |
|---|---|
| `int`, `long`, `short`, `byte` | `0` |
| `double`, `float` | `0.0` |
| `boolean` | `false` |
| Object (e.g., `String`) | `null` |

---

### 3. Initialize an Array

```java
int[] numbers = {10, 20, 30, 40, 50};
```

For Strings:

```java
String[] names = {"Raj", "John", "Alex"};
```

---

### 4. Access an Element

Java arrays use **zero-based indexing**.

```java
int[] numbers = {10, 20, 30, 40, 50};

System.out.println(numbers[0]);  // Output: 10
System.out.println(numbers[2]);  // Output: 30
```

**Time Complexity: `O(1)`**

Address formula: `Base + (index Ã— elementSize)`

---

### 5. Update an Element

```java
numbers[2] = 100;
```

Before:

```text
[10, 20, 30, 40, 50]
```

After:

```text
[10, 20, 100, 40, 50]
```

**Time Complexity: `O(1)`**

---

### 6. Get Array Length

`length` is a **property**, not a method (no parentheses).

```java
int[] numbers = {10, 20, 30, 40, 50};

System.out.println(numbers.length);  // Output: 5
```

### Important Interview Distinction

```java
array.length        // Array     -> property (no parentheses)
string.length()     // String    -> method (with parentheses)
list.size()         // ArrayList -> method (with parentheses)
```

---

### 7. Traverse Using `for`

```java
int[] numbers = {10, 20, 30, 40, 50};

for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

Use when you need the **index** during traversal.

---

### 8. Traverse Using Enhanced `for`

Also called the **for-each loop**.

```java
for (int number : numbers) {
    System.out.println(number);
}
```

Use when you **don't need the index** â€” cleaner and less error-prone.

---

## `java.util.Arrays` Methods

```java
import java.util.Arrays;
```

---

### 9. `Arrays.toString()`

Converts a one-dimensional array to a readable string.

```java
int[] numbers = {50, 20, 40, 10, 30};

System.out.println(Arrays.toString(numbers));
// Output: [50, 20, 40, 10, 30]
```

> Without `Arrays.toString()`, printing the array directly gives a cryptic memory reference like `[I@6d06d69c`.

---

### 10. `Arrays.sort()`

Sorts an array in **ascending order** in-place.

```java
int[] numbers = {50, 20, 40, 10, 30};

Arrays.sort(numbers);

System.out.println(Arrays.toString(numbers));
// Output: [10, 20, 30, 40, 50]
```

**Syntax:**

```java
Arrays.sort(array);
Arrays.sort(array, fromIndex, toIndex);  // Sort a range
```

**Time Complexity:** `O(n log n)` â€” uses Dual-Pivot Quicksort for primitives, TimSort for objects.

---

### 11. `Arrays.binarySearch()`

Searches for an element using binary search.

> **The array must be sorted first.**

```java
int[] numbers = {10, 20, 30, 40, 50};

int index = Arrays.binarySearch(numbers, 30);

System.out.println(index);  // Output: 2
```

If the element isn't found, returns a **negative value**.

**Syntax:**

```java
Arrays.binarySearch(array, key);
```

**Time Complexity:** `O(log n)`

---

### 12. `Arrays.equals()`

Checks if two arrays contain the **same elements in the same order**.

```java
int[] a = {10, 20, 30};
int[] b = {10, 20, 30};

System.out.println(Arrays.equals(a, b));  // true
```

```java
int[] a = {10, 20, 30};
int[] b = {30, 20, 10};

System.out.println(Arrays.equals(a, b));  // false
```

---

### 13. `Arrays.copyOf()`

Creates a new array with a specified length copied from the original.

```java
int[] numbers = {10, 20, 30};

int[] copy = Arrays.copyOf(numbers, 5);

System.out.println(Arrays.toString(copy));
// Output: [10, 20, 30, 0, 0]
```

If the new length is larger, remaining positions are filled with the default value.

**Syntax:**

```java
Arrays.copyOf(originalArray, newLength);
```

---

### 14. `Arrays.copyOfRange()`

Copies a specific range from an array.

```java
int[] numbers = {10, 20, 30, 40, 50};

int[] result = Arrays.copyOfRange(numbers, 1, 4);

System.out.println(Arrays.toString(result));
// Output: [20, 30, 40]
```

**Syntax:**

```java
Arrays.copyOfRange(array, from, to);
```

- `from` â€” inclusive
- `to` â€” exclusive

```text
index:     0   1   2   3   4
array:    10  20  30  40  50

                  ^       ^
                from     to (exclusive)

copyOfRange(numbers, 1, 4) -> [20, 30, 40]
```

---

### 15. `Arrays.fill()`

Fills all elements with a specified value.

```java
int[] numbers = new int[5];

Arrays.fill(numbers, 10);

System.out.println(Arrays.toString(numbers));
// Output: [10, 10, 10, 10, 10]
```

### Fill a Specific Range

```java
int[] numbers = {1, 2, 3, 4, 5};

Arrays.fill(numbers, 1, 4, 100);

System.out.println(Arrays.toString(numbers));
// Output: [1, 100, 100, 100, 5]
```

---

### 16. `Arrays.compare()`

Compares two arrays **lexicographically**.

```java
int[] a = {10, 20, 30};
int[] b = {10, 20, 40};

int result = Arrays.compare(a, b);

System.out.println(result);  // Negative (a < b)
```

| Return Value | Meaning |
|---|---|
| `0` | Arrays are equal |
| `< 0` | First array is lexicographically smaller |
| `> 0` | First array is lexicographically larger |

---

### 17. `Arrays.mismatch()`

Finds the **first index** where two arrays differ.

```java
int[] a = {10, 20, 30, 40};
int[] b = {10, 20, 50, 40};

int index = Arrays.mismatch(a, b);

System.out.println(index);  // Output: 2
```

```text
index:  0   1   2   3
a:     10  20  30  40
b:     10  20  50  40
                ^
             mismatch at index 2
```

Returns `-1` if arrays are equal.

---

### 18. `Arrays.deepToString()`

Prints **multidimensional arrays** in a readable format.

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6}
};

System.out.println(Arrays.deepToString(matrix));
// Output: [[1, 2, 3], [4, 5, 6]]
```

> Use `Arrays.toString()` for 1D arrays, `Arrays.deepToString()` for 2D/nD arrays.

---

### 19. `Arrays.deepEquals()`

Compares **multidimensional arrays** for equality.

```java
int[][] a = {
    {1, 2},
    {3, 4}
};

int[][] b = {
    {1, 2},
    {3, 4}
};

System.out.println(Arrays.deepEquals(a, b));  // true
```

---

### 20. Convert Array to Stream

For object arrays:

```java
String[] names = {"Raj", "John", "Alex"};

Arrays.stream(names)
      .forEach(System.out::println);
```

For primitive `int[]`:

```java
int[] numbers = {10, 20, 30, 40};

Arrays.stream(numbers)
      .forEach(System.out::println);
```

Stream aggregate operations:

```java
int sum    = Arrays.stream(numbers).sum();
int max    = Arrays.stream(numbers).max().getAsInt();
int min    = Arrays.stream(numbers).min().getAsInt();
double avg = Arrays.stream(numbers).average().getAsDouble();

System.out.println(sum);  // Output: 100
```

---

## Important Array Operations for DSA

These are the operations most frequently evaluated in coding interviews. You should know how to implement them **manually**, not just with built-in methods.

| Operation | Example | Time Complexity |
|---|---|---|
| Access | `arr[index]` | `O(1)` |
| Update | `arr[index] = value` | `O(1)` |
| Traverse | `for` loop | `O(n)` |
| Linear Search | Manual loop | `O(n)` |
| Binary Search | Sorted array | `O(log n)` |
| Insert at End | `arr[index] = value` | `O(1)` |
| Insert at Beginning | Shift all elements right | `O(n)` |
| Delete from End | Reduce logical size | `O(1)` |
| Delete from Beginning | Shift all elements left | `O(n)` |
| Sort | `Arrays.sort()` | `O(n log n)` typically |
| Copy | `Arrays.copyOf()` | `O(n)` |

> *For a fixed-size Java array, the physical array size cannot change. You need a new array to increase capacity.*

---

## Most Important Methods to Remember

```java
import java.util.Arrays;
```

### Basic Array Properties

```java
array.length              // Get size (property, not method)
array[index]              // Access element
array[index] = value;     // Update element
```

### `Arrays` Utility Methods â€” Interview Must-Know

```java
Arrays.toString(array);                    // Print 1D array

Arrays.sort(array);                        // Sort ascending

Arrays.binarySearch(array, key);           // Binary search (array must be sorted)

Arrays.equals(array1, array2);             // Compare 1D arrays

Arrays.copyOf(array, newLength);           // Copy with new length

Arrays.copyOfRange(array, from, to);       // Copy a range (to is exclusive)

Arrays.fill(array, value);                 // Fill all with value

Arrays.compare(array1, array2);            // Lexicographic compare

Arrays.mismatch(array1, array2);           // First differing index

Arrays.deepToString(matrix);               // Print 2D array

Arrays.deepEquals(matrix1, matrix2);       // Compare 2D arrays

Arrays.stream(array);                      // Convert to Stream
```

---

## Array vs. ArrayList â€” Key Interview Distinction

This is one of the **most common interview questions**.

| Feature | `int[] arr` (Array) | `ArrayList<Integer> list` |
|---|---|---|
| **Size** | Fixed after creation | Dynamic (resizes automatically) |
| **Type** | Primitive or Object | Object types only (autoboxing for primitives) |
| **Performance** | Faster for indexed access | Slightly slower due to autoboxing overhead |
| **Memory** | No boxing overhead | Boxing `int` to `Integer` adds object overhead |
| **Methods** | Very few (`length` property only) | Rich API (`add`, `remove`, `contains`, `get`, `set`, `size`, etc.) |

```java
// Fixed-size array
int[] arr = new int[5];

// Resizable collection
ArrayList<Integer> list = new ArrayList<>();
```

### Methods that belong to `ArrayList` (NOT regular arrays)

```java
list.add(element);
list.remove(index);
list.contains(element);
list.get(index);
list.set(index, value);
list.size();
list.isEmpty();
list.clear();
list.indexOf(element);
```

> **Interview Tip:** `ArrayList` internally uses an `Object[]` array. When it runs out of capacity, it creates a new array with **1.5x the previous capacity** and copies all elements over â€” amortized `O(1)` insertion.

---

## Quick Reference Cheat Sheet

```text
ARRAY PROPERTY vs METHOD:
   array.length       <- Property (no parentheses)
   string.length()    <- Method (with parentheses)
   list.size()        <- Method (with parentheses)

DEFAULT VALUES AFTER new int[n]:
   int[]     -> 0
   double[]  -> 0.0
   boolean[] -> false
   String[]  -> null

ARRAYS.SORT() ALGORITHM:
   Primitives -> Dual-Pivot Quicksort  O(n log n)
   Objects    -> TimSort               O(n log n)

INDEXING:
   First element -> arr[0]
   Last element  -> arr[arr.length - 1]
   Out of bounds -> ArrayIndexOutOfBoundsException
```

---

# ðŸ“š Arrays Class in Java (`java.util.Arrays`)

> The `Arrays` class in `java.util` is a utility class that provides static methods to perform operations like sorting, searching, comparing, and converting arrays. It **cannot be instantiated** and is used only for utility purposes.

---

## Class Declaration

`Arrays` is a `final` utility class in `java.util` package that extends `Object`:

```java
public final class Arrays
```

It **implicitly extends Object** and is not meant to be instantiated.

### How to use

```java
import java.util.Arrays;

// Call methods directly on the class
Arrays.sort(array_name);
Arrays.binarySearch(array_name, key);
```

---

## Why Do We Need the Arrays Class?

Java's `Arrays` utility class allows developers to perform common array operations easily and efficiently without writing boilerplate code:

| Need | Method |
|---|---|
| Fill an array with a particular value | `Arrays.fill()` |
| Sort an array | `Arrays.sort()` |
| Search in a sorted array | `Arrays.binarySearch()` |
| Compare two arrays | `Arrays.equals()`, `Arrays.compare()` |
| Copy an array | `Arrays.copyOf()`, `Arrays.copyOfRange()` |
| Convert to readable string | `Arrays.toString()` |
| Work with multidimensional arrays | `Arrays.deepToString()`, `Arrays.deepEquals()` |

---

## Methods in Java Arrays Class â€” With Examples

---

### 1. `asList()` Method

Converts an array into a fixed-size `List`.

```java
import java.util.Arrays;

class Geeks {
    public static void main(String[] args) {
        // Get the Array
        int intArr[] = {10, 20, 15, 22, 35};

        // To convert the elements as List
        System.out.println("int Array as List: "
            + Arrays.asList(intArr));
    }
}
```

**Output:**

```text
int Array as List: [[I@19469ea2]
```

**Explanation:** `Arrays.asList()` works properly with **object arrays** (like `Integer[]`, `String[]`), but with **primitive arrays**, the whole array becomes a single list element instead of individual values.

> **Important Note:** `asList()` does **not** work properly with primitive arrays (like `int[]`, `char[]`). It treats the entire primitive array as a single element.
>
> **Correct usage with object arrays:**
> ```java
> Integer[] intArr = {10, 20, 15, 22, 35};
> List<Integer> list = Arrays.asList(intArr);
> // Output: [10, 20, 15, 22, 35]
> ```

---

### 2. `binarySearch()` Method

Searches for a specified element using the **binary search algorithm**.

> **The array must be sorted before calling binarySearch.**

```java
import java.util.Arrays;

public class Geeks {
    public static void main(String[] args) {
        // Get the Array
        int intArr[] = {10, 20, 15, 22, 35};

        Arrays.sort(intArr);

        int intKey = 22;

        // Print the key and corresponding index
        System.out.println(intKey + " found at index = "
            + Arrays.binarySearch(intArr, intKey));
    }
}
```

**Output:**

```text
22 found at index = 3
```

**Explanation:** Searches for an element in a sorted array using binary search. Returns the index if found; otherwise returns a **negative value** indicating the insertion point.

---

### 3. `binarySearch(array, fromIndex, toIndex, key)` Method

Searches within a **specific range** of the array using binary search.

```java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        // Get the Array
        int intArr[] = {10, 20, 15, 22, 35};

        Arrays.sort(intArr);

        int intKey = 22;

        System.out.println(intKey + " found at index = "
            + Arrays.binarySearch(intArr, 1, 3, intKey));
    }
}
```

**Output:**

```text
22 found at index = -4
```

**Explanation:** Searches for an element within a specified range `[fromIndex, toIndex)` of the array. Returns a negative value when the element falls outside the searched range.

```text
After sort: [10, 15, 20, 22, 35]
Searched range (index 1 to 3): [15, 20]
22 is NOT in this range â†’ returns negative insertion point
```

---

### 4. `compare(array1, array2)` Method

Compares two arrays **lexicographically** and returns an integer difference.

```java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        // Get the Array
        int intArr[] = {10, 20, 15, 22, 35};

        // Get the second Array
        int intArr1[] = {10, 15, 22};

        // To compare both arrays
        System.out.println("int Arrays on comparison: "
            + Arrays.compare(intArr, intArr1));
    }
}
```

**Output:**

```text
int Arrays on comparison: 1
```

**Explanation:** Compares arrays element by element. Returns `0` if equal, a **negative value** if the first array is smaller, and a **positive value** if the first array is greater.

| Return Value | Meaning |
|---|---|
| `0` | Arrays are equal |
| `< 0` | First array is lexicographically smaller |
| `> 0` | First array is lexicographically larger |

---

## Complete Methods Reference Table

| Method | Action Performed |
|---|---|
| `asList()` | Returns a fixed-size list backed by the specified array |
| `binarySearch()` | Searches for an element using Binary Search Algorithm |
| `binarySearch(array, fromIndex, toIndex, key, Comparator)` | Searches a range of the array using Binary Search Algorithm |
| `compare(array1, array2)` | Compares two arrays lexicographically: returns negative, 0, or positive |
| `copyOf(originalArray, newLength)` | Copies the array, truncating or padding with default values to the specified length |
| `copyOfRange(originalArray, fromIndex, endIndex)` | Copies the specified range of the array into a new array |
| `deepEquals(Object[] a1, Object[] a2)` | Returns `true` if the two arrays are deeply equal (for multidimensional arrays) |
| `deepHashCode(Object[] a)` | Returns a hash code based on the "deep contents" of the array |
| `deepToString(Object[] a)` | Returns a string representation of the deep contents of a multidimensional array |
| `equals(array1, array2)` | Checks if both arrays are equal (same elements, same order) |
| `fill(originalArray, fillValue)` | Assigns the fill value to each index of the array |
| `hashCode(originalArray)` | Returns an integer hash code of the array |
| `mismatch(array1, array2)` | Finds and returns the index of the first unmatched element between the two arrays |
| `parallelPrefix(originalArray, fromIndex, endIndex, functionalOperator)` | Performs parallelPrefix for the given range with the specified functional operator |
| `parallelPrefix(originalArray, operator)` | Performs parallelPrefix for the complete array with the specified functional operator |
| `parallelSetAll(originalArray, functionalGenerator)` | Sets all elements in parallel using the provided generator function |
| `parallelSort(originalArray)` | Sorts the array using parallel sort (uses Fork/Join framework) |
| `setAll(originalArray, functionalGenerator)` | Sets all elements using the provided generator function |
| `sort(originalArray)` | Sorts the complete array in ascending order |
| `sort(originalArray, fromIndex, endIndex)` | Sorts the specified range of the array in ascending order |
| `sort(T[] a, int fromIndex, int toIndex, Comparator<? super T> c)` | Sorts the specified range using the specified comparator |
| `sort(T[] a, Comparator<? super T> c)` | Sorts the entire array of objects using the specified comparator |
| `spliterator(originalArray)` | Returns a `Spliterator` covering all of the specified array |
| `spliterator(originalArray, fromIndex, endIndex)` | Returns a `Spliterator` covering the specified range of the array |
| `stream(originalArray)` | Returns a sequential stream with the specified array as its source |
| `toString(originalArray)` | Returns a string representation of the array (elements in `[]`, separated by `, `) |

---

## Key Differences: `sort()` vs `parallelSort()`

| Feature | `Arrays.sort()` | `Arrays.parallelSort()` |
|---|---|---|
| **Algorithm** | Dual-Pivot Quicksort (primitives), TimSort (objects) | Merge sort using Fork/Join framework |
| **Threading** | Single-threaded | Multi-threaded (uses available CPU cores) |
| **Best For** | Small to medium arrays | Large arrays (typically 8192+ elements) |
| **Overhead** | No thread overhead | Thread creation/coordination overhead |
| **Time Complexity** | `O(n log n)` | `O(n log n)` (faster in practice for large `n`) |

```java
// Single-threaded sort
Arrays.sort(largeArray);

// Multi-threaded sort (better for large arrays)
Arrays.parallelSort(largeArray);
```

---

## Key Differences: `equals()` vs `deepEquals()`

| Feature | `Arrays.equals()` | `Arrays.deepEquals()` |
|---|---|---|
| **Works On** | 1D arrays | Multidimensional arrays (2D, 3D, etc.) |
| **Nested Arrays** | Compares by reference | Recursively compares contents |

```java
int[][] a = {{1, 2}, {3, 4}};
int[][] b = {{1, 2}, {3, 4}};

Arrays.equals(a, b);      // false (compares array references, not contents)
Arrays.deepEquals(a, b);  // true  (recursively compares all nested elements)
```

---

## Key Differences: `toString()` vs `deepToString()`

```java
int[] arr1D = {1, 2, 3};
int[][] arr2D = {{1, 2}, {3, 4}};

// For 1D arrays
System.out.println(Arrays.toString(arr1D));       // [1, 2, 3]

// For 2D arrays â€” use deepToString
System.out.println(Arrays.deepToString(arr2D));   // [[1, 2], [3, 4]]

// Common mistake: using toString() on 2D array
System.out.println(Arrays.toString(arr2D));       // [[I@6d06d69c, [I@7852e922]
```


---

# Part 2 — Strings in Java
# ðŸ“ Strings in Java â€” Complete DSA Interview Guide

> A comprehensive guide covering Java String fundamentals, memory model, immutability, the CharSequence interface, all important methods, StringBuilder, and DSA-critical patterns. Structured for both foundational understanding and senior-level interview readiness.

---

## ðŸ“‘ Table of Contents

- [What is a String?](#what-is-a-string)
- [Ways of Creating a String](#ways-of-creating-a-java-string)
- [Interfaces and Classes in Strings](#interfaces-and-classes-in-strings-in-java)
- [Immutable String in Java](#immutable-string-in-java)
- [How Strings are Stored in Java Memory](#how-strings-are-stored-in-java-memory)
- [String Properties & Key Facts](#2-important-properties-of-java-string)
- [Why is String Immutable?](#3-why-is-string-immutable)
- [String Pool & `==` vs `equals()`](#6--vs-equals)
- [Core String Methods](#7-string-length)
  - [length(), charAt(), isEmpty(), isBlank()](#7-string-length)
  - [Traversal patterns](#9-iterating-through-a-string)
  - [toCharArray()](#10-converting-string-to-character-array)
  - [Comparison: equals(), compareTo()](#13-string-comparison)
  - [Search: contains(), indexOf(), lastIndexOf()](#16-searching-inside-a-string)
  - [startsWith(), endsWith()](#18-startswith)
  - [substring()](#20-extracting-part-of-a-string)
  - [Concatenation, replace(), split(), join()](#22-concatenation)
  - [Case, trim, strip](#26-uppercase-and-lowercase)
  - [Number conversions](#34-converting-numbers-to-string)
- [Character Operations](#37-character-to-integer)
  - [char â†” int arithmetic](#37-character-to-integer)
  - [Character classification methods](#39-character-classification)
  - [ASCII-based operations & frequency arrays](#41-ascii-based-character-operations)
- [StringBuilder](#45-stringbuilder)
  - [All key methods](#49-stringbuilder-methods-important-for-dsa)
  - [String vs StringBuilder vs StringBuffer](#55-stringbuilder-vs-string)
- [DSA Patterns](#61-important-dsa-string-patterns)
  - [Pattern 1â€“8: Traversal, Frequency, Two Pointers, Sliding Window, etc.](#61-important-dsa-string-patterns)
  - [Palindrome, Anagram, Rotation, Sorting a String](#71-string-rotation)
  - [Substring vs Subsequence](#69-substring-vs-subsequence)
- [Cheat Sheets](#58-important-string-methods-cheat-sheet)
  - [String methods cheat sheet](#58-important-string-methods-cheat-sheet)
  - [Character methods cheat sheet](#59-character-methods-cheat-sheet)
  - [StringBuilder cheat sheet](#60-stringbuilder-cheat-sheet)
  - [One-page DSA template](#85-one-page-java-string-dsa-template)
- [Time Complexity Reference](#80-time-complexity-of-common-string-operations)
- [DSA Learning Roadmap](#84-string-dsa-topics-you-should-learn-after-this)

---

## What is a String?

> **A String in Java is an object used to store a sequence of characters enclosed in double quotes. It uses UTF-16 encoding and provides methods for handling text data.**

### Key Facts

- Each character is stored using **16-bit Unicode (UTF-16)** encoding.
- Strings are **immutable** â€” their value cannot be changed after creation.
- Java provides a rich API for manipulation, comparison, and concatenation.

```java
String name = "Geeks";
String num  = "1234";
```

> **Note:** Since Java 9, Java uses **Compact Strings** (`byte[]` with a `coder` field) instead of `char[]`. The JVM does not expose internal object memory addresses directly.

### Example

```java
public class Geeks {
    public static void main(String args[]) {
        // Creating a String using the new keyword
        String str = new String("Geeks");
        System.out.println(str);
    }
}
```

**Output:**

```text
Geeks
```

---

## Ways of Creating a Java String

### 1. String Literal (String Pool / Static Memory)

```java
String str = "GeeksforGeeks";
```

Java stores string literals in the **String Pool** (part of heap). If the same literal already exists in the pool, Java **reuses the existing object** â€” making this memory efficient.

### 2. Using `new` Keyword (Heap Memory)

```java
String str = new String("GeeksforGeeks");
```

Using `new` **always creates a new object in heap memory**, even if the same string already exists in the pool.

- One object is created in heap memory
- The string literal is stored in the String Pool (if not already present)
- The reference variable points to the **heap object**, not the pool

---

## Interfaces and Classes in Strings in Java

### CharSequence Interface

The `CharSequence` interface represents a sequence of characters. It provides common methods such as `length()`, `charAt()`, `subSequence()`, and `toString()`.

Classes that implement `CharSequence`:

| Class | Description |
|---|---|
| **`String`** | Immutable â€” any change creates a new String object |
| **`StringBuffer`** | Mutable and **thread-safe** â€” used in multithreaded environments |
| **`StringBuilder`** | Mutable and **not thread-safe** â€” faster for single-threaded use |
| **`StringTokenizer`** | Utility class to split strings into tokens by delimiters |

---

## Immutable String in Java

Once a String object is created, its data/state **cannot be changed**. Any operation that appears to modify a String actually creates a **new String object**.

```java
public class GFG {
    public static void main(String[] args) {
        String str = "Hello";
        str.concat(" World");       // Creates a new object; result is discarded
        System.out.println(str);    // Still prints "Hello"
    }
}
```

**Output:**

```text
Hello
```

**Explanation:**

- `str.concat(" World")` creates a new String object `"Hello World"`.
- The original `"Hello"` is **unchanged**.
- Since the new object is not assigned to any variable, it is **discarded**.

To actually update the reference:

```java
str = str.concat(" World");
System.out.println(str);  // Hello World
```

---

## How Strings are Stored in Java Memory

### String Literal â†’ String Pool

```java
String str1 = "Hello";
```

```text
Stack           String Pool (Heap)
+------+        +----------+
| str1 | -----> | "Hello"  |
+------+        +----------+
```

### Same Literal â†’ Reuses Pooled Object

```java
String str1 = "Hello";
String str2 = "Hello";
```

```text
Stack           String Pool (Heap)
+------+        +----------+
| str1 | -----> | "Hello"  | <----- str2
+------+        +----------+
+------+
| str2 |
+------+
```

Both `str1` and `str2` point to the **same pooled object**.

### `new` Keyword â†’ Separate Heap Objects

```java
String str1 = new String("John");
String str2 = new String("Deo");
```

```text
Stack           Heap             String Pool
+------+        +--------+       +--------+
| str1 | -----> | "John" |       | "John" |
+------+        +--------+       | "Deo"  |
+------+        +--------+       +--------+
| str2 | -----> | "Deo"  |
+------+        +--------+
```

### The `intern()` Method

Returns the canonical String reference from the String Pool. If an equal String is not already in the pool, it is added; otherwise, the existing reference is returned.

```java
String demoString = new String("Bhubaneswar");
String internedString = demoString.intern();  // Adds to pool, returns pool reference
```

### String Pool Migration: PermGen â†’ Heap

| Java Version | String Pool Location |
|---|---|
| Before Java 7 | **PermGen** area (fixed size, prone to OutOfMemoryError) |
| Java 7 onwards | **Normal Heap** (managed by GC, no PermGen limits) |

### Complete Example

```java
class Geeks {
    public static void main(String args[]) {
        // String literals â†’ can share pooled object
        String s1 = "TAT";
        String s2 = "TAT";

        // new keyword â†’ separate heap objects
        String s3 = new String("TAT");
        String s4 = new String("TAT");

        System.out.println(s1);  // TAT
        System.out.println(s2);  // TAT
        System.out.println(s3);  // TAT
        System.out.println(s4);  // TAT

        System.out.println(s1 == s2);       // true  (same pooled object)
        System.out.println(s3 == s4);       // false (different heap objects)
        System.out.println(s1.equals(s3));  // true  (same contents)
    }
}
```

> **Note:** All objects in Java are stored in the heap. Reference variables are stored on the stack (or inside other objects on the heap).

---

# Strings in Java for DSA

If you're preparing **Java for DSA/interviews**, the key areas are: string types, mutability, memory behavior, important methods, character operations, conversions, and patterns commonly used in coding problems.

---

## 2. Important Properties of Java String

| Property | Explanation |
|---|---|
| **Class** | `String` is a Java class, not a primitive |
| **Immutable** | Once created, contents cannot be changed |
| **Indexed** | Characters accessed via zero-based index |
| **Ordered** | Character order is preserved |
| **Unicode** | Supports full Unicode (UTF-16) |
| **Thread-safe** | Safe by immutability â€” contents can't be modified |
| **String Pool** | Literals can be stored and reused from the String Pool |

---

## 3. Why is String Immutable?

```java
String s = "Hello";
s.concat(" World");
System.out.println(s);  // Hello â€” original unchanged
```

```java
String s = "Hello";
s = s.concat(" World");
System.out.println(s);  // Hello World â€” reassigned reference
```

```text
Original object â†’ "Hello"
                      |
              concat()â†“
            New object â†’ "Hello World"
```

This is one of the **most important concepts for Java interviews**.

---

## 4. String Creation

### 4.1 String Literal

```java
String s1 = "Hello";
```

Uses the String Pool. Preferred for DSA.

### 4.2 Using `new`

```java
String s2 = new String("Hello");
```

Forces a new heap object. Avoid unless you specifically need it.

---

## 5. String Pool

```java
String a = "hello";
String b = "hello";
System.out.println(a == b);   // true â€” same pooled object
```

```java
String a = new String("hello");
String b = new String("hello");
System.out.println(a == b);   // false â€” different heap objects
```

---

## 6. `==` vs `equals()`

**This is extremely important in interviews.**

| Operator/Method | What it checks |
|---|---|
| `==` | Whether two references point to the **same object** |
| `equals()` | Whether the String **contents** are equal |

```java
String a = new String("hello");
String b = new String("hello");

System.out.println(a == b);       // false (different objects)
System.out.println(a.equals(b));  // true  (same contents)
```

> **DSA Rule:** Always use `a.equals(b)` to compare String contents. Never use `==`.

---

## 7. String Length

```java
String s = "Hello";
int n = s.length();
System.out.println(n);  // 5
```

**DSA usage â€” very frequent:**

```java
for (int i = 0; i < s.length(); i++) {
    System.out.println(s.charAt(i));
}
```

> **Interview distinction:** `array.length` (property) vs `string.length()` (method) vs `list.size()` (method)

---

## 8. Accessing Characters â€” `charAt()`

```java
String s = "Hello";
char ch = s.charAt(1);
System.out.println(ch);  // e
```

**Time Complexity: `O(1)`**

Invalid index throws `StringIndexOutOfBoundsException`.

---

## 9. Iterating Through a String

### Normal `for` loop

```java
String s = "Hello";

for (int i = 0; i < s.length(); i++) {
    char ch = s.charAt(i);
    System.out.println(ch);
}
```

### Reverse traversal

```java
for (int i = s.length() - 1; i >= 0; i--) {
    System.out.println(s.charAt(i));
}
```

Used in: Reverse String, Palindrome, Two-pointer problems, String comparison, Subsequence problems.

---

## 10. Converting String to Character Array â€” `toCharArray()`

```java
String s = "hello";
char[] arr = s.toCharArray();

for (char ch : arr) {
    System.out.println(ch);
}
```

---

## 11. Why `char[]` is Important in DSA

Since String is immutable, you cannot do `s[0] = 'H'`. But with `char[]`:

```java
char[] arr = s.toCharArray();
arr[0] = 'H';
```

**Common DSA technique:**

```java
String s = "hello";
char[] arr = s.toCharArray();
arr[0] = 'H';
s = new String(arr);
System.out.println(s);  // Hello
```

Pattern: `String â†’ char[] â†’ modify â†’ new String`

---

## 12. Converting Character Array to String

```java
char[] arr = {'H', 'e', 'l', 'l', 'o'};

String s = new String(arr);       // Preferred
String s2 = String.valueOf(arr);  // Also valid
```

---

## 13. String Comparison

### `equals()`

```java
String a = "hello";
String b = "hello";

if (a.equals(b)) {
    System.out.println("Equal");
}
```

### `equalsIgnoreCase()`

```java
String a = "Hello";
String b = "hello";
System.out.println(a.equalsIgnoreCase(b));  // true
```

---

## 14. Lexicographical Comparison â€” `compareTo()`

```java
a.compareTo(b);
```

| Return Value | Meaning |
|---|---|
| `< 0` | `a` comes before `b` |
| `= 0` | `a` equals `b` |
| `> 0` | `a` comes after `b` |

```java
System.out.println("apple".compareTo("apple"));   // 0
System.out.println("apple".compareTo("banana"));  // negative
System.out.println("banana".compareTo("apple"));  // positive
```

Used in: Sorting Strings, dictionary-order problems, Priority Queues, custom comparisons.

---

## 15. `compareToIgnoreCase()`

```java
String a = "Apple";
String b = "apple";
System.out.println(a.compareToIgnoreCase(b));  // 0
```

---

## 16. Searching Inside a String

### `contains()`

```java
String s = "Hello World";
System.out.println(s.contains("World"));  // true
```

### `indexOf()`

```java
String s = "banana";
System.out.println(s.indexOf('a'));    // 1
System.out.println(s.indexOf("ana")); // 1
```

Returns `-1` if not found. This `-1` pattern is very important:

```java
int index = s.indexOf('x');
if (index == -1) {
    System.out.println("Not found");
}
```

---

## 17. `lastIndexOf()`

```java
String s = "banana";
System.out.println(s.lastIndexOf('a'));  // 5
```

Useful for: Last occurrence, searching from right, parsing problems.

---

## 18. `startsWith()`

```java
String s = "Hello World";
System.out.println(s.startsWith("Hello"));  // true
```

---

## 19. `endsWith()`

```java
String s = "Hello World";
System.out.println(s.endsWith("World"));  // true
```

---

## 20. Extracting Part of a String â€” `substring()`

One of the **most important String methods in DSA**.

### `substring(startIndex)`

```java
String s = "Hello World";
System.out.println(s.substring(6));  // World
```

### `substring(start, end)` â€” `start` inclusive, `end` exclusive

```java
String s = "Hello World";
System.out.println(s.substring(0, 5));  // Hello
```

```text
H e l l o   W o r l d
0 1 2 3 4 5 6 7 8 9 10

substring(0, 5) â†’ indexes 0,1,2,3,4 â†’ "Hello"
```

---

## 21. Important DSA Rule â€” Substring Interval

```java
substring(start, end)   // [start, end)  â€” end is exclusive
```

```java
s.substring(2, 7)   // contains indexes: 2, 3, 4, 5, 6
```

Same half-open interval concept used throughout DSA (e.g., `copyOfRange`, loop bounds).

---

## 22. Concatenation

### Using `+`

```java
String a = "Hello";
String b = "World";
String c = a + " " + b;  // "Hello World"
```

### `concat()`

```java
String b = a.concat(" World");
```

---

## 23. `isEmpty()`

```java
String s = "";
System.out.println(s.isEmpty());  // true
// equivalent to s.length() == 0
```

---

## 24. `isBlank()`

```java
String s = "   ";
System.out.println(s.isBlank());  // true
```

Checks empty or whitespace-only. Available from **Java 11**.

---

## 25. Removing Spaces

### `trim()`

Removes leading and trailing ASCII whitespace.

```java
String s = "  Hello  ";
System.out.println(s.trim());  // "Hello"
```

### `strip()`

Modern Java alternative with broader Unicode whitespace support.

```java
String s = "  Hello  ";
System.out.println(s.strip());  // "Hello"
```

For most DSA problems with ASCII input, `trim()` is sufficient.

---

## 26. Uppercase and Lowercase

```java
String s = "Hello";
System.out.println(s.toUpperCase());  // HELLO
System.out.println(s.toLowerCase());  // hello
```

Useful in: case-insensitive matching, character normalization.

---

## 27. Replacing Characters â€” `replace(char, char)`

```java
String s = "banana";
String result = s.replace('a', 'x');
System.out.println(result);  // bxnxnx
```

---

## 28. Replacing Strings â€” `replace(String, String)`

```java
String s = "I like Java";
String result = s.replace("Java", "Kotlin");
System.out.println(result);  // I like Kotlin
```

---

## 29. `replaceFirst()`

Uses a regular expression. Replaces only the first match.

```java
String s = "banana";
System.out.println(s.replaceFirst("a", "x"));  // bxnana
```

---

## 30. `replaceAll()`

Uses regular expressions. Replaces all matches.

```java
String s = "a1b2c3";
String result = s.replaceAll("\\d", "");
System.out.println(result);  // abc
```

> For many DSA problems, **manually processing characters is simpler and more efficient** than using regex.

---

## 31. Splitting Strings â€” `split()`

```java
String s = "apple,banana,orange";
String[] fruits = s.split(",");
// fruits[0] = "apple", fruits[1] = "banana", fruits[2] = "orange"
```

For space-separated input:

```java
String s = "Java Python Kotlin";
String[] words = s.split(" ");
```

---

## 32. Important `split()` Warning

`split()` uses a **regular expression**. For special characters like `.`, `|`, `*`, you must escape them:

```java
s.split("\\.");   // literal dot
s.split("\\|");   // literal pipe
```

For performance-critical competitive programming, `StringTokenizer` can be faster.

---

## 33. Joining Strings â€” `String.join()`

```java
String result = String.join("-", "2026", "09", "16");
System.out.println(result);  // 2026-09-16
```

---

## 34. Converting Numbers to String

```java
int n = 123;
String s = String.valueOf(n);      // Preferred
String s2 = Integer.toString(n);   // Also valid
```

---

## 35. Converting String to Integer

```java
String s = "123";
int n = Integer.parseInt(s);  // n = 123
```

---

## 36. Other Numeric Conversions

```java
long l    = Long.parseLong("123456");
double d  = Double.parseDouble("12.5");
float f   = Float.parseFloat("12.5");
boolean b = Boolean.parseBoolean("true");
```

For DSA, the most common conversions are `Integer.parseInt()` and `Long.parseLong()`.

---

## 37. Character to Integer

**This is extremely important in DSA.**

```java
char ch = '7';
int digit = ch - '0';  // digit = 7
```

This works because `'0'` through `'9'` are consecutive in ASCII/Unicode:

```text
'0' = 48
'1' = 49
...
'9' = 57
```

So `'7' - '0' = 55 - 48 = 7`.

---

## 38. Integer to Character

```java
int digit = 7;
char ch = (char) ('0' + digit);  // ch = '7'
```

This is heavily used in DSA (e.g., building numeric strings, digit problems).

---

## 39. Character Classification

Java's `Character` class provides static utility methods:

### Check alphabet

```java
Character.isLetter(ch);
```

### Check digit

```java
Character.isDigit(ch);
```

### Check whitespace

```java
Character.isWhitespace(ch);
```

### Check uppercase / lowercase

```java
Character.isUpperCase(ch);
Character.isLowerCase(ch);
```

---

## 40. Character Conversion

```java
char ch = 'a';
char upper = Character.toUpperCase(ch);  // 'A'
char lower = Character.toLowerCase(ch);  // 'a'
```

---

## 41. ASCII-Based Character Operations

For many DSA problems, ASCII arithmetic is essential.

```java
char ch = 'c';
int value = ch - 'a';  // 2
```

Mapping for lowercase letters:

```text
'a' â†’ 0
'b' â†’ 1
'c' â†’ 2
...
'z' â†’ 25
```

This is the foundation of **frequency array** solutions.

---

## 42. Frequency Array â€” Most Important String Technique

Count each lowercase English letter in a string:

```java
String s = "banana";
int[] freq = new int[26];

for (int i = 0; i < s.length(); i++) {
    char ch = s.charAt(i);
    freq[ch - 'a']++;
}

// Result:
// a â†’ freq[0]  = 3
// b â†’ freq[1]  = 1
// n â†’ freq[13] = 2
```

**One of the most important String techniques in DSA interviews.**

---

## 43. Frequency Array for Digits

```java
int[] freq = new int[10];

for (char ch : s.toCharArray()) {
    if (Character.isDigit(ch)) {
        freq[ch - '0']++;
    }
}
```

---

## 44. Frequency Array for Full ASCII

```java
int[] freq = new int[128];

for (char ch : s.toCharArray()) {
    freq[ch]++;
}
```

Use when the problem guarantees standard ASCII characters.

---

## 45. StringBuilder

**Extremely important for DSA.**

Since `String` is immutable, repeatedly doing:

```java
s = s + ch;
```

creates many intermediate String objects and can be slow. Instead use:

```java
StringBuilder sb = new StringBuilder();
```

---

## 46. Adding Characters â€” `append()`

```java
StringBuilder sb = new StringBuilder();

sb.append('a');
sb.append('b');
sb.append('c');

System.out.println(sb);  // abc
```

---

## 47. Adding Strings

```java
StringBuilder sb = new StringBuilder();

sb.append("Hello");
sb.append(" ");
sb.append("World");

System.out.println(sb);  // Hello World
```

---

## 48. Converting StringBuilder to String â€” `toString()`

```java
String result = sb.toString();
```

Very common pattern:

```java
StringBuilder sb = new StringBuilder();

for (char ch : s.toCharArray()) {
    sb.append(ch);
}

String result = sb.toString();
```

---

## 49. StringBuilder Methods Important for DSA

| Method | Purpose |
|---|---|
| `append(x)` | Add data at the end |
| `insert(index, x)` | Insert data at a position |
| `delete(start, end)` | Delete range `[start, end)` |
| `deleteCharAt(index)` | Delete a single character |
| `setCharAt(index, ch)` | Modify a character in-place |
| `charAt(index)` | Access a character |
| `reverse()` | Reverse the contents |
| `length()` | Get current length |
| `capacity()` | Get current buffer capacity |
| `substring(start, end)` | Extract a portion |
| `toString()` | Convert to String |

---

## 50. `setCharAt()` â€” In-place Modification

Unlike `String`, `StringBuilder` is **mutable**.

```java
StringBuilder sb = new StringBuilder("hello");
sb.setCharAt(0, 'H');
System.out.println(sb);  // Hello
```

---

## 51. `deleteCharAt()`

```java
StringBuilder sb = new StringBuilder("hello");
sb.deleteCharAt(1);
System.out.println(sb);  // hllo
```

---

## 52. `delete(start, end)` â€” `[start, end)`

```java
StringBuilder sb = new StringBuilder("abcdef");
sb.delete(1, 4);           // Deletes indexes 1, 2, 3
System.out.println(sb);   // aef
```

---

## 53. `insert(index, x)`

```java
StringBuilder sb = new StringBuilder("ac");
sb.insert(1, 'b');
System.out.println(sb);  // abc
```

---

## 54. `reverse()`

Very important in DSA.

```java
StringBuilder sb = new StringBuilder("hello");
sb.reverse();
System.out.println(sb);  // olleh
```

To reverse a String and return a new String:

```java
String reversed = new StringBuilder(s).reverse().toString();
```

---

## 55. StringBuilder vs String

| Feature | `String` | `StringBuilder` |
|---|---|---|
| **Mutable** | No | Yes |
| **Modification** | Creates new String each time | Modifies existing object |
| `append()` | Not available | Yes |
| `reverse()` | Not available | Yes |
| **DSA construction** | Less suitable for repeated changes | Excellent |
| **Thread-safe** | Immutable (inherently safe) | No |
| **Typical use** | Fixed/read-only text | Frequently changing text |

---

## 56. StringBuffer

`StringBuffer` is another mutable character sequence, identical API to `StringBuilder` but **synchronized** (thread-safe).

```java
StringBuffer sb = new StringBuffer();
```

**For DSA:** Always prefer `StringBuilder` â€” the synchronization overhead of `StringBuffer` is unnecessary in single-threaded interview problems.

---

## 57. String vs StringBuilder vs StringBuffer

```text
String
   â†’ Immutable
   â†’ Any "change" creates a new object
   â†’ Thread-safe by immutability

StringBuilder
   â†’ Mutable
   â†’ Modifies the same object
   â†’ Faster (no synchronization)
   â†’ Use for DSA

StringBuffer
   â†’ Mutable
   â†’ Synchronized (thread-safe)
   â†’ Slower due to locking
   â†’ Use in multithreaded code
```

---

## 58. Important String Methods Cheat Sheet

### Basic

```java
s.length()
s.charAt(i)
s.isEmpty()
s.isBlank()
```

### Comparison

```java
s.equals(t)
s.equalsIgnoreCase(t)
s.compareTo(t)
s.compareToIgnoreCase(t)
```

### Searching

```java
s.contains(t)
s.indexOf(ch)
s.indexOf(t)
s.lastIndexOf(ch)
s.lastIndexOf(t)
```

### Extraction

```java
s.substring(start)
s.substring(start, end)    // [start, end) â€” end is exclusive
```

### Modification-like (all return a NEW String)

```java
s.concat(t)
s.replace(oldChar, newChar)
s.replace(oldStr, newStr)
s.replaceFirst(regex, replacement)
s.replaceAll(regex, replacement)
s.toUpperCase()
s.toLowerCase()
s.trim()
s.strip()
```

### Conversion

```java
s.toCharArray()
String.valueOf(x)
Integer.parseInt(s)
Long.parseLong(s)
```

### Prefix / Suffix

```java
s.startsWith(prefix)
s.endsWith(suffix)
```

### Splitting / Joining

```java
s.split(regex)
String.join(delimiter, parts...)
```

---

## 59. Character Methods Cheat Sheet

```java
Character.isLetter(ch)
Character.isDigit(ch)
Character.isLetterOrDigit(ch)
Character.isWhitespace(ch)
Character.isUpperCase(ch)
Character.isLowerCase(ch)
Character.toUpperCase(ch)
Character.toLowerCase(ch)
```

---

## 60. StringBuilder Cheat Sheet

```java
StringBuilder sb = new StringBuilder();

sb.append(x);                  // Add at end
sb.insert(index, x);           // Insert at position
sb.delete(start, end);         // Delete range [start, end)
sb.deleteCharAt(index);        // Delete single char
sb.setCharAt(index, ch);       // Modify char in-place
sb.charAt(index);              // Read char
sb.reverse();                  // Reverse in-place
sb.length();                   // Current length
sb.toString();                 // Convert to String
```

---

# DSA Patterns

## 61. Pattern 1 â€” Character Traversal

```java
for (int i = 0; i < s.length(); i++) {
    char ch = s.charAt(i);
    // process ch
}
```

Use when: processing every character according to a condition.

---

## 62. Pattern 2 â€” Reverse Traversal

```java
for (int i = s.length() - 1; i >= 0; i--) {
    char ch = s.charAt(i);
    // process ch
}
```

Use when: reading from the end, reversing, palindrome checks.

---

## 63. Pattern 3 â€” Character Frequency Array

For lowercase English letters:

```java
int[] freq = new int[26];

for (char ch : s.toCharArray()) {
    freq[ch - 'a']++;
}
```

For full ASCII:

```java
int[] freq = new int[128];

for (char ch : s.toCharArray()) {
    freq[ch]++;
}
```

---

## 64. Pattern 4 â€” HashMap Frequency

When the character range is unknown or flexible:

```java
Map<Character, Integer> freq = new HashMap<>();

for (char ch : s.toCharArray()) {
    freq.put(ch, freq.getOrDefault(ch, 0) + 1);
}
```

Use when: Unicode/general characters, non-fixed key set, needing flexible mapping.

---

## 65. Pattern 5 â€” Two Pointers

Very common for: Palindrome, Reverse, comparing characters from both ends.

```java
int left = 0;
int right = s.length() - 1;

while (left < right) {
    if (s.charAt(left) != s.charAt(right)) {
        return false;
    }
    left++;
    right--;
}

return true;
```

---

## 66. Pattern 6 â€” StringBuilder for Construction

```java
StringBuilder sb = new StringBuilder();

for (char ch : s.toCharArray()) {
    if (/* condition */) {
        sb.append(ch);
    }
}

return sb.toString();
```

Use when: building a result String character by character.

---

## 67. Pattern 7 â€” Sliding Window

One of the most important String patterns in DSA.

Used for: Longest substring without repeating characters, minimum window substring, longest substring with constraints.

```java
int left = 0;

for (int right = 0; right < s.length(); right++) {
    // Expand: add s.charAt(right) to window

    while (/* window is invalid */) {
        // Shrink: remove s.charAt(left) from window
        left++;
    }

    // Update answer using current valid window [left, right]
}
```

---

## 68. Pattern 8 â€” Substring Generation

When a problem asks about contiguous portions of a String:

```java
substring(start, end)
```

> **Warning:** A String of length `n` has `n(n+1)/2` substrings. Brute-force generation is `O(nÂ²)` â€” use sliding window or hashing when possible.

---

## 69. Substring vs Subsequence

**This distinction is very important in interviews.**

### Substring â€” characters must be **contiguous**

```text
String:    abcde
Substring: bcd    âœ“ (contiguous)
           ace    âœ— (not contiguous)
```

### Subsequence â€” characters must maintain **order but need not be contiguous**

```text
String:       abcde
Subsequence:  ace   âœ“ (a at 0, c at 2, e at 4 â€” order preserved)
              eca   âœ— (order not preserved)
```

---

## 70. Subarray vs Substring vs Subsequence

```text
Subarray     (arrays)  â†’ contiguous elements
Substring    (strings) â†’ contiguous characters
Subsequence  (either)  â†’ non-contiguous, but order preserved
```

This terminology is **frequently tested in interviews**.

---

## 71. String Rotation

Check if one string is a rotation of another:

```java
// Ensure lengths are equal first
if (s.length() != target.length()) return false;

String doubled = s + s;
return doubled.contains(target);
```

Example:

```text
s      = "abcd"
target = "cdab"
doubled = "abcdabcd"
doubled.contains("cdab") â†’ true  âœ“
```

---

## 72. Palindrome Pattern

```java
int left = 0;
int right = s.length() - 1;

while (left < right) {
    if (s.charAt(left) != s.charAt(right)) {
        return false;
    }
    left++;
    right--;
}

return true;
```

**Time: `O(n)` | Space: `O(1)`** â€” preferred over creating a reversed String.

---

## 73. Anagram Pattern

Two strings are anagrams if they have the same characters with the same frequencies.

```java
if (a.length() != b.length()) return false;

int[] freq = new int[26];

for (char ch : a.toCharArray()) freq[ch - 'a']++;
for (char ch : b.toCharArray()) freq[ch - 'a']--;

for (int count : freq) {
    if (count != 0) return false;
}

return true;
```

**Time: `O(n)` | Space: `O(1)`** (fixed 26-size array)

---

## 74. Sorting a String

Java has no `String.sort()`. Use the `char[]` conversion:

```java
String s = "dcba";

char[] arr = s.toCharArray();
Arrays.sort(arr);
s = new String(arr);

System.out.println(s);  // abcd
```

**Time: `O(n log n)`**

---

## 75. When to Use Frequency Array vs HashMap

| Condition | Use |
|---|---|
| Character set is small and fixed (e.g., `a-z`) | `int[] freq = new int[26]` |
| Character/key range is unknown or large | `Map<Character, Integer> map = new HashMap<>()` |

```text
Fixed small domain  â†’ Array   (faster, O(1) lookup, no hashing)
Dynamic/unknown     â†’ HashMap (flexible, O(1) average)
```

---

## 76. String Input in DSA

### `Scanner` â€” single token

```java
Scanner sc = new Scanner(System.in);
String s = sc.next();       // reads one word
String line = sc.nextLine(); // reads entire line
```

### `BufferedReader` â€” faster I/O

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
String s = br.readLine();
```

### `StringTokenizer` â€” fast token splitting

```java
StringTokenizer st = new StringTokenizer(br.readLine());
String first  = st.nextToken();
String second = st.nextToken();
```

---

## 77. Important Java Imports

```java
import java.util.*;   // Arrays, HashMap, HashSet, StringTokenizer, Scanner
import java.io.*;     // BufferedReader, InputStreamReader, IOException
```

---

## 80. Time Complexity of Common String Operations

For a String of length `n`:

| Operation | Typical Complexity |
|---|---|
| `length()` | `O(1)` |
| `charAt(i)` | `O(1)` |
| `equals()` | `O(n)` worst case |
| `compareTo()` | `O(n)` worst case |
| `contains()` | Depends on implementation |
| `indexOf()` | Depends on implementation |
| `substring(start, end)` | `O(k)` to create result (length `k`) |
| `toCharArray()` | `O(n)` |
| `toLowerCase()` / `toUpperCase()` | `O(n)` |
| `replace()` | `O(n)` typically |
| `split()` | `O(n)` or more depending on regex |
| `StringBuilder.append()` | Amortized `O(1)` |
| `StringBuilder.charAt()` | `O(1)` |
| `StringBuilder.setCharAt()` | `O(1)` |
| `StringBuilder.reverse()` | `O(n)` |

---

## 81. String Concatenation â€” Important Interview Concept

Using `+` in a loop:

```java
String result = "";

for (int i = 0; i < n; i++) {
    result += s.charAt(i);   // Creates a new String object each iteration
}
```

This can be `O(nÂ²)` in total because each `+` creates a new immutable String.

**Correct approach for DSA:**

```java
StringBuilder result = new StringBuilder();

for (int i = 0; i < n; i++) {
    result.append(s.charAt(i));   // O(1) amortized per append
}

return result.toString();  // O(n) final conversion
```

This is one of the **most common performance discussions in Java interviews**.

---

## 82. Most Important Methods to Memorize

### Level 1 â€” Must Know

```java
length()
charAt()
equals()
substring()
indexOf()
contains()
toCharArray()
```

### Level 2 â€” Very Important

```java
compareTo()
lastIndexOf()
startsWith()
endsWith()
replace()
split()
toLowerCase()
toUpperCase()
```

### Level 3 â€” Character Operations

```java
Character.isLetter()
Character.isDigit()
Character.isWhitespace()
Character.toLowerCase()
Character.toUpperCase()
```

### Level 4 â€” StringBuilder

```java
append()
charAt()
setCharAt()
deleteCharAt()
delete()
insert()
reverse()
length()
toString()
```

### Level 5 â€” Conversion

```java
String.valueOf()
Integer.parseInt()
Long.parseLong()
```

---

## 83. How to Decide Which Technique to Use

```text
                 STRING PROBLEM
                       |
          +------------+-------------+
          |            |             |
      Inspect       Count          Construct
          |            |             |
       charAt()     freq[]         StringBuilder
       loop         HashMap
          |
          +----------------+
          |                |
       Compare          Search
          |                |
     Two pointers      indexOf()
     equals()          contains()
     compareTo()
```

Pattern â†’ Technique mapping:

```text
Check both ends?              â†’ Two pointers
Count characters?             â†’ Frequency array / HashMap
Find longest/shortest range?  â†’ Sliding window
Build answer character by ch? â†’ StringBuilder
Need ordered comparison?      â†’ compareTo()
Need contiguous section?      â†’ Substring / sliding window
Need non-contiguous chars?    â†’ Subsequence techniques / DP
Need repeated prefix/suffix?  â†’ KMP / Z algorithm / Trie
Fast repeated search?         â†’ Hashing / rolling hash
```

---

## 84. String DSA Topics â€” Learning Roadmap

```text
1.  String basics (this guide)
         â†“
2.  Character operations
         â†“
3.  StringBuilder
         â†“
4.  Frequency counting
         â†“
5.  Two pointers
         â†“
6.  Sliding window
         â†“
7.  Hashing with Strings
         â†“
8.  Prefix / suffix concepts
         â†“
9.  Substring problems
         â†“
10. Subsequence problems
         â†“
11. Stack + String
         â†“
12. String + HashMap
         â†“
13. String + Sorting
         â†“
14. Trie
         â†“
15. KMP algorithm
         â†“
16. Z algorithm
         â†“
17. Rabin-Karp / rolling hash
```

For **FAANG-level interviews**, levels 1â€“12 are essential. Levels 13â€“17 are relevant for senior/staff-level or specialized problem sets.

---

## 85. One-Page Java String DSA Template

```java
// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ String Basics â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
String s = "hello";
int n = s.length();                    // 5
char ch = s.charAt(i);                 // O(1)
char[] arr = s.toCharArray();          // O(n)

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Comparison â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
s.equals(t);                           // content equality
s.equalsIgnoreCase(t);                 // case-insensitive
s.compareTo(t);                        // lexicographic order

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Search â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
s.contains(t);                         // boolean
s.indexOf(ch);                         // first occurrence, -1 if not found
s.lastIndexOf(ch);                     // last occurrence
s.startsWith(prefix);
s.endsWith(suffix);

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Extraction â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
s.substring(start);                    // [start, end)
s.substring(start, end);

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Modification (new String) â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
s.toLowerCase();
s.toUpperCase();
s.trim();
s.replace(oldChar, newChar);
s.split(regex);
String.join(delimiter, parts...);

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ StringBuilder â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
StringBuilder sb = new StringBuilder();
sb.append(ch);                         // O(1) amortized
sb.setCharAt(i, ch);                   // O(1)
sb.deleteCharAt(i);
sb.delete(start, end);                 // [start, end)
sb.insert(i, ch);
sb.reverse();                          // O(n)
String result = sb.toString();         // O(n)

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Character Utilities â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
Character.isLetter(ch);
Character.isDigit(ch);
Character.isWhitespace(ch);
Character.toLowerCase(ch);
Character.toUpperCase(ch);

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ char â†” number â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
int digit    = ch - '0';              // char '7' â†’ int 7
char digitCh = (char)('0' + digit);  // int 7 â†’ char '7'
int alpha    = ch - 'a';             // char 'c' â†’ int 2 (for freq arrays)

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ String â†” number â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
int number   = Integer.parseInt(s);
long big     = Long.parseLong(s);
String text  = String.valueOf(number);

// â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Frequency Array â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
int[] freq = new int[26];            // for lowercase a-z
freq[ch - 'a']++;

int[] freqASCII = new int[128];      // for full ASCII
freqASCII[ch]++;
```

---

> **Key Interview Mindset:**
>
> Don't approach a String problem as _"Which String method should I use?"_
>
> Approach it as: **"What data structure or algorithmic pattern does this problem require?"**
>
> ```text
> Simple traversal       â†’ for loop + charAt()
> Count characters       â†’ frequency array / HashMap
> Compare both ends      â†’ two pointers
> Build a result         â†’ StringBuilder
> Find valid range       â†’ sliding window
> Compare ordered text   â†’ compareTo()
> Contiguous characters  â†’ substring / sliding window
> Non-contiguous         â†’ subsequence / DP
> Prefix/suffix match    â†’ KMP / Z / Trie
> Fast repeated search   â†’ hashing / rolling hash
> ```
>
> **DSA interviews test problem-solving patterns more than knowledge of Java's String API.**



---

# Part 3 — Java Collection Framework

> A senior engineer's architectural guide to the Java Collection Framework (JCF): covering data structure internals, memory layouts, algorithmic complexities, modern Java idioms, concurrent collections, and 10+ years experienced interview scenarios.

---

## 📑 Part 3 Table of Contents

1. [JCF Core Architecture & Taxonomy](#1-jcf-core-architecture--taxonomy)
2. [Iterable, Iterator & ListIterator](#2-iterable-iterator--listiterator)
3. [Collection Interface](#3-collection-interface)
4. [List Interface & Implementations](#4-list-interface--implementations)
   - [ArrayList Deep Dive](#arraylist-deep-dive)
   - [LinkedList Deep Dive](#linkedlist-deep-dive)
   - [Vector & Stack Legacy Analysis](#vector--stack-legacy-analysis)
   - [AbstractList & Skeletal Classes](#abstractlist--skeletal-classes)
5. [Set Interface & Implementations](#5-set-interface--implementations)
   - [HashSet](#hashset)
   - [LinkedHashSet](#linkedhashset)
   - [TreeSet & NavigableSet](#treeset--navigableset)
6. [Queue & Deque Interfaces](#6-queue--deque-interfaces)
   - [Queue Contract (Throwing vs Special Value)](#queue-contract)
   - [PriorityQueue Deep Dive (Binary Heap)](#priorityqueue-deep-dive)
   - [ArrayDeque (Circular Buffer)](#arraydeque-circular-buffer)
7. [Map Interface & Implementations](#7-map-interface--implementations)
   - [Why Map is Separate from Collection](#why-map-is-separate-from-collection)
   - [HashMap Internal Architecture (Treeification, Buckets, Sizing)](#hashmap-internal-architecture)
   - [LinkedHashMap & LRU Cache](#linkedhashmap--lru-cache)
   - [TreeMap (Red-Black Tree)](#treemap)
   - [Specialized Maps (WeakHashMap, IdentityHashMap)](#specialized-maps)
8. [Comparable vs Comparator](#8-comparable-vs-comparator)
9. [Collections Utility Class & Modern Collection APIs](#9-collections-utility-class--modern-collection-apis)
10. [Concurrency Collections (java.util.concurrent)](#10-concurrency-collections-javautilconcurrent)
    - [ConcurrentHashMap (Java 8+ CAS & Synchronized Bins)](#concurrenthashmap)
    - [CopyOnWriteArrayList](#copyonwritearraylist)
    - [BlockingQueue & ConcurrentLinkedQueue](#blockingqueue--concurrentlinkedqueue)
11. [Core Contracts & Critical Pitfalls](#11-core-contracts--critical-pitfalls)
    - [equals() & hashCode() Contract Violations](#equals--hashcode-contract)
    - [Fail-Fast vs Fail-Safe vs Weakly Consistent](#fail-fast-vs-fail-safe-vs-weakly-consistent)
    - [Primitive Boxing & JVM Memory Overhead](#primitive-boxing--jvm-memory-overhead)
12. [Complexity Master Cheat Sheet & Decision Matrix](#12-complexity-master-cheat-sheet--decision-matrix)
13. [Senior / Staff-Level Interview Master Q&A (25+ Questions)](#13-senior--staff-level-interview-master-qa)

---

## 1. JCF Core Architecture & Taxonomy

### What is the Java Collection Framework?

The **Java Collection Framework (JCF)** is a unified architecture located in `java.util` comprising:
1. **Core Interfaces**: Abstract data types representing collections (e.g., `Collection`, `List`, `Set`, `Queue`, `Deque`, `Map`).
2. **Concrete Implementations**: High-performance, reusable data structures (e.g., `ArrayList`, `HashSet`, `PriorityQueue`, `HashMap`).
3. **Algorithms & Utilities**: Static polymorphic algorithms for sorting, searching, reversing, and thread-synchronization (`Collections`, `Arrays`).

```
Interview Definition (Senior Level):
"The Java Collection Framework provides a standardized, type-safe API for common abstract data types, decoupling algorithmic contracts from concrete data structures to enable optimal memory-to-throughput trade-offs across single-threaded and concurrent execution environments."
```

### Why Do We Need It?

| Limitation of Raw Arrays | JCF Solution | Architectural Impact |
| :--- | :--- | :--- |
| **Fixed Allocation** | Resizable containers (`ArrayList`, `ArrayDeque`) | Eliminates manual buffer management and reallocations |
| **Monolithic Type System** | Generic abstractions (`<E>`, `<K, V>`) | Enforces compile-time type safety; eliminates runtime class cast errors |
| **Manual Data Structures** | Out-of-the-box Red-Black Trees, Hash Tables, Heaps | Production-hardened, battle-tested algorithms (`O(1)` hash lookups, `O(log n)` sorted traversals) |
| **No Interoperability** | Standardized interface contracts | Easy to substitute implementations (e.g., swap `LinkedList` for `ArrayDeque` with zero caller changes) |

---

### Collection vs Collections vs Collection Framework

| Term | Category | Description | Canonical Example |
| :--- | :--- | :--- | :--- |
| **`Collection`** | **Interface** | Root interface representing a single-value group of objects (`List`, `Set`, `Queue`). | `Collection<String> c = new ArrayList<>();` |
| **`Collections`** | **Utility Class** | Final class containing static algorithms and factory wrappers (`sort`, `unmodifiableList`, `synchronizedMap`). | `Collections.sort(myList);` |
| **`Collection Framework`** | **Architecture** | The entire ecosystem: interfaces, concrete classes, abstract skeletal classes, and iterators. | Java's `java.util` & `java.util.concurrent` suites. |

> [!IMPORTANT]
> **Why does `Map` NOT extend `Collection`?**
> A `Collection` models a group of individual elements (`E`), where operations operate on single items (`add(E)`, `contains(Object)`). A `Map` models **key-value associations** (`<K, V>`). Methods such as `add(E)` make no semantic sense for key-value pairs without knowing both key and value. While `Map` is an integral part of JCF, it sits at its own hierarchy root.

---

### Main Hierarchy Diagram

```text
                             Iterable<T>
                                 |
                           Collection<E>
             /                   |                          List<E>                Set<E>               Queue<E>
      /   |   \               /     \                 |    ArrayList |  Vector     HashSet   SortedSet<E>      Deque<E> PriorityQueue
      LinkedList   \        |          |            /                      Stack  LinkedHashSet NavigableSet ArrayDeque LinkedList
                                       |
                                    TreeSet


Separate Hierarchy:
                                 Map<K, V>
             /                 /     |    \                      HashMap       LinkedHashMap   |   Hashtable    IdentityHashMap
                               SortedMap<K, V>
                                     |
                               NavigableMap<K, V>
                                     |
                                  TreeMap

Concurrent Hierarchy (java.util.concurrent):
• ConcurrentMap<K, V>  --> ConcurrentHashMap<K, V>, ConcurrentSkipListMap<K, V>
• CopyOnWriteArrayList<E>, CopyOnWriteArraySet<E>
• BlockingQueue<E>     --> ArrayBlockingQueue, LinkedBlockingQueue, PriorityBlockingQueue
• ConcurrentLinkedQueue<E>, ConcurrentLinkedDeque<E>
```

---

### 💡 10+ Years Experienced Interview Questions: Architecture & Design

#### Q1: "Why does Java use skeletal implementations like `AbstractList`, `AbstractSet`, and `AbstractMap`? How does this relate to the Template Method pattern?"
**Architectural Answer:**
> "Java's skeletal implementations (`AbstractCollection`, `AbstractList`, `AbstractSet`, `AbstractMap`) drastically reduce the boilerplate needed to implement custom collection types. They implement the **Template Method Design Pattern**: the skeletal class provides concrete implementations for all non-primitive methods (e.g., `contains()`, `addAll()`, `equals()`, `hashCode()`, `toString()`) purely in terms of a minimal set of abstract primitive methods.
> For instance, to write a read-only custom list using `AbstractList`, a developer only needs to override `get(int index)` and `size()`. To make it mutable, they simply override `set(int index, E element)`. This ensures consistency across the framework and enforces DRY (Don't Repeat Yourself) at the API design level."

#### Q2: "What is the memory and performance penalty of storing primitives in JCF collections versus raw arrays or specialized libraries?"
**Staff-Level Answer:**
> "JCF collections can only hold object references (`List<Integer>`, not `List<int>`). On a 64-bit HotSpot JVM with Compressed OOPs enabled:
> 1. An `int[]` array of size $N$ consumes $16\text{ bytes (header)} + 4N\text{ bytes} + \text{padding}$.
> 2. An `ArrayList<Integer>` consumes:
>    - The `ArrayList` object header + references (~24 bytes).
>    - An internal `Object[]` array ($16 + 4N\text{ bytes}$ for reference pointers).
>    - $N$ distinct `java.lang.Integer` heap objects. Each `Integer` has a 12-byte Mark/Klass word header + 4-byte int payload + 8-byte alignment padding = **16 bytes per object**.
> Total memory for $N$ integers in `ArrayList<Integer>` is $\approx 24N\text{ bytes}$, compared to $4N\text{ bytes}$ for `int[]` — **a 6x memory explosion**.
> Furthermore, accessing elements incurs pointer chasing (dereferencing references scattered across heap memory), completely thrashing CPU L1/L2 cache lines compared to contiguous hardware pre-fetching in `int[]`. In ultra-low-latency financial systems or high-throughput big data pipelines, we bypass JCF in favor of primitive-specialized collections such as **Agrona**, **Eclipse Collections**, or **fastutil**."

---

## 2. Iterable, Iterator & ListIterator

### Concept & Contract

- **`Iterable<T>`**: Any class implementing `Iterable<T>` can be the target of the enhanced for-loop (`for (T item : collection)`). It declares a single abstract method: `Iterator<T> iterator()`.
- **`Iterator<T>`**: Enables forward-only unidirectional traversal. Supports safe element removal during iteration via `iterator.remove()`.
- **`ListIterator<T>`**: Available only on `List` implementations. Extends `Iterator` to support bidirectional traversal (`hasPrevious()`, `previous()`), index queries (`nextIndex()`, `previousIndex()`), and mutations during traversal (`add()`, `set()`).

```java
// Safe removal during iteration
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String val = it.next();
    if (val.startsWith("DEBUG_")) {
        it.remove(); // SAFE: updates both collection and iterator state
    }
}

// Java 8+ Functional Equivalent (internally uses Iterator or bulk bitmask removal)
list.removeIf(val -> val.startsWith("DEBUG_"));
```

---

### Iterator vs ListIterator

| Feature | `Iterator<E>` | `ListIterator<E>` |
| :--- | :--- | :--- |
| **Applicability** | Any `Collection<E>` (`List`, `Set`, `Queue`) | Exclusively `List<E>` |
| **Traversal Direction** | Forward only (`next()`, `hasNext()`) | Bidirectional (`next()`, `previous()`) |
| **Index Awareness** | No index awareness | `nextIndex()`, `previousIndex()` |
| **Modifications Supported** | `remove()` only | `remove()`, `set(E e)`, `add(E e)` |

---

### 💡 10+ Years Experienced Interview Questions: Iteration & Modification

#### Q1: "How does the fail-fast mechanism work internally in `ArrayList`? What exact JVM field controls it, and can it be bypassed?"
**Internal Mechanics Answer:**
> "`ArrayList` maintains an internal `protected transient int modCount = 0;` field inherited from `AbstractList`. Every structural modification (`add()`, `remove()`, `ensureCapacity()`, `clear()`) increments `modCount++`.
> When `list.iterator()` is called, the iterator instance snapshots this value into an internal field: `int expectedModCount = modCount;`.
> On every call to `iterator.next()` or `iterator.remove()`, the iterator executes:
> ```java
> final void checkForComodification() {
>     if (modCount != expectedModCount)
>         throw new ConcurrentModificationException();
> }
> ```
> If external code calls `list.add()` or `list.remove()` during iteration, `modCount` diverges from `expectedModCount`, immediately throwing `ConcurrentModificationException`.
> 
> **Can it be bypassed?**
> Yes:
> 1. Calling `iterator.remove()` updates `expectedModCount = modCount`, avoiding the exception.
> 2. Iterating with a standard index-based loop (`for (int i = 0; i < list.size(); i++)`) does not check `modCount`, though modifying elements shifts indices and may cause skipped elements or `IndexOutOfBoundsException`.
> 3. Concurrent collections (`CopyOnWriteArrayList`, `ConcurrentHashMap`) do not use fail-fast iterators; they use snapshot or weakly consistent iterators."

#### Q2: "Is `ConcurrentModificationException` guaranteed to be thrown in a multi-threaded race condition?"
**Concurrency Answer:**
> "No. As explicitly stated in JavaDoc, `ConcurrentModificationException` is a **best-effort detection tool**, not a synchronization guarantee.
> `modCount` is an ordinary `int` field — it is **not volatile**. Under concurrent multi-threaded execution without locks, CPU core caches may delay visibility of `modCount` updates across threads, or instructions may be reordered by the JIT compiler. Consequently, a race condition may cause silent data corruption, stale reads, or infinite loops instead of throwing `ConcurrentModificationException`. Fail-fast behavior should only ever be relied upon to detect single-threaded programmatic bugs."

---

## 3. Collection Interface

### Core Contract

`public interface Collection<E> extends Iterable<E>` is the root of the collection hierarchy. It declares fundamental operations shared by all single-value containers:
- **Query Operations**: `size()`, `isEmpty()`, `contains(Object o)`, `iterator()`
- **Modification Operations**: `add(E e)`, `remove(Object o)`
- **Bulk Operations**: `containsAll(Collection<?> c)`, `addAll(Collection<? extends E> c)`, `removeAll(Collection<?> c)`, `retainAll(Collection<?> c)`, `clear()`
- **Array Conversion**: `toArray()`, `toArray(T[] a)`
- **Stream Integration**: `stream()`, `parallelStream()`, `removeIf(Predicate<? super E> filter)`

---

### Curated Essential Methods

| Method Signature | Time Complexity (Avg) | Critical Interview Insight |
| :--- | :--- | :--- |
| `boolean add(E e)` | $O(1)$ amortized (`ArrayList`), $O(1)$ (`HashSet`) | Returns `false` if the collection already contains element and prohibits duplicates (`Set`). |
| `boolean contains(Object o)` | $O(1)$ (`HashSet`), $O(n)$ (`List`) | Takes `Object`, not `E`, because equality depends on `o.equals(element)`. |
| `boolean remove(Object o)` | $O(1)$ (`HashSet`), $O(n)$ (`List`) | Removes first matching instance based on `.equals()`. |
| `boolean retainAll(Collection<?> c)` | $O(n \times m)$ or $O(n)$ if `c` is `Set` | Computes intersection; modifies calling collection in-place. |
| `boolean removeIf(Predicate<? super E> p)`| $O(n)$ | In `ArrayList`, removes in a single pass using a bitset mask, avoiding $O(n^2)$ shifts. |
| `<T> T[] toArray(T[] a)` | $O(n)$ | Pass zero-length array: `list.toArray(new String[0])` for optimal JIT optimization. |

> [!TIP]
> **Why pass `new String[0]` instead of `new String[list.size()]` to `toArray(T[] a)`?**
> In modern JVMs (Java 6u14+), `list.toArray(new String[0])` is faster than pre-sizing `new String[list.size()]` because JVM escape analysis and zero-allocation reflection allow the JVM to allocate the exact array size internally with optimized intrinsics without zero-filling an unused pre-allocated array.

---

### 💡 10+ Years Experienced Interview Questions: Collection Interface

#### Q1: "Why do `contains(Object o)` and `remove(Object o)` accept `Object` instead of generic type `E`?"
**Type System Answer:**
> "This is a deliberate design choice rooted in mathematical set theory and Java's type safety.
> In Java, two objects of different types can legally be equal according to `equals()`. For example, `new Long(5L).equals(new Integer(5))` is false, but custom classes or subtypes can implement symmetric equality across class hierarchies.
> More importantly, asking *'Does this collection of Dogs contain this Cat?'* is a completely safe read-only inquiry. Requiring type `E` would unnecessarily restrict type querying when dealing with wildcards (e.g., `Collection<? extends Number>`). Forcing `contains(E)` would prevent querying a `List<Apple>` with a reference typed as `Fruit`. Hence, read-only and equality-based lookup methods accept `Object`."

---

## 4. List Interface & Implementations

### List Contract & Semantics

`public interface List<E> extends Collection<E>` models an **ordered sequence (sequence)**:
1. **Positional Access**: Supports index-based access, insertion, and removal (`get(int)`, `set(int, E)`, `add(int, E)`, `remove(int)`).
2. **Duplicates Allowed**: Permits multiple identical elements and multiple `null` entries.
3. **Range-View**: `subList(fromIndex, toIndex)` returns a live backing view (mutations in sublist reflect in parent list and vice versa).

---

### ArrayList Deep Dive

`ArrayList<E>` is backed by a contiguous `Object[] elementData` array.

#### 1. Internal Lifecycle & Lazy Initialization
- **Empty State**: `new ArrayList<>()` initializes `elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;` with capacity $0$. It does **not** allocate memory on heap until the first element is added.
- **First Insertion**: Calling `add()` triggers capacity expansion to `DEFAULT_CAPACITY = 10`.

#### 2. Growth Formula & Amortization
When capacity is exceeded, `grow(minCapacity)` calculates:
$$\text{newCapacity} = \text{oldCapacity} + (\text{oldCapacity} \gg 1) \approx 1.5 \times \text{oldCapacity}$$
- The old array is copied to the new array using `System.arraycopy()` (native C++ memory copy).
- **Time Complexity**: Inserting $N$ elements takes $O(N)$ total time, yielding **amortized $O(1)$** per append.

```text
Capacity Progression: 10 → 15 → 22 → 33 → 49 → 73 → 109 ...
Growth Factor: 1.5x (chosen to allow memory allocator to reuse freed adjacent memory blocks over 2.0x)
```

---

### LinkedList Deep Dive

`LinkedList<E>` is implemented as a **Doubly-Linked List**. Each element is wrapped inside a private `Node<E>`:
```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```

#### Node Allocation & Traversal Overhead
- **Memory Footprint**: On a 64-bit JVM with Compressed OOPs:
  - Node object header: 12 bytes + 4 bytes padding = 16 bytes.
  - Three references (`item`, `next`, `prev`): $3 \times 4 = 12$ bytes.
  - Total = **24 bytes of overhead per single element** (excluding element itself).
- **Indexed Access Traversal**: `get(index)` optimizes traversal by starting from head if $\text{index} < (\text{size} \gg 1)$, or tail if $\text{index} \ge (\text{size} \gg 1)$. However, it remains **$O(n)$**.

> [!WARNING]
> **The `LinkedList.get(i)` Anti-pattern:**
> ```java
> for (int i = 0; i < linkedList.size(); i++) {
>     System.out.println(linkedList.get(i)); // DISASTER: O(n^2) total time!
> }
> ```
> Always use an `Iterator` or enhanced for-loop ($O(n)$ total time) for `LinkedList`.

---

### Vector & Stack Legacy Analysis

- **`Vector<E>`**: Introduced in Java 1.0. Backed by resizable array where **every public method is `synchronized`**. In single-threaded code, acquiring intrinsic locks incurs unnecessary CPU overhead. When resizing, it doubles capacity ($2\times$) by default.
- **`Stack<E>`**: Extends `Vector`. Violates the **Liskov Substitution Principle (LSP)** because as a subclass of `Vector`, callers can invoke `stack.add(0, item)` or `stack.remove(5)`, breaking LIFO stack invariants.
- **Modern Replacement**: Always use `Deque<E> stack = new ArrayDeque<>();`.

---

### AbstractList & Skeletal Classes

`AbstractList` manages `modCount` and provides default implementations for index iteration (`listIterator()`), `subList()`, `equals()`, and `hashCode()`.
- For random-access data structures (like arrays), extend `AbstractList`.
- For sequential-access data structures (like linked lists), extend `AbstractSequentialList`.

---

### AbstractSequentialList

`AbstractSequentialList<E>` extends `AbstractList<E>` and is the skeletal implementation designed for **sequential-access** data structures such as linked lists.

#### Design Principle
- Requires implementing only **`listIterator(int index)`** and **`size()`**.
- All positional access operations (`get`, `set`, `add`, `remove`) are implemented in terms of the list iterator — traversal is sequential, not random.
- `LinkedList<E>` directly extends `AbstractSequentialList<E>`.

```java
// Minimal custom sequential list — only 2 abstract methods required:
class MyLinkedList<E> extends AbstractSequentialList<E> {
    @Override
    public ListIterator<E> listIterator(int index) {
        // implement forward/backward traversal
        return ...;
    }

    @Override
    public int size() {
        return ...;
    }
}
```

| Feature | `AbstractList<E>` | `AbstractSequentialList<E>` |
| :--- | :--- | :--- |
| **Requires** | `get(int index)` + `size()` | `listIterator(int index)` + `size()` |
| **Best for** | Random-access structures (arrays) | Sequential structures (linked lists) |
| **Random Access?** | Yes — O(1) via `get()` | No — O(n) sequential traversal via iterator |
| **Used by** | `ArrayList` | `LinkedList` |

> **Interview Insight**: `AbstractSequentialList` implements the Template Method pattern — it provides final `get()`, `set()`, `add()`, and `remove()` methods that delegate all work to the `listIterator()` the subclass provides. This means you only write traversal logic once.

---

### ArrayList vs LinkedList: Architecture & Hardware Comparison

| Feature | `ArrayList<E>` | `LinkedList<E>` |
| :--- | :--- | :--- |
| **Backing Structure** | Contiguous `Object[]` array | Dispersed heap `Node` objects |
| **Random Access (`get(i)`)** | **$O(1)$** | **$O(n)$** |
| **Insert/Delete at Head** | $O(n)$ (shifts elements) | **$O(1)$** |
| **Insert/Delete at Tail** | **Amortized $O(1)$** | **$O(1)$** |
| **Insert/Delete in Middle** | $O(n)$ (data shift dominates) | $O(n)$ (traversal dominates) |
| **CPU Cache Locality** | **Exceptional** (contiguous memory prefetching) | **Terrible** (cache misses chasing heap pointers) |
| **GC Pressure** | Low (single array reference) | High (millions of small `Node` objects) |
| **Recommended Usage** | 99% of general-purpose List requirements | Only for queue/deque when `ArrayDeque` cannot be used |

---

### Curated Essential List Methods

```java
List<String> list = new ArrayList<>(32); // Pre-size capacity to avoid resizing
list.add("Java");
list.add(1, "Go");                // O(n) shift
list.set(0, "Rust");               // O(1) in-place replacement
String val = list.get(0);          // O(1) random access
list.remove(1);                    // O(n) shift
list.replaceAll(String::toUpperCase); // In-place transformation
list.sort(Comparator.naturalOrder()); // TimSort: O(n log n)
List<String> view = list.subList(0, 1); // Live sub-window view
```

---

### 💡 10+ Years Experienced Interview Questions: List Internals

#### Q1: "Why does `LinkedList` perform slower than `ArrayList` in real-world benchmarks even for frequent middle insertions?"
**Hardware & Memory Systems Answer:**
> "Algorithmic textbooks claim `LinkedList` has $O(1)$ insertion while `ArrayList` has $O(n)$ shift cost. However, in modern hardware architectures:
> 1. **Node Traversal Cost**: To insert into `LinkedList` at index $k$, one must first traverse $k$ nodes ($O(k)$ pointer dereferences). For `ArrayList`, finding index $k$ is an immediate $O(1)$ offset calculation.
> 2. **Hardware Cache Lines & Prefetching**: Modern CPUs load memory in 64-byte cache lines. An `ArrayList`'s array elements are contiguous in physical RAM, allowing CPU hardware prefetchers to anticipate memory access and load consecutive elements into L1/L2 cache ahead of time. `LinkedList` nodes are allocated arbitrarily across the JVM heap; dereferencing `node.next` almost always causes a **CPU L1/L2 cache miss**, stalling the CPU pipeline while fetching from high-latency main RAM.
> 3. **Native Memory Copying**: In `ArrayList`, element shifting is executed via `System.arraycopy()`, which maps directly to hardware vectorized SIMD instructions (`memmove`/`memcpy`), moving hundreds of bytes in a few CPU cycles.
> Thus, `ArrayList` consistently outperforms `LinkedList` for sizes well into tens of thousands of elements."

#### Q2: "What occurs if you mutate the structural topology of a parent `ArrayList` while holding an active reference to a `subList()` view?"
**JVM Internals Answer:**
> "The returned `subList()` is an instance of the inner class `java.util.ArrayList.SubList`. It does not create an independent copy; it retains a reference to the parent `ArrayList` and captures the parent's `modCount` at the time of view creation.
> If the parent `ArrayList` undergoes structural modification (e.g., `parent.add(...)`, `parent.remove(...)`), the parent's `modCount` increments. When any method (`get()`, `add()`, `size()`) is subsequently called on the `subList`, it verifies:
> ```java
> if (this.modCount != this.root.modCount)
>     throw new ConcurrentModificationException();
> ```
> Thus, modifying the parent invalidates all active `subList` instances, throwing `ConcurrentModificationException` upon the next sublist access."

---

## 5. Set Interface & Implementations

### Set Contract

`public interface Set<E> extends Collection<E>` models a mathematical set:
- **No Duplicates**: At most one element `e1` such that `e1.equals(e2)`.
- **Null Semantics**: `HashSet` and `LinkedHashSet` permit at most one `null`; `TreeSet` prohibits `null` (throws `NullPointerException` on comparison).
- **Equality Basis**: Elements are distinguished via `hashCode()` and `equals()`, or via `compareTo()` / `Comparator` in sorted sets.

---

### HashSet

**Package**: `java.util.HashSet<E>` | **Implements**: `Set<E>`, `Cloneable`, `Serializable` | **Backed by**: `HashMap<E, Object>`

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `HashSet()` | Empty set; default capacity 16, load factor 0.75 |
| `HashSet(int initialCapacity)` | Specified initial capacity |
| `HashSet(int initialCapacity, float loadFactor)` | Specified capacity and load factor |
| `HashSet(Collection<? extends E> c)` | Initializes from a collection (duplicates removed) |

#### Internal Structure
Backed entirely by an internal `HashMap<E, Object>` instance:
```java
private transient HashMap<E, Object> map;
private static final Object PRESENT = new Object(); // Dummy placeholder

public boolean add(E e) {
    return map.put(e, PRESENT) == null;
}
```

#### Methods

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `add(E e)` | Adds element; returns `false` if already present | O(1) avg |
| `remove(Object o)` | Removes element; returns `false` if not found | O(1) avg |
| `contains(Object o)` | Returns `true` if element exists | O(1) avg |
| `size()` | Returns number of elements | O(1) |
| `isEmpty()` | Returns `true` if empty | O(1) |
| `clear()` | Removes all elements | O(n) |
| `iterator()` | Returns an iterator (no guaranteed order) | O(1) |
| `toArray()` | Returns all elements as `Object[]` | O(n) |
| `addAll(Collection c)` | Adds all elements — set union | O(n) |
| `retainAll(Collection c)` | Keeps only common elements — intersection | O(n) |
| `removeAll(Collection c)` | Removes all elements in c — difference | O(n) |
| `containsAll(Collection c)` | Returns `true` if all elements of c are present | O(n) |

```java
HashSet<String> set = new HashSet<>();
set.add("Apple");  set.add("Mango");  set.add("Apple"); // Duplicate ignored
System.out.println(set.size());           // 2
System.out.println(set.contains("Mango")); // true
set.remove("Mango");
System.out.println(set);                  // [Apple] (order may vary)
```

#### Advantages
- **Fastest Set** — O(1) average for add, remove, contains
- **Automatic deduplication** — ideal for uniqueness checks in DSA
- Permits **one `null`** element
- Uses battle-tested `HashMap` internally

#### Disadvantages
- **No ordering** — iteration order is unpredictable and may change on rehash
- **Not thread-safe** — wrap with `Collections.synchronizedSet()` or use `ConcurrentHashMap.newKeySet()`
- Extra memory overhead from the dummy `PRESENT` object per entry
- **Worst-case O(n)** on hash collisions (Java 8+ mitigates with treeification at bucket size 8)

---

### LinkedHashSet

**Package**: `java.util.LinkedHashSet<E>` | **Extends**: `HashSet<E>` | **Backed by**: `LinkedHashMap<E, Object>`

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `LinkedHashSet()` | Empty set; default capacity 16, load factor 0.75 |
| `LinkedHashSet(int initialCapacity)` | Specified initial capacity |
| `LinkedHashSet(int initialCapacity, float loadFactor)` | Specified capacity and load factor |
| `LinkedHashSet(Collection<? extends E> c)` | From a collection; preserves insertion order, removes duplicates |

#### Internal Structure
- Extends `HashSet`, backed by `LinkedHashMap`.
- Maintains a **doubly-linked list** through all entries — ensures insertion-order iteration.
- Each entry stores `before` and `after` pointer references in addition to the hash bucket link.

#### Methods
All methods are inherited from `HashSet`. The key behavioral difference is **guaranteed insertion-order iteration**.

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `add(E e)` | Adds element at tail of linked list; no-op if duplicate | O(1) avg |
| `remove(Object o)` | Removes element, updates linked order | O(1) avg |
| `contains(Object o)` | Checks element presence | O(1) avg |
| `size()` | Number of elements | O(1) |
| `isEmpty()` | `true` if set has no elements | O(1) |
| `clear()` | Removes all elements | O(n) |
| `iterator()` | Returns iterator in **insertion order** | O(1) |

```java
LinkedHashSet<String> lhs = new LinkedHashSet<>();
lhs.add("Banana"); lhs.add("Apple"); lhs.add("Mango"); lhs.add("Apple"); // dup
System.out.println(lhs); // [Banana, Apple, Mango] — insertion order preserved
```

#### Advantages
- **Insertion-order iteration** — predictable, reproducible ordering unlike `HashSet`
- O(1) average for add, remove, contains — same performance as `HashSet`
- Eliminates duplicates while preserving **first-seen order**
- Drop-in replacement for `HashSet` when order matters

#### Disadvantages
- **Higher memory** than `HashSet` — each entry has additional `before`/`after` pointer fields
- **Not thread-safe**
- Slightly slower than `HashSet` due to maintaining the linked list on every insertion/removal

---

### TreeSet & NavigableSet

**Package**: `java.util.TreeSet<E>` | **Implements**: `NavigableSet<E>`, `SortedSet<E>` | **Backed by**: `TreeMap<E, Object>` (Red-Black Tree)

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `TreeSet()` | Empty set using natural ordering (`Comparable`) |
| `TreeSet(Comparator<? super E> comparator)` | Empty set with custom sort order |
| `TreeSet(Collection<? extends E> c)` | From a collection, sorted by natural order |
| `TreeSet(SortedSet<E> s)` | From another `SortedSet`, preserving comparator |

#### Methods

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `add(E e)` | Inserts element in sorted position | O(log n) |
| `remove(Object o)` | Removes element | O(log n) |
| `contains(Object o)` | Checks element presence | O(log n) |
| `first()` | Returns the lowest element | O(log n) |
| `last()` | Returns the highest element | O(log n) |
| `ceiling(E e)` | Smallest element ≥ e (or `null`) | O(log n) |
| `floor(E e)` | Largest element ≤ e (or `null`) | O(log n) |
| `higher(E e)` | Smallest element > e strictly | O(log n) |
| `lower(E e)` | Largest element < e strictly | O(log n) |
| `pollFirst()` | Removes and returns the lowest | O(log n) |
| `pollLast()` | Removes and returns the highest | O(log n) |
| `headSet(E to)` | View of elements < to | O(log n) |
| `tailSet(E from)` | View of elements ≥ from | O(log n) |
| `subSet(E from, E to)` | View of elements [from, to) | O(log n) |
| `size()` | Number of elements | O(1) |
| `iterator()` | Iterator in ascending order | O(1) |
| `descendingIterator()` | Iterator in descending order | O(1) |
| `descendingSet()` | Reverse-order view of the set | O(1) |

```java
TreeSet<Integer> ts = new TreeSet<>(List.of(30, 10, 50, 20, 40));
System.out.println(ts);                         // [10, 20, 30, 40, 50]
System.out.println(ts.ceiling(25));             // 30
System.out.println(ts.floor(25));               // 20
System.out.println(ts.subSet(20, true, 40, true)); // [20, 30, 40]
System.out.println(ts.pollFirst());             // 10 (removed)
```

#### Advantages
- **Always sorted** — no manual sorting needed
- **Rich navigation API** — `floor`, `ceiling`, `higher`, `lower`, `subSet`, `headSet`, `tailSet`
- **Guaranteed O(log n)** — no hash collision worst-case unlike `HashSet`
- **Range queries** — ideal for interval problems, sliding window with sorted order

#### Disadvantages
- **O(log n) per operation** — slower than `HashSet` O(1) for basic add/remove/contains
- **Not thread-safe** — use `ConcurrentSkipListSet` for concurrent sorted set
- **Null elements prohibited** — throws `NullPointerException` (comparison fails on null)
- Higher constant factor than `HashSet` due to Red-Black Tree rebalancing

---

### SortedSet Interface

`public interface SortedSet<E> extends Set<E>` maintains all elements in **ascending sorted order** (natural ordering or via a `Comparator`).

#### Key Methods

| Method | Description |
| :--- | :--- |
| `first()` | Returns the lowest element |
| `last()` | Returns the highest element |
| `headSet(toElement)` | View of elements **strictly less than** `toElement` |
| `tailSet(fromElement)` | View of elements **≥ `fromElement`** |
| `subSet(from, to)` | View from `from` (inclusive) to `to` (exclusive) |
| `comparator()` | Returns the Comparator, or `null` if natural ordering |

```java
SortedSet<Integer> sorted = new TreeSet<>(List.of(10, 30, 20, 50, 40));
System.out.println(sorted.first());         // 10
System.out.println(sorted.last());          // 50
System.out.println(sorted.headSet(30));     // [10, 20]
System.out.println(sorted.tailSet(30));     // [30, 40, 50]
System.out.println(sorted.subSet(20, 40));  // [20, 30]
```

> `SortedSet` is the foundation for all range-based Set queries. The primary implementation is `TreeSet`.

---

### NavigableSet

`public interface NavigableSet<E> extends SortedSet<E>` extends `SortedSet` with **navigation methods** that find the closest match to a given target.

#### Key Methods

| Method | Description |
| :--- | :--- |
| `ceiling(e)` | Smallest element **≥ e** (or `null`) |
| `floor(e)` | Largest element **≤ e** (or `null`) |
| `higher(e)` | Smallest element **> e** strictly (or `null`) |
| `lower(e)` | Largest element **< e** strictly (or `null`) |
| `pollFirst()` | Retrieves **and removes** the lowest element |
| `pollLast()` | Retrieves **and removes** the highest element |
| `descendingSet()` | Returns a reverse-order view of the set |
| `descendingIterator()` | Iterator in descending order |

```java
NavigableSet<Integer> ns = new TreeSet<>(List.of(10, 20, 30, 40, 50));

System.out.println(ns.ceiling(25));       // 30
System.out.println(ns.floor(25));         // 20
System.out.println(ns.higher(30));        // 40
System.out.println(ns.lower(30));         // 20
System.out.println(ns.pollFirst());       // 10 (removes it)
System.out.println(ns.descendingSet());   // [50, 40, 30, 20]
```

**Implemented by**: `TreeSet` (single-threaded), `ConcurrentSkipListSet` (thread-safe)

---

### EnumSet

`EnumSet<E extends Enum<E>>` is a **specialized, high-performance `Set`** implementation exclusively for `enum` types.

#### Internal Architecture — Bit Vector
- Backed by a **bit vector** — a single `long` field (for enums with ≤ 64 constants) or a `long[]` array (for larger enums).
- Each bit position corresponds to an enum constant's `ordinal()`.
- All operations use **bitwise instructions** — `add`, `contains`, `remove` are all `O(1)` with zero hashing overhead.

#### Key Characteristics
- All elements must come from a **single enum type** — enforced at compile time.
- **Null elements are prohibited** — throws `NullPointerException`.
- **Not thread-safe** — wrap with `Collections.synchronizedSet()` if needed.
- **Iteration order** = enum declaration order (ordinal order).
- No public constructors — use static factory methods.

```java
enum Day { MON, TUE, WED, THU, FRI, SAT, SUN }

// Static factory methods
EnumSet<Day> weekdays = EnumSet.range(Day.MON, Day.FRI);       // [MON, TUE, WED, THU, FRI]
EnumSet<Day> weekend  = EnumSet.complementOf(weekdays);        // [SAT, SUN]
EnumSet<Day> workDays = EnumSet.of(Day.MON, Day.WED, Day.FRI); // [MON, WED, FRI]
EnumSet<Day> allDays  = EnumSet.allOf(Day.class);              // [MON, TUE, WED, THU, FRI, SAT, SUN]
EnumSet<Day> noDays   = EnumSet.noneOf(Day.class);             // []

// Set operations (bitwise under the hood)
weekdays.retainAll(workDays);  // Bitwise AND
weekdays.addAll(weekend);      // Bitwise OR
```

| Feature | `EnumSet` | `HashSet` |
| :--- | :--- | :--- |
| **Performance** | `O(1)` via bitwise ops | `O(1)` average via hashing |
| **Memory** | ~1 `long` per 64 constants | Full `HashMap` backing |
| **Ordering** | Enum declaration order | Unpredictable |
| **Null support** | No (NPE) | Yes (1 null) |
| **Type restriction** | Enum only | Any type |

> **Interview Insight**: When the value domain is a fixed `enum`, `EnumSet` is always the correct choice — it is significantly faster and more memory-efficient than any other `Set`.

---

### ConcurrentSkipListSet

`ConcurrentSkipListSet<E>` is a **thread-safe, sorted set** backed by a `ConcurrentSkipListMap<E, Object>` from `java.util.concurrent`.

#### Internal Architecture — Skip List
A Skip List is a probabilistic data structure of layered linked lists enabling fast lookup:

```text
Level 3:  HEAD ─────────────────────────────> 50 ──> null
Level 2:  HEAD ─────────> 20 ──────────────> 50 ──> null
Level 1:  HEAD ──> 10 ──> 20 ──> 30 ──> 40 ──> 50 ──> null
```

- Lookups traverse upper "express lane" levels, skipping over many nodes.
- Each level is a subset of the level below — built probabilistically.
- **Lock-free**: All mutations use CAS (Compare-And-Swap) operations.

#### Key Characteristics
- **Thread-safe** — concurrent reads and writes without locking.
- **Sorted** — iteration is in ascending element order.
- **Null elements prohibited** (NullPointerException).
- Implements `NavigableSet` — supports `ceiling()`, `floor()`, `higher()`, `lower()`, `pollFirst()`, `pollLast()`.
- **Expected complexity**: `O(log n)` for add, remove, contains.

```java
ConcurrentSkipListSet<Integer> set = new ConcurrentSkipListSet<>();
set.add(30); set.add(10); set.add(50); set.add(20);

System.out.println(set);              // [10, 20, 30, 50]
System.out.println(set.first());      // 10
System.out.println(set.ceiling(25));  // 30
System.out.println(set.pollLast());   // 50 (removed)
```

| Feature | `TreeSet` | `ConcurrentSkipListSet` |
| :--- | :--- | :--- |
| **Thread-Safe** | No | **Yes** |
| **Backing** | Red-Black Tree | Skip List (CAS-based) |
| **Complexity** | O(log n) guaranteed | O(log n) expected |
| **Null support** | Prohibited | Prohibited |
| **Use case** | Single-threaded sorted set | Concurrent sorted set |

---

### List vs Set

| Feature | `List<E>` | `Set<E>` |
| :--- | :--- | :--- |
| **Duplicates** | Permitted | Strictly Prohibited |
| **Ordering** | Insertion / Index sequence | Unordered (`HashSet`), Insertion (`LinkedHashSet`), Sorted (`TreeSet`) |
| **Positional Access** | Yes (`list.get(index)`) | No (must traverse via iterator/stream) |
| **Null Elements** | Multiple `null`s permitted | At most 1 `null` (`HashSet`/`LinkedHashSet`), 0 (`TreeSet`) |

---

### Curated Essential Set Methods

```java
Set<String> set = new HashSet<>();
set.add("A");
set.add("B");
boolean added = set.add("A"); // Returns false (duplicate ignored)
boolean exists = set.contains("B"); // O(1) average lookup

NavigableSet<Integer> sorted = new TreeSet<>(List.of(10, 20, 30, 40, 50));
int floor = sorted.floor(25);     // 20
int ceiling = sorted.ceiling(25); // 30
NavigableSet<Integer> range = sorted.subSet(20, true, 40, false); // [20, 30]
```

---

### 💡 10+ Years Experienced Interview Questions: Set Mechanics

#### Q1: "What catastrophic bug occurs if `compareTo()` in a `TreeSet` is inconsistent with `equals()`?"
**Contract Violation Answer:**
> "`Set`'s general contract mandates uniqueness based on `equals()`. However, `TreeSet` uses `Comparable.compareTo()` or `Comparator.compare()` for all element equality and uniqueness checks.
> If `a.equals(b)` is `false`, but `a.compareTo(b) == 0`, `TreeSet` treats `a` and `b` as **identical keys**:
> ```java
> class Employee implements Comparable<Employee> {
>     int id; String name;
>     public int compareTo(Employee o) { return Integer.compare(this.id, o.id); }
>     public boolean equals(Object o) { /* compares both id AND name */ }
> }
> ```
> When inserting two employees with the same `id` but different `name`:
> 1. `set.add(emp1)` succeeds.
> 2. `set.add(emp2)` returns `false` and discards `emp2`!
> 3. `set.contains(emp2)` returns `true` even though `emp1.equals(emp2)` is false.
> This violates the fundamental `Set` contract. In production, always ensure that `(x.compareTo(y) == 0) == x.equals(y)`."

---

## 6. Queue & Deque Interfaces

### Queue Contract

`public interface Queue<E> extends Collection<E>` models a collection designed for holding elements prior to processing.
It exposes **two sets of methods** for every fundamental operation:

| Operation | Throws Exception (if failed) | Returns Special Value (`false` / `null`) |
| :--- | :--- | :--- |
| **Insert at Tail** | `add(e)` (throws `IllegalStateException` if bounded full) | `offer(e)` (returns `false` if bounded full) |
| **Remove from Head** | `remove()` (throws `NoSuchElementException` if empty) | `poll()` (returns `null` if empty) |
| **Examine Head** | `element()` (throws `NoSuchElementException` if empty) | `peek()` (returns `null` if empty) |

> [!TIP]
> In production code and competitive programming, **always prefer `offer()`, `poll()`, and `peek()`** to handle empty/full queue states without paying the heavy performance penalty of generating Java exception stack traces.

---

### PriorityQueue Deep Dive

**Package**: `java.util.PriorityQueue<E>` | **Implements**: `Queue<E>` | **Backed by**: Array-Based Binary Min-Heap

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `PriorityQueue()` | Default capacity 11, natural ordering |
| `PriorityQueue(int initialCapacity)` | Specified initial capacity |
| `PriorityQueue(Comparator<? super E> comparator)` | Default capacity, custom ordering |
| `PriorityQueue(int initialCapacity, Comparator<? super E> comparator)` | Specified capacity and comparator |
| `PriorityQueue(Collection<? extends E> c)` | From collection; heapified in O(n) via Floyd's algorithm |
| `PriorityQueue(PriorityQueue<? extends E> c)` | From another PriorityQueue |

#### Internal Structure — Binary Min-Heap
- Backed by `Object[] queue`. For any node at index $k$: Left = $2k+1$, Right = $2k+2$, Parent = $(k-1) \gg 1$.
- `offer(e)`: Appends to end, runs `siftUp()` → **O(log n)**.
- `poll()`: Replaces root with last element, runs `siftDown()` → **O(log n)**.
- `peek()`: Reads `queue[0]` → **O(1)**.
- **Heapify Construction**: `new PriorityQueue<>(collection)` uses Floyd's bottom-up algorithm → **O(n)**.
- **Iteration Order**: `iterator()` traverses raw array — **NOT in sorted order**. Use repeated `poll()` for ordered output.

#### Methods

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `offer(E e)` | Inserts element (preferred; returns false on failure) | O(log n) |
| `add(E e)` | Inserts element (throws `IllegalStateException` on failure) | O(log n) |
| `poll()` | Removes and returns the minimum element; `null` if empty | O(log n) |
| `remove()` | Same as `poll()`; throws `NoSuchElementException` if empty | O(log n) |
| `peek()` | Returns minimum without removing; `null` if empty | O(1) |
| `element()` | Same as `peek()`; throws if empty | O(1) |
| `remove(Object o)` | Removes a specific element (linear scan) | O(n) |
| `contains(Object o)` | Checks if element exists | O(n) |
| `size()` | Number of elements | O(1) |
| `isEmpty()` | `true` if no elements | O(1) |
| `clear()` | Removes all elements | O(n) |
| `toArray()` | Returns heap array in heap order (NOT sorted) | O(n) |
| `comparator()` | Returns comparator, or `null` if natural order | O(1) |

```java
// Min-Heap (default — natural ordering)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
minHeap.offer(30); minHeap.offer(10); minHeap.offer(20);
System.out.println(minHeap.peek()); // 10
System.out.println(minHeap.poll()); // 10 (removed)

// Max-Heap
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());
maxHeap.offer(30); maxHeap.offer(10); maxHeap.offer(20);
System.out.println(maxHeap.poll()); // 30

// Custom comparator — sort strings by length
PriorityQueue<String> byLength = new PriorityQueue<>(Comparator.comparingInt(String::length));
byLength.offer("Banana"); byLength.offer("Apple"); byLength.offer("Fig");
System.out.println(byLength.poll()); // Fig
```

#### Advantages
- **Efficient Min/Max extraction** — O(log n) poll, O(1) peek
- **O(n) bulk construction** — `new PriorityQueue<>(collection)` is faster than n insertions
- **Custom ordering** via `Comparator` — min-heap or max-heap with one line
- Ideal for: **Top-K problems**, Dijkstra's algorithm, task scheduling, merge K sorted arrays

#### Disadvantages
- **Does NOT iterate in sorted order** — `for-each` traverses raw heap array, not sorted
- **Not thread-safe** — use `PriorityBlockingQueue` for concurrent access
- **Null elements prohibited** — throws `NullPointerException`
- `contains()` and element-specific `remove(Object)` are **O(n)** — no index structure
- **Not stable** — equal-priority elements have arbitrary relative ordering

---

### ArrayDeque (Circular Buffer)

**Package**: `java.util.ArrayDeque<E>` | **Implements**: `Deque<E>`, `Queue<E>` | **Backed by**: Resizable circular array

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `ArrayDeque()` | Default initial capacity of 16 |
| `ArrayDeque(int numElements)` | Capacity large enough to hold `numElements` |
| `ArrayDeque(Collection<? extends E> c)` | Initialized from a collection |

#### Internal Structure
- Resizable circular array `Object[] elements` with `int head` and `int tail` pointers.
- Capacity always forced to a **power of two** ($2^k$) — index wrapping uses bitwise masking:
  $\text{head} = (\text{head} - 1) \ \& \ (\text{elements.length} - 1)$
- **Zero per-operation allocations** — no `Node` objects created per push/offer.
- **Null elements prohibited** — null is used as sentinel for empty slots.

#### Methods — As Stack (LIFO)

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `push(E e)` | Pushes element to head (`addFirst`) | O(1) amortized |
| `pop()` | Removes and returns head (`removeFirst`) | O(1) |
| `peek()` / `peekFirst()` | Returns head without removing | O(1) |

#### Methods — As Queue (FIFO)

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `offer(E e)` / `offerLast(E e)` | Adds element to tail | O(1) amortized |
| `poll()` / `pollFirst()` | Removes and returns from head | O(1) |
| `peek()` / `peekFirst()` | Inspects head without removing | O(1) |

#### Methods — As Deque (Both Ends)

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `addFirst(E e)` / `offerFirst(E e)` | Inserts at head | O(1) amortized |
| `addLast(E e)` / `offerLast(E e)` | Inserts at tail | O(1) amortized |
| `removeFirst()` / `pollFirst()` | Removes from head | O(1) |
| `removeLast()` / `pollLast()` | Removes from tail | O(1) |
| `peekFirst()` | Inspects head | O(1) |
| `peekLast()` | Inspects tail | O(1) |
| `size()` | Number of elements | O(1) |
| `isEmpty()` | `true` if empty | O(1) |
| `contains(Object o)` | Checks presence (linear scan) | O(n) |
| `remove(Object o)` | Removes first occurrence | O(n) |
| `clear()` | Removes all elements | O(n) |
| `iterator()` | Forward iterator | O(1) |
| `descendingIterator()` | Reverse iterator | O(1) |

```java
// As Stack (LIFO)
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10); stack.push(20); stack.push(30);
System.out.println(stack.pop()); // 30

// As Queue (FIFO)
Queue<String> queue = new ArrayDeque<>();
queue.offer("A"); queue.offer("B"); queue.offer("C");
System.out.println(queue.poll()); // A

// As Deque (both ends)
ArrayDeque<Integer> deque = new ArrayDeque<>();
deque.addFirst(1); deque.addLast(2); deque.addFirst(0);
System.out.println(deque); // [0, 1, 2]
System.out.println(deque.peekLast()); // 2
```

#### Advantages
- **Most versatile** — serves as Stack, Queue, and Deque in one class
- **Faster than `LinkedList`** at both ends — zero heap allocations per operation
- **Faster than legacy `Stack`** — no synchronization overhead
- **Excellent cache locality** — circular array fits in CPU cache lines
- Preferred by JDK team over `Stack` and `LinkedList` for stack/queue use

#### Disadvantages
- **Null elements prohibited** — throws `NullPointerException`
- **Not thread-safe** — use `ConcurrentLinkedDeque` for concurrent access
- `contains()` and `remove(Object)` are **O(n)** — no index structure
- Resize copies entire array — amortized O(1) but worst-case O(n) per add
- **No random access** — can only access head/tail efficiently

---

### AbstractQueue

`AbstractQueue<E>` is the **skeletal base class** for Queue implementations. It implements the Queue interface's *throwing* methods in terms of the *special-value* methods.

#### Design — Template Method Pattern

| Implemented (throws on failure) | Delegates to (returns special value) |
| :--- | :--- |
| `add(e)` | `offer(e)` — throws `IllegalStateException` if rejected |
| `remove()` | `poll()` — throws `NoSuchElementException` if empty |
| `element()` | `peek()` — throws `NoSuchElementException` if empty |
| `addAll(c)` | Iterates `c` and calls `add(e)` for each element |
| `clear()` | Repeatedly calls `poll()` until empty |

```java
// To implement a custom Queue, extend AbstractQueue and provide:
//   offer(E e), poll(), peek(), size(), iterator()
class BoundedQueue<E> extends AbstractQueue<E> {
    private final Object[] data;
    private int head = 0, tail = 0, count = 0;
    private final int capacity;

    BoundedQueue(int capacity) {
        this.capacity = capacity;
        this.data = new Object[capacity];
    }

    @Override
    public boolean offer(E e) {
        if (count == capacity) return false;
        data[tail++ % capacity] = e;
        count++;
        return true;
    }

    @Override @SuppressWarnings("unchecked")
    public E poll() {
        if (count == 0) return null;
        E val = (E) data[head++ % capacity];
        count--;
        return val;
    }

    @Override @SuppressWarnings("unchecked")
    public E peek() { return count == 0 ? null : (E) data[head % capacity]; }

    @Override public int size() { return count; }
    @Override public Iterator<E> iterator() { throw new UnsupportedOperationException(); }
}
```

**Known concrete subclasses**: `PriorityQueue`, `ArrayBlockingQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue`, `DelayQueue`.

---

### ConcurrentLinkedQueue

`ConcurrentLinkedQueue<E>` is an **unbounded, thread-safe, non-blocking FIFO queue** based on the **Michael-Scott lock-free linked queue** algorithm.

#### Internal Architecture
- Backed by singly-linked `Node<E>` objects with `volatile` `item` and `next` fields.
- Uses **CAS (Compare-And-Swap)** on the `tail` pointer for enqueue and `head` pointer for dequeue.
- Multiple threads can enqueue and dequeue **simultaneously without acquiring any lock**.

```text
head (sentinel)              tail
      ↓                        ↓
  [null] ──> [A] ──> [B] ──> [C] ──> null
```

#### Key Characteristics
- **Thread-safe** — lock-free via CAS.
- **Unbounded** — grows dynamically; never blocks.
- **Null elements prohibited** — throws `NullPointerException`.
- `size()` is **O(n)** — traverses the entire linked chain! Use `isEmpty()` for size checks.
- Implements **weakly consistent** iterator — never throws `ConcurrentModificationException`.
- No blocking methods (`put`/`take`) — use `BlockingQueue` when coordination is needed.

```java
ConcurrentLinkedQueue<String> queue = new ConcurrentLinkedQueue<>();
queue.offer("Task1");
queue.offer("Task2");
queue.offer("Task3");

String task = queue.poll();         // "Task1" — removed
System.out.println(queue.peek());   // "Task2" — not removed
System.out.println(queue.isEmpty()); // false  — prefer over size() > 0
```

| Feature | `ConcurrentLinkedQueue` | `ArrayBlockingQueue` | `LinkedBlockingQueue` |
| :--- | :--- | :--- | :--- |
| **Blocking** | Non-blocking | Blocking (`put`, `take`) | Blocking (`put`, `take`) |
| **Capacity** | Unbounded | Bounded | Bounded or Unbounded |
| **Locking** | CAS (lock-free) | Single `ReentrantLock` | `putLock` + `takeLock` |
| **`size()`** | O(n) traversal | O(1) | O(1) |
| **Best for** | High-throughput async tasks | Strict capacity control | Producer-consumer pipelines |

---

### Curated Essential Queue & Deque Methods

```java
// FIFO Queue
Queue<String> queue = new ArrayDeque<>();
queue.offer("Req1");
queue.offer("Req2");
String next = queue.poll(); // "Req1"

// Min-Heap PriorityQueue
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(50);
pq.offer(10);
pq.offer(30);
int min = pq.poll(); // 10

// Max-Heap PriorityQueue
PriorityQueue<Integer> maxPq = new PriorityQueue<>(Comparator.reverseOrder());

// LIFO Stack (ArrayDeque as Stack)
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10); // addFirst()
stack.push(20);
int top = stack.pop(); // removeFirst() -> 20
```

---

### 💡 10+ Years Experienced Interview Questions: Queues & Heaps

#### Q1: "Why should `ArrayDeque` always be preferred over `LinkedList` for implementing FIFO Queues and LIFO Stacks?"
**Systems & Memory Answer:**
> "`ArrayDeque` beats `LinkedList` on every architectural metric:
> 1. **Memory Allocation**: `LinkedList` allocates a new `Node` heap object for every `push()` / `offer()`, generating constant GC allocation churn. `ArrayDeque` uses a contiguous circular array buffer, generating zero per-element heap allocations once sized.
> 2. **Cache Locality**: `ArrayDeque` reads contiguous memory, which fits inside CPU L1 cache lines. `LinkedList` requires chasing pointers to fragmented heap addresses, causing constant CPU cache misses.
> 3. **Execution Overhead**: In benchmarks, `ArrayDeque` is roughly **2x to 3x faster than `LinkedList` as a Queue**, and significantly faster than legacy `Stack` (which suffers from synchronization locks)."

#### Q2: "Can `PriorityQueue` be used as a stable priority queue? What happens when two elements have identical priority?"
**Algorithm Answer:**
> "By default, Java's `PriorityQueue` is **NOT stable**. If two elements have equal priority (`compare(a, b) == 0`), the tie-breaker is arbitrary and depends entirely on heap tree arrangement. FIFO ordering among equal elements is not preserved.
> To make it stable in a production job scheduler, you must wrap elements in a sequence-tagged container:
> ```java
> record ScheduledJob(Job job, int priority, long sequenceNumber) {}
> 
> Comparator<ScheduledJob> cmp = Comparator
>     .comparingInt(ScheduledJob::priority)
>     .thenComparingLong(ScheduledJob::sequenceNumber);
> PriorityQueue<ScheduledJob> stablePq = new PriorityQueue<>(cmp);
> ```
> The atomic `sequenceNumber` acts as an invariant tie-breaker, guaranteeing strict FIFO behavior for equal-priority jobs."

---

## 7. Map Interface & Implementations

### Why Map is Separate from Collection

`public interface Map<K, V>` models a mapping from unique keys to values.
- Keys cannot be duplicated.
- Each key maps to at most one value.
- Provides three collection views:
  1. `keySet()`: `Set<K>` of keys.
  2. `values()`: `Collection<V>` of values.
  3. `entrySet()`: `Set<Map.Entry<K, V>>` of key-value pairs (the fastest way to iterate a map).

---

### HashMap Internal Architecture

**Package**: `java.util.HashMap<K,V>` | **Implements**: `Map<K,V>` | **Backed by**: Hash table (array of linked nodes / Red-Black trees)

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `HashMap()` | Default capacity 16, load factor 0.75 |
| `HashMap(int initialCapacity)` | Specified initial capacity |
| `HashMap(int initialCapacity, float loadFactor)` | Specified capacity and load factor |
| `HashMap(Map<? extends K, ? extends V> m)` | Initializes from another map |

#### Methods

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `put(K key, V value)` | Associates key with value; returns old value | O(1) avg |
| `get(Object key)` | Returns value for key; `null` if absent | O(1) avg |
| `remove(Object key)` | Removes key-value pair | O(1) avg |
| `containsKey(Object key)` | Returns `true` if key exists | O(1) avg |
| `containsValue(Object value)` | Returns `true` if value exists (linear scan) | O(n) |
| `size()` | Number of key-value mappings | O(1) |
| `isEmpty()` | `true` if no entries | O(1) |
| `clear()` | Removes all entries | O(n) |
| `keySet()` | Returns `Set<K>` of all keys | O(1) |
| `values()` | Returns `Collection<V>` of all values | O(1) |
| `entrySet()` | Returns `Set<Map.Entry<K,V>>` — fastest way to iterate | O(1) |
| `putIfAbsent(K key, V value)` | Inserts only if key not already mapped | O(1) avg |
| `getOrDefault(Object key, V def)` | Returns value or default if absent | O(1) avg |
| `merge(K key, V value, BiFunction f)` | Merges value with existing (e.g., frequency count) | O(1) avg |
| `computeIfAbsent(K key, Function f)` | Computes and stores value if key absent | O(1) avg |
| `forEach(BiConsumer action)` | Applies action to each entry | O(n) |
| `replace(K key, V value)` | Replaces value only if key already mapped | O(1) avg |

```java
HashMap<String, Integer> map = new HashMap<>();
map.put("Apple", 1); map.put("Mango", 2); map.put("Apple", 3); // overwrites
System.out.println(map.get("Apple"));              // 3
System.out.println(map.getOrDefault("Grape", 0));  // 0
map.merge("Apple", 1, Integer::sum);               // Apple -> 4
map.computeIfAbsent("Banana", k -> k.length());    // Banana -> 6

// Fastest iteration pattern
for (Map.Entry<String, Integer> e : map.entrySet()) {
    System.out.println(e.getKey() + " -> " + e.getValue());
}
```

#### Advantages
- **O(1) average** for put, get, remove — fastest Map for unsorted data
- Allows **one null key** and **multiple null values**
- Rich Java 8+ functional API: `merge`, `computeIfAbsent`, `getOrDefault`, `forEach`
- Default choice for most DSA key-value problems

#### Disadvantages
- **No ordering** — iteration order is unpredictable and changes on rehash
- **Not thread-safe** — use `ConcurrentHashMap` for concurrent access
- **Worst-case O(n)** on hash collisions (mitigated by treeification in Java 8+)
- Memory overhead: each entry is a `Node` object with key, value, hash, and next reference

#### Internal Architecture

A standard `HashMap<K, V>` is an array of hash buckets (`Node<K, V>[] table`).

```text
HashMap Internal Structure:
table (Node<K,V>[] of size 2^k)
 [0] -> Node(K, V) -> Node(K, V) -> null (Linked List bucket)
 [1] -> null
 [2] -> TreeNode(K, V) [Red-Black Tree: activated when bin length >= 8 and cap >= 64]
 [3] -> Node(K, V) -> null
```

#### 1. Hash Calculation & Bitwise Scrambling
To protect against poorly implemented `hashCode()` methods that only vary in upper bits:
```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```
The upper 16 bits of the hash are XORed with the lower 16 bits. This ensures high-order bits influence the lower index calculation.

#### 2. Bucket Index Calculation
```java
index = (n - 1) & hash; // where n is capacity (must be power of 2)
```
Because $n$ is always a power of 2, `(n - 1)` is a bitmask of all 1s (e.g., $16 - 1 = 15 = 00001111_2$). The bitwise AND is equivalent to `hash % n`, but executes in a single CPU clock cycle.

#### 3. Collision Resolution & Treeification (Java 8+)
- **Chaining**: Elements colliding in the same bucket are initially stored as a singly linked list (`Node<K,V>`).
- **Treeification**: When a bucket's collision chain reaches `TREEIFY_THRESHOLD = 8`:
  - If total capacity is $< \text{MIN_TREEIFY_CAPACITY (64)}$, the map simply doubles its table capacity via `resize()`.
  - If capacity is $\ge 64$, the linked list is converted into a balanced **Red-Black Tree** (`TreeNode<K,V>`). Worst-case search complexity drops from **$O(n)$ down to $O(\log n)$**.
- **Untreeification**: If removals/resizing reduce tree nodes to $\le \text{UNTREEIFY_THRESHOLD = 6}$, the tree converts back into a singly linked list.

#### 4. Load Factor & Sizing Rationale
- **Default Initial Capacity**: $16$
- **Default Load Factor**: $0.75$
- **Threshold**: $\text{Capacity} \times \text{Load Factor} = 16 \times 0.75 = 12$. Adding the 13th element triggers resizing.
- *Mathematical Rationale*: Based on Poisson distribution under random hash codes, the probability of a bucket having 8 collisions is less than $1 \times 10^{-7}$. 0.75 offers the ideal balance between memory utilization and collision frequency.

---

### LinkedHashMap & LRU Cache

**Package**: `java.util.LinkedHashMap<K,V>` | **Extends**: `HashMap<K,V>` | **Extra**: Doubly-linked list through all entries

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `LinkedHashMap()` | Default capacity 16, load factor 0.75, insertion-order |
| `LinkedHashMap(int initialCapacity)` | Specified capacity, insertion-order |
| `LinkedHashMap(int initialCapacity, float loadFactor)` | Specified capacity and load factor |
| `LinkedHashMap(Map<? extends K, ? extends V> m)` | From another map, insertion-order |
| `LinkedHashMap(int capacity, float loadFactor, boolean accessOrder)` | `true` = access-order (LRU), `false` = insertion-order |

#### Methods
All methods inherited from `HashMap`. Key behavioral difference: predictable **insertion-order** or **access-order** iteration.

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `put(K key, V value)` | Inserts / updates; moves to tail in access-order mode | O(1) avg |
| `get(Object key)` | Returns value; moves entry to tail in access-order mode | O(1) avg |
| `remove(Object key)` | Removes entry, updates linked order | O(1) avg |
| `containsKey(Object key)` | Checks key existence | O(1) avg |
| `size()` | Number of entries | O(1) |
| `keySet()` | Keys in insertion / access order | O(1) |
| `values()` | Values in insertion / access order | O(1) |
| `entrySet()` | Entries in insertion / access order | O(1) |
| `removeEldestEntry(Map.Entry e)` | Override to evict oldest entry automatically | O(1) |

#### Internal Structure & LRU Cache
`LinkedHashMap<K, V>` extends `HashMap<K, V>`. Every node contains `before` and `after` pointers:
```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after;
}
```
- **Ordering Modes**:
  1. *Insertion-Order* (default): Iteration reflects order of key insertions.
  2. *Access-Order*: Calling `get()` or `put()` moves the accessed entry to the tail.

#### LRU Cache Implementation
```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int maxCapacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true); // true = access-order mode
        this.maxCapacity = capacity;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > maxCapacity; // Evicts oldest accessed entry
    }
}
```

#### Advantages
- **Insertion or access-order iteration** — predictable ordering unlike `HashMap`
- Same O(1) average performance as `HashMap`
- **Natural LRU cache** — extend and override `removeEldestEntry()` — no extra library needed
- Useful for: ordered iteration, deduplication with order, cache implementations

#### Disadvantages
- **Higher memory** than `HashMap` — `before`/`after` pointers per node
- **Not thread-safe**
- Slightly slower than `HashMap` due to linked list maintenance on each insertion

---

### TreeMap

**Package**: `java.util.TreeMap<K,V>` | **Implements**: `NavigableMap<K,V>`, `SortedMap<K,V>` | **Backed by**: Red-Black Tree

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `TreeMap()` | Empty map using natural key ordering (`Comparable`) |
| `TreeMap(Comparator<? super K> comparator)` | Empty map with custom key order |
| `TreeMap(Map<? extends K, ? extends V> m)` | From another map, sorted by natural order |
| `TreeMap(SortedMap<K, ? extends V> m)` | From another `SortedMap`, preserving comparator |

#### Methods

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `put(K key, V value)` | Inserts key-value in sorted position | O(log n) |
| `get(Object key)` | Returns value for key | O(log n) |
| `remove(Object key)` | Removes key-value pair | O(log n) |
| `containsKey(Object key)` | Checks key presence | O(log n) |
| `firstKey()` | Returns the lowest key | O(log n) |
| `lastKey()` | Returns the highest key | O(log n) |
| `ceilingKey(K key)` | Smallest key ≥ given key | O(log n) |
| `floorKey(K key)` | Largest key ≤ given key | O(log n) |
| `higherKey(K key)` | Smallest key > given key | O(log n) |
| `lowerKey(K key)` | Largest key < given key | O(log n) |
| `pollFirstEntry()` | Removes and returns entry with lowest key | O(log n) |
| `pollLastEntry()` | Removes and returns entry with highest key | O(log n) |
| `headMap(K toKey)` | View of entries with keys < toKey | O(log n) |
| `tailMap(K fromKey)` | View of entries with keys ≥ fromKey | O(log n) |
| `subMap(K from, K to)` | View of entries with keys [from, to) | O(log n) |
| `keySet()` | Keys in ascending sorted order | O(1) |
| `descendingKeySet()` | Keys in descending order | O(1) |
| `size()` | Number of entries | O(1) |

```java
TreeMap<String, Integer> tm = new TreeMap<>();
tm.put("Banana", 2); tm.put("Apple", 1); tm.put("Mango", 3);

System.out.println(tm);                  // {Apple=1, Banana=2, Mango=3} sorted!
System.out.println(tm.firstKey());        // Apple
System.out.println(tm.ceilingKey("B"));  // Banana
System.out.println(tm.headMap("Mango")); // {Apple=1, Banana=2}
System.out.println(tm.pollFirstEntry()); // Apple=1 (removed)
```

#### Advantages
- **Always sorted by key** — no manual sorting needed
- **Rich navigation API** — `ceilingKey`, `floorKey`, `headMap`, `tailMap`, `subMap`
- **Guaranteed O(log n)** — no hash collision worst-case
- **Range queries** — ideal for interval problems and time-series data

#### Disadvantages
- **O(log n) per operation** — slower than `HashMap` O(1) average
- **Not thread-safe** — use `ConcurrentSkipListMap` for concurrent sorted map
- **Null keys prohibited** — throws `NullPointerException` (comparison fails on null)
- Higher constant factor due to Red-Black Tree rebalancing on every insert/delete

---

### Specialized Maps: Quick Comparison

| Map Class | Thread-Safe | Null Keys/Values | Key Ordering | Special Characteristics |
| :--- | :--- | :--- | :--- | :--- |
| **`HashMap`** | No | 1 null key, multiple null values | None | Default choice; $O(1)$ avg lookup |
| **`LinkedHashMap`** | No | 1 null key, multiple null values | Insertion or Access order | Backing structure for LRU Caches |
| **`TreeMap`** | No | No null keys, multiple null values | Sorted by Key | $O(\log n)$ operations, range queries |
| **`ConcurrentHashMap`**| **Yes** | **No null keys, No null values** | None | High concurrency; striping / node locking |
| **`Hashtable`** | **Yes** (Legacy) | **No null keys, No null values** | None | Coarse synchronized methods; obsolete |
| **`WeakHashMap`** | No | 1 null key, multiple null values | None | Keys held via `WeakReference`; auto-GC'd |
| **`IdentityHashMap`** | No | 1 null key, multiple null values | None | Compares keys using `==` (reference equality) |

---

### WeakHashMap (Detailed)

**Package**: `java.util.WeakHashMap<K,V>` | **Implements**: `Map<K,V>` | **Key storage**: `WeakReference<K>` (GC-reclaimable keys)

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `WeakHashMap()` | Default capacity 16, load factor 0.75 |
| `WeakHashMap(int initialCapacity)` | Specified initial capacity |
| `WeakHashMap(int initialCapacity, float loadFactor)` | Specified capacity and load factor |
| `WeakHashMap(Map<? extends K, ? extends V> m)` | From another map |

#### Methods
All standard `Map` methods are supported. Unique behavior: **entries silently disappear** when keys become GC-eligible.

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `put(K key, V value)` | Associates key with value (key stored as WeakReference) | O(1) avg |
| `get(Object key)` | Returns value; `null` if absent or GC-reclaimed | O(1) avg |
| `remove(Object key)` | Explicitly removes entry | O(1) avg |
| `containsKey(Object key)` | Returns `true` if key still alive and present | O(1) avg |
| `size()` | Approximate size (may include not-yet-expunged entries) | O(n) |
| `isEmpty()` | `true` if no live entries | O(n) |
| `clear()` | Removes all entries | O(n) |
| `keySet()` | Set of currently alive keys | O(n) |
| `values()` | Collection of values for alive keys | O(n) |
| `entrySet()` | Set of live entries | O(n) |

#### How It Works
- In a regular `HashMap`, a key in the map is a **strong reference** — the GC never collects it.
- In `WeakHashMap`, each key is wrapped in a `WeakReference<K>`. When the key has **no other strong reference** in the JVM, the GC may reclaim it.
- After GC reclaims the key, the entry is placed in a `ReferenceQueue`. On subsequent map operations (`put`, `get`, `size`), `WeakHashMap` polls this queue and **automatically expunges stale entries**.

```java
WeakHashMap<Object, String> cache = new WeakHashMap<>();
Object key = new Object();
cache.put(key, "metadata");

System.out.println(cache.size()); // 1

key = null; // Remove the only strong reference to the key
System.gc(); // Suggest GC run
Thread.sleep(100);

System.out.println(cache.size()); // 0 — entry auto-removed by GC!
```

#### Use Cases
- **Metadata caches**: Attach rendering state or computed properties to objects without preventing GC.
- **Listener registries**: Prevent memory leaks when listeners are never explicitly deregistered.
- **Canonicalization caches**: Pool/intern objects but release them when unused elsewhere.

#### Advantages
- **Automatic memory management** — no explicit removal needed; GC-driven expiry
- **Prevents memory leaks** — entries self-destruct when keys become unreachable
- Same `Map` API as `HashMap` — drop-in for cache use cases

#### Disadvantages
- **Non-deterministic expiry** — entries are removed at GC discretion, not on a schedule
- **Not thread-safe** — must synchronize externally
- **`size()` may be inaccurate** — stale entries may not be expunged yet
- **Cannot use intern'd constants as keys** — `String` literals, `Integer` cached values never get GC'd

> [!CAUTION]
> **Never use `WeakHashMap` with JVM-interned constants** — `String` literals, `Integer` cached values (−128 to +127), enum constants, and class literals are permanently strongly referenced by the JVM. `WeakHashMap` will behave identically to `HashMap` for such keys and the entries will never be collected.

---

### IdentityHashMap (Detailed)

**Package**: `java.util.IdentityHashMap<K,V>` | **Implements**: `Map<K,V>` | **Key equality**: `==` (reference identity, not `equals()`)

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `IdentityHashMap()` | Default expected max size of 21 |
| `IdentityHashMap(int expectedMaxSize)` | Sized to hold expected number of mappings |
| `IdentityHashMap(Map<? extends K, ? extends V> m)` | From another map |

#### Key Differences from `HashMap`

| Feature | `HashMap` | `IdentityHashMap` |
| :--- | :--- | :--- |
| **Key Equality** | `key1.equals(key2)` | `key1 == key2` (reference identity) |
| **Hash Code** | `key.hashCode()` | `System.identityHashCode(key)` (memory-address based) |
| **Backing Structure** | Hash table with chaining (linked list / Red-Black tree) | Linear probing open-addressing table |
| **Null Keys** | 1 allowed | 1 allowed |

#### Methods
All standard `Map` methods are supported. The unique behavior is **reference-based key lookup**.

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `put(K key, V value)` | Stores entry; key equality by `==` | O(1) avg |
| `get(Object key)` | Returns value; lookup by `==` | O(1) avg |
| `remove(Object key)` | Removes entry by reference identity | O(1) avg |
| `containsKey(Object key)` | Returns `true` if exact reference is present | O(1) avg |
| `containsValue(Object value)` | Checks value by `==` (reference equality!) | O(n) |
| `size()` | Number of entries | O(1) |
| `keySet()` | Set of keys (identity-based) | O(1) |
| `entrySet()` | Set of entries | O(1) |
| `clear()` | Removes all entries | O(n) |

```java
IdentityHashMap<String, Integer> imap = new IdentityHashMap<>();

String a = new String("hello"); // New heap object
String b = new String("hello"); // Another new heap object

imap.put(a, 1);
imap.put(b, 2);

// a.equals(b) == true, but a != b (different references)
System.out.println(imap.size()); // 2 — treated as DIFFERENT keys!
System.out.println(imap.get(a)); // 1
System.out.println(imap.get(b)); // 2

String c = a; // Same reference
System.out.println(imap.get(c)); // 1 — same reference, same key
```

#### Use Cases
- **Object graph serialization/deserialization**: Track already-visited objects by identity to detect cycles.
- **Proxy and instrumentation frameworks**: Map original objects to their proxy counterparts by reference.
- **JVM agents / memory profilers**: Associate metadata with object instances by reference identity.
- **Graph traversal cycle detection**: Use as a "visited" set where `==` identity matters, not `equals()`.

#### Advantages
- **Reference-identity keying** — the ONLY standard Java Map that uses `==`
- **More memory-efficient** than `HashMap` — no `Node` chaining, uses open addressing
- **Fast** for identity-based lookups

#### Disadvantages
- **Violates `Map` contract** — not a general-purpose Map; misuse causes subtle bugs
- **Not thread-safe**
- **Iteration order unpredictable**
- Rarely needed — specialized use cases only

---

### HashTable (Detailed)

**Package**: `java.util.Hashtable<K,V>` | **Implements**: `Map<K,V>` | **Thread safety**: Full object lock on every method (obsolete)

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `Hashtable()` | Default capacity 11, load factor 0.75 |
| `Hashtable(int initialCapacity)` | Specified initial capacity |
| `Hashtable(int initialCapacity, float loadFactor)` | Specified capacity and load factor |
| `Hashtable(Map<? extends K, ? extends V> t)` | From another map |

#### Methods

| Method | Description | Thread Safety |
| :--- | :--- | :--- |
| `put(K key, V value)` | Inserts / updates entry | `synchronized` |
| `get(Object key)` | Returns value; `null` if absent | `synchronized` |
| `remove(Object key)` | Removes entry | `synchronized` |
| `containsKey(Object key)` | Checks key presence | `synchronized` |
| `containsValue(Object value)` | Checks value presence (linear scan) | `synchronized` |
| `contains(Object value)` | Legacy alias for `containsValue()` | `synchronized` |
| `size()` | Number of entries | `synchronized` |
| `isEmpty()` | `true` if no entries | `synchronized` |
| `clear()` | Removes all entries | `synchronized` |
| `keys()` | Legacy `Enumeration<K>` of keys | `synchronized` |
| `elements()` | Legacy `Enumeration<V>` of values | `synchronized` |
| `keySet()` | Modern `Set<K>` of keys | `synchronized` |
| `entrySet()` | Modern `Set<Map.Entry<K,V>>` | `synchronized` |

#### Architecture
- Every public method is declared `synchronized`, acquiring an intrinsic lock on the **entire `Hashtable` object**.
- **No null keys or null values** — throws `NullPointerException`.
- Resizes by formula: `newCapacity = (oldCapacity × 2) + 1`.
- Backed by a `Entry<K,V>[]` array with separate chaining (linked list buckets).
- Provides legacy `Enumeration`-based iteration (`keys()`, `elements()`) in addition to the modern `Iterator`.

```java
Hashtable<String, Integer> table = new Hashtable<>();
table.put("one", 1);
table.put("two", 2);
// table.put(null, 3);      // NullPointerException!
// table.put("key", null);  // NullPointerException!

int val = table.get("one"); // 1
table.remove("two");

// Legacy enumeration (pre-Iterator)
Enumeration<String> keys = table.keys();
while (keys.hasMoreElements()) {
    System.out.println(keys.nextElement());
}
```

#### Why `Hashtable` Is Deprecated

| Dimension | `Hashtable` | `ConcurrentHashMap` |
| :--- | :--- | :--- |
| **Locking** | Full object lock on **every** operation | Per-bucket lock / CAS on empty bins |
| **Read throughput** | Serialized — only 1 thread reads at a time | **Lock-free reads** via `volatile` |
| **Write concurrency** | 1 writer at a time across entire map | Many concurrent writers (different buckets) |
| **Null support** | No nulls | No nulls |
| **Iteration** | Legacy `Enumeration` | Weakly consistent `Iterator` |
| **Use today?** | **No — never use in new code** | ✅ Preferred for thread-safe maps |

#### Advantages
- Thread-safe out of the box (no external synchronization needed)
- Provides legacy `Enumeration` API for backward compatibility

#### Disadvantages
- **Coarse locking** — entire map locked for every operation, catastrophic throughput under concurrency
- **No null keys or values** — throws `NullPointerException`
- **Obsolete** — replaced by `ConcurrentHashMap` (concurrency) and `HashMap` (single-thread)
- **Legacy API** — `Enumeration`-based iteration is verbose and read-only

> [!CAUTION]
> **Never use `Hashtable` in new code.** Use `ConcurrentHashMap` for concurrent access and `HashMap` for single-threaded use.

---

### Curated Essential Map Operations (Java 8+)

```java
Map<String, Integer> map = new HashMap<>();

// 1. computeIfAbsent: Atomic compute if missing
map.computeIfAbsent("Apple", k -> 100);

// 2. getOrDefault: Avoids null checks
int count = map.getOrDefault("Banana", 0);

// 3. merge: Exceptional for frequency counting
map.merge("Apple", 1, Integer::sum); // Increments count by 1

// 4. putIfAbsent: Inserts only if unmapped
map.putIfAbsent("Orange", 50);

// 5. High-efficiency entry traversal
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " -> " + entry.getValue());
}
```

---

### 💡 10+ Years Experienced Interview Questions: Map Internals

#### Q1: "Why did Java 8 introduce Treeification in `HashMap`? What real-world security vulnerability did it solve?"
**Security & Systems Architecture Answer:**
> "Java 8 introduced Treeification to mitigate **Hash Collision Denial of Service (HashDoS) attacks**.
> In Java 7 and earlier, `HashMap` resolved collisions exclusively via singly-linked lists. If an attacker crafted HTTP POST requests containing thousands of parameter keys with identical hash codes (e.g., keys mathematically crafted to collide), all entries fell into a single bucket.
> Lookup and insertion performance degraded from $O(1)$ to **$O(n)$**. Processing a request with $N$ parameters required $O(N^2)$ CPU comparisons, allowing a modest network stream to peg CPU cores at 100% and completely freeze enterprise web servers (such as Tomcat).
> By converting bucket lists exceeding 8 nodes into balanced Red-Black trees, lookup complexity is capped at **$O(\log n)$**, rendering HashDoS attacks computationally ineffective."

#### Q2: "What happens if a mutable object is used as a `HashMap` key and mutated after insertion?"
**JVM Trap Answer:**
> "If an object's field that participates in `hashCode()` or `equals()` is modified after insertion:
> 1. The key's new hash code will map to a different bucket index.
> 2. Calling `map.get(key)` calculates the *new* bucket index, where no matching entry exists $\rightarrow$ **returns `null`**.
> 3. The original entry is trapped indefinitely in the old bucket: it cannot be retrieved, updated, or removed via normal key operations, leading to a **silent memory leak**.
> **Golden Rule**: Always use strictly **immutable objects** (such as `String`, `Integer`, `UUID`, or Java 14+ `record`) as `Map` keys."

---

## 8. Comparable vs Comparator

### Fundamental Distinction

| Feature | `Comparable<T>` | `Comparator<T>` |
| :--- | :--- | :--- |
| **Package** | `java.lang` | `java.util` |
| **Method** | `int compareTo(T other)` | `int compare(T o1, T o2)` |
| **Responsibility** | Defines **natural (default)** ordering inside class | Defines **external / alternate** ordering strategy |
| **Class Modification** | Requires altering target class source code | Zero modification to target class |
| **Flexibility** | Exactly 1 natural ordering | Infinite distinct ordering strategies |

---

### Modern Java 8+ Comparator Construction

```java
record Employee(int id, String department, double salary) implements Comparable<Employee> {
    @Override
    public int compareTo(Employee o) {
        return Integer.compare(this.id, o.id); // Natural order: by ID
    }
}

// Complex multi-level comparator
Comparator<Employee> customCmp = Comparator
    .comparing(Employee::department)
    .thenComparingDouble(Employee::salary).reversed()
    .thenComparing(Employee::id, Comparator.nullsLast(Integer::compareTo));

employees.sort(customCmp);
```

---

### 💡 10+ Years Experienced Interview Questions: Sorting & Contracts

#### Q1: "Why does custom `Comparator` code using `return (o1.val - o2.val);` trigger catastrophic bugs in production?"
**Subtle Bug Answer:**
> "Writing `(o1.val - o2.val)` suffers from **32-bit Integer Overflow / Underflow**:
> ```java
> int a = Integer.MIN_VALUE; // -2,147,483,648
> int b = 1;
> int diff = a - b; // Integer underflow wraps to +2,147,483,647 (positive!)
> ```
> The comparator reports that `a > b`, which is completely false! In Java 7+, `Collections.sort()` and `Arrays.sort()` use **TimSort**, which enforces strict transitivity:
> If transitivity is violated by numeric overflow, TimSort throws:
> `java.lang.IllegalArgumentException: Comparison method violates its general contract!`
> **Production Fix**: Always use `Integer.compare(o1.val, o2.val)` or `Double.compare(o1.val, o2.val)`."

---

## 9. Collections Utility Class & Modern Collection APIs

### `Collections` Utility Methods

```java
// Thread-safety wrappers (coarse intrinsic lock)
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

// Read-only view wrappers
List<String> readOnly = Collections.unmodifiableList(originalList);

// Specialized Singletons
List<String> single = Collections.singletonList("ROOT");
Map<String, String> empty = Collections.emptyMap();

// Search & Reordering
Collections.sort(list);
int idx = Collections.binarySearch(sortedList, "Target");
Collections.reverse(list);
Collections.shuffle(list);
```

---

### Java 9+ Immutable Factory Methods (`List.of`, `Set.of`, `Map.of`)

```java
List<String> list = List.of("A", "B", "C");
Set<String> set = Set.of("X", "Y");
Map<String, Integer> map = Map.of("K1", 1, "K2", 2);
```

#### `Collections.unmodifiableList()` vs `List.of()`

| Feature | `Collections.unmodifiableList(list)` | `List.of(...)` (Java 9+) |
| :--- | :--- | :--- |
| **Immutability** | **Read-only View**: If underlying list changes, view reflects changes | **Truly Immutable**: Backing elements cannot change |
| **Null Elements** | Allows `null` if backing list has `null` | **Rejects `null`** (throws NPE immediately) |
| **Memory Allocation** | Wraps existing list object | Compact, zero-allocation internal representation |
| **Thread Safety** | Not safe if underlying list is concurrently modified | 100% Thread-safe |

---

### Enumeration Interface (Legacy)

`java.util.Enumeration<E>` is the **legacy iteration interface** from Java 1.0, predating `Iterator<E>`. It is the predecessor to `Iterator` and is now considered obsolete for new code.

#### Interface Definition

```java
public interface Enumeration<E> {
    boolean hasMoreElements();
    E nextElement();
}
```

#### Enumeration vs Iterator

| Feature | `Enumeration<E>` | `Iterator<E>` |
| :--- | :--- | :--- |
| **Java version** | Java 1.0 | Java 1.2 |
| **Method names** | `hasMoreElements()`, `nextElement()` | `hasNext()`, `next()` |
| **Remove support** | **No** — read-only traversal only | Yes — `iterator.remove()` |
| **Fail-fast?** | No | Yes (for most non-concurrent collections) |
| **Used by** | `Vector`, `Hashtable`, `Stack`, `Properties` | All modern JCF collections |

```java
// Legacy usage with Vector
Vector<String> vector = new Vector<>(List.of("A", "B", "C"));
Enumeration<String> e = vector.elements();
while (e.hasMoreElements()) {
    System.out.println(e.nextElement()); // A, B, C
}

// Legacy usage with Hashtable
Hashtable<String, Integer> table = new Hashtable<>();
table.put("x", 1); table.put("y", 2);

Enumeration<String> keys   = table.keys();     // Key enumeration
Enumeration<Integer> values = table.elements(); // Value enumeration

// Wrapping a modern Collection as Enumeration (for legacy API compatibility)
List<String> list = List.of("X", "Y", "Z");
Enumeration<String> wrapped = Collections.enumeration(list);
```

> **Interview Insight**: `Enumeration` is read-only — it has no `remove()` method, making it impossible to safely remove elements during traversal. This was a major design flaw that motivated the introduction of `Iterator` in Java 1.2. In all modern code, use `Iterator`, the enhanced for-loop, or Stream API. `Enumeration` appears only in legacy APIs and library compatibility bridges.

---

## 10. Concurrency Collections (`java.util.concurrent`)

### ConcurrentHashMap

**Package**: `java.util.concurrent.ConcurrentHashMap<K,V>` | **Implements**: `ConcurrentMap<K,V>` | **Backed by**: Segmented hash table (Java 7) / Node array with CAS (Java 8+)

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `ConcurrentHashMap()` | Default capacity 16, load factor 0.75, concurrency level 16 |
| `ConcurrentHashMap(int initialCapacity)` | Specified capacity |
| `ConcurrentHashMap(int capacity, float loadFactor)` | Specified capacity and load factor |
| `ConcurrentHashMap(int capacity, float loadFactor, int concurrencyLevel)` | Controls parallelism level |
| `ConcurrentHashMap(Map<? extends K, ? extends V> m)` | From another map |

#### Methods

| Method | Description | Thread Safety |
| :--- | :--- | :--- |
| `put(K key, V value)` | Inserts / updates entry | CAS or bin lock |
| `get(Object key)` | Returns value; **lock-free** | Lock-free (volatile read) |
| `remove(Object key)` | Removes entry | Bin-level lock |
| `containsKey(Object key)` | Checks key presence; lock-free | Lock-free |
| `size()` | Approximate total count (uses `LongAdder` pattern) | Eventually consistent |
| `mappingCount()` | Same as `size()` but returns `long` (preferred) | Eventually consistent |
| `isEmpty()` | Checks if map has no entries | Eventually consistent |
| `putIfAbsent(K key, V value)` | Atomic conditional insert | Atomic |
| `replace(K key, V old, V new)` | Atomic conditional replace | Atomic |
| `remove(Object key, Object val)` | Atomic conditional remove | Atomic |
| `compute(K key, BiFunction f)` | Atomic compute for key | Bin-level lock |
| `merge(K key, V val, BiFunction f)` | Atomic merge (frequency counts etc.) | Bin-level lock |
| `computeIfAbsent(K key, Function f)` | Atomic: compute only if absent | Bin-level lock |
| `keySet()` | Returns weakly consistent key set | Weakly consistent |
| `newKeySet()` (static) | Creates a thread-safe `Set` backed by `ConcurrentHashMap` | Thread-safe |
| `entrySet()` | Weakly consistent entry set | Weakly consistent |
| `forEach(BiConsumer action)` | Applies action (weakly consistent) | Lock-free traversal |

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("Apple", 1);
map.get("Apple");              // Lock-free read
map.putIfAbsent("Mango", 2);  // Atomic
map.merge("Apple", 1, Integer::sum); // Apple -> 2 (atomic frequency count)
map.computeIfAbsent("Grape", k -> k.length()); // Grape -> 5

// Thread-safe Set
Set<String> keys = ConcurrentHashMap.newKeySet();
keys.add("A"); keys.add("B");
```

#### Evolution: Java 7 vs Java 8+
- **Java 7 (Segmented Locking)**: Divided map into 16 `Segment` arrays (each extending `ReentrantLock`). Supported concurrent writes across different segments, but locked entire segments.
- **Java 8+ (Lock-Free CAS + Bin Synchronized)**:
  - **Reads**: 100% lock-free via `volatile` node references (`volatile V val`, `volatile Node<K,V> next`).
  - **Writes to Empty Bin**: Uses hardware atomic **CAS (Compare-And-Swap)** to insert the first node without taking any lock.
  - **Writes to Populated Bin**: Synchronizes only on the head `Node` of that specific bucket using intrinsic `synchronized(f)`. Locks are localized to a single hash bucket!
  - **No Nulls**: Prohibits `null` keys and values to prevent race conditions during `get()` vs `containsKey()`.

#### Advantages
- **Massive throughput** — lock-free reads, per-bucket writes instead of full map locking
- **No full map lock** — multiple writers can operate on different buckets simultaneously
- **Rich atomic operations** — `compute`, `merge`, `computeIfAbsent` are all atomic
- **Thread-safe `Set`** via `ConcurrentHashMap.newKeySet()`

#### Disadvantages
- **No null keys or values** — throws `NullPointerException`
- `size()` is approximate — not atomically consistent with concurrent writes (use `mappingCount()`)
- **Not suitable for coarse-grained atomic operations** — multi-step conditional logic (check-then-act) must use `compute` / `merge`
- More complex API than `HashMap`

---

### CopyOnWriteArrayList

**Package**: `java.util.concurrent.CopyOnWriteArrayList<E>` | **Implements**: `List<E>` | **Backed by**: Volatile array reference (new copy per write)

#### Constructors

| Constructor | Description |
| :--- | :--- |
| `CopyOnWriteArrayList()` | Empty list |
| `CopyOnWriteArrayList(Collection<? extends E> c)` | Initialized from a collection |
| `CopyOnWriteArrayList(E[] toCopyIn)` | Initialized from an array |

#### Principle
- Any mutative operation (`add`, `set`, `remove`) **allocates a fresh copy** of the internal array (`Arrays.copyOf()`).
- The new array is atomically published via a `volatile Object[] array` field.
- **Iterators** traverse a stable, immutable **snapshot** of the array taken at iterator creation time — never throw `ConcurrentModificationException`, never support `remove()`.

#### Methods

| Method | Description | Time Complexity |
| :--- | :--- | :--- |
| `add(E e)` | Appends; allocates new array copy | O(n) |
| `add(int index, E e)` | Inserts at index; allocates new array | O(n) |
| `set(int index, E e)` | Replaces element; allocates new array | O(n) |
| `remove(int index)` | Removes element; allocates new array | O(n) |
| `get(int index)` | Returns element at index (reads snapshot) | O(1) |
| `size()` | Returns current element count | O(1) |
| `contains(Object o)` | Linear scan of current snapshot | O(n) |
| `iterator()` | Returns snapshot iterator (never throws CME) | O(1) |
| `addIfAbsent(E e)` | Adds element only if not already present | O(n) |
| `addAllAbsent(Collection c)` | Adds all elements not already present | O(n) |

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("A"); list.add("B"); list.add("C");

// Safe to iterate while another thread modifies
for (String s : list) {
    System.out.println(s); // Iterates over snapshot
    list.add("D");         // No ConcurrentModificationException!
}

list.addIfAbsent("A"); // No-op (A already present)
```

#### Advantages
- **No `ConcurrentModificationException`** — iterators traverse stable snapshots
- **Lock-free reads** — `get()` needs only a volatile read
- **Thread-safe** without explicit locking for iteration
- Ideal for: event listener registries, configuration lists, cache invalidation lists

#### Disadvantages
- **Every write is O(n)** — allocates and copies entire array
- **High GC pressure** — frequent writes create many short-lived array objects
- **Stale reads** — iterators see snapshot; concurrent mutations are invisible until next iterator
- **Not suitable for write-heavy workloads** — use `ConcurrentLinkedQueue` or `LinkedBlockingQueue`

---

### BlockingQueue & Producer-Consumer Coordination

**Package**: `java.util.concurrent.BlockingQueue<E>` | **Extends**: `Queue<E>` | **Key property**: Blocking `put`/`take` operations

#### Key Methods

| Method | Behavior if Full / Empty | Returns |
| :--- | :--- | :--- |
| `put(E e)` | **Blocks** until space is available | `void` |
| `take()` | **Blocks** until an element is available | `E` |
| `offer(E e)` | Returns `false` immediately if full | `boolean` |
| `poll()` | Returns `null` immediately if empty | `E` |
| `offer(E e, long timeout, TimeUnit u)` | Waits up to timeout, returns `false` if still full | `boolean` |
| `poll(long timeout, TimeUnit u)` | Waits up to timeout, returns `null` if still empty | `E` |
| `peek()` | Inspects head without removing; `null` if empty | `E` |
| `size()` | Current number of elements | `int` |
| `remainingCapacity()` | Available slots before blocking | `int` |
| `drainTo(Collection c)` | Removes all elements into collection atomically | `int` (count) |

```java
BlockingQueue<Task> queue = new ArrayBlockingQueue<>(100);

// Producer thread
queue.put(new Task()); // Blocks if full
queue.offer(new Task(), 500, TimeUnit.MILLISECONDS); // Timed wait

// Consumer thread
Task task = queue.take(); // Blocks if empty
```

#### Implementations Comparison

| Implementation | Backing | Lock Strategy | Capacity | Use Case |
| :--- | :--- | :--- | :--- | :--- |
| `ArrayBlockingQueue` | Circular array | Single `ReentrantLock` | **Bounded** | Strict capacity control |
| `LinkedBlockingQueue` | Linked nodes | `putLock` + `takeLock` (2 locks) | Bounded or Unbounded | High-throughput P-C pipeline |
| `PriorityBlockingQueue` | Binary heap | Single lock | Unbounded | Priority-based scheduling |
| `SynchronousQueue` | None (hand-off) | CAS | 0 (direct hand-off) | Thread-to-thread handoff |
| `DelayQueue` | Priority heap | Single lock | Unbounded | Scheduled/delayed tasks |

#### `ArrayBlockingQueue` vs `LinkedBlockingQueue`
- **`ArrayBlockingQueue`**: Backed by a circular array buffer. Uses a **single `ReentrantLock`** for both `put` and `take`. Producers and consumers contend for the same lock.
- **`LinkedBlockingQueue`**: Backed by linked nodes. Uses **two independent locks**: `putLock` and `takeLock`. Producers and consumers operate completely concurrently without contention!

#### Advantages
- **Automatic thread coordination** — no manual `wait()/notify()` needed
- **Backpressure support** — producers automatically slow down when queue is full
- **Decouples** producers from consumers — different speeds handled automatically
- Foundation for **ThreadPoolExecutor** and most Java concurrency patterns

#### Disadvantages
- **Blocking** — threads block if queue is empty or full (can cause thread starvation)
- **Bounded capacity** — must choose capacity carefully (`ArrayBlockingQueue`)
- **Not for high-frequency non-blocking use** — use `ConcurrentLinkedQueue` instead

---

### 💡 10+ Years Experienced Interview Questions: Concurrency

#### Q1: "Why do `ConcurrentHashMap` and `Hashtable` forbid `null` keys and values, whereas `HashMap` permits them?"
**Concurrency Design Answer:**
> "In single-threaded `HashMap`, if `map.get(key)` returns `null`, the caller can distinguish between 'key is absent' and 'key is mapped to null' by calling `map.containsKey(key)`.
> In concurrent maps, this two-step check is inherently non-atomic. Between `map.get(key)` returning `null` and the subsequent `map.containsKey(key)` call, another thread could insert or remove the key. The returned state would be a false race condition.
> Doug Lea deliberately prohibited `null` keys and values in all concurrent collections to prevent ambiguous null states."

#### Q2: "How does `ConcurrentHashMap.size()` calculate total elements without locking the entire map?"
**High-Performance Architecture Answer:**
> "In high-concurrency environments, updating a single global atomic counter would create severe cache-line bouncing and CPU bus saturation.
> `ConcurrentHashMap` uses an architecture derived from `LongAdder`:
> 1. It maintains a `baseCount` field updated via CAS under low contention.
> 2. Under thread contention, threads hash to an array of **`CounterCell`** objects, updating their own independent striped counter.
> 3. When `size()` or `mappingCount()` is called, it sums `baseCount` and all `CounterCell` values:
>    $$\text{Total Size} = \text{baseCount} + \sum \text{CounterCell}[i].\text{value}$$
> This provides an eventually consistent estimate without taking a single lock."

---

## 11. Core Contracts & Critical Pitfalls

### `equals()` & `hashCode()` Contract

1. **Reflexive, Symmetric, Transitive, Consistent**: Standard `equals()` requirements.
2. **The Golden Rule**:
   $$\text{If } a.\text{equals}(b) == \text{true} \implies a.\text{hashCode}() == b.\text{hashCode}()$$
3. **The Inverse is NOT Required**:
   $$\text{If } a.\text{hashCode}() == b.\text{hashCode}() \not\implies a.\text{equals}(b) \text{ (This is a hash collision)}$$

> [!CAUTION]
> **The Classic Interview Failure:**
> Overriding `equals()` without overriding `hashCode()` means two logically identical objects receive different hash codes from `Object.hashCode()` (memory addresses). Storing them in a `HashSet` or `HashMap` will cause duplicate entries, failed lookups, and memory leaks.

---

### Fail-Fast vs Fail-Safe vs Weakly Consistent

| Iteration Model | Concrete Implementations | Behavior on Concurrent Mutation | Iterates Snapshot? |
| :--- | :--- | :--- | :--- |
| **Fail-Fast** | `ArrayList`, `HashSet`, `HashMap` | Throws `ConcurrentModificationException` immediately | No |
| **Fail-Safe (Snapshot)** | `CopyOnWriteArrayList`, `CopyOnWriteArraySet` | Never throws; iterates untouched snapshot | Yes (immutable copy) |
| **Weakly Consistent** | `ConcurrentHashMap`, `ConcurrentLinkedQueue` | Never throws; reflects some/all updates post-creation | No (traverses live links safely) |

---

## 12. Complexity Master Cheat Sheet & Decision Matrix

### Time & Space Complexity Master Table

| Collection Class | Positional Access | Insert / Add | Delete / Remove | Search / Lookup | Order Guarantee |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`ArrayList`** | **$O(1)$** | Amortized $O(1)$ (tail), $O(n)$ (mid) | $O(n)$ | $O(n)$ (by val), $O(1)$ (by idx) | Insertion Order |
| **`LinkedList`** | $O(n)$ | **$O(1)$ (ends)**, $O(n)$ (mid) | **$O(1)$ (ends)**, $O(n)$ (mid) | $O(n)$ | Insertion Order |
| **`ArrayDeque`** | $O(1)$ (ends only) | **$O(1)$ (both ends)** | **$O(1)$ (both ends)** | $O(n)$ | Deque / FIFO / LIFO |
| **`PriorityQueue`** | $O(1)$ (peek head) | **$O(\log n)$** | **$O(\log n)$ (poll)** | $O(n)$ | Heap Min/Max Priority |
| **`HashSet`** | N/A | **Avg $O(1)$**, Worst $O(\log n)$ | **Avg $O(1)$**, Worst $O(\log n)$ | **Avg $O(1)$**, Worst $O(\log n)$ | None |
| **`LinkedHashSet`** | N/A | **Avg $O(1)$** | **Avg $O(1)$** | **Avg $O(1)$** | Insertion Order |
| **`TreeSet`** | N/A | **$O(\log n)$** | **$O(\log n)$** | **$O(\log n)$** | Sorted Natural / Comparator |
| **`HashMap`** | N/A | **Avg $O(1)$**, Worst $O(\log n)$ | **Avg $O(1)$**, Worst $O(\log n)$ | **Avg $O(1)$**, Worst $O(\log n)$ | None |
| **`LinkedHashMap`** | N/A | **Avg $O(1)$** | **Avg $O(1)$** | **Avg $O(1)$** | Insertion or Access Order |
| **`TreeMap`** | N/A | **$O(\log n)$** | **$O(\log n)$** | **$O(\log n)$** | Sorted by Key |
| **`ConcurrentHashMap`**| N/A | **Avg $O(1)$ (Thread-safe)** | **Avg $O(1)$ (Thread-safe)** | **Avg $O(1)$ (Lock-Free)** | None |

---

### DSA Interview Decision Matrix

```text
Do you need Key-Value mapping?
├── YES ──> Do keys need to be sorted?
│           ├── YES ──> TreeMap
│           └── NO  ──> Need insertion/access order?
│                       ├── YES ──> LinkedHashMap (LRU Cache)
│                       └── NO  ──> Need thread-safety?
│                                   ├── YES ──> ConcurrentHashMap
│                                   └── NO  ──> HashMap
│
└── NO (Single Values)
    ├── Do elements need to be UNIQUE?
    │   ├── YES (Set) ──> Need sorted elements?
    │   │                 ├── YES ──> TreeSet
    │   │                 └── NO  ──> Need insertion order?
    │   │                             ├── YES ──> LinkedHashSet
    │   │                             └── NO  ──> HashSet
    │   └── NO (Duplicates Allowed)
    │       ├── Need repeated Min/Max extraction? ──> PriorityQueue
    │       ├── Need LIFO (Stack)? ───────────────> ArrayDeque (as Stack)
    │       ├── Need FIFO (Queue)? ───────────────> ArrayDeque (as Queue)
    │       ├── Need operations at both ends? ────> ArrayDeque
    │       └── General list / random access? ────> ArrayList
```

---

## 13. Senior / Staff-Level Interview Master Q&A

### Q1. What is the fundamental difference between `Collection` and `Collections`?
> **Answer**: `Collection` is the root interface for single-value containers (`List`, `Set`, `Queue`). `Collections` is a non-instantiable utility class consisting solely of static algorithms (sorting, searching, shuffing) and factory wrappers (synchronization, unmodifiable views).

### Q2. Why does `Map` not extend `Collection`?
> **Answer**: A `Collection` stores single elements (`E`) with methods like `add(E)`. A `Map` stores key-value associations (`<K, V>`). Forcing `Map` under `Collection` would violate the interface contract because `add(E)` cannot function without both a key and a value.

### Q3. How does `ArrayList` resize, and what is its growth factor?
> **Answer**: When capacity is exceeded, `ArrayList` grows by $50\%$ ($\text{oldCapacity} + (\text{oldCapacity} \gg 1)$). It allocates a new contiguous array and copies elements via `System.arraycopy()`. The amortized cost per append is $O(1)$.

### Q4. Why is `ArrayDeque` superior to `Stack` and `LinkedList` for stack operations?
> **Answer**: `Stack` extends `Vector`, forcing every method to acquire heavy synchronized locks, and violates LSP by exposing arbitrary index insertions. `LinkedList` allocates a new `Node` on every push and thrashes CPU cache lines. `ArrayDeque` uses a circular array buffer with zero per-push allocations and optimal cache locality.

### Q5. How does `HashMap` compute a bucket index from a key?
> **Answer**: First, it scrambles the hash: `h ^ (h >>> 16)` to mix upper bits into lower bits. Then it applies a bitwise mask: `(capacity - 1) & hash`. This requires capacity to be a power of two, replacing expensive modulo division with a single-cycle bitwise operation.

### Q6. What is Treeification in `HashMap`, and what are its thresholds?
> **Answer**: When collisions in a single bucket reach `TREEIFY_THRESHOLD = 8` and total map capacity is $\ge \text{MIN_TREEIFY_CAPACITY = 64}$, the singly linked list converts into a Red-Black Tree. Worst-case lookup drops from $O(n)$ to $O(\log n)$. If deletions reduce tree nodes to $\le 6$, it reverts to a linked list.

### Q7. Why does `ConcurrentHashMap` prohibit `null` keys and values?
> **Answer**: To prevent non-deterministic race conditions. If `map.get(key)` returned `null`, a thread could not reliably tell if the key was mapped to `null` or absent, because calling `containsKey()` subsequently is not atomic.

### Q8. What is the difference between fail-fast and weakly consistent iterators?
> **Answer**: Fail-fast iterators (`ArrayList`, `HashMap`) inspect `modCount` and throw `ConcurrentModificationException` immediately if concurrent structural mutations occur. Weakly consistent iterators (`ConcurrentHashMap`) never throw; they safely traverse live links and reflect some or all mutations.

### Q9. When would you choose `CopyOnWriteArrayList` over a synchronized list?
> **Answer**: Exclusively in read-heavy scenarios (e.g., event listeners) where reads outnumber writes by orders of magnitude (1000:1). Reads are lock-free and iterate over an immutable snapshot, but writes allocate and copy the entire underlying array.

### Q10. What happens if two unequal objects return the same `hashCode()`?
> **Answer**: A **hash collision** occurs. Both entries are stored in the same bucket. In Java 8+, they form a linked list or Red-Black tree. The map uses `key.equals()` to distinguish between them. It impacts performance, but correctness is preserved.

### Q11. What is the contract between `Comparable` and `equals()` in `TreeSet`?
> **Answer**: `TreeSet` determines uniqueness and identity using `compareTo() == 0`, completely ignoring `equals()`. If `compareTo()` returns 0 for two objects where `equals()` returns false, the set will silently discard one of the objects.

### Q12. How does `LinkedHashMap` implement an LRU cache?
> **Answer**: It initializes with access-order mode (`super(cap, 0.75f, true)`). Every `get()` moves the entry to the tail of its internal doubly-linked list. Overriding `removeEldestEntry()` to return `size() > maxCapacity` automatically evicts the head (least recently used) node upon new insertions.

### Q13. Why does `PriorityQueue` iteration not return elements in sorted order?
> **Answer**: `PriorityQueue` is backed by a binary min-heap stored in a flat array. The heap invariant only guarantees that a parent is smaller than its children. It does not enforce horizontal sorting across array indices. To retrieve sorted order, one must drain the heap via repeated `poll()` calls.

### Q14. What is the difference between `Collections.unmodifiableList()` and `List.of()`?
> **Answer**: `Collections.unmodifiableList()` is a read-only view wrapper over an underlying list; if the underlying list changes, the view changes. `List.of()` produces an intrinsically immutable, compact list that rejects `null`s and cannot be altered by any reference.

### Q15. How does `ConcurrentHashMap` achieve high write concurrency in Java 8?
> **Answer**: It eliminates Segment locks. It uses lock-free CAS to insert into empty buckets, and synchronizes only on the head node of a specific bucket (`synchronized(headNode)`) when resolving collisions. Writes to different buckets execute simultaneously without contention.

### Q16. What is the difference between `poll()` and `remove()` in a Queue?
> **Answer**: Both retrieve and remove the head element. When the queue is empty, `remove()` throws `NoSuchElementException`, while `poll()` returns `null`.

### Q17. Why is `String` the most popular `HashMap` key in Java?
> **Answer**: `String` is immutable, meaning its state cannot change after insertion. Furthermore, `String` caches its hash code (`hash` field), computing it only once on first access and making repeated map lookups extremely fast.

### Q18. How does `System.arraycopy()` optimize `ArrayList` operations?
> **Answer**: It is a native JVM intrinsic mapped directly to hardware block-copy instructions (`memmove`/`memcpy`). It transfers contiguous blocks of memory in vectorized SIMD CPU cycles, far faster than manual Java loop copying.

### Q19. What is the difference between `peek()` and `element()`?
> **Answer**: Both inspect the head element of a queue without removing it. If the queue is empty, `element()` throws `NoSuchElementException`, while `peek()` returns `null`.

### Q20. Can you modify a collection while iterating over it with an enhanced for-loop?
> **Answer**: No. The enhanced for-loop compiles to an `Iterator`. Calling `collection.remove()` or `add()` modifies `modCount`, causing the iterator's next call to throw `ConcurrentModificationException`. You must use `iterator.remove()` or `collection.removeIf()`.

### Q21. How does `WeakHashMap` automatically reclaim memory?
> **Answer**: Its internal entry objects extend `WeakReference<K>`. When a key is no longer strongly referenced anywhere else in the application, the Garbage Collector clears the weak reference and places it in a `ReferenceQueue`. `WeakHashMap` polls this queue during subsequent operations and expels the associated value.

### Q22. What is the difference between `IdentityHashMap` and `HashMap`?
> **Answer**: `HashMap` uses `hashCode()` and `equals()`. `IdentityHashMap` uses `System.identityHashCode(k)` and reference equality (`k1 == k2`), treating two objects as equal only if they occupy the exact same memory address.

### Q23. Why does `Arrays.asList()` return a fixed-size list?
> **Answer**: It returns an internal private class `Arrays.ArrayList` that wraps the original array directly. Structural modifications like `add()` or `remove()` are not supported and throw `UnsupportedOperationException`, though elements can be replaced via `set()`.

### Q24. What is the difference between `LinkedBlockingQueue` and `ArrayBlockingQueue`?
> **Answer**: `ArrayBlockingQueue` uses a single shared lock for both putting and taking (causing contention between producers and consumers). `LinkedBlockingQueue` uses two separate locks (`putLock` and `takeLock`), allowing producers and consumers to work concurrently.

### Q25. What is the time complexity of `Collections.binarySearch()` on a `LinkedList`?
> **Answer**: $O(n)$. Because `LinkedList` does not support random access, the binary search must traverse linked nodes sequentially to reach the midpoint on each step, degrading the search from $O(\log n)$ to $O(n)$.

---
