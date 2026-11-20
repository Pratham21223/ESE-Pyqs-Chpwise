# Data Structures — Last-Minute Revision (DJS23)

**One-file cheat sheet: definitions, formulas, algorithms, complexities and past-paper numerical answers.**
Covers Units 1–6 of `DJS23IPC201`. Theory is compressed; use the unit files for full explanations.

> **Exam:** DJS23 Semester-III papers are **60 marks, 2 hours**, with 4 questions of 15 marks and internal `OR`. The Jan-2024 paper is **65 marks** (Q1 = 20, Q2–Q4 = 15 each). Read both sides of every `OR` before choosing.

---

# 0. Question-type → answer template

| If the question says… | Write this |
|---|---|
| **Define / explain** (5) | 1-line definition → 3–5 key points → 1 example |
| **Characteristics / types** (5) | Numbered list with a one-line explanation each |
| **Differentiate** (5) | Table with 4–6 rows (definition, structure, time, space, use) |
| **Algorithm** (5/10) | Pseudocode with pre/post-conditions, edge cases, complexity |
| **C program** (8/10) | Complete program: headers → struct → functions → `main` → sample output |
| **Trace / illustrate** (5/10) | Table of steps with the data structure after each step |
| **Numerical** (5/10) | Formula → substitution → table/diagram → final answer |
| **Tree/graph drawing** (5/10) | Draw every intermediate stage; label balance factors/queue/stack |
| **Compare algorithms** (5) | Table: best, average, worst, space, stable, in-place |
| **Short note** (5) | Definition → 4–5 bullet points → 1 example/application |

**Time plan:** 5 min read/choose → 20–25 min per 15-mark question → 10 min final check. Draw diagrams first; they carry marks.

---

# 1. Basics of algorithms and data structures

## 1.1 Key definitions

| Term | One-line answer |
|---|---|
| **Algorithm** | Finite, unambiguous sequence of steps that takes input, produces output and terminates. |
| **Characteristics** | Finiteness, definiteness, input, output, effectiveness, generality, clarity. |
| **Data structure** | A logical organization of data in memory plus the operations on it. |
| **ADT** | Logical model: operations and their behaviour, implementation hidden. |
| **Data / data type / data object** | Raw value / set of values + operations / a specific collection of values. |
| **File structure** | Organization of records on secondary storage (sequential, indexed, direct). |

## 1.2 Classification

```
Primitive: int, float, char, pointer
Non-primitive
├── Linear: array, linked list, stack, queue
└── Non-linear: tree, graph, heap
```

Static (array) vs dynamic (linked list); homogeneous vs heterogeneous.

## 1.3 Complexity order

```
O(1) < O(log n) < O(√n) < O(n) < O(n log n) < O(n²) < O(n³) < O(2ⁿ) < O(n!)
```

| Notation | Bound | Formal condition |
|---|---|---|
| **Big-O** | upper | `f(n) ≤ c·g(n)` for `n ≥ n₀` |
| **Omega** | lower | `f(n) ≥ c·g(n)` |
| **Theta** | tight | `c₁g(n) ≤ f(n) ≤ c₂g(n)` |

**Examples:** `3n²+2n+1 = Θ(n²)`; `5n+3 = Θ(n)`; `2^{n+1} = O(2ⁿ)`; `n log n + n = Θ(n log n)`.

## 1.4 Time vs space

| Algorithm | Time | Extra space |
|---|---|---|
| Linear search | `O(n)` | `O(1)` |
| Binary search (iterative) | `O(log n)` | `O(1)` |
| Merge sort | `O(n log n)` | `O(n)` |
| Quick sort | `O(n log n)` avg, `O(n²)` worst | `O(log n)` stack |
| Bubble/selection/insertion | `O(n²)` worst | `O(1)` |

**Best/worst/average:** linear search best 1, worst `n`, average `(n+1)/2`; quick sort worst `O(n²)` on sorted input with first/last pivot.

## 1.5 Code-snippet formulas

```
for i=1..n:  for j=1..n:  x++        → O(n²)
for i=1..n:  for j=1..n-i: x++      → n(n−1)/2 = O(n²)
for k=1; k≤n; k=2k: x++             → O(log n)
```

Two sibling loops inside one outer loop: add, do not multiply.

---

# 2. List and linked lists

## 2.1 List ADT operations

