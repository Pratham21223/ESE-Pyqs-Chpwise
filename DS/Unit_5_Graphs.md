# Unit 5 — Non-Linear Data Structure: Graphs

> **Syllabus (DJS23IPC201, 6 hrs):** Graph terminologies, types of graphs, representation of graph using arrays and linked lists, Breadth-First Search (BFS), Depth-First Search (DFS), applications of graphs — topological sorting.
>
> **Past-paper coverage:** graph terminology and types; adjacency matrix vs adjacency list; BFS and DFS algorithms with traces and complexity; BFS vs DFS; topological sort (Kahn and DFS); cycle detection; strongly connected graphs; reversing a directed graph; in-degree/out-degree.

**Source codes**

| Code | Paper |
|---|---|
| IT-F24 | IT Final 2024-25 |
| CO-F24 | Computer Final 2024-25 |
| IO-F24 | IOT Final 2024-25 |
| AI-F24 | AIDS/AIML Final 2024-25 |
| IT-R25 | IT Re-Exam 2024-25 |
| CO-R25 | Computer Re-Exam 2024-25 |
| AI-R25 | AIDS/AIML Re-Exam 2024-25 |
| P8 Jan-24 | Common Final 2023-24 (65 marks) |
| IT-F25 | IT Final 2025-26 |
| IT-R26 | IT Re-Exam 2025-26 |
| AI-R26 | AIDS Re-Exam 2025-26 |
| IC-R26 | CSE (ICB) Re-Exam 2025-26 |
| CO-R26 | Computer Re-Exam 2024-25 batch |
| CO-B26 | 2023-24 Backlog Re-Exam (65 marks) |

---

## 5.1 Graph terminology and types **[5]**

**Asked in:** IT-F25 Q2B(1) (strongly connected); CO-B26 Q4B (representation); AI-R25 Q4(2).

**Question (verbatim):**
> What is strongly connected graph? Write an algorithm to check whether the directed graph is strongly connected or not.

**Answer**

A **graph** `G = (V, E)` consists of a set of **vertices** `V` and a set of **edges** `E` connecting pairs of vertices.

**Terminology**

| Term | Meaning |
|---|---|
| **Undirected edge** | `{u, v}` — no direction; `u` and `v` are adjacent. |
| **Directed edge (arc)** | `(u, v)` — from `u` to `v`; `u` is the tail, `v` the head. |
| **Weighted graph** | Every edge has a cost/weight. |
| **Degree** | Number of incident edges (undirected). |
| **In-degree / out-degree** | Number of incoming / outgoing arcs (directed). |
| **Path** | Sequence of vertices connected by edges. |
| **Simple path** | No vertex repeated. |
| **Cycle** | Path that starts and ends at the same vertex. |
| **DAG** | Directed Acyclic Graph — no directed cycle. |
| **Connected graph** | A path exists between every pair of vertices (undirected). |
| **Strongly connected** | In a directed graph, a path exists from every vertex to every other vertex. |
| **Weakly connected** | The underlying undirected graph is connected (directed). |
| **Complete graph** | Every pair of distinct vertices is adjacent; `n(n−1)/2` undirected edges. |
| **Simple graph** | No self-loops and no multiple edges. |
| **Multigraph** | Multiple edges between the same pair allowed. |
| **Sparse / dense** | Few edges (`E ≪ V²`) / many edges (`E ≈ V²`). |

**Strongly connected graph**

A directed graph is **strongly connected** if for every ordered pair `(u, v)` there is a directed path from `u` to `v` (and therefore also from `v` to `u`). A single vertex is strongly connected by convention.

**Checking algorithm (Kosaraju, two DFS passes)**

```
IS_STRONGLY_CONNECTED(G):
1. mark all vertices unvisited
2. DFS from any vertex s; if some vertex remains unvisited → return false
3. build the transpose Gᵀ (reverse every edge)
4. mark all vertices unvisited
5. DFS on Gᵀ from the same s; if some vertex remains unvisited → return false
6. return true
```

