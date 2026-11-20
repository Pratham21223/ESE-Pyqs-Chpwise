# Unit 1 — Basics of Algorithms and Data Structures

> **Syllabus (DJS23IPC201, 4 hrs):** Algorithms, characteristics of an algorithm, time and space complexities, order of growth functions, preliminary asymptotic notations. Data structures: introduction, need of data structures, types of data structures, Abstract Data Types (ADT).
>
> **Past-paper coverage:** definition and characteristics of an algorithm; data-structure definition, need and classification; ADT vs implementation; time vs space complexity; Big-O/Omega/Theta; best/worst/average case; complexity of code snippets; file structure vs data structure.

**Source codes**

| Code | Paper |
|---|---|
| IT-F24 | IT Final 2024-25 (Dec 2024) |
| CO-F24 | Computer Final 2024-25 (Dec 2024) |
| IO-F24 | IOT Final 2024-25 (Dec 2024) |
| AI-F24 | AIDS/AIML Final 2024-25 (Dec 2024) |
| IT-R25 | IT Re-Exam 2024-25 (Mar 2025) |
| CO-R25 | Computer Re-Exam 2024-25 (Mar 2025) |
| AI-R25 | AIDS/AIML Re-Exam 2024-25 (Mar 2025) |
| P8 Jan-24 | Common Final 2023-24 (Jan 2024, 65 marks) |
| IT-F25 | IT Final 2025-26 (Dec 2025) |
| IT-R26 | IT Re-Exam 2025-26 (Mar 2026) |
| AI-R26 | AIDS Re-Exam 2025-26 (Mar 2026) |
| IC-R26 | CSE (ICB) Re-Exam 2025-26 (Mar 2026) |
| CO-R26 | Computer Re-Exam 2024-25 batch (Mar 2026) |
| CO-B26 | 2023-24 Backlog Re-Exam (Mar 2026, 65 marks) |

---

## 1.1 Algorithm: definition and characteristics **[5]**

**Asked in:** AI-R26 Q3B(1).

**Question (verbatim):**
> Explain what is an Algorithm and its characteristic.

**Answer**

An **algorithm** is a finite, ordered set of unambiguous instructions that takes zero or more inputs, performs a well-defined sequence of operations, and produces one or more outputs. It is a solution method, independent of any programming language.

**Characteristics**

| Characteristic | Meaning |
|---|---|
| **Finiteness** | The algorithm must terminate after a finite number of steps. |
| **Definiteness (unambiguity)** | Every step must have exactly one meaning; no vague instructions. |
| **Input** | Zero or more inputs are supplied before execution. |
| **Output** | At least one output must be produced. |
| **Effectiveness (feasibility)** | Every instruction must be basic enough to be carried out exactly and in finite time. |
| **Generality** | It should solve a class of problems, not one specific instance. |
| **Clarity** | The steps should be readable and easy to translate into a program. |

**Example — sum of the first `n` natural numbers**

```
Algorithm SUM(n)
1. sum ← 0
2. for i ← 1 to n do
3.     sum ← sum + i
4. return sum
```

- Finiteness: the loop runs exactly `n` times.
- Definiteness: `sum ← sum + i` has one meaning.
- Input: one integer `n`; output: one integer `sum`.
- Effectiveness: only addition and assignment are used.
- Generality: works for every positive integer `n`.

**Time complexity:** `O(n)`; **space complexity:** `O(1)`.

---

## 1.2 Data structure: definition, need and classification **[5]**

**Asked in:** CO-F24 Q2B; IT-R26 Q1B; AI-R26 Q3B(2) (definition half).

**Questions (verbatim):**
> Explain the types of data structures in detail?
>
> Discuss the significance of data structures and its major classifications.
>
> What is Data Structure?

**Answer**

A **data structure** is a particular way of organizing, storing and accessing data in memory so that the required operations can be performed efficiently. It combines:

- a **logical organization** (how elements relate to each other), and
- an **implementation** (arrays, pointers, records) on which operations run.

**Need / significance**