`insert`, `delete`, `search`, `get(i)`, `set(i,x)`, `length`, `traverse`.

| Point | Array | Linked list |
|---|---|---|
| Access `i` | `O(1)` | `O(n)` |
| Insert/delete front | `O(n)` | `O(1)` |
| Insert/delete after known node | `O(n)` | `O(1)` |
| Search | `O(n)` (`O(log n)` sorted) | `O(n)` |
| Memory | contiguous | nodes + pointer(s) |

**Applications:** dynamic memory, stack/queue, undo/redo, browser history, adjacency list, round-robin, polynomial, playlist, hash chaining, sparse matrix.

## 2.2 Singly linked list — operation costs

| Operation | Time | Key steps |
|---|---|---|
| Insert beginning | `O(1)` | `new->next=head; head=new` |
| Insert end | `O(n)` | walk to last; `last->next=new` |
| Insert after key | `O(n)` | find key; `new->next=temp->next; temp->next=new` |
| Insert position `pos` | `O(n)` | walk to `pos−1` |
| Delete beginning | `O(1)` | `temp=head; head=head->next; free(temp)` |
| Delete last | `O(n)` | keep `prev`, set `prev->next=NULL` |
| Delete key | `O(n)` | keep `prev`, relink |
| Search/count/min/max/sum | `O(n)` | one traversal |
| Reverse | `O(n)` | three pointers `prev,curr,next` |
| Remove duplicates (sorted) | `O(n)` | compare adjacent |
| Remove duplicates (unsorted) | `O(n²)` or `O(n)` hash | nested scan / hash set |
| Merge two sorted lists | `O(m+n)` | dummy head, splice smaller |
| Split halves | `O(n)` | slow/fast pointers |
| Middle | `O(n)` | slow/fast |
| Loop length | `O(n)` | Floyd slow/fast, then count around |

## 2.3 Reverse a singly linked list

```
prev ← NULL; curr ← head
while curr ≠ NULL:
    next ← curr.next
    curr.next ← prev
    prev ← curr
    curr ← next
return prev
```

`1→2→3` becomes `3→2→1`. `O(n)` time, `O(1)` space.

## 2.4 Slow/fast pointer rules

- **Middle:** `slow` one step, `fast` two. Odd → exact middle; even → second middle (or first, depending on stop condition).
- **Split:** stop when `fast.next == NULL` or `fast.next.next == NULL`; `slow.next` starts the second half. For odd length, the first half gets the extra node.
- **Loop:** if `slow == fast`, a loop exists. Reset `slow=head`; move both one step; meeting point = loop start. Loop length = count steps from meeting point back to itself.

## 2.5 Circular and doubly linked lists

**CLL:** last node's `next` points to head. Traverse with `do…while (temp != head)`. Insert at beginning: find last, `new->next=head`, `last->next=new`, `head=new` (`O(n)`; `O(1)` with tail pointer).

**DLL node:** `prev | data | next`.

| Operation | Steps |
|---|---|
| Insert beginning | `new->next=head; if head head->prev=new; head=new` |
| Insert end | walk to last; `last->next=new; new->prev=last` |
| Insert after `y` | find `y`; link `new` between `y` and `y->next` |
| Delete beginning | `head=head->next; if head head->prev=NULL` |
| Delete index `i` | walk to node; `prev->next=next; next->prev=prev` |
| Delete k-th from end | two pointers separated by `k` |
| Reverse display | go to tail, follow `prev` |
| Median (sorted) | count, walk to middle; average for even |

## 2.6 Special list questions

| Question | Key answer |
|---|---|
| **Is a linked list linear or non-linear?** | **Linear** — one predecessor/successor; scattered memory is a physical property, not logical shape. |
| **Traverse SLL / DLL / CLL** | SLL: `while temp != NULL`; DLL: forward via `next`, backward via `prev`; CLL: `do…while (temp != head)`. All `O(n)`. |
| **Sum two numbers stored as digit lists** | Push both lists on stacks; pop, add with carry; prepend each result digit. `O(m+n)`. |
| **Split / middle / loop** | Slow-fast pointers; loop length counts from meeting point. |

**Sum example:** `554 + 753 = 1307` → `1→3→0→7`. The printed `1→2→0→7` in one paper is an arithmetic slip; `1207` would need `554 + 653`.

## 2.7 Polynomials

