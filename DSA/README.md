# 🧠 Data Structures & Algorithms (DSA) — Interview Preparation

Welcome to the **Data Structures and Algorithms (DSA)** technical preparation module! This guide is crafted specifically for software engineers and architects interviewing at tier-1 technology companies (Google, Microsoft, Amazon, Meta, Apple, etc.), with a strong emphasis on **performance, memory trade-offs, architecture decisions, and real-world system design implications**.

---

## 📚 Guides & Resources

| File | Focus | Experience Level | Description |
|---|---|---|---|
| [**`data_types_and_classification.md`**](./data_types_and_classification.md) | Data Types & Structural Classification | **Foundations & Senior Q&A** | Primitive vs. non-primitive types, JVM boxing rules, linear (Array, List, Stack, Queue) vs. non-linear (Tree, Graph, Heap), ADTs, and 17 Q&As. |
| [**`time_and_space_complexity.md`**](./time_and_space_complexity.md) | Asymptotic Complexity, Big-O & Trade-offs | **All Levels / Senior Focus** | Complete guide to time & space complexity, growth rates ($O(1)$ to $O(n!)$), Big-O calculation rules, trade-offs, and 20 targeted interview Q&As. |
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