1. **Efficient use of memory:** stores only what is needed and avoids duplication.
2. **Efficient operations:** the right structure reduces search, insert and delete time (e.g., hash table vs linear list).
3. **Problem modelling:** trees model hierarchies, graphs model networks, queues model waiting lines.
4. **Reusability and clarity:** a well-defined structure separates the *what* from the *how*.
5. **Scalability:** algorithm performance depends heavily on the chosen structure.
6. **Basis of algorithms:** sorting, searching, graph and database algorithms are built on these structures.

**Classification**

```
Data structures
├── Primitive (basic, provided by the language)
│   ├── integer
│   ├── float / real
│   ├── character
│   └── pointer (reference)
└── Non-primitive (built from primitives)
    ├── Linear (elements form one sequence)
    │   ├── Array
    │   ├── Linked list
    │   ├── Stack
    │   └── Queue
    └── Non-linear (elements form a hierarchy or network)
        ├── Tree
        │   ├── Binary tree / BST
        │   ├── AVL tree
        │   └── Heap
        └── Graph
```

| Type | Definition | Examples | Typical operations |
|---|---|---|---|
| **Primitive** | Directly supported by the machine/language | `int`, `float`, `char`, pointer | arithmetic, comparison |
| **Non-primitive** | Derived/composed structure | array, list, stack, tree | insert, delete, traverse |
| **Linear** | Every element has at most one predecessor and one successor | array, linked list, stack, queue | traversal in one direction |
| **Non-linear** | An element can have multiple predecessors/successors | tree, graph | hierarchical traversal, path search |
| **Static** | Size fixed at compile time | array | random access |
| **Dynamic** | Size grows/shrinks at run time | linked list, tree | pointer-based insert/delete |

**Static vs dynamic** is a second useful classification; a structure can be linear and dynamic (linked list), or linear and static (array).

---

## 1.2A Linear vs non-linear data structures **[5]**

**Asked in:** AI-F24 Q3(2) (OR alternative).

**Question (verbatim):**
> Explain the differences and similarities between linear and non-linear data structures.

**Answer**

**Similarities**

1. Both organize data and define operations on it.
2. Both can be static (array-based) or dynamic (pointer-based).
3. Both can be implemented using arrays or linked structures.
4. Both support traversal, insertion, deletion and search, with different costs.
5. Both are used as building blocks of larger algorithms.

**Differences**

| Point | Linear | Non-linear |
|---|---|---|
| Arrangement | Elements form one sequence | Elements form a hierarchy or network |
| Predecessor/successor | At most one of each | Multiple predecessors/successors |
| Traversal | One direction (forward/backward) | Multiple paths (root-to-leaf, graph search) |
| Examples | Array, linked list, stack, queue | Tree, graph, heap |
| Typical access | Index or position | Parent/child, neighbour, path |
| Memory | Often contiguous (array) | Usually pointer-based |
| Search | `O(n)` or `O(log n)` | `O(log n)` tree, `O(V+E)` graph |
| Applications | Lists, buffers, scheduling | Hierarchies, networks, indexes, expressions |
| Complexity of shape | Simple, one level | Recursive, multi-level |

**Key point:** the distinction is **logical**, not physical. A linked list stores nodes in scattered memory but is still **linear** because each node has one predecessor and one successor. A tree stored in an array is still **non-linear** because the parent–child relationships branch.

---

## 1.3 Abstract Data Type (ADT), data, data type and data object **[5]**

**Asked in:** CO-R26 Q2B; AI-R25 Q3(2) (ADT alternative); AI-R25 Q1(1) (List ADT, Unit 2); AI-R25 Q2(2) (Stack ADT, Unit 3); IC-R26 Q1b(i) (Queue ADT, Unit 3).

**Questions (verbatim):**
> Define following terms: Data, Data type, Data object, Data structure, and Abstract Data type.
>
> What is an Abstract Data Type (ADT)? Discuss the difference between an ADT and its implementation. Provide examples of ADTs and explain how they can be implemented using different data structures.

**Answer**