Why it works: the first DFS checks that every vertex is reachable **from** `s`; the second checks that every vertex can reach `s` in `G`, which is the same as `s` reaching every vertex in `Gᵀ`. Together they give reachability in both directions.

**Example**

```
0 → 1 → 2 → 0        strongly connected
0 → 1 → 2            not strongly connected (2 cannot reach 0)
```

Time `O(V + E)` for each DFS, total `O(V + E)`; space `O(V + E)` for the transpose.

---

## 5.2 Graph representation: adjacency matrix and adjacency list **[5–10]**

**Asked in:** IT-F24 Q3B; AI-R25 Q4(2); AI-R26 Q3A; CO-B26 Q4B; IT-R25 Q3B.

**Questions (verbatim):**
> Discuss how a Graph can be represented using an Adjacency List. Discuss the scenario where this representation might be more memory-efficient than adjacency matrix. Support your answer with suitable example.
>
> Explain Graph representation using matrix and Linked List with suitable example.
>
> Represent the following Graph in memory using adjacency matrix and adjacency list.
>
> What are the different ways in which one can represent a graph? Use suitable example to support your answer.
>
> Compare Array and Linked List Representations of Graphs. Also discuss which storage method would be more efficient for a dense graph and Why?

**Answer**

**Adjacency matrix**

A `V × V` matrix `A`:

- `A[i][j] = 1` if there is an edge `i → j` (or `i–j`), else `0`.
- Weighted graph: store the weight instead of 1, and `∞`/0 for no edge.
- Undirected graph: the matrix is symmetric; store each edge twice.

**Adjacency list**

An array of `V` lists. `adj[u]` contains every vertex adjacent to `u` (and optionally the edge weight).

**Example — undirected graph**

```
        0
       / \
      1   2
      |   |
      3───4
```

Edges: `0–1, 0–2, 1–3, 2–4, 3–4`.

Matrix:

```
     0 1 2 3 4
  0  0 1 1 0 0
  1  1 0 0 1 0
  2  1 0 0 0 1
  3  0 1 0 0 1
  4  0 0 1 1 0
```

List:

```
0: 1 → 2
1: 0 → 3
2: 0 → 4
3: 1 → 4
4: 2 → 3
```

**Example — directed graph**

```
0 → 1, 0 → 2, 1 → 3, 2 → 3
```

Matrix:

```
     0 1 2 3
  0  0 1 1 0
  1  0 0 0 1
  2  0 0 0 1
  3  0 0 0 0
```

List:

```
0: 1 → 2
1: 3
2: 3
3:
```

**AI-R26 graph** (undirected): edges `0–2, 0–1, 2–3, 1–3, 1–5, 5–4, 5–6, 6–7`.

Matrix (8 × 8):

```
     0 1 2 3 4 5 6 7
  0  0 1 1 0 0 0 0 0
  1  1 0 0 1 0 1 0 0
  2  1 0 0 1 0 0 0 0
  3  0 1 1 0 0 0 0 0
  4  0 0 0 0 0 1 0 0
  5  0 1 0 0 1 0 1 0
  6  0 0 0 0 0 1 0 1
  7  0 0 0 0 0 0 1 0
```

List:

```
0: 1, 2
1: 0, 3, 5
2: 0, 3
3: 1, 2
4: 5
5: 1, 4, 6
6: 5, 7
7: 6
```

**Comparison**

| Point | Adjacency matrix | Adjacency list |
|---|---|---|
| Memory | `O(V²)` | `O(V + E)` |
| Edge lookup `(u,v)` | `O(1)` | `O(deg(u))` |
| List all neighbours of `u` | `O(V)` | `O(deg(u))` |
| Add edge | `O(1)` | `O(1)` (append) |
| Delete edge | `O(1)` | `O(deg(u))` |
| BFS/DFS | `O(V²)` | `O(V + E)` |
| Sparse graph | wastes memory | efficient |
| Dense graph | acceptable, simple | pointer overhead |
| Weighted graph | natural | store `(v, w)` pairs |