Node: `coefficient | exponent | next`, kept in **decreasing exponent** order.

```
while P and Q:
    if P.exp > Q.exp: copy P
    if P.exp < Q.exp: copy Q
    else: add coefficients (drop if zero)
copy remaining terms
```

`O(m+n)` time. Subtraction negates coefficients of `Q`, not exponents.

---

# 3. Stack and queue

## 3.1 Stack ADT

`push`, `pop`, `peek`, `isEmpty`, `isFull`.

| Implementation | Push | Pop | Overflow |
|---|---|---|---|
| Array (`top=-1`) | `O(1)` | `O(1)` | `top == MAX−1` |
| Linked list (head = top) | `O(1)` | `O(1)` | `malloc` fails |

**Applications:** reversing, expression conversion/evaluation, balanced parentheses, recursion, undo, DFS, parsing.

## 3.2 Infix → postfix rules

Precedence: `^` (3, right-assoc) > `* / %` (2, left) > `+ −` (1, left). `(` is a sentinel.

```
operand → output
'(' → push
')' → pop to '(' and discard
operator → pop while top has greater precedence (or equal and left-assoc); push
end → pop all
```

**Must-know conversions**

| Infix | Postfix |
|---|---|
| `(a−b)/((c*d)−(e+f))` | `ab−cd*ef+−/` |
| `p*q^r−s/t+(u/v)` | `pqr^*st/−uv/+` |
| `(A+B*C−D)/(E+F)` | `ABC*+D−EF+/` |
| `(A+B)*(C−D)+E` | `AB+CD−*E+` |
| `A+(B−C)*(D/E)+G` | `ABC−DE/*+G+` |
| `A*B^C−(D/E)+(F/G)` | `ABC^*DE/−FG/+` |

## 3.3 Infix → prefix

Scan **right to left**; operand → output; `)` → push; `(` → pop to `)`; operator → pop while **strictly greater** precedence; push. Reverse the output.

| Infix | Prefix |
|---|---|
| `A/B^C+D*E−F*G` | `−+/A^BC*DE*FG` |
| `(A+B*C−D)/(E+F)` | `/−+A*BCD+EF` |

## 3.4 Postfix evaluation

Push operands; on operator pop `b` then `a`, push `a op b`.

| Expression | Values | Result |
|---|---|---:|
| `6523+8*+3+*` | digits | **288** |
| `AB+CD*+E−` | A2 B3 C4 D5 E6 | **19** |
| `ABC^*DE/−FG/+` | A3 B2 C4 D10 E5 F8 G2 | **50** |

## 3.5 Expression tree

Operands = leaves; operators = internal nodes. Build with a stack of nodes from postfix: operand → push leaf; operator → pop right then left, push subtree. Inorder = infix, postorder = postfix, preorder = prefix.

## 3.6 Queue ADT and implementations

`enqueue` (rear), `dequeue` (front), `front`, `rear`, `isEmpty`, `isFull`.

**Linear array queue:** `front=0, rear=-1`; full `rear=MAX−1`; empty `front>rear`. False overflow after deletions.

**Circular queue:** `rear=(rear+1)%MAX`; `front=(front+1)%MAX`.

| Condition | Formula |
|---|---|
| Empty | `front == −1` |
| Full | `(rear+1)%MAX == front` |
| Enqueue | `rear=(rear+1)%MAX; Q[rear]=x` |
| Dequeue | `x=Q[front]; front=(front+1)%MAX` |

**Queue using linked list:** `front` and `rear` pointers; both operations `O(1)`.

**Priority queue:** delete highest priority. Unsorted array `O(1)` insert / `O(n)` delete; heap `O(log n)` both.

**DEQueue:** insert/delete at both ends.

- **Input-restricted:** insert rear only; delete front and rear.
- **Output-restricted:** delete front only; insert front and rear.

## 3.7 Queue applications

| Problem | Algorithm |
|---|---|
| Reverse queue | dequeue all → stack; pop all → enqueue. `O(n)` |
| Reverse first `k` | dequeue `k` → stack → enqueue; rotate `n−k` elements. `O(n)` |
| Interleave halves | split into `Q1,Q2`; alternate dequeue into result. `O(n)` |
| Stack using queue | push: enqueue then rotate `n−1`; pop: dequeue. `push O(n)`, `pop O(1)` |
| Queue using stack | two stacks; enqueue `O(1)`, dequeue `O(n)` amortised |

