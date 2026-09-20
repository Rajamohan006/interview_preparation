# 🧠 Data Structures & Algorithms (DSA) — Interview Preparation

Welcome to the **Data Structures and Algorithms (DSA)** technical preparation module! This guide is crafted specifically for software engineers and architects interviewing at tier-1 technology companies (Google, Microsoft, Amazon, Meta, Apple, etc.), with a strong emphasis on **performance, memory trade-offs, architecture decisions, and real-world system design implications**.

---

## 📚 Guides & Resources

| File | Focus | Experience Level | Description |
|---|---|---|---|
| [**`collections.md`**](./collections.md) | Arrays, Strings & Java Collection Framework (JCF) | **Foundations to 10+ Years Senior Focus** | Comprehensive three-part architectural guide: **Part 1** covers Java Arrays (primitives vs objects, memory model, `java.util.Arrays` methods, DSA patterns); **Part 2** covers Java Strings (UTF-16, String Pool, immutability, `StringBuilder`, sliding window/two pointers); **Part 3** covers the Java Collection Framework (`List`, `Set`, `Queue`, `Deque`, `Map`, `ConcurrentHashMap`, `PriorityQueue`, `ArrayDeque`, internals, memory layouts, 10+ years experienced interview questions and architectural trade-offs). |
| [**`time_and_space_complexity.md`**](./time_and_space_complexity.md) | Data Types, Classification, Linked List Foundations, Time & Space Complexity | **Foundations to Senior Focus** | Complete guide to data types, Linked List prerequisite foundations (references, memory models, traversal, array vs list), linear vs. non-linear data structures, Big-O hierarchy ($O(1)$ to $O(n!)$), and 42 targeted interview Q&As. |
| [**`linked_list.md`**](./linked_list.md) | Linked List Architecture, Patterns & Interview Mastery | **Foundations to Senior Focus** | Complete interview guide covering internal memory structures, Kotlin & Java implementations, Singly/Doubly/Circular variations, real-world systems (LRU, OS scheduling), 11-step interview framework, and 17 core Q&As. |
| [**`dsa_java.md`**](./dsa_java.md) | Java DSA Architecture & Q&A | **10+ Years / Senior / Staff** | Deep-dive architectural definitions, time/space trade-offs, internal mechanics (`HashMap`, Red-Black trees, heaps, caches), and 32 senior-level interview questions. |

---

## 🗺️ Topic Coverage Roadmap

```text
                                  DSA
                                   │
              ┌────────────────────┴────────────────────┐
        Data Structures                           Algorithms
              │                                       │
       ┌──────┴──────┐                         ┌──────┴──────┐
    Linear       Non-Linear                 Searching      Sorting
       │             │                         │             │
    Array         Tree (BST, AVL, Red-Black)Binary Search  Merge Sort
    LinkedList    Graph (BFS, DFS, Dijkstra)Two Pointers   Quick Sort
    Stack         Heap (PriorityQueue)      Sliding Window TimSort
    Queue         Trie (Prefix Tree)        Dynamic Prog   Greedy
    HashMap       Disjoint Set (Union-Find) Backtracking   Recursion
```

---

## ⚡ Asymptotic Complexity Cheat Sheet

| Data Structure | Average Access | Average Search | Average Insertion | Average Deletion | Space Complexity |
|---|---|---|---|---|---|
| **Array** | $O(1)$ | $O(n)$ | $O(n)$ | $O(n)$ | $O(n)$ |
| **Singly Linked List** | $O(n)$ | $O(n)$ | $O(1)$ | $O(1)^*$ | $O(n)$ |
| **Doubly Linked List** | $O(n)$ | $O(n)$ | $O(1)$ | $O(1)$ | $O(n)$ |
| **Stack (`ArrayDeque`)**| $O(1)$ | $O(n)$ | $O(1)$ | $O(1)$ | $O(n)$ |
| **Queue (`ArrayDeque`)**| $O(1)$ | $O(n)$ | $O(1)$ | $O(1)$ | $O(n)$ |
| **Hash Table (`HashMap`)**|$O(1)$ | $O(1)$ | $O(1)$ | $O(1)$ | $O(n)$ |
| **Binary Search Tree** | $O(\log n)$ | $O(\log n)$ | $O(\log n)$ | $O(\log n)$ | $O(n)$ |
| **Red-Black Tree (`TreeMap`)**|$O(\log n)$ | $O(\log n)$ | $O(\log n)$ | $O(\log n)$ | $O(n)$ |
| **Binary Heap (`PriorityQueue`)**| $O(1)$ (min/max) | $O(n)$ | $O(\log n)$ | $O(\log n)$ | $O(n)$ |

$^*$ With known node pointer.

---

## 🎯 7-Step Problem Solving Framework for Senior Interviews

```text
1. Clarify Requirements & Constraints (Inputs, outputs, bounds, edge cases)
                     ↓
2. State Assumptions & Data Volume (Memory limits, concurrency, latency SLAs)
                     ↓
3. Propose Brute-Force Solution (Establish correctness & benchmark complexity)
                     ↓
4. Identify Bottlenecks (Redundant subproblems, repeated linear scans)
                     ↓
5. Select Optimal Data Structure (Justify choice based on access patterns)
                     ↓
6. Implement Defensively (Clean, modular code with boundary guards)
                     ↓
7. Dry-Run & Complexity Verification (Trace edge cases, compute Big-O)
```