**Which is better?**

- **Sparse graph** (`E ≪ V²`, e.g., social network, road map): **adjacency list** — memory `O(V + E)` and traversal `O(V + E)`.
- **Dense graph** (`E ≈ V²`, e.g., complete graph, Floyd–Warshall input): **adjacency matrix** — `O(V²)` memory is unavoidable, and `O(1)` edge lookup plus simple array access wins. For `V` small, the matrix is also simpler and faster due to cache locality.
- **Very large sparse graph:** list; **frequent edge existence queries:** matrix or hash-based adjacency.

---

## 5.3 Breadth-First Search (BFS) **[5–10]**

**Asked in:** AI-F24 Q3(1); AI-R25 Q3(1); IT-R26 Q3B; CO-F24 Q4A(ii); CO-R25 Q4A(ii); CO-R26 Q4A(ii).

**Questions (verbatim):**
> Find the breadth first search traversal (BFS) of the below graph. Suppose we select vertex “1” as starting point. Also write the time complexity of BFS.
>
> Write Algorithm of Breadth-First Search (BFS) traversal for graph data structure and explain it with suitable example. Also write the time complexity of BFS.
>
> Write down Breadth First Search algorithm. Trace the algorithm on any directed graph with minimum 5 vertices.
>
> Explain Depth First Search and Breadth First Search with example.
>
> Write a menu driven program/algorithm to implement Breadth First Search and Depth First search.
>
> Write a menu driven program to implement Breadth First Search.

**Answer**

**Idea:** BFS explores the graph **level by level**. It visits all neighbours of the start vertex first, then their unvisited neighbours, and so on. A **queue** (FIFO) drives the frontier.

```
BFS(G, s):
1. for each vertex u in V:
       visited[u] ← false
       dist[u] ← ∞
       parent[u] ← NULL
2. visited[s] ← true; dist[s] ← 0
3. Q ← empty; enqueue(Q, s)
4. while Q not empty:
       u ← dequeue(Q)
       print u
       for each v in adj[u]:
           if not visited[v]:
               visited[v] ← true
               dist[v] ← dist[u] + 1
               parent[v] ← u
               enqueue(Q, v)
```

BFS produces a **shortest path tree** in an unweighted graph: `dist[v]` is the minimum number of edges from `s` to `v`.

**Example 1 — AI-F24 graph, start 1**

Edges: `1–2, 1–5, 2–5, 2–3, 5–3, 5–4, 3–4`.

Adjacency (ascending):

```
1: 2, 5
2: 1, 3, 5
3: 2, 4, 5
4: 3, 5
5: 1, 2, 3, 4
```

| Step | Dequeue | Queue after | Visited |
|---|---|---|---|
| 0 | — | 1 | {1} |
| 1 | 1 | 2, 5 | {1,2,5} |
| 2 | 2 | 5, 3 | {1,2,5,3} |
| 3 | 5 | 3, 4 | {1,2,3,4,5} |
| 4 | 3 | 4 | — |
| 5 | 4 | — | — |

**BFS order: `1, 2, 5, 3, 4`.**  
Distances: `d(1)=0, d(2)=1, d(5)=1, d(3)=2, d(4)=2`.

**Example 2 — small graph**

```
A: B, C
B: A, D, E
C: A, F
D: B
E: B, F
F: C, E
```

BFS from A: `A, B, C, D, E, F`.

**Example 3 — directed graph with 5 vertices**

```
1 → 2, 1 → 3, 2 → 4, 3 → 4, 4 → 5
```

BFS from 1: `1, 2, 3, 4, 5`.

**Complexity**

| Representation | Time | Space |
|---|---|---|
| Adjacency list | `O(V + E)` | `O(V)` queue + visited |
| Adjacency matrix | `O(V²)` | `O(V)` |