**Stack-using-queue trace:** `PUSH 5,10,15; POP; PUSH 20,25,30; POP` → outputs **15, 30**.

---

# 4. Trees

## 4.1 Terminology

Root, parent, child, sibling, leaf, internal node, degree, level, depth, height, subtree, ancestor, descendant, forest.

| Type | Rule |
|---|---|
| Full | 0 or 2 children |
| Complete | all levels full except last, filled left to right |
| Perfect | all leaves same level |
| Skewed | only left or only right children |
| BST | left < node < right |
| AVL | `\|BF\| ≤ 1` at every node |

**Height/level:** height = longest path in edges from node to leaf; level of root = 0 (state convention).

**Applications of binary trees:** expression trees, BST, heap, Huffman, syntax tree, file system, decision tree, database index.

## 4.2 Traversals

| Traversal | Order | BST output |
|---|---|---|
| Inorder | Left → Root → Right | **sorted ascending** |
| Preorder | Root → Left → Right | root first |
| Postorder | Left → Right → Root | root last |

**Example** `A(B(D,E),C(.,F))`:
inorder `D B E A C F`; preorder `A B D E C F`; postorder `D E B F C A`.

Time `O(n)`, space `O(h)`.

## 4.3 Reconstruct a tree

- **Preorder + inorder:** first preorder = root; split inorder at root; recurse.
- **Postorder + inorder:** last postorder = root; split inorder; recurse.
- **BST:** inorder is sorted; preorder alone is enough.

**Worked finals**

| Paper | Traversals | Tree (bracket form) |
|---|---|---|
| AI-F24 | inorder + preorder | `57(41(35,52(48,.)),90(72(.,85(79,86)),.))` |
| CO-F24 | postorder + inorder | `8(5(9,7(1,12(2,.))),4(.,11(3,.)))` |
| CO-R25 | postorder + inorder | `8(3(1,5(.,6(.,7))),11(9(.,10),14(12(.,13),15)))` |
| CO-R26 | preorder + inorder | `F(A(E,K(C,.)),D(H,G(B,.)))` |

`k(L,R)` means root `k`, left subtree `L`, right subtree `R`; `.` is empty.

## 4.4 BST operations

| Operation | Algorithm | Time |
|---|---|---|
| Search | compare, go left/right | `O(h)` |
| Insert | search until `NULL`, attach | `O(h)` |
| Min | leftmost | `O(h)` |
| Max | rightmost | `O(h)` |
| Delete leaf | remove | `O(h)` |
| Delete one child | replace by child | `O(h)` |
| Delete two children | replace by inorder successor (min of right), delete successor | `O(h)` |
| Mirror | swap left/right at every node | `O(n)` |
| LCA | first node between the two keys | `O(h)` |

**Height:** balanced `O(log n)`, skewed `O(n)`.

**AI-R26:** insert 72 → left child of 75; delete 70 (only right child 75) → 75 replaces 70, 72 stays left of 75.

**P8 Jan-24 BST:** min = 1 (8→3→1), max = 14 (8→10→13→14).

## 4.5 AVL tree

```
BF = height(left) − height(right) ∈ {−1, 0, +1}
```

| Case | Shape | Rotation |
|---|---|---|
| **LL** | left-left | right rotate |
| **RR** | right-right | left rotate |
| **LR** | left-right | left on left child, then right |
| **RL** | right-left | right on right child, then left |

**Rotation code (right rotation)**

```
RIGHT_ROTATE(y):
x = y.left
T2 = x.right
x.right = y
y.left = T2
update heights of y, x
return x
```

**Insert:** BST insert, update heights on the way back, apply one of the four cases.

**Delete:** BST delete, update heights, apply LL/RR/LR/RL using the child's balance factor.

**Final roots of past sequences**