| Term | Definition |
|---|---|
| **Data** | Raw facts/values without meaning, e.g., `25`, `"Ravi"`. |
| **Data type** | A set of values plus the operations allowed on them, e.g., `int` with `+ − × ÷`. |
| **Data object** | A specific instance/collection of values of a data type, e.g., the array `{3, 1, 4}`. |
| **Data structure** | A physical organization of data in memory plus the operations on it, e.g., a linked list with `next` pointers. |
| **ADT** | A **logical** model that specifies *what* operations are performed and *what* they do, but **not how** they are implemented. |

**ADT in detail**

An ADT is defined by:

1. the **domain** of values it stores, and
2. the **operations** on those values, with their inputs, outputs and semantics.

The internal representation and the code are hidden (encapsulation). The same ADT can have several implementations.

**Examples**

| ADT | Operations | Possible implementations |
|---|---|---|
| **List** | `insert`, `delete`, `search`, `traverse`, `get(i)` | array, singly linked list, doubly linked list |
| **Stack** | `push`, `pop`, `peek`, `isEmpty`, `isFull` | array, linked list |
| **Queue** | `enqueue`, `dequeue`, `front`, `rear`, `isEmpty` | array, circular array, linked list |
| **Dictionary / Map** | `put(key, value)`, `get(key)`, `remove(key)` | hash table, balanced BST |

**ADT vs implementation**

| Point | ADT | Implementation |
|---|---|---|
| Focus | What operations do | How operations are coded |
| Visibility | Public interface | Private data and code |
| Language | Independent | Language/compiler dependent |
| Example | Stack: LIFO `push/pop` | Array with `top` index, or linked list with `head` |
| Change | Interface stays fixed | Can be replaced without changing users |

**Example — Stack ADT**

```
ADT Stack
  push(x)   : add x at the top
  pop()     : remove and return the top element
  peek()    : return the top element without removing it
  isEmpty() : true if no element
```

Array implementation uses `top`; linked-list implementation uses a `head` pointer. Both satisfy the same contract, so the user code does not change.

---

## 1.4 Time complexity and space complexity **[5]**

**Asked in:** IT-F25 Q1B; AI-F24 Q3(2) (space complexity); AI-R25 Q3(2) (case analysis); AI-R26 Q3B(2) (types of time complexity).

**Questions (verbatim):**
> Explain time complexity and space complexity. Discuss its significance in performance analysis of algorithms.
>
> Discuss the concept of space complexity. How does space complexity differ from time complexity? Provide examples of algorithms with different space complexities.
>
> What is Data Structure? Provide various types of time complexities you learned with example of each.

**Answer**

**Time complexity** is the number of basic operations (comparisons, assignments, arithmetic) an algorithm performs as a function of the input size `n`. It is usually expressed asymptotically, e.g., `O(n²)`, because exact machine timings depend on hardware.

**Space complexity** is the total memory used by an algorithm as a function of `n`:

```
Space(n) = fixed part + auxiliary part
```

- **Fixed part:** instructions, constants, simple variables — independent of `n`.
- **Auxiliary part:** temporary arrays, recursion stack, dynamic nodes — depends on `n`.

**Significance**

1. Predicts running time and memory before coding.
2. Compares two algorithms for the same problem independent of machine speed.
3. Identifies the bottleneck as `n` grows (scalability).
4. Helps choose the right data structure (array vs linked list, hash vs tree).
5. Guides trade-offs: a faster algorithm may use more memory (time–space trade-off).

**Common time complexities**

| Complexity | Name | Example |
|---|---|---|
| `O(1)` | constant | array index access, `push` on a stack |
| `O(log n)` | logarithmic | binary search, balanced BST search |
| `O(n)` | linear | linear search, traversal of a list |
| `O(n log n)` | linearithmic | merge sort, heap sort, AVL operations |
| `O(n²)` | quadratic | bubble/selection/insertion sort (worst) |
| `O(n³)` | cubic | Floyd–Warshall, naive matrix multiplication |
| `O(2ⁿ)` | exponential | recursive subset enumeration, naive Fibonacci |
| `O(n!)` | factorial | brute-force travelling salesman |

**Examples of different space complexities**