**Applications:** shortest path in unweighted graphs, web crawling, social-network levels, bipartite checking, flood fill, connected components, GPS navigation with uniform weights.

---

## 5.4 Depth-First Search (DFS) **[5–10]**

**Asked in:** IO-F24 Q3B; CO-F24 Q4A(ii); CO-R25 Q4A(ii); IT-R25 Q4B(1); IT-F25 Q3B (cycle detection).

**Questions (verbatim):**
> Apply the Depth -First Search (BFS) traversal algorithm step-by-step on the graph to generate the traversal path, starting from node A.
>
> Explain Depth First Search and Breadth First Search with example.
>
> State the difference between BFS and DFS using suitable example.
>
> How can DFS be used to detect cycles in directed and undirected graphs? Explain with suitable example.

**Answer**

**Idea:** DFS goes as **deep as possible** along one branch before backtracking. It uses a **stack** (the call stack in recursion).

```
DFS(G, u):
1. visited[u] ← true
2. print u
3. for each v in adj[u]:
       if not visited[v]:
           parent[v] ← u
           DFS(G, v)
```

Iterative version uses an explicit stack; push neighbours in reverse order to match the recursive visit order.

**Example 1 — IO-F24 graph, start A**

Edges: `A–B, A–C, B–D, B–E, D–H, E–H, C–F, C–G, F–H, G–H`.

Adjacency (alphabetical):

```
A: B, C
B: A, D, E
C: A, F, G
D: B, H
E: B, H
F: C, H
G: C, H
H: D, E, F, G
```

**Trace**

| Step | Visit | Action |
|---|---|---|
| 1 | A | go to first unvisited neighbour B |
| 2 | B | go to D |
| 3 | D | go to H |
| 4 | H | neighbours D (visited), E → visit E |
| 5 | E | neighbours B, H visited → backtrack to H |
| 6 | H | next unvisited F → visit F |
| 7 | F | neighbour C unvisited → visit C |
| 8 | C | neighbours A, F visited; G unvisited → visit G |
| 9 | G | neighbours C, H visited → backtrack |
| 10 | — | unwind to A; all visited |

**DFS order: `A, B, D, H, E, F, C, G`.**

The DFS tree edges are `A–B, B–D, D–H, H–E, H–F, F–C, C–G`; the non-tree edges `A–C, B–E, C–A, G–H` are back/cross edges.

**Example 2 — small graph** (same as BFS example)

```
A: B, C
B: A, D, E
C: A, F
D: B
E: B, F
F: C, E
```

DFS from A: `A, B, D, E, F, C`.

**Complexity**

| Representation | Time | Space |
|---|---|---|
| Adjacency list | `O(V + E)` | `O(V)` recursion/stack |
| Adjacency matrix | `O(V²)` | `O(V)` |

**Applications:** cycle detection, topological sort, connected components, strongly connected components (Kosaraju/Tarjan), maze solving, path finding, bridge/articulation points, backtracking.

---

## 5.5 BFS vs DFS **[5]**

**Asked in:** IT-R25 Q4B(1); CO-F24 Q4A(ii).

**Question (verbatim):**
> State the difference between BFS and DFS using suitable example.

**Answer**

| Point | BFS | DFS |
|---|---|---|
| Data structure | Queue (FIFO) | Stack / recursion (LIFO) |
| Exploration | Level by level | Deep along one branch, then backtrack |
| Shortest path (unweighted) | Yes — first visit is shortest | No (not guaranteed) |
| Time (list / matrix) | `O(V+E)` / `O(V²)` | `O(V+E)` / `O(V²)` |
| Space | `O(V)` queue (can be wide) | `O(V)` stack (can be deep) |
| Complete | Yes (finds a path if one exists) | Yes |
| Typical uses | shortest path, bipartite, web crawl, level order | cycle detection, topological sort, SCC, backtracking |
| Traversal shape | radial / concentric | one long path then backtrack |