| Paper | Sequence | Final root | Final tree (bracket) |
|---|---|---|---|
| AI-F24 | 54,12,24,68,85,99,42,27,87,80 | **68** | `68(24(12,42(27,54)),87(85(80,.),99))` |
| AI-R25 | 10,20,30,40,50,25,5,15,35,45 | **30** | `30(20(10(5,15),25),40(35,50(45,.)))` |
| IT-R25 | 44,17,32,78,50,88,48,62,54 | **50** | `50(32(17,44(.,48)),78(62(54,.),88))` |
| CO-R26 | 52,26,12,6,7,4,32,20,18,16 | **18** | `18(7(6(4,.),12(.,16)),26(20,52(32,.)))` |
| CO-B26 | A,Z,B,Y,C,X,D,U,E | **C** | `C(B(A,.),U(D(.,E),Y(X,Z)))` |
| AI-R26 | 100…160, delete 125,25,50 | **100** | `100(47(45,48),150(149,160))` |
| IT-F25 | 12,8,18,5,11,17,4, delete 11,8 | **12** | `12(5(4,.),18(17,.))` |

**Rotation triggers:** LL/RR are single rotations; LR/RL are double rotations. Always update heights bottom-up.

## 4.6 Heap

| Property | Max-heap | Min-heap |
|---|---|---|
| Parent vs children | parent ≥ children | parent ≤ children |
| Root | maximum | minimum |

**Array (1-based):** `parent=i/2`, `left=2i`, `right=2i+1`.

**MAX_HEAPIFY(A,i,n)**

```
largest = i
if 2i ≤ n and A[2i] > A[largest]: largest = 2i
if 2i+1 ≤ n and A[2i+1] > A[largest]: largest = 2i+1
if largest ≠ i: swap(A[i],A[largest]); MAX_HEAPIFY(A,largest,n)
```

`O(log n)`.

**Build-heap:** `for i=n/2 down to 1: MAX_HEAPIFY(i)`. `O(n)`.

**Insert:** append, bubble up while `A[i] > A[parent(i)]`. `O(log n)`.

**Delete max:** replace root by last, shrink, heapify down. `O(log n)`.

**Delete k-th:** replace `A[k]` by last, shrink; if `A[k] > parent`, bubble up; else heapify down.

**Build max-heap `[1,5,6,8,12,14,16]` → `[16,12,14,8,5,1,6]`.**

**Heap-sort `[40,20,30,10,50]`:** build `[50,40,30,10,20]`; extract → `[10,20,30,40,50]`.

**CO-B26:** delete 50 → `[30,16,20,15,10,8]`; insert 60 → `[60,16,30,15,10,8,20]`; insert 45 → `[60,45,30,16,10,8,20,15]`.

## 4.7 Huffman

**Algorithm:** min-heap of frequencies; repeatedly combine the two smallest; left = 0, right = 1. Symbols only at leaves → prefix-free.

| Paper | Codes | WPL |
|---|---|---:|
| CO-F24 | 25=00, 15=011, 19=100, 20=101, 22=110, 11a=1110, 11b=1111, 5b=0100, 5a=01011, 2=01010 | **421** |
| CO-R25 | 90=00, 40=010, 50=011, 60=100, 30=1010, 10=10110, 20=10111, 70=110, 80=111 | **1350** |
| IC-R26 | A=0, C=100, B=101, F=1100, E=1101, D=111 | **224** |

Ties can change the code assignment but not the WPL.

## 4.8 B-tree

Order `m` = max children; max keys = `m−1`; min children = `⌈m/2⌉`; all leaves same level.

**Overflow:** split around the median; left keeps `⌈m/2⌉−1` keys, median moves up, right keeps the rest.

**Order 4, data `3,12,7,32,8,1,5,15,4,9,13,17,6,21`:**

```
                [7]
              /     \
          [3]         [12 15]
         /   \       /   |    \
      [1]  [4 5 6] [8 9][13] [17 21 32]
```

---

# 5. Graphs

## 5.1 Terminology

Directed/undirected, weighted, simple, complete (`n(n−1)/2` edges), path, cycle, DAG, connected, strongly connected, weakly connected, degree, in-degree, out-degree.

**Handshake:** `Σ deg(v) = 2E`; `Σ indeg = Σ outdeg = E`.

## 5.2 Representations

| Point | Matrix | List |
|---|---|---|
| Memory | `O(V²)` | `O(V+E)` |
| Edge lookup | `O(1)` | `O(deg)` |
| Neighbours | `O(V)` | `O(deg)` |
| BFS/DFS | `O(V²)` | `O(V+E)` |
| Sparse | wasteful | **best** |
| Dense | **best** | pointer overhead |

Undirected matrix is symmetric. Weighted list stores `(v, weight)`.

## 5.3 BFS

Queue; level order; shortest path in unweighted graphs.