| Algorithm | Time | Extra space | Why |
|---|---|---|---|
| Iterative sum of an array | `O(n)` | `O(1)` | one accumulator |
| Merge sort | `O(n log n)` | `O(n)` | temporary merge array |
| Quick sort | `O(n log n)` average | `O(log n)` | recursion stack |
| Binary search (iterative) | `O(log n)` | `O(1)` | three indices |
| Binary search (recursive) | `O(log n)` | `O(log n)` | recursion stack |
| Creating a copy of a list | `O(n)` | `O(n)` | `n` new nodes |

**Key distinction:** time complexity counts *work done*; space complexity counts *memory held at peak*. They are independent: an algorithm can be time-efficient but memory-heavy (merge sort) or memory-light but slow (bubble sort).

---

## 1.5 Asymptotic notations: Big-O, Omega and Theta **[5]**

**Asked in:** IT-F24 Q1(b); IT-R25 Q1(b); CO-B26 Q3B(2); AI-F24 Q3(2) (notation half).

**Questions (verbatim):**
> Discuss Big O, Omega, and Theta notation for complexity Analysis of Algorithm. Support your answer using neat, labelled diagram.
>
> Discuss various Asymptotic notations using neat and labelled diagram for Complexity Analysis.
>
> What is O, Θ and Ω in algorithm analysis? Explain each one with suitable example.

**Answer**

Asymptotic notation describes the growth rate of a function `f(n)` as `n → ∞`, ignoring constants and lower-order terms. Let `f(n)` and `g(n)` be non-negative functions.

| Notation | Definition | Meaning | Analogy |
|---|---|---|---|
| **Big-O — `O(g(n))`** | `∃ c > 0, n₀ > 0` such that `0 ≤ f(n) ≤ c·g(n)` for all `n ≥ n₀` | **Upper bound**; growth is no faster than `g` | `≤` |
| **Big-Omega — `Ω(g(n))`** | `∃ c > 0, n₀ > 0` such that `0 ≤ c·g(n) ≤ f(n)` for all `n ≥ n₀` | **Lower bound**; growth is at least `g` | `≥` |
| **Big-Theta — `Θ(g(n))`** | `∃ c₁, c₂ > 0, n₀ > 0` such that `c₁·g(n) ≤ f(n) ≤ c₂·g(n)` for all `n ≥ n₀` | **Tight bound**; same growth rate | `=` |

**Diagram (conceptual)**

```
f(n)
 |            /  c2·g(n)   ← upper
 |          /   /
 |        /   /  f(n)
 |      /  /
 |    / /  c1·g(n)          ← lower
 |  / /
 |/________________________ n
        n₀
```

For `n ≥ n₀`, the curve `f(n)` lies between `c₁·g(n)` and `c₂·g(n)` for Theta; below `c·g(n)` for Big-O; above `c·g(n)` for Omega.

**Worked example**

Let `f(n) = 3n² + 2n + 1`.

1. **Big-O:** for `n ≥ 1`, `3n² + 2n + 1 ≤ 3n² + 2n² + n² = 6n²`, so `f(n) = O(n²)` with `c = 6, n₀ = 1`.
2. **Omega:** for `n ≥ 1`, `3n² + 2n + 1 ≥ 3n²`, so `f(n) = Ω(n²)` with `c = 3, n₀ = 1`.
3. **Theta:** both bounds hold, so `f(n) = Θ(n²)`.

**More examples**

| Function | Big-O | Omega | Theta |
|---|---|---|---|
| `5n + 3` | `O(n)` | `Ω(n)` | `Θ(n)` |
| `2n² + 7n` | `O(n²)` | `Ω(n²)` | `Θ(n²)` |
| `n log n + n` | `O(n log n)` | `Ω(n log n)` | `Θ(n log n)` |
| `2ⁿ + n¹⁰` | `O(2ⁿ)` | `Ω(2ⁿ)` | `Θ(2ⁿ)` |
| `1000` | `O(1)` | `Ω(1)` | `Θ(1)` |

**Important facts**