**Same graph, different order**

```
A: B, C
B: A, D, E
C: A, F
D: B
E: B, F
F: C, E
```

- BFS: `A, B, C, D, E, F`
- DFS: `A, B, D, E, F, C`

BFS reaches `F` in 3 edges (`A–C–F` or `A–B–E–F`); DFS may reach it along a longer branch first.

**Choice:** BFS when the shortest path or nearest solution matters; DFS when the structure (cycles, topological order, components) matters or memory is tight on wide graphs.

---

## 5.6 Topological sorting **[5–10]**

**Asked in:** IT-F24 Q4B(1); AI-F24 Q4(2); P8 Jan-24 Q3A; IC-R26 Q2a; AI-R26 Q4B; CO-R26 Q3B(2).

**Questions (verbatim):**
> Explain Topological sorting with a suitable example.
>
> Apply topological sorting on the following graph and find the topological order.
>
> Write down an algorithm for topological sort. Trace the algorithm on following graph to generate at least one topological order.
>
> Trace the Topological sorting Algorithm stepwise on following graph to generate at least one topological order.
>
> Write the algorithm steps in topological sorting and discuss the application of the topological sort.
>
> Define topological sorting. Explain the same with suitable example.

**Answer**

A **topological order** of a **DAG** is a linear ordering of vertices such that for every directed edge `u → v`, `u` appears before `v`. It exists **iff** the graph has no directed cycle.

**Kahn's algorithm (BFS-based)**

```
TOPOLOGICAL_SORT(G):
1. compute indegree[v] for every vertex
2. Q ← all vertices with indegree 0
3. order ← empty
4. while Q not empty:
       u ← dequeue(Q)
       append u to order
       for each v in adj[u]:
           indegree[v] ← indegree[v] − 1
           if indegree[v] = 0: enqueue(Q, v)
5. if |order| < |V|: report "cycle exists"
6. return order
```

Time `O(V + E)`; space `O(V)`.

**DFS-based alternative:** run DFS; push each vertex onto a stack **after** all its descendants are finished; pop the stack for the topological order. A back edge (to a gray vertex) means a cycle.

**Example 1 — AI-F24 graph**

Edges: `1→2, 1→3, 2→4, 2→5, 3→4, 3→6, 4→5, 4→6`.

Indegrees: `1:0, 2:1, 3:1, 4:2, 5:2, 6:2`.

| Step | Queue | Pop | Order | Indegree changes |
|---|---|---|---|---|
| 0 | 1 | — | — | — |
| 1 | 2,3 | 1 | 1 | 2→0, 3→0 |
| 2 | 3 | 2 | 1,2 | 4→1, 5→1 |
| 3 | 4 | 3 | 1,2,3 | 4→0, 6→1 |
| 4 | 5,6 | 4 | 1,2,3,4 | 5→0, 6→0 |
| 5 | 6 | 5 | 1,2,3,4,5 | — |
| 6 | — | 6 | **1,2,3,4,5,6** | — |

**One valid order: `1, 2, 3, 4, 5, 6`.**

**Example 2 — Jan-24 graph**

Edges: `6→1, 6→5, 1→5, 1→2, 0→2, 5→3, 2→3, 5→4`.

Indegrees: `0:0, 1:1, 2:2, 3:2, 4:1, 5:2, 6:0`.

Kahn with an ascending queue:

| Step | Queue | Pop | Order |
|---|---|---|---|
| 0 | 0,6 | — | — |
| 1 | 6 | 0 | 0 |
| 2 | 1 | 6 | 0,6 |
| 3 | 2,5 | 1 | 0,6,1 |
| 4 | 5 | 2 | 0,6,1,2 |
| 5 | 3,4 | 5 | 0,6,1,2,5 |
| 6 | 4 | 3 | 0,6,1,2,5,3 |
| 7 | — | 4 | **0,6,1,2,5,3,4** |