```
visited[s]=true; Q.enqueue(s)
while Q not empty:
    u=Q.dequeue(); print u
    for v in adj[u]:
        if not visited[v]: visited[v]=true; dist[v]=dist[u]+1; Q.enqueue(v)
```

`O(V+E)` list, `O(V²)` matrix.

**AI-F24 graph, start 1:** `1, 2, 5, 3, 4`; distances `0,1,1,2,2`.

## 5.4 DFS

Stack/recursion; go deep, then backtrack.

```
DFS(u):
visited[u]=true; print u
for v in adj[u]:
    if not visited[v]: DFS(v)
```

`O(V+E)` list, `O(V²)` matrix.

**IO-F24 graph, start A:** `A, B, D, H, E, F, C, G`.

## 5.5 BFS vs DFS

| BFS | DFS |
|---|---|
| Queue | Stack/recursion |
| Level order | Deep path |
| Shortest path unweighted | Not shortest |
| `O(V+E)` | `O(V+E)` |
| Wide memory | Deep memory |
| Bipartite, web crawl | Cycles, topo sort, SCC |

## 5.6 Topological sort

Only for a DAG. Edge `u→v` ⇒ `u` before `v`.

**Kahn:**

```
compute indegrees
Q = all indegree-0 vertices
while Q: u=dequeue; order+=u; for v in adj[u]: if --indeg[v]==0 enqueue
if |order|<V: cycle
```

`O(V+E)`.

**DFS:** push vertex after all descendants finish; reverse the finish order.

**Past answers**

| Paper | Order |
|---|---|
| AI-F24 | `1,2,3,4,5,6` |
| P8 Jan-24 | `0,6,1,2,5,3,4` |
| IC-R26 | `4,5,0,2,3,1` |

**Applications:** build systems, prerequisites, task scheduling, deadlock detection, package managers.

## 5.7 Other graph algorithms

| Problem | Idea | Time |
|---|---|---|
| **Cycle — directed** | DFS colours; edge to GRAY = back edge | `O(V+E)` |
| **Cycle — undirected** | visited neighbour ≠ parent | `O(V+E)` |
| **Strongly connected** | DFS from `s`; DFS on transpose from `s`; both must reach all | `O(V+E)` |
| **Reverse graph** | for each `u→v`, add `v→u` | `O(V+E)` |
| **In/out degree** | outdeg[u] = len(adj[u]); scan lists for indeg | `O(V+E)` |

---

# 6. Searching, sorting and hashing

## 6.1 Searching

| Algorithm | Precondition | Best | Worst | Space |
|---|---|---|---|---|
| Linear | none | `O(1)` | `O(n)` | `O(1)` |
| Binary | sorted + random access | `O(1)` | `O(log n)` | `O(1)` |
| Fibonacci | sorted + sequential | `O(1)` | `O(log n)` | `O(1)` |

**Binary trace 35 in `[4,9,13,21,27,35,41,56,62]`:** mid 4 (27) → mid 6 (41) → mid 5 (35). 3 comparisons.

**Fibonacci trace 24 in `[12,14,16,17,20,24,31,43,50,62]`:** indices 4 (20) → 7 (43) → 5 (24). 3 probes.

**Fibonacci vs binary:** no division, works on sequential access; slightly fewer comparisons on some data.

## 6.2 Sorting comparison

| Algorithm | Best | Average | Worst | Space | Stable | In-place |
|---|---|---|---|---|---|---|
| Bubble | `O(n)` | `O(n²)` | `O(n²)` | `O(1)` | Yes | Yes |
| Selection | `O(n²)` | `O(n²)` | `O(n²)` | `O(1)` | No | Yes |
| Insertion | `O(n)` | `O(n²)` | `O(n²)` | `O(1)` | Yes | Yes |
| Merge | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(n)` | Yes | No |
| Quick | `O(n log n)` | `O(n log n)` | `O(n²)` | `O(log n)` | No | Yes |
| Heap | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(1)` | No | Yes |
| Radix | `O(d(n+k))` | `O(d(n+k))` | `O(d(n+k))` | `O(n+k)` | Yes | No |

**Nearly sorted → insertion sort**, `O(n + inversions)`.

**Quick worst case:** sorted/reverse with first/last pivot → `T(n)=T(n−1)+O(n)=O(n²)`. Fix with random/median-of-three pivot.