- Big-O is the most commonly asked bound because it answers “will it finish in time?”.
- `O` is not unique: `f(n) = O(n)` also implies `f(n) = O(n²)`. The tightest useful bound is normally written.
- `2^{n+1} = O(2ⁿ)` because `2^{n+1} = 2·2ⁿ`; constants are ignored.
- `nᵏ = O(nᵐ)` for `k ≤ m`; `log n` grows slower than any positive power of `n`.
- Growth order: `1 < log n < √n < n < n log n < n² < n³ < 2ⁿ < n!`.

---

## 1.6 Best-case, worst-case and average-case analysis **[5]**

**Asked in:** AI-R25 Q3(2) (case-analysis alternative); AI-R26 Q3B(2).

**Questions (verbatim):**
> Explain the difference between best-case, worst-case, and average-case time complexities. Provide examples to illustrate each case.
>
> What is Data Structure? Provide various types of time complexities you learned with example of each.

**Answer**

| Case | Meaning | What it measures | Example |
|---|---|---|---|
| **Best case** | Input that makes the algorithm do the least work | Lower bound on running time | Linear search finds the key at index `0` → `Ω(1)` |
| **Worst case** | Input that makes the algorithm do the most work | Upper bound; used for guarantees | Linear search key absent → `O(n)` |
| **Average case** | Expected work over all inputs, assuming a distribution | Typical performance | Linear search key equally likely in `n` positions → `(n+1)/2 = Θ(n)` |

**Examples**

1. **Linear search** on `n` elements:
   - best: `1` comparison → `O(1)`;
   - worst: `n` comparisons → `O(n)`;
   - average: `(n+1)/2` → `Θ(n)`.

2. **Binary search** on a sorted array:
   - best: key at the middle → `O(1)`;
   - worst/average: `O(log n)`.

3. **Quick sort**:
   - best/average: pivot splits nearly in half → `O(n log n)`;
   - worst: pivot is always the smallest/largest (already sorted input with first/last pivot) → `O(n²)`.

4. **Insertion sort**:
   - best: already sorted → `O(n)`;
   - worst: reverse sorted → `O(n²)`;
   - average: `O(n²)`.

**Why worst case matters:** it gives a guaranteed upper bound for any input, so real-time and competitive systems are designed against it. Average case is more realistic but requires an assumption about input distribution.

---

## 1.7 Time complexity of code snippets **[5]**

**Asked in:** IO-F24 Q1B(2); IC-R26 Q1b(ii); IT-F24 Q1(b) (notation).

**Questions (verbatim):**
> Find time complexity of following snippet:
> ```
> for (i=1;i<n;i++)
>   for(j=0;j<n-i;j++)
>     if(a[j] > a[j+1])
>     { temp=a[j];
>       a[j]=a[j=a];
>       a[j+1]=temp; }
> ```
>
> Find time complexity of following snippet:
> ```
> for(i=1;i<=n;i++)
> { for(j=1;j<n;j++)
>       x=x+1;
>   for(k=1;k<=n;k=2*k)
>       x=x+1;
> }
> ```

**Answer**

**Snippet 1 — bubble sort**

The outer loop runs `i = 1 … n−1`. For a fixed `i`, the inner loop runs `n−i` times.

```
Total comparisons = Σ_{i=1}^{n−1} (n−i)
                  = (n−1) + (n−2) + … + 1
                  = n(n−1)/2
                  = O(n²)
```

The `if` and swap are `O(1)` each, so the total is **`O(n²)`** time and **`O(1)`** extra space. The statement `a[j]=a[j=a]` is a printing/typing error for a normal swap; it does not change the complexity.

**Snippet 2 — two sibling loops**

```
for i = 1 … n                      → n iterations
    for j = 1 … n−1                → n−1 iterations each
        x = x + 1
    for k = 1; k ≤ n; k = 2k       → log₂n + 1 iterations each
        x = x + 1
```

The `j` loop and the `k` loop are **siblings** inside the `i` loop (not nested in each other). Therefore:

```
T(n) = n · [(n−1) + (log₂n + 1)]
     = n² + n log n
     = O(n²)
```

If the `k` loop were inside the `j` loop, the cost would be `O(n² log n)`; the braces in the printed snippet place it outside.

**General rules**