Other valid orders: `6,1,5,4,0,2,3`; `0,6,1,5,2,3,4`.

**Example 3 — IC-R26 graph**

Edges: `5→0, 5→2, 2→3, 3→1, 4→0, 4→1`.

Indegrees: `0:2, 1:2, 2:1, 3:1, 4:0, 5:0`.

Kahn: start `4,5`; pop 4 → order 4; pop 5 → 0 and 2 become ready; pop 0; pop 2 → 3 ready; pop 3 → 1 ready; pop 1.

**Order: `4, 5, 0, 2, 3, 1`.** Another valid order: `5,4,2,3,0,1`.

**Applications**

1. **Build systems** — compile a module after its dependencies (`make`, Gradle).
2. **Course prerequisites** — semester planning.
3. **Task scheduling** — project tasks with precedence constraints.
4. **Deadlock detection** — wait-for graphs; a cycle means deadlock.
5. **Data-pipeline / workflow ordering** — ETL jobs with dependencies.
6. **Package managers** — install dependencies before dependants.

**Cycle check:** if the graph has a cycle, no topological order exists. Kahn detects this when `|order| < |V|`; DFS detects a back edge.

---

## 5.7 Cycle detection using DFS **[5]**

**Asked in:** IT-F25 Q3B.

**Question (verbatim):**
> How can DFS be used to detect cycles in directed and undirected graphs? Explain with suitable example.

**Answer**

**Directed graph — three colours**

```
WHITE = unvisited, GRAY = in current recursion stack, BLACK = finished

DFS_CYCLE(u):
1. color[u] ← GRAY
2. for each v in adj[u]:
       if color[v] = GRAY: return true       /* back edge → cycle */
       if color[v] = WHITE and DFS_CYCLE(v): return true
3. color[u] ← BLACK
4. return false
```

A **back edge** to a GRAY ancestor closes a directed cycle.

**Example:** `1→2, 2→3, 3→1`.

- DFS(1): 1 GRAY; DFS(2): 2 GRAY; DFS(3): 3 GRAY; edge `3→1`, 1 is GRAY → **cycle**.

A DAG never has a back edge; its DFS produces only tree/forward/cross edges and finishes with a topological order.

**Undirected graph — parent check**

```
DFS_CYCLE_UNDIRECTED(u, parent):
1. visited[u] ← true
2. for each v in adj[u]:
       if not visited[v]:
           if DFS_CYCLE_UNDIRECTED(v, u): return true
       else if v ≠ parent:
           return true                       /* visited neighbour, not parent */
3. return false
```

In an undirected graph, every edge is seen twice (once from each end), so the edge back to the **parent** is not a cycle; any other visited neighbour creates a cycle.

**Example:** `A–B, B–C, C–A`.

- DFS(A, −): visit B; DFS(B, A): visit C; DFS(C, B): neighbour A is visited and `A ≠ parent B` → **cycle**.

**Example without a cycle:** `A–B, B–C` is a tree; at C, the only visited neighbour is parent B → no cycle.

**Complexity:** `O(V + E)` time, `O(V)` colour/visited array plus recursion stack.

---

## 5.8 Reverse a directed graph **[5–10]**

**Asked in:** IT-R26 Q2B(1); CO-B26 Q3A.

**Questions (verbatim):**
> Write down the algorithm that returns the reverse of the directed graph (each edge from v to w is replaced by an edge from w to v).
>
> Give an algorithm/program that returns the reverse of the directed graph (each edge from v to w is replaced by an edge from w to v). Show the working of the algorithm using suitable example.

**Answer**

The **transpose** `Gᵀ` has the same vertices and every edge reversed: `(u,v) ∈ E ⇔ (v,u) ∈ Eᵀ`.

**Adjacency-list algorithm**

```
REVERSE_GRAPH(G):
1. create V empty lists rev[0…V−1]
2. for u ← 0 to V−1:
       for each v in adj[u]:
           append u to rev[v]
3. return rev
```