**Count occurrences in a sorted array:** binary-search the first and last occurrence; count = `last − first + 1`. Two `O(log n)` searches. Example `[1,2,2,2,3,4]`, key 2 → first 1, last 3, count 3.

## 6.3 Must-know sort traces

**Bubble `40,10,50,70,30`:**
`10,40,50,30,70` → `10,40,30,50,70` → `10,30,40,50,70`.

**Selection `30,10,50,20,40`:**
`10,30,50,20,40` → `10,20,50,30,40` → `10,20,30,50,40` → `10,20,30,40,50`.

**Insertion `54,26,93,17,77,31,44,55,20`:** final `17,20,26,31,44,54,55,77,93`.

**Merge `38,27,43,3,9,82,10`:** `27,38` + `3,43` → `3,27,38,43`; `9,82` + `10` → `9,10,82`; final `3,9,10,27,38,43,82`.

**Quick `[5,3,8,4,2,7,1]`, last pivot:** `1,3,8,4,2,7,5` → `1,3,4,2,5,7,8` → `1,2,4,3,5,7,8` → `1,2,3,4,5,7,8`.

**Quick `[15,82,63,91,28,74,47,39,55,18,70,26]`, last pivot:** final `15,18,26,28,39,47,55,63,70,74,82,91`.

**Radix `170,45,75,90,802,24,2,66`:**

| Pass | Array |
|---|---|
| units | `170,90,802,2,24,45,75,66` |
| tens | `802,2,24,45,66,170,75,90` |
| hundreds | `2,24,45,66,75,90,170,802` |

**Radix `329,457,657,839,436,720,355`:** final `329,355,436,457,657,720,839`.

**Heap sort `40,20,30,10,50`:** build `50,40,30,10,20`; final `10,20,30,40,50`.

**Sort 0s, 1s, 2s (Dutch flag):** three pointers `low, mid, high`; `0` swap low/mid and advance both; `1` advance mid; `2` swap mid/high and decrement high only. `O(n)`, `O(1)`. Input `0,1,1,0,1,2,1,2,0,0,0,1` → `0,0,0,0,0,1,1,1,1,1,2,2`.

## 6.4 Hashing formulas

| Method | Formula |
|---|---|
| Division | `h(k) = k mod m` |
| Mid-square | square, take middle `r` digits |
| Folding | split, add, take last digits |
| Multiplication | `h(k)=⌊m·frac(kA)⌋`, `A≈0.6180339887` |
| Universal | `((a·k+b) mod p) mod m` |

`α = n/m` (load factor).

**Mid-square (100 locations):** 5642→32, 6789→90, 5124→55.

**Multiplication (m=1000, k=12345):** `12345×0.6180339887=7629.6296` → **629**.

## 6.5 Collision resolution

| Method | Probe |
|---|---|
| Linear | `(h(k)+i) mod m` |
| Quadratic | `(h(k)+c₁i+c₂i²) mod m` |
| Double hashing | `(h₁(k)+i·h₂(k)) mod m` |
| Chaining | linked list per bucket |

- **Linear:** primary clustering; deletion needs tombstones.
- **Quadratic:** secondary clustering; may not visit all slots.
- **Double:** best distribution; `h₂` coprime to `m`.
- **Chaining:** easy deletion, extra pointers.

## 6.6 Past hash tables (memorise)

**IT-F24 — quadratic, m=7, c₁=0, c₂=1**

`[430, 22, 3920, 2341, 2839, 397, 4234]`; 5 extra probes.

**IT-R25 — double hashing, m=20, h₁=n%20, h₂=n%13**

Slots: `1→1, 63→3, 5→5, 48→6, 27→7, 8→8, 9→9, 69→13, 34→14, 16→16, 37→17`; 3 collisions.

**CO-R25 — quadratic, m=10, c₁=1, c₂=3**

`[—, 81, 72, 63, 24, 101, 36, 27, —, —]`; 1 collision.

**CO-R26 — linear, m=10**

`[—, 81, 72, 63, 24, 92, 36, 27, 101, —]`; 10 collisions.

**IO-F24 — linear, m=10**

`[18, 29, 62, 101, —, —, 36, 27, 37, 28]`; 8 collisions.

**IC-R26 — linear, m=10**

`[90, 11, 22, 9, 44, 34, 32, 67, 28, 88]`; 9 collisions.

**CO-B26 — chaining, m=9**