- Sequential loops add: `O(n) + O(n²) = O(n²)`.
- Nested loops multiply: `O(n) × O(n) = O(n²)`.
- Halving/doubling loop counters give `O(log n)`.
- Recursion: write the recurrence and solve by substitution, recursion tree or Master theorem.

---

## 1.8 File structure vs data structure **[5]**

**Asked in:** P8 Jan-24 Q3B(1).

**Question (verbatim):**
> What is the difference between file structure and data structure? Give suitable example of each.

**Answer**

A **data structure** is the logical and physical arrangement of data in **main memory** during program execution. A **file structure** is the organization of records in **secondary storage** (disk), including how records are placed in blocks and how they are located.

| Point | Data structure | File structure |
|---|---|---|
| Storage | Main memory (RAM) | Secondary storage (disk/SSD) |
| Lifetime | During program execution | Persistent across runs |
| Unit | Element/node (int, record, node) | Record/block/page |
| Size | Limited by RAM | Very large, disk-sized |
| Access | Direct/pointer/index | Block I/O, buffering, file pointers |
| Organization | Array, list, stack, queue, tree, graph | Sequential, indexed, direct/hash, heap |
| Example | Linked list of student records in RAM | A sequential file of student records on disk; an indexed file with a primary index on `roll_no` |
| Operations | insert/delete/search/traverse | open/read/write/seek/close, block allocation |

**Examples**

- **Data structure:** an array `int marks[50]` or a linked list used by a program to sort marks.
- **File structure:** a sequential file `students.dat` where records are stored one after another; or an indexed file with an index on `roll_no` to locate a record without scanning the whole file.

**Relationship:** file structures are usually built *on top of* data structures — e.g., a B+ tree index in a file system is a tree data structure stored on disk.

---

## Quick revision checklist — Unit 1

**Algorithm and data structures**

- [ ] Algorithm definition: finite, unambiguous, input/output, effective.
- [ ] Seven characteristics: finiteness, definiteness, input, output, effectiveness, generality, clarity.
- [ ] Data structure definition: logical organization + implementation + operations.
- [ ] Need: memory, speed, modelling, reuse, scalability.
- [ ] Classification: primitive vs non-primitive; linear vs non-linear; static vs dynamic.
- [ ] Linear examples: array, linked list, stack, queue.
- [ ] Non-linear examples: tree, graph, heap.
- [ ] Primitive examples: int, float, char, pointer.

**ADT**

- [ ] ADT definition: logical model, operations only, implementation hidden.
- [ ] ADT has domain + operations + semantics.
- [ ] ADT vs implementation: interface vs code; language-independent vs dependent.
- [ ] List ADT: insert, delete, search, traverse, get.
- [ ] Stack ADT: push, pop, peek, isEmpty, isFull.
- [ ] Queue ADT: enqueue, dequeue, front, rear, isEmpty.
- [ ] Data vs data type vs data object vs data structure.
- [ ] Same ADT, multiple implementations (stack: array or linked list).

**Complexity**

- [ ] Time complexity = basic operations vs input size.
- [ ] Space complexity = fixed + auxiliary.
- [ ] Significance: compare algorithms, scalability, bottleneck, time–space trade-off.
- [ ] `O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(n³) < O(2ⁿ) < O(n!)`.
- [ ] Big-O = upper bound; Omega = lower bound; Theta = tight bound.
- [ ] Formal definitions with `c` and `n₀`.
- [ ] `3n²+2n+1 = Θ(n²)`.
- [ ] `2^{n+1} = O(2ⁿ)`.
- [ ] Best case = least work; worst = most work; average = expected work.
- [ ] Linear search: best `O(1)`, worst `O(n)`, average `Θ(n)`.
- [ ] Binary search: best `O(1)`, worst `O(log n)`.
- [ ] Quick sort worst `O(n²)`, average `O(n log n)`.
- [ ] Bubble-sort snippet: `n(n−1)/2 = O(n²)`.
- [ ] Two sibling loops inside one loop: `O(n²)`, not `O(n² log n)`.
- [ ] File structure = disk organization; data structure = memory organization.
- [ ] File organizations: sequential, indexed, direct/hash.