Time `O(V + E)`, space `O(V + E)`.

**Example**

```
Original:
0 → 1, 0 → 2
1 → 3
2 → 3
3 →

Reversed:
0 →
1 → 0
2 → 0
3 → 1, 3 → 2
```

**C-style implementation**

```c
/* adj is an array of linked lists; rev is built the same way */
for (int u = 0; u < V; u++)
    for (struct Node *p = adj[u]; p != NULL; p = p->next)
        addEdge(rev, p->vertex, u);      /* add u to rev[p->vertex] */
```

**Adjacency-matrix version:** `rev[i][j] = A[j][i]` for all `i, j` — a transpose, `O(V²)`.

**Why it is useful:** Kosaraju's strongly-connected-components algorithm runs DFS on `G`, then on `Gᵀ`; reachability in the transpose answers “which vertices can reach `u`?”.

---

## 5.9 In-degree and out-degree from an adjacency list **[5]**

**Asked in:** P8 Jan-24 Q4B.

**Question (verbatim):**
> Given the adjacency list of a graph, write a program/algorithm to calculate the in degree and out degree of all N nodes in the given directed graph. State the time required to perform the said operation.

**Answer**

- **Out-degree** of `u` = number of vertices in `adj[u]`.
- **In-degree** of `v` = number of times `v` appears in all adjacency lists.

```
DEGREES(adj, N):
1. for i ← 0 to N−1:
       outdeg[i] ← 0
       indeg[i] ← 0
2. for u ← 0 to N−1:
       for each v in adj[u]:
           outdeg[u] ← outdeg[u] + 1
           indeg[v] ← indeg[v] + 1
3. return indeg, outdeg
```

**Example**

```
0: 1, 2
1: 3
2: 3
3:
```

| Vertex | In-degree | Out-degree |
|---|---:|---:|
| 0 | 0 | 2 |
| 1 | 1 | 1 |
| 2 | 1 | 1 |
| 3 | 2 | 0 |

**Time:** every edge is examined once, so `O(V + E)`. With an adjacency matrix it is `O(V²)`.

**C function**

```c
void degrees(int adj[][MAX], int V, int indeg[], int outdeg[])
{
    for (int i = 0; i < V; i++) indeg[i] = outdeg[i] = 0;
    for (int u = 0; u < V; u++)
        for (int v = 0; v < V; v++)
            if (adj[u][v]) { outdeg[u]++; indeg[v]++; }   /* O(V²) matrix */
}
```

For a list representation, replace the inner `v` loop with a traversal of `adj[u]`, giving `O(V + E)`.

**Handshake lemma:** `Σ indeg(v) = Σ outdeg(v) = |E|`.

---

## 5.10 Menu-driven BFS/DFS program **[10]**

**Asked in:** CO-R25 Q4A(ii); CO-R26 Q4A(ii).

**Question (verbatim):**
> Write a menu driven program/algorithm to implement Breadth First Search and Depth First search.
>
> Write a menu driven program to implement Breadth First Search.

**Answer**

The program uses an adjacency matrix and a global `visited` array. BFS uses a circular queue; DFS uses recursion.