`0: 18→36→54`, `2: 11`, `5: 23`, `6: 24`, `7: 7→52`; 3 collisions.

**AI-F24 — 8 keys, m=5, chaining**

`1: 6→11`, `2: 2→7→12`, `3: 3→13`, `4: 9`, `0: empty`. (Linear probing needs ≥8 slots; a 10-slot version gives 4 collisions.)

**IT-R26 — [5,15,25,35], table 5**

All keys hash to 0 under `k mod 5`; linear probing puts them in slots 0–3; 6 extra probes.

## 6.7 Hashing vs rehashing

| Hashing | Rehashing |
|---|---|
| Map key → index | Grow table and reinsert all keys |
| `O(1)` average | `O(n)` rebuild, amortised `O(1)` |
| Fixed table | New size ≈ 2× old, next prime |
| — | Restores low load factor |

**Extendible hashing:** directory + buckets with local/global depth; split a bucket on overflow; double directory only when needed. **Linear hashing:** grow one bucket at a time with a split pointer.

---

# 7. Rapid-fire definitions

| Term | Answer |
|---|---|
| **ADT** | Logical operations + semantics, implementation hidden |
| **LIFO / FIFO** | Stack / queue |
| **Circular queue** | Ring array reusing freed slots; avoids false overflow |
| **Priority queue** | Delete highest-priority element, not FIFO |
| **DEQueue** | Insert/delete at both ends |
| **Expression tree** | Leaves operands, internal nodes operators |
| **BST** | Left < node < right; inorder sorted |
| **AVL** | BST with `\|BF\| ≤ 1`; rotations keep `O(log n)` |
| **Heap** | Complete tree; parent ≥/≤ children |
| **Huffman** | Optimal prefix code from frequencies |
| **B-tree** | Balanced multi-way search tree; all leaves same level |
| **Graph** | `G=(V,E)` |
| **DAG** | Directed acyclic graph |
| **Topological order** | Linear order respecting all directed edges |
| **Strongly connected** | Path between every ordered pair |
| **Spanning tree** | `V−1` edges, connects all vertices, no cycle |
| **Hash collision** | Two keys map to the same index |
| **Load factor** | `α = n/m` |
| **Stable sort** | Equal keys keep their relative order |
| **In-place sort** | `O(1)` extra memory |

---

# 8. Final checklist

**Before the exam**

- [ ] Can write the four AVL rotations from memory.
- [ ] Can convert any infix expression to postfix/prefix and evaluate postfix.
- [ ] Can build a tree from preorder+inorder and postorder+inorder.
- [ ] Can insert/delete in a BST (all three delete cases).
- [ ] Can build a max-heap and run heap sort.
- [ ] Can run BFS, DFS and Kahn's topological sort on a graph.
- [ ] Can trace quick, merge, radix and insertion sort.
- [ ] Can do linear, quadratic, double hashing and chaining numericals.
- [ ] Know the complexities and stability of all seven sorts.
- [ ] Know linked-list pointer updates for SLL, DLL and CLL.

**In the exam**

- [ ] State the convention (pivot position, order of B-tree, table size, adjacency order).
- [ ] Draw the data structure after **every** operation when the question says “step by step”.
- [ ] Show the formula before substituting numbers.
- [ ] For code, include `struct`, function, `main` and a small output.
- [ ] For comparisons, use a table rather than paragraphs.
- [ ] If a question is inconsistent, state your assumption in one line and proceed.

**Highest-yield numerical answers**

- [ ] AVL final roots: AI-F24 **68**, AI-R25 **30**, IT-R25 **50**, CO-R26 **18**, CO-B26 **C**, AI-R26 **100**, IT-F25 **12**.
- [ ] Heap build `[1,5,6,8,12,14,16]` → `[16,12,14,8,5,1,6]`.
- [ ] Huffman WPL: CO-F24 **421**, CO-R25 **1350**, IC-R26 **224**.
- [ ] Topological orders: AI-F24 `1,2,3,4,5,6`; Jan-24 `0,6,1,2,5,3,4`; IC-R26 `4,5,0,2,3,1`.
- [ ] BFS AI-F24: `1,2,5,3,4`; DFS IO-F24: `A,B,D,H,E,F,C,G`.
- [ ] Search 35 binary: 3 comparisons; search 25 Fibonacci: 2 probes.