```c
#include <stdio.h>

#define MAX 20
int adj[MAX][MAX], visited[MAX], n;

void bfs(int s) {
    int q[MAX], front = 0, rear = 0;
    for (int i = 0; i < n; i++) visited[i] = 0;
    visited[s] = 1;
    q[rear++] = s;
    printf("BFS: ");
    while (front < rear) {
        int u = q[front++];
        printf("%d ", u);
        for (int v = 0; v < n; v++)
            if (adj[u][v] && !visited[v]) {
                visited[v] = 1;
                q[rear++] = v;
            }
    }
    printf("\n");
}

void dfs(int u) {
    visited[u] = 1;
    printf("%d ", u);
    for (int v = 0; v < n; v++)
        if (adj[u][v] && !visited[v]) dfs(v);
}

int main(void) {
    int e, u, v, choice, s;
    printf("Vertices: "); scanf("%d", &n);
    printf("Edges: ");    scanf("%d", &e);
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++) adj[i][j] = 0;
    printf("Enter %d edges (u v):\n", e);
    for (int i = 0; i < e; i++) {
        scanf("%d %d", &u, &v);
        adj[u][v] = 1;
        adj[v][u] = 1;              /* remove for a directed graph */
    }
    do {
        printf("1.BFS 2.DFS 3.Exit\nChoice: ");
        scanf("%d", &choice);
        if (choice == 1 || choice == 2) {
            printf("Start vertex: "); scanf("%d", &s);
            for (int i = 0; i < n; i++) visited[i] = 0;
            if (choice == 1) bfs(s);
            else { printf("DFS: "); dfs(s); printf("\n"); }
        }
    } while (choice != 3);
    return 0;
}
```

**Sample input**

```
5
5
0 1
0 2
1 3
2 4
3 4
```

BFS from 0: `0 1 2 3 4`; DFS from 0: `0 1 3 4 2`.

**Complexity:** BFS `O(V²)` with a matrix (`O(V+E)` with a list); DFS `O(V²)` with a matrix, `O(V+E)` with a list.

---

## Quick revision checklist — Unit 5

**Terminology**

- [ ] Graph `G=(V,E)`; directed/undirected; weighted/unweighted.
- [ ] Degree; in-degree; out-degree; handshake lemma.
- [ ] Path, simple path, cycle, DAG.
- [ ] Connected, strongly connected, weakly connected.
- [ ] Complete graph: `n(n−1)/2` undirected edges.
- [ ] Sparse vs dense.

**Representation**

- [ ] Matrix: `V×V`, `O(V²)` memory, `O(1)` edge lookup.
- [ ] List: `V` lists, `O(V+E)` memory, `O(deg)` neighbour scan.
- [ ] Undirected matrix is symmetric.
- [ ] Sparse → list; dense → matrix.
- [ ] BFS/DFS: `O(V+E)` list, `O(V²)` matrix.

**BFS**

- [ ] Queue; level order; shortest path in unweighted graphs.
- [ ] Algorithm with visited/dist/parent.
- [ ] AI-F24 graph from 1: `1,2,5,3,4`.
- [ ] Complexity `O(V+E)`.
- [ ] Applications: shortest path, bipartite, web crawl, components.

**DFS**

- [ ] Stack/recursion; go deep then backtrack.
- [ ] IO-F24 graph from A: `A,B,D,H,E,F,C,G`.
- [ ] Complexity `O(V+E)`.
- [ ] Applications: cycles, topological sort, SCC, bridges.

**BFS vs DFS**

- [ ] Queue vs stack.
- [ ] Level order vs deep path.
- [ ] BFS shortest path; DFS structure.
- [ ] Same time complexity, different space shape.

**Topological sort**

- [ ] Only for DAG; edge `u→v` means `u` before `v`.
- [ ] Kahn: indegree 0 queue, decrement neighbours.
- [ ] Detect cycle if output count `< V`.
- [ ] DFS alternative: push on finish, reverse.
- [ ] AI-F24 order `1,2,3,4,5,6`.
- [ ] Jan-24 order `0,6,1,2,5,3,4`.
- [ ] IC-R26 order `4,5,0,2,3,1`.
- [ ] Applications: build, prerequisites, scheduling, deadlock detection.

**Other algorithms**

- [ ] Cycle detection: directed = back edge to GRAY; undirected = visited non-parent.
- [ ] Strongly connected: DFS from `s`, then DFS on transpose from `s`.
- [ ] Reverse graph: for each `u→v`, add `v→u`; `O(V+E)`.
- [ ] In/out degree: scan all adjacency lists; `O(V+E)`.
