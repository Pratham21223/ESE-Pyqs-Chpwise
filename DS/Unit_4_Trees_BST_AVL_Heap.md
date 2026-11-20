# Unit 4 — Non-Linear Data Structure: Trees

> **Syllabus (DJS23IPC201, 10 hrs):** Tree terminologies, tree as an ADT, binary tree — operations, tree traversals, Binary Search Tree (BST) — operations, expression trees. Height-balanced tree: AVL tree — creation, insertion and deletion. Heap — various operations and applications.
>
> **Past-paper coverage:** tree terminology; binary-tree applications; inorder/preorder/postorder traversals; constructing a tree from traversals; BST insert/search/delete/min/max/mirror/LCA; AVL balance factor, rotations, insertion and deletion; min/max heap, build-heap, insert, delete, heapify; Huffman tree; B-tree.

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

## 4.1 Tree terminology, binary tree and applications **[5–10]**

**Asked in:** IO-F24 Q3A(1); AI-R25 Q1(1) (balanced-BST alternative); CO-F24 Q1A(i) (BST creation, §4.4).

**Questions (verbatim):**
> Discuss applications of Binary tree. Construct expression tree with the help of all necessary steps for the following expressions `(a^2+2*a*b+b^2)^3 + (c^2-2*c*d+d^2)^3`
>
> What is a balanced binary search tree? How can it be used to improve the performance of search and insert operations?

**Answer**

A **tree** is a non-linear hierarchy of nodes connected by edges. A **binary tree** is a tree in which every node has at most two children, called the **left child** and **right child**.

**Terminology**

| Term | Meaning |
|---|---|
| **Root** | Top node with no parent (one per tree). |
| **Parent / child** | An edge from `p` to `c` makes `p` the parent and `c` the child. |
| **Sibling** | Two nodes with the same parent. |
| **Leaf / external node** | Node with no children. |
| **Internal node** | Node with at least one child. |
| **Degree of a node** | Number of children (0, 1 or 2 in a binary tree). |
| **Degree of a tree** | Maximum degree of any node. |
| **Level** | Root is level 0 (or 1, if your class starts at 1); children are one level below. |
| **Depth of a node** | Number of edges from the root to that node. |
| **Height of a node** | Longest path in edges from the node down to a leaf. |
| **Height of the tree** | Height of the root. |
| **Subtree** | A node together with all its descendants. |
| **Ancestor / descendant** | A node on the path to the root / below a node. |
| **Forest** | A set of disjoint trees. |

**Types of binary trees**

| Type | Definition |
|---|---|
| **Full (proper) binary tree** | Every node has 0 or 2 children. |
| **Complete binary tree** | All levels filled except possibly the last, which is filled left to right. |
| **Perfect binary tree** | Every internal node has two children and all leaves are at the same level. |
| **Skewed binary tree** | Every node has only a left (left-skewed) or only a right (right-skewed) child. |
| **Balanced binary tree** | Heights of left and right subtrees of every node differ by at most 1 (AVL condition). |
| **Binary Search Tree** | For every node, left subtree keys < node key < right subtree keys. |

**Binary tree ADT**

```
ADT BinaryTree
  create()            : empty tree
  insert(x)           : insert a node
  delete(x)           : remove a node
  search(x)           : find a node
  inorder()           : left → root → right
  preorder()          : root → left → right
  postorder()         : left → right → root
  height()            : height of the tree
  isEmpty()           : true if no nodes
```

**Applications of binary trees**

1. **Expression trees** — parse and evaluate arithmetic expressions; leaves are operands, internal nodes are operators.
2. **Binary Search Tree** — ordered dictionary with `O(h)` search/insert/delete; `h = O(log n)` when balanced.
3. **Huffman coding** — optimal prefix codes for compression.
4. **Heap** — priority queue, heap sort, Dijkstra/Prim.
5. **Syntax trees** — compilers parse source code into an abstract syntax tree.
6. **File systems / XML / JSON** — hierarchical directory and document structures.
7. **Decision trees** — machine-learning classification and game trees (minimax).
8. **Database indexes** — B-tree/B+ tree variants store ordered records on disk.
9. **Arithmetic expression evaluation** — used by calculators and interpreters.
10. **Trie-like structures and spatial trees** — prefix search, quadtrees, k-d trees.

**Expression tree for `(a² + 2ab + b²)³ + (c² − 2cd + d²)³`**

Parse each bracket as a sum of three terms, then cube it, then add the two cubes.

**Precise bracket form**

```
+ ( ^3 ( + ( + ( ^2(a,2), * ( * (2,a), b ) ), ^2(b,2) ), 3 ),
    ^3 ( + ( - ( ^2(c,2), * ( * (2,c), d ) ), ^2(d,2) ), 3 ) )
```

`op(L,R)` means an operator node `op` with left subtree `L` and right subtree `R`.

**Top-level shape**

```
                    +
                 /     \
               ^3       ^3
              /  \      /  \
            S1    3   S2   3
```

- `S1 = a² + 2ab + b²` — the first bracket.
- `S2 = c² − 2cd + d²` — the second bracket.

**S1**

```
              +
            /   \
          +      ^2
        /  \     / \
      ^2    *   b   2
     / \   / \
    a   2 *   b
         / \
        2   a
```

**S2**

```
              +
            /   \
          -      ^2
        /  \     / \
      ^2    *   d   2
     / \   / \
    c   2 *   d
         / \
        2   c
```

**Construction steps**

1. `^2(a,2)` and `^2(b,2)` for the square terms.
2. `*(2,a)` then `*( *(2,a), b )` for `2ab` (similarly `2cd`).
3. `+( ^2(a,2), *( *(2,a), b ) )`, then add `^2(b,2)` on the right.
4. Wrap the first bracket in `^3(S1, 3)`.
5. Repeat for `S2 = +( -( ^2(c,2), *( *(2,c), d ) ), ^2(d,2) )` and `^3(S2, 3)`.
6. Root `+` adds the two cubes.

The subtraction in `S2` is explicit: `c² − 2cd + d²` parses as `(c² − 2cd) + d²`, which is why the `−` node is the left child of the top `+` in S2.

**Balanced BST (AI-R25 Q1(1), OR alternative)**

A **balanced BST** keeps the height `h = O(log n)` by restricting the height difference of the two subtrees at every node (AVL: `|BF| ≤ 1`; red-black: color rules). Search, insert and delete then take `O(log n)` instead of the `O(n)` of a skewed BST. Rotations restore balance after an insertion/deletion. See §4.5 for the AVL mechanics.

---

## 4.2 Binary tree traversals **[5–8]**

**Asked in:** CO-R25 Q1A(i); CO-R26 Q1A(i); IT-R26 Q2A(i).

**Questions (verbatim):**
> Write a complete C program / Algorithm to perform Postorder Traversal on a Binary Search Tree. Give simple example.
>
> Write a complete C program / Algorithm to perform Preorder Traversal on a Binary Search Tree. Give simple example.
>
> Write a C program to implement Binary Search Tree (BST) operations: Insert, Search, Inorder, Preorder, Postorder traversals.

**Answer**

**The three depth-first traversals**

| Traversal | Order | Mnemonic |
|---|---|---|
| **Inorder** | Left → Root → Right | gives **sorted** output for a BST |
| **Preorder** | Root → Left → Right | root first (copy/serialize) |
| **Postorder** | Left → Right → Root | children before parent (delete/evaluate) |

**Example tree**

```
          A
        /   \
       B     C
      / \     \
     D   E     F
```

| Traversal | Output |
|---|---|
| Inorder | `D B E A C F` |
| Preorder | `A B D E C F` |
| Postorder | `D E B F C A` |

**Recursive algorithms**

```
INORDER(root):
1. if root = NULL: return
2. INORDER(root.left)
3. print root.data
4. INORDER(root.right)

PREORDER(root):
1. if root = NULL: return
2. print root.data
3. PREORDER(root.left)
4. PREORDER(root.right)

POSTORDER(root):
1. if root = NULL: return
2. POSTORDER(root.left)
3. POSTORDER(root.right)
4. print root.data
```

Time `O(n)` (each node visited once); space `O(h)` for the recursion stack (`O(n)` worst case for a skewed tree, `O(log n)` balanced).

**C functions**

```c
struct Node { int data; struct Node *left, *right; };

void inorder(struct Node *root) {
    if (root == NULL) return;
    inorder(root->left);
    printf("%d ", root->data);
    inorder(root->right);
}

void preorder(struct Node *root) {
    if (root == NULL) return;
    printf("%d ", root->data);
    preorder(root->left);
    preorder(root->right);
}

void postorder(struct Node *root) {
    if (root == NULL) return;
    postorder(root->left);
    postorder(root->right);
    printf("%d ", root->data);
}
```

**Trace on the example**

| Step | Inorder | Preorder | Postorder |
|---|---|---|---|
| visit A | left first | print A | left first |
| left B | left D | print B | left D |
| D | print D | print D | print D |
| E | print E | print E | print E |
| B | print B | — | print B |
| C | right C | print C | right C |
| F | print F | print F | print F |
| C | print C | — | print C |
| A | print A | — | print A |
| **Result** | `D B E A C F` | `A B D E C F` | `D E B F C A` |

**Iterative inorder (stack)** — useful when recursion depth is a concern:

```
INORDER_ITER(root):
1. st ← empty; curr ← root
2. while curr ≠ NULL or st not empty:
       while curr ≠ NULL: push curr; curr ← curr.left
       curr ← pop; print curr.data
       curr ← curr.right
```

**Preorder on a BST (CO-R26)** uses the same function; for a BST the output starts with the root and can be used to rebuild the tree together with the inorder sequence.

---

## 4.3 Construct a binary tree from traversals **[5–10]**

**Asked in:** AI-F24 Q1(1)(ii); CO-F24 Q3B(i); CO-R25 Q3B(i); CO-R26 Q3B(i).

**Questions (verbatim):**
> Build a Binary Search Tree for the given following Traversals: In order 35, 41, 48, 52, 57, 72, 79, 85, 86, 90; Preorder: 57, 41, 35, 52, 48, 90, 72, 85, 79, 86
>
> Construct a binary tree using following tree traversal. Postorder: 9 1 2 12 7 5 3 11 4 8; Inorder: 9 5 1 7 2 12 8 4 3 11
>
> Construct a binary tree using following tree traversal. Postorder: 1 7 6 5 3 10 9 13 12 15 14 11 8; Inorder: 1 3 5 6 7 8 9 10 11 12 13 14 15
>
> Construct a binary tree using following tree traversal. Preorder: FAEKCDHGB; Inorder: EACKFHDBG

**Answer**

**Key facts**

- **Preorder** gives the **root first**.
- **Postorder** gives the **root last**.
- **Inorder** splits the remaining nodes into the **left subtree** and **right subtree**.
- Recurse on the two parts.

```
BUILD(pre, in):
1. if pre empty: return NULL
2. root ← new Node(pre[0])
3. k ← position of pre[0] in in
4. root.left  ← BUILD(pre[1 … k], in[0 … k−1])
5. root.right ← BUILD(pre[k+1 …], in[k+1 …])
6. return root

BUILD_POST(post, in):
1. if post empty: return NULL
2. root ← new Node(post[last])
3. k ← position of post[last] in in
4. root.left  ← BUILD_POST(post[0 … k−1], in[0 … k−1])
5. root.right ← BUILD_POST(post[k … last−1], in[k+1 …])
6. return root
```

For a **BST**, the inorder sequence is sorted, so the preorder alone is enough; the same split rule applies.

### 4.3.1 AI-F24 — BST from inorder and preorder

Inorder: `35 41 48 52 57 72 79 85 86 90`  
Preorder: `57 41 35 52 48 90 72 85 79 86`

| Step | Root | Left inorder | Right inorder |
|---|---|---|---|
| 1 | **57** | 35 41 48 52 | 72 79 85 86 90 |
| 2 | **41** (left of 57) | 35 | 48 52 |
| 3 | **35** | — | — |
| 4 | **52** (right of 41) | 48 | — |
| 5 | **48** (left of 52) | — | — |
| 6 | **90** (right of 57) | 72 79 85 86 | — |
| 7 | **72** (left of 90) | — | 79 85 86 |
| 8 | **85** (right of 72) | 79 | 86 |
| 9 | **79** (left of 85) | — | — |
| 10 | **86** (right of 85) | — | — |

**Tree**

```
              57
            /    \
          41      90
         /  \    /
       35    52  72
            /      \
           48       85
                    /  \
                   79  86
```

Inorder check: `35 41 48 52 57 72 79 85 86 90` ✓.

### 4.3.2 CO-F24 — tree from postorder and inorder

Postorder: `9 1 2 12 7 5 3 11 4 8`  
Inorder: `9 5 1 7 2 12 8 4 3 11`

| Step | Root (last of post) | Left inorder | Right inorder |
|---|---|---|---|
| 1 | **8** | 9 5 1 7 2 12 | 4 3 11 |
| 2 | **5** (left of 8) | 9 | 1 7 2 12 |
| 3 | **9** (left of 5) | — | — |
| 4 | **7** (right of 5) | 1 | 2 12 |
| 5 | **1** (left of 7) | — | — |
| 6 | **12** (right of 7) | 2 | — |
| 7 | **2** (left of 12) | — | — |
| 8 | **4** (right of 8) | — | 3 11 |
| 9 | **11** (right of 4) | 3 | — |
| 10 | **3** (left of 11) | — | — |

**Tree**

```
              8
            /   \
           5     4
          / \     \
         9   7     11
            / \    /
           1  12  3
             /
            2
```

Postorder check: `9 1 2 12 7 5 3 11 4 8` ✓.

### 4.3.3 CO-R25 — tree from postorder and inorder

Postorder: `1 7 6 5 3 10 9 13 12 15 14 11 8`  
Inorder: `1 3 5 6 7 8 9 10 11 12 13 14 15`

| Step | Root | Left inorder | Right inorder |
|---|---|---|---|
| 1 | **8** | 1 3 5 6 7 | 9 10 11 12 13 14 15 |
| 2 | **3** (left of 8) | 1 | 5 6 7 |
| 3 | **1** | — | — |
| 4 | **5** (right of 3) | — | 6 7 |
| 5 | **6** (right of 5) | — | 7 |
| 6 | **7** (right of 6) | — | — |
| 7 | **11** (right of 8) | 9 10 | 12 13 14 15 |
| 8 | **9** (left of 11) | — | 10 |
| 9 | **10** (right of 9) | — | — |
| 10 | **14** (right of 11) | 12 13 | 15 |
| 11 | **12** (left of 14) | — | 13 |
| 12 | **13** (right of 12) | — | — |
| 13 | **15** (right of 14) | — | — |

**Tree**

```
                8
             /     \
           3        11
          / \      /   \
         1   5    9     14
              \    \    / \
              6    10  12  15
               \         \
                7         13
```

Postorder check: `1 7 6 5 3 10 9 13 12 15 14 11 8` ✓.

### 4.3.4 CO-R26 — tree from preorder and inorder

Preorder: `F A E K C D H G B`  
Inorder: `E A C K F H D B G`

| Step | Root | Left inorder | Right inorder |
|---|---|---|---|
| 1 | **F** | E A C K | H D B G |
| 2 | **A** (left of F) | E | C K |
| 3 | **E** (left of A) | — | — |
| 4 | **K** (right of A) | C | — |
| 5 | **C** (left of K) | — | — |
| 6 | **D** (right of F) | H | B G |
| 7 | **H** (left of D) | — | — |
| 8 | **G** (right of D) | B | — |
| 9 | **B** (left of G) | — | — |

**Tree**

```
              F
            /   \
           A     D
          / \   / \
         E   K H   G
            /     /
           C     B
```

Preorder check: `F A E K C D H G B` ✓.

---

## 4.4 Binary Search Tree (BST) operations **[8–10]**

**Asked in:** IT-F24 Q4A(i); CO-F24 Q1A(i); P8 Jan-24 Q2B(2); IT-F25 Q2A(i); AI-R26 Q2B(2); CO-R26 Q1A(i); IC-R26 Q3A(i); IT-R26 Q2A(i).

**Questions (verbatim):**
> Write a function to insert nodes and search a node in Binary Search Tree.
>
> Write a complete C program / Algorithm to Create Binary Search Tree and Delete a node from it. Give simple example.
>
> Write program/algorithm for finding minimum node and Maximum node in the given BST and apply the algorithm on given BST.
>
> Write down the following algorithm for Binary Search Tree (BST). a. Find Min b. Mirror of BST c. Given pointers to two nodes, find the lowest common ancestor (LCA).
>
> You are given the following Binary Search Tree: 55 … Perform the following operations in sequence and write pseudocode for them: 1. Insert 72 2. Delete 70. Draw the BST after each operation and explain the structural changes based on BST rules.
>
> Write a complete C program / Algorithm to perform Preorder Traversal on a Binary Search Tree. Give simple example.
>
> Illustrate insertion of a new element in a Binary Search Tree providing its algorithm / function. Provide an algorithm / function for finding minimum element from the same.
>
> Write a C program to implement Binary Search Tree (BST) operations: Insert, Search, Inorder, Preorder, Postorder traversals.

**Answer**

A **BST** is a binary tree satisfying, for every node `x`:

```
all keys in x.left  <  x.key  <  all keys in x.right
```

Consequences: inorder traversal is sorted; search follows one path; duplicates are usually rejected or kept on a fixed side.

**Node**

```c
struct Node {
    int data;
    struct Node *left, *right;
};
```

### 4.4.1 Insert

```
INSERT(root, x):
1. if root = NULL: return new Node(x)
2. if x < root.data: root.left ← INSERT(root.left, x)
3. else if x > root.data: root.right ← INSERT(root.right, x)
4. else: print "duplicate"           /* or place on a fixed side */
5. return root
```

Iterative:

```
INSERT_ITER(root, x):
1. new ← Node(x)
2. if root = NULL: return new
3. curr ← root
4. loop:
       if x < curr.data:
           if curr.left = NULL: curr.left ← new; return root
           curr ← curr.left
       else if x > curr.data:
           if curr.right = NULL: curr.right ← new; return root
           curr ← curr.right
       else: return root
```

Time `O(h)`; `h = O(log n)` balanced, `O(n)` skewed.

**Trace:** insert `50, 30, 70, 20, 40, 60, 80`

```
50
├─ 30
│  ├─ 20
│  └─ 40
└─ 70
   ├─ 60
   └─ 80
```

### 4.4.2 Search

```
SEARCH(root, key):
1. if root = NULL: return NULL
2. if key = root.data: return root
3. if key < root.data: return SEARCH(root.left, key)
4. return SEARCH(root.right, key)
```

Iterative: `while root != NULL and root.data != key: root = key < root.data ? root.left : root.right`.

Time `O(h)`, space `O(1)` iterative / `O(h)` recursive.

### 4.4.3 Delete

Three cases:

1. **Leaf** — remove it (return `NULL` to the parent).
2. **One child** — replace the node by its only child.
3. **Two children** — replace the node's key by its **inorder successor** (minimum of the right subtree), then delete that successor from the right subtree.

```
DELETE(root, key):
1. if root = NULL: return NULL
2. if key < root.data: root.left ← DELETE(root.left, key)
3. else if key > root.data: root.right ← DELETE(root.right, key)
4. else:
       if root.left = NULL: return root.right
       if root.right = NULL: return root.left
       succ ← MIN(root.right)
       root.data ← succ.data
       root.right ← DELETE(root.right, succ.data)
5. return root
```

**Trace:** delete `30` from

```
        50
       /  \
      30   70
     / \   / \
    20 40 60 80
```

`30` has two children → successor is `40` (minimum of right subtree `40`). Replace `30` by `40`; delete the old `40` leaf:

```
        50
       /  \
      40   70
     /    / \
    20   60 80
```

### 4.4.4 Minimum and maximum

```
MIN(root):
1. while root.left ≠ NULL: root ← root.left
2. return root

MAX(root):
1. while root.right ≠ NULL: root ← root.right
2. return root
```

Time `O(h)`; the minimum is the leftmost node, the maximum the rightmost.

**P8 Jan-24 application** — tree:

```
              8
           /     \
          3       10
         / \      / \
        1   6    9   13
           / \      /  \
          4   7    11   14
           \        \
            5        12
```

- **Minimum:** `8 → 3 → 1` → **1**.
- **Maximum:** `8 → 10 → 13 → 14` → **14**.

### 4.4.5 Mirror of a BST

```
MIRROR(root):
1. if root = NULL: return NULL
2. swap(root.left, root.right)
3. MIRROR(root.left)
4. MIRROR(root.right)
5. return root
```

The mirror of a BST is a BST with the order reversed: every left key > node > every right key. Inorder of the mirror is the reverse sorted order.

**Example**

```
     4                  4
   /   \              /   \
  2     6    →       6     2
 / \   / \          / \   / \
1   3 5   7        7   5 3   1
```

### 4.4.6 Lowest Common Ancestor (LCA) in a BST

For two existing keys `a` and `b`:

```
LCA(root, a, b):
1. while root ≠ NULL:
       if a < root.data and b < root.data: root ← root.left
       else if a > root.data and b > root.data: root ← root.right
       else: return root
2. return NULL
```

The first node whose key lies between `a` and `b` (inclusive) is the LCA. Time `O(h)`.

**Example:** tree `50(30(20,40),70(60,80))`, LCA(20,40) = 30; LCA(20,80) = 50; LCA(60,80) = 70.

### 4.4.7 Complete C program — BST create, insert, search, delete, traversals

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *left, *right;
};

struct Node *newNode(int x) {
    struct Node *n = malloc(sizeof(struct Node));
    n->data = x; n->left = n->right = NULL;
    return n;
}

struct Node *insert(struct Node *root, int x) {
    if (root == NULL) return newNode(x);
    if (x < root->data)      root->left  = insert(root->left, x);
    else if (x > root->data) root->right = insert(root->right, x);
    return root;                        /* duplicate ignored */
}

struct Node *search(struct Node *root, int key) {
    if (root == NULL || root->data == key) return root;
    if (key < root->data) return search(root->left, key);
    return search(root->right, key);
}

struct Node *minNode(struct Node *root) {
    while (root && root->left) root = root->left;
    return root;
}

struct Node *deleteNode(struct Node *root, int key) {
    if (root == NULL) return NULL;
    if (key < root->data)      root->left  = deleteNode(root->left, key);
    else if (key > root->data) root->right = deleteNode(root->right, key);
    else {
        if (root->left == NULL)  { struct Node *t = root->right; free(root); return t; }
        if (root->right == NULL) { struct Node *t = root->left;  free(root); return t; }
        struct Node *t = minNode(root->right);
        root->data = t->data;
        root->right = deleteNode(root->right, t->data);
    }
    return root;
}

void inorder(struct Node *r)  { if (r) { inorder(r->left);  printf("%d ", r->data); inorder(r->right); } }
void preorder(struct Node *r) { if (r) { printf("%d ", r->data); preorder(r->left); preorder(r->right); } }
void postorder(struct Node *r){ if (r) { postorder(r->left); postorder(r->right); printf("%d ", r->data); } }

int main(void) {
    struct Node *root = NULL;
    int keys[] = {50, 30, 70, 20, 40, 60, 80};
    for (int i = 0; i < 7; i++) root = insert(root, keys[i]);

    printf("Inorder: ");   inorder(root);   printf("\n");
    printf("Preorder: ");  preorder(root);  printf("\n");
    printf("Postorder: "); postorder(root); printf("\n");

    printf("Search 40: %s\n", search(root, 40) ? "found" : "not found");
    root = deleteNode(root, 30);
    printf("After deleting 30: "); inorder(root); printf("\n");
    return 0;
}
```

**Output**

```
Inorder: 20 30 40 50 60 70 80
Preorder: 50 30 20 40 70 60 80
Postorder: 20 40 30 60 80 70 50
Search 40: found
After deleting 30: 20 40 50 60 70 80
```

### 4.4.8 AI-R26 — insert 72, delete 70

**Initial BST**

```
            55
          /    \
        28      82
       /  \    /  \
      15  40  70  95
         /   /  \
        35  75  .
                (72 inserted next)
```

More readably, the given tree is:

```
            55
          /    \
        28      82
       /  \    /  \
      15  40  70  95
         /      \
        35      75
```

**Insert 72**

`72 < 55` → left; `72 > 28` → right; `72 > 40` → right; `72 < 82` → left; `72 > 70` → right; `72 < 75` → left of 75.

```
            55
          /    \
        28      82
       /  \    /  \
      15  40  70  95
         /      \
        35      75
                /
               72
```

**Delete 70**

`70` has only a right child `75` (no left child). Replace `70` by `75`; the subtree rooted at `75` (including its left child `72`) moves up.

```
            55
          /    \
        28      82
       /  \    /  \
      15  40  75  95
         /    /
        35   72
```

Inorder after both operations: `15 28 35 40 55 72 75 82 95` — still sorted, so BST property is preserved.

### 4.4.9 Preorder traversal function (CO-R26)

```c
void preorder(struct Node *root) {
    if (root == NULL) return;
    printf("%d ", root->data);   /* root */
    preorder(root->left);        /* left */
    preorder(root->right);       /* right */
}
```

For the tree `50(30(20,40),70(60,80))`, preorder prints `50 30 20 40 70 60 80`.

### 4.4.10 BST vs AVL tree

**Asked in:** AI-R26 Q1A(2).

| Point | BST | AVL tree |
|---|---|---|
| Balance | None guaranteed | Height difference ≤ 1 at every node |
| Height | `O(n)` worst (skewed) | `O(log n)` |
| Search/insert/delete | `O(h)`, worst `O(n)` | `O(log n)` |
| Rotations | None | LL, RR, LR, RL after insert/delete |
| Implementation | Simpler | Extra height field + rotations |
| Use | General ordered tree | Lookup-heavy, guaranteed performance |

---

## 4.5 AVL tree: balance factor, rotations, insertion, deletion **[10]**

**Asked in:** AI-F24 Q2(2); AI-R25 Q2(2); AI-R26 Q1A(2); CO-R26 Q2A(ii); CO-B26 Q2B(ii); IT-F25 Q2A(ii); IT-R25 Q4A(ii).

**Questions (verbatim):**
> Construct an AVL tree by inserting the following elements in the given order. Apply necessary rotations wherever required. 54, 12, 24, 68, 85, 99, 42, 27, 87, 80
>
> Define AVL Tree & Insert following nodes in sequence in AVL Tree: 10, 20, 30, 40, 50, 25, 5, 15, 35, 45
>
> What is the key difference between Binary Search Tree and AVL Tree? Create AVL tree from the following operations … Insert: 100,50,45,150,149,125,25,47,48,160 Delete: 125, 25, 50 … Traverse organized AVL tree using In-order traversal.
>
> What is height balanced tree? Construct an AVL tree with all rotations required for the following data: 52, 26, 12, 6, 7, 4, 32, 20, 18, 16
>
> Insert following sequence of keys into the AVL tree. Find out the type of rotation required in each case. A, Z, B, Y, C, X, D, U, E
>
> Discuss all rotations required to perform in AVL tree when the tree is imbalance with suitable example. Construct an AVL tree from the sequence: 12, 8, 18, 5, 11, 17, 4. Then delete 11 and 8. Apply insertion and deletion rotations and display each step clearly.
>
> What is balance factor in AVL tree? What are the rotations used in AVL tree to make it balance? Construct an AVL Tree by inserting the following elements: 44, 17, 32, 78, 50, 88, 48, 62, 54.

**Answer**

An **AVL tree** is a height-balanced BST. For every node:

```
Balance Factor (BF) = height(left subtree) − height(right subtree)
BF ∈ {−1, 0, +1}
```

If an insertion or deletion makes `|BF| = 2`, the tree is rebalanced with **rotations**. Height is `O(log n)`, so search/insert/delete are `O(log n)`.

**The four rotations**

| Case | Imbalance | Rotation |
|---|---|---|
| **LL** | Left-left (new key in left subtree of left child) | one **right** rotation |
| **RR** | Right-right | one **left** rotation |
| **LR** | Left-right | **left** on left child, then **right** on node |
| **RL** | Right-left | **right** on right child, then **left** on node |

**LL — right rotation at `z`**

```
      z                 y
     /                 / \
    y        →        x   z
   /
  x
```

**RR — left rotation at `z`**

```
    z                     y
     \                   / \
      y        →        z   x
       \
        x
```

**LR — left rotation at `y`, then right rotation at `z`**

```
      z                 z                 x
     /                 /                 / \
    y        →        x        →        y   z
     \               /
      x             y
```

**RL — right rotation at `y`, then left rotation at `z`**

```
    z                 z                     x
     \                 \                   / \
      y        →        x        →        z   y
     /                   \
    x                     y
```

**Insertion algorithm**

```
INSERT_AVL(node, key):
1. ordinary BST insert
2. update height(node)
3. bf ← height(left) − height(right)
4. if bf > 1 and key < node.left.key:  return RIGHT_ROTATE(node)
5. if bf > 1 and key > node.left.key:  node.left ← LEFT_ROTATE(node.left); return RIGHT_ROTATE(node)
6. if bf < −1 and key > node.right.key: return LEFT_ROTATE(node)
7. if bf < −1 and key < node.right.key: node.right ← RIGHT_ROTATE(node.right); return LEFT_ROTATE(node)
8. return node
```

**Deletion algorithm**

Delete as in a BST, then update heights and apply the same four cases using the child's balance factor:

```
if bf > 1 and bf(left) ≥ 0: LL → right rotate
if bf > 1 and bf(left) < 0: LR → left-right
if bf < −1 and bf(right) ≤ 0: RR → left rotate
if bf < −1 and bf(right) > 0: RL → right-left
```

**Notation used in the stage tables**

`k(L,R)` = root `k` with left subtree `L` and right subtree `R`; `.` = empty.  
Example: `50(30(20,40),70)` is

```
        50
       /  \
      30   70
     /  \
    20  40
```

### 4.5.1 AI-F24 — 54, 12, 24, 68, 85, 99, 42, 27, 87, 80

| Insert | Tree after insertion | Rotation |
|---|---|---|
| 54 | `54` | — |
| 12 | `54(12,.)` | — |
| 24 | `24(12,54)` | **LR at 54** |
| 68 | `24(12,54(.,68))` | — |
| 85 | `24(12,68(54,85))` | **RR at 54** (left) |
| 99 | `68(24(12,54),85(.,99))` | **RR at 24** (left) |
| 42 | `68(24(12,54(42,.)),85(.,99))` | — |
| 27 | `68(24(12,42(27,54)),85(.,99))` | **LL at 54** (right) |
| 87 | `68(24(12,42(27,54)),87(85,99))` | **RL at 85** |
| 80 | `68(24(12,42(27,54)),87(85(80,.),99))` | — |

**Final tree**

```
              68
            /    \
          24      87
         /  \    /  \
       12   42  85   99
            / \  /
           27 54 80
```

Inorder: `12 24 27 42 54 68 80 85 87 99` ✓.

### 4.5.2 AI-R25 — 10, 20, 30, 40, 50, 25, 5, 15, 35, 45

| Insert | Tree after insertion | Rotation |
|---|---|---|
| 10 | `10` | — |
| 20 | `10(.,20)` | — |
| 30 | `20(10,30)` | **RR at 10** (left) |
| 40 | `20(10,30(.,40))` | — |
| 50 | `20(10,40(30,50))` | **RR at 30** (left) |
| 25 | `30(20(10,25),40(.,50))` | **RL at 20** |
| 5 | `30(20(10(5,.),25),40(.,50))` | — |
| 15 | `30(20(10(5,15),25),40(.,50))` | — |
| 35 | `30(20(10(5,15),25),40(35,50))` | — |
| 45 | `30(20(10(5,15),25),40(35,50(45,.)))` | — |

**Final tree**

```
              30
            /    \
          20      40
         /  \    /  \
       10   25  35   50
       / \           /
      5  15         45
```

### 4.5.3 IT-R25 — 44, 17, 32, 78, 50, 88, 48, 62, 54

| Insert | Tree after insertion | Rotation |
|---|---|---|
| 44 | `44` | — |
| 17 | `44(17,.)` | — |
| 32 | `32(17,44)` | **LR at 44** |
| 78 | `32(17,44(.,78))` | — |
| 50 | `32(17,50(44,78))` | **RL at 44** |
| 88 | `50(32(17,44),78(.,88))` | **RR at 32** (left) |
| 48 | `50(32(17,44(.,48)),78(.,88))` | — |
| 62 | `50(32(17,44(.,48)),78(62,88))` | — |
| 54 | `50(32(17,44(.,48)),78(62(54,.),88))` | — |

**Final tree**

```
              50
            /    \
          32      78
         /  \    /  \
       17   44  62   88
              \  /
              48 54
```

### 4.5.4 CO-R26 — 52, 26, 12, 6, 7, 4, 32, 20, 18, 16

| Insert | Tree after insertion | Rotation |
|---|---|---|
| 52 | `52` | — |
| 26 | `52(26,.)` | — |
| 12 | `26(12,52)` | **LL at 52** (right) |
| 6 | `26(12(6,.),52)` | — |
| 7 | `26(7(6,12),52)` | **LR at 12** |
| 4 | `7(6(4,.),26(12,52))` | **LL at 26** (right) |
| 32 | `7(6(4,.),26(12,52(32,.)))` | — |
| 20 | `7(6(4,.),26(12(.,20),52(32,.)))` | — |
| 18 | `7(6(4,.),26(18(12,20),52(32,.)))` | **RL at 12** |
| 16 | `18(7(6(4,.),12(.,16)),26(20,52(32,.)))` | **RL at 7** |

**Final tree**

```
              18
            /    \
           7      26
          / \    /  \
         6  12  20   52
        /     \      /
       4      16    32
```

### 4.5.5 CO-B26 — A, Z, B, Y, C, X, D, U, E

Treat letters in alphabetical order (`A < B < … < Z`).

| Insert | Tree after insertion | Rotation |
|---|---|---|
| A | `A` | — |
| Z | `A(.,Z)` | — |
| B | `B(A,Z)` | **RL at A** |
| Y | `B(A,Z(Y,.))` | — |
| C | `B(A,Y(C,Z))` | **LL at Z** (right) |
| X | `C(B(A,.),Y(X,Z))` | **RL at B** |
| D | `C(B(A,.),Y(X(D,.),Z))` | — |
| U | `C(B(A,.),Y(U(D,X),Z))` | **LR at X** |
| E | `C(B(A,.),U(D(.,E),Y(X,Z)))` | **LL at Y** (right) |

**Final tree**

```
              C
            /   \
           B     U
          /     / \
         A     D   Y
                \ / \
                E X  Z
```

Inorder: `A B C D E U X Y Z` ✓.

### 4.5.6 IT-F25 — 12, 8, 18, 5, 11, 17, 4; delete 11, 8

| Operation | Tree after | Rotation |
|---|---|---|
| insert 12 | `12` | — |
| insert 8 | `12(8,.)` | — |
| insert 18 | `12(8,18)` | — |
| insert 5 | `12(8(5,.),18)` | — |
| insert 11 | `12(8(5,11),18)` | — |
| insert 17 | `12(8(5,11),18(17,.))` | — |
| insert 4 | `12(8(5(4,.),11),18(17,.))` | — |
| delete 11 | `12(5(4,8),18(17,.))` | **LL at 8** (right) |
| delete 8 | `12(5(4,.),18(17,.))` | — |

**Final tree**

```
            12
           /  \
          5    18
         /    /
        4    17
```

### 4.5.7 AI-R26 — 100, 50, 45, 150, 149, 125, 25, 47, 48, 160; delete 125, 25, 50

| Operation | Tree after | Rotation |
|---|---|---|
| insert 100 | `100` | — |
| insert 50 | `100(50,.)` | — |
| insert 45 | `50(45,100)` | **LL at 100** |
| insert 150 | `50(45,100(.,150))` | — |
| insert 149 | `50(45,149(100,150))` | **RL at 100** |
| insert 125 | `100(50(45,.),149(125,150))` | **RL at 50** |
| insert 25 | `100(45(25,50),149(125,150))` | **LL at 50** |
| insert 47 | `100(45(25,50(47,.)),149(125,150))` | — |
| insert 48 | `100(45(25,48(47,50)),149(125,150))` | **LR at 50** |
| insert 160 | `100(45(25,48(47,50)),149(125,150(.,160)))` | — |
| delete 125 | `100(45(25,48(47,50)),150(149,160))` | **RR at 149** |
| delete 25 | `100(48(45(.,47),50),150(149,160))` | **RR at 45** |
| delete 50 | `100(47(45,48),150(149,160))` | **LR at 48** |

**Final tree**

```
              100
            /     \
          47       150
         /  \     /   \
       45   48  149   160
```

**Inorder traversal:** `45 47 48 100 149 150 160`.

**Deletion details worth showing**

- Delete `125` (leaf): remove it; `149` becomes right-heavy (`BF = −2`) → **RR** at `149` → left rotate; `150` rises.
- Delete `25` (leaf): `45` becomes right-heavy → **RR at 45** → left rotate; `48` rises.
- Delete `50` (leaf): remove it; `48` becomes left-heavy (`BF = +2`) and its left child `45` is right-heavy (`BF = −1`), so the case is **LR at 48**: left-rotate `45`, then right-rotate `48`. The resulting tree above is correct.

---

## 4.6 Heap: min-heap, max-heap, build, insert, delete **[10]**

**Asked in:** IO-F24 Q3A(2); IT-F24 Q4A(ii); IT-R26 Q2A(ii); CO-B26 Q1A(ii).

**Questions (verbatim):**
> Write an algorithm for Min and Max Heap with the help of suitable example.
>
> Write down Build Heap algorithm. Construct a max heap for the following elements. 1, 5, 6, 8, 12, 14, 16. Show all internal steps.
>
> Write down the MAX_Heapify algorithm and use that algorithm for deleting the kth indexed element in a given Max-heap. Explain the deletion process with suitable example.
>
> Consider the following max heap: 50, 30, 20, 15, 10, 8, 16. 1) Delete a node with value 50. 2) After deleting 50, insert value 60. 3) After inserting 60, insert value 45. Show stepwise formation.

**Answer**

A **heap** is a **complete binary tree** stored in an array, with the heap-order property:

- **Max-heap:** every parent ≥ its children → root is the maximum.
- **Min-heap:** every parent ≤ its children → root is the minimum.

**Array indexing (1-based)**

```
parent(i) = ⌊i/2⌋
left(i)   = 2i
right(i)  = 2i + 1
```

For 0-based arrays: `parent(i) = (i−1)/2`, `left = 2i+1`, `right = 2i+2`.

**Example max-heap** `[50, 30, 20, 15, 10, 8, 16]`

```
            50
          /    \
        30      20
       /  \    /  \
      15  10  8   16
```

**Example min-heap** `[5, 10, 20, 15, 30, 25]`

```
            5
          /   \
        10     20
       /  \   /
      15  30 25
```

### 4.6.1 MAX_HEAPIFY

```
MAX_HEAPIFY(A, i, n):
1. l ← 2i; r ← 2i + 1; largest ← i
2. if l ≤ n and A[l] > A[largest]: largest ← l
3. if r ≤ n and A[r] > A[largest]: largest ← r
4. if largest ≠ i:
       swap(A[i], A[largest])
       MAX_HEAPIFY(A, largest, n)
```

Time `O(log n)` (height of the heap); space `O(log n)` recursive, `O(1)` iterative.

**MIN_HEAPIFY** is identical with `<` instead of `>`.

### 4.6.2 Build-heap

```
BUILD_MAX_HEAP(A, n):
1. for i ← ⌊n/2⌋ down to 1:
       MAX_HEAPIFY(A, i, n)
```

Start from the last internal node because leaves are already valid heaps. Time `O(n)` (tight bound; the naive `O(n log n)` is a safe upper bound).

**IT-F24 — build max-heap from `1, 5, 6, 8, 12, 14, 16`**

Array: `[1, 5, 6, 8, 12, 14, 16]`, `n = 7`; start at `i = 3`.

| Step | i | Compare | Swap | Array |
|---|---:|---|---|---|
| 1 | 3 | 6 with children 14, 16 | 6 ↔ 16 | `[1, 5, 16, 8, 12, 14, 6]` |
| 2 | 2 | 5 with children 8, 12 | 5 ↔ 12 | `[1, 12, 16, 8, 5, 14, 6]` |
| 3 | 1 | 1 with children 12, 16 | 1 ↔ 16 | `[16, 12, 1, 8, 5, 14, 6]` |
| 4 | 3 (after swap) | 1 with children 14, 6 | 1 ↔ 14 | `[16, 12, 14, 8, 5, 1, 6]` |

**Final max-heap:** `[16, 12, 14, 8, 5, 1, 6]`

```
            16
          /    \
        12      14
       /  \    /  \
      8   5   1   6
```

### 4.6.3 Insert

```
INSERT_MAX_HEAP(A, x):
1. n ← n + 1
2. A[n] ← x
3. i ← n
4. while i > 1 and A[i] > A[parent(i)]:
       swap(A[i], A[parent(i)])
       i ← parent(i)
```

Time `O(log n)`.

### 4.6.4 Delete the root (maximum)

```
DELETE_MAX(A):
1. max ← A[1]
2. A[1] ← A[n]
3. n ← n − 1
4. MAX_HEAPIFY(A, 1, n)
5. return max
```

### 4.6.5 Delete the k-th indexed element (IT-R26)

```
DELETE_KTH(A, k, n):
1. if k < 1 or k > n: error
2. removed ← A[k]
3. A[k] ← A[n]
4. n ← n − 1
5. if k ≤ n:
       if k > 1 and A[k] > A[parent(k)]: bubble-up(k)
       else: MAX_HEAPIFY(A, k, n)
6. return removed
```

After replacing with the last element, the node may violate the heap property in either direction, so check the parent first (bubble up) and otherwise heapify down.

**Example:** max-heap `[50, 30, 40, 10, 20, 35]`, delete the 3rd element (`40`).

```
Step 1: removed = 40
Step 2: move last 35 to index 3 → [50, 30, 35, 10, 20]
Step 3: parent 50 ≥ 35, children none (index 3 is a leaf) → valid
Result: [50, 30, 35, 10, 20]
```

### 4.6.6 CO-B26 — delete 50, insert 60, insert 45

Initial max-heap: `[50, 30, 20, 15, 10, 8, 16]`

```
            50
          /    \
        30      20
       /  \    /  \
      15  10  8   16
```

**1. Delete 50**

Replace root with last element `16`, shrink to 6 nodes:

```
[16, 30, 20, 15, 10, 8]
```

`MAX_HEAPIFY(1)`: children 30 and 20 → swap 16 with 30:

```
[30, 16, 20, 15, 10, 8]
```

At index 2, children 15 and 10; 16 > both → stop.

```
            30
          /    \
        16      20
       /  \    /
      15  10  8
```

**2. Insert 60**

Append 60 at index 7: `[30, 16, 20, 15, 10, 8, 60]`. Bubble up:

- 60 > parent 20 → swap → `[30, 16, 60, 15, 10, 8, 20]`
- 60 > parent 30 → swap → `[60, 16, 30, 15, 10, 8, 20]`

```
            60
          /    \
        16      30
       /  \    /  \
      15  10  8   20
```

**3. Insert 45**

Append at index 8: `[60, 16, 30, 15, 10, 8, 20, 45]`. Bubble up:

- 45 > parent 15 → swap → `[60, 16, 30, 45, 10, 8, 20, 15]`
- 45 > parent 16 → swap → `[60, 45, 30, 16, 10, 8, 20, 15]`
- 45 < parent 60 → stop.

```
            60
          /    \
        45      30
       /  \    /  \
      16  10  8   20
     /
    15
```

**Final array:** `[60, 45, 30, 16, 10, 8, 20, 15]`.

---

## 4.7 Huffman tree **[10]**

**Asked in:** CO-F24 Q2A(ii); CO-R25 Q2A(ii); IC-R26 Q3A(ii).

**Questions (verbatim):**
> What is Huffman tree? Construct the Huffman tree for the weights 22, 5, 11, 19, 2, 11, 25, 5, 15, 20
>
> Demonstrate step by step creation of the Huffman tree for the weights 10, 20, 30, 40, 50, 60, 70, 80, 90
>
> What are applications of Huffman tree? You are given the following characters and their frequencies: (A, 45), (B, 13), (C, 12), (D, 16), (E, 9), (F, 5). Construct the Huffman tree step by step and write the Huffman code for each character.

**Answer**

A **Huffman tree** is a binary tree that produces an **optimal prefix code** for a set of symbols with known frequencies. Symbols are leaves; the path from the root gives the code (left = 0, right = 1). No code is a prefix of another, so decoding is unambiguous. The **weighted path length** `Σ frequency × code length` is minimised.

**Algorithm**

```
HUFFMAN(S):
1. create a min-heap with one leaf per symbol, keyed by frequency
2. while more than one node remains:
       a ← extract-min
       b ← extract-min
       t ← new internal node with frequency a.freq + b.freq
       t.left ← a; t.right ← b
       insert t
3. return the remaining node (root)
```

Ties may produce different but equally optimal trees; state the tie rule (e.g., smaller weight first, then alphabetical).

### 4.7.1 CO-F24 — weights `22, 5, 11, 19, 2, 11, 25, 5, 15, 20`

Label the duplicate weights `5a, 5b` and `11a, 11b`.

**Merge sequence**

| Step | Two smallest | New node |
|---|---|---|
| 1 | 2, 5a | 7 |
| 2 | 5b, 7 | 12 |
| 3 | 11a, 11b | 22 |
| 4 | 15, 12 | 27 |
| 5 | 19, 20 | 39 |
| 6 | 22 (weight), 22 (11a+11b) | 44 |
| 7 | 25, 27 | 52 |
| 8 | 39, 44 | 83 |
| 9 | 52, 83 | **135** |

**Tree**

```
                         [135]
                       /       \
                   [52]          [83]
                  /   \         /    \
                25    [27]    [39]   [44]
                     /   \    /  \   /   \
                  [12]   15  19  20 22  [22]
                 /   \                  /   \
              5b      [7]            11a    11b
                     /   \
                    2    5a
```

**Codes**

| Weight | Code | Length |
|---|---|---:|
| 25 | 00 | 2 |
| 5b | 0100 | 4 |
| 2 | 01010 | 5 |
| 5a | 01011 | 5 |
| 15 | 011 | 3 |
| 19 | 100 | 3 |
| 20 | 101 | 3 |
| 22 | 110 | 3 |
| 11a | 1110 | 4 |
| 11b | 1111 | 4 |

**Weighted path length:** `25·2 + 5·4 + 2·5 + 5·5 + 15·3 + 19·3 + 20·3 + 22·3 + 11·4 + 11·4 = 50 + 20 + 10 + 25 + 45 + 57 + 60 + 66 + 44 + 44 = 421`.

### 4.7.2 CO-R25 — weights `10, 20, 30, 40, 50, 60, 70, 80, 90`

**Merge sequence**

| Step | Two smallest | New node |
|---|---|---|
| 1 | 10, 20 | 30 |
| 2 | 30 (original), 30 (new) | 60 |
| 3 | 40, 50 | 90 |
| 4 | 60 (original), 60 (new) | 120 |
| 5 | 70, 80 | 150 |
| 6 | 90 (original), 90 (new) | 180 |
| 7 | 120, 150 | 270 |
| 8 | 180, 270 | **450** |

**Tree**

```
                    [450]
                  /       \
             [180]         [270]
            /     \        /     \
          90      [90]   [120]   [150]
                 /  \    /   \   /   \
               40   50  60   [60] 70  80
                             /  \
                           30   [30]
                                /  \
                              10   20
```

Read it carefully: the left 90 is a leaf (original weight 90); the right 90 is the internal node `40 + 50`. Under `[120]`, the left 60 is the original leaf; the right `[60]` is the internal node `30 + 30`. Its left 30 is the original leaf; its right `[30]` is the internal node `10 + 20`.

**Explicit structure**

```
[450]
├─ [180]
│  ├─ 90              (original weight 90)
│  └─ [90]            (40 + 50)
│     ├─ 40
│     └─ 50
└─ [270]
   ├─ [120]
   │  ├─ 60           (original weight 60)
   │  └─ [60]         (30 + 30)
   │     ├─ 30        (original weight 30)
   │     └─ [30]      (10 + 20)
   │        ├─ 10
   │        └─ 20
   └─ [150]
      ├─ 70
      └─ 80
```

**Codes**

| Weight | Code | Length |
|---|---|---:|
| 90 | 00 | 2 |
| 40 | 010 | 3 |
| 50 | 011 | 3 |
| 60 | 100 | 3 |
| 30 | 1010 | 4 |
| 10 | 10110 | 5 |
| 20 | 10111 | 5 |
| 70 | 110 | 3 |
| 80 | 111 | 3 |

**Weighted path length:** `90·2 + 40·3 + 50·3 + 60·3 + 30·4 + 10·5 + 20·5 + 70·3 + 80·3 = 180 + 120 + 150 + 180 + 120 + 50 + 100 + 210 + 240 = 1350`.

### 4.7.3 IC-R26 — characters `(A,45), (B,13), (C,12), (D,16), (E,9), (F,5)`

**Merge sequence**

| Step | Two smallest | New node |
|---|---|---|
| 1 | F(5), E(9) | 14 |
| 2 | C(12), B(13) | 25 |
| 3 | 14, D(16) | 30 |
| 4 | 25, 30 | 55 |
| 5 | A(45), 55 | **100** |

**Tree**

```
                    [100]
                  /       \
                A(45)     [55]
                          /   \
                       [25]    [30]
                       / \     /   \
                     C12 B13 [14]  D16
                              /  \
                            F5   E9
```

**Codes**

| Character | Frequency | Code | Length |
|---|---:|---|---:|
| A | 45 | 0 | 1 |
| C | 12 | 100 | 3 |
| B | 13 | 101 | 3 |
| F | 5 | 1100 | 4 |
| E | 9 | 1101 | 4 |
| D | 16 | 111 | 3 |

**Weighted path length:** `45·1 + 12·3 + 13·3 + 16·3 + 9·4 + 5·4 = 45 + 36 + 39 + 48 + 36 + 20 = 224`.

**Applications:** file compression (ZIP, GZIP), image/video codecs, fax and text transmission, prefix coding, decision tables. Huffman coding is optimal among prefix codes when symbol frequencies are known.

---

## 4.8 B-tree **[5]**

**Asked in:** CO-F24 Q3B(ii).

**Question (verbatim):**
> Define B Tree. Construct a B tree of order 4 using following data. 3,12,7,32,8,1,5,15,4,9,13,17,6,21

**Answer**

A **B-tree of order `m`** is a balanced multi-way search tree:

1. Every node has at most `m` children (and `m−1` keys).
2. Every node except the root has at least `⌈m/2⌉` children.
3. The root has at least 2 children unless it is a leaf.
4. A node with `k` children has `k−1` keys, stored in sorted order.
5. All leaves are at the same level.

**Convention used here:** order `m = 4` means **maximum 4 children**, so a node holds at most **3 keys**. Before descending into a **full** node (3 keys), split it around its median: the left part keeps `⌈m/2⌉−1 = 1` key, the median moves up, the right part keeps 1 key; then insert the new key into the appropriate side. This is the standard pre-split B-tree insertion.

**Insertion sequence**

`3, 12, 7, 32, 8, 1, 5, 15, 4, 9, 13, 17, 6, 21`

| Insert | Action | Tree after |
|---|---|---|
| 3, 12, 7 | fill root | `[3 7 12]` |
| 32 | root full → split 7 up, then insert 32 | `[7]` with children `[3]`, `[12 32]` |
| 8 | into right leaf | `[7]`; `[3]`, `[8 12 32]` |
| 1 | into left leaf | `[7]`; `[1 3]`, `[8 12 32]` |
| 5 | into left leaf | `[7]`; `[1 3 5]`, `[8 12 32]` |
| 15 | right leaf full → split 12 up, then insert 15 | `[7 12]`; `[1 3 5]`, `[8]`, `[15 32]` |
| 4 | left leaf full → split 3 up, then insert 4 | `[3 7 12]`; `[1]`, `[4 5]`, `[8]`, `[15 32]` |
| 9 | root is full → pre-split around median 7 before descending | `[7]`; `[3]`, `[12]`; leaves `[1]`, `[4 5]`, `[8 9]`, `[15 32]` |
| 13 | into right leaf | `[7]`; `[3]`, `[12]`; `[1]`, `[4 5]`, `[8 9]`, `[13 15 32]` |
| 17 | right leaf full → split 15 up, then insert 17 | `[7]`; `[3]`, `[12 15]`; `[1]`, `[4 5]`, `[8 9]`, `[13]`, `[17 32]` |
| 6 | into `[4 5]` | `[7]`; `[3]`, `[12 15]`; `[1]`, `[4 5 6]`, `[8 9]`, `[13]`, `[17 32]` |
| 21 | into `[17 32]` | `[7]`; `[3]`, `[12 15]`; `[1]`, `[4 5 6]`, `[8 9]`, `[13]`, `[17 21 32]` |

**Final tree**

```
                    [7]
                  /     \
              [3]         [12 15]
             /   \       /   |    \
          [1]  [4 5 6] [8 9][13] [17 21 32]
```

All leaves are at the same level; every internal node has between 2 and 4 children; keys inside every node are sorted. B-trees are used in database and file-system indexes because a node can hold many keys, keeping the height small.

---

## Quick revision checklist — Unit 4

**Tree basics**

- [ ] Root, leaf, parent, child, sibling, degree, level, depth, height, subtree.
- [ ] Binary tree: at most two children.
- [ ] Full: 0 or 2 children; complete: filled left to right; perfect: all leaves same level.
- [ ] BST property: left < node < right.
- [ ] Applications: expression tree, BST, heap, Huffman, syntax tree, file system.

**Traversals**

- [ ] Inorder = left-root-right; sorted output for BST.
- [ ] Preorder = root-left-right.
- [ ] Postorder = left-right-root.
- [ ] Recursive code and `O(n)` time, `O(h)` space.
- [ ] Example `A(B(D,E),C(.,F))`: inorder `D B E A C F`, preorder `A B D E C F`, postorder `D E B F C A`.
- [ ] Iterative inorder with an explicit stack.
- [ ] Reconstruct from preorder+inorder or postorder+inorder.

**BST**

- [ ] Insert/search/delete `O(h)`.
- [ ] Delete leaf, one child, two children (inorder successor).
- [ ] Min = leftmost; max = rightmost.
- [ ] Mirror swaps left/right at every node.
- [ ] LCA: first node between the two keys.
- [ ] Balanced vs skewed height.
- [ ] BST vs AVL: balance, worst-case `O(n)` vs `O(log n)`, rotations.

**AVL**

- [ ] `BF = height(left) − height(right)`; valid `−1, 0, +1`.
- [ ] LL → right rotation; RR → left; LR → left-right; RL → right-left.
- [ ] Insert and delete both rebalance on the path back to the root.
- [ ] AI-F24 final root 68.
- [ ] AI-R25 final root 30.
- [ ] IT-R25 final root 50.
- [ ] CO-R26 final root 18.
- [ ] CO-B26 final root C.
- [ ] AI-R26 after deletions: `100(47(45,48),150(149,160))`.
- [ ] AVL height `O(log n)`; operations `O(log n)`.

**Heap**

- [ ] Max-heap parent ≥ children; min-heap parent ≤ children.
- [ ] Complete tree in an array; parent/left/right formulas.
- [ ] `MAX_HEAPIFY` `O(log n)`.
- [ ] Build-heap from `⌊n/2⌋` down to 1, `O(n)`.
- [ ] Insert: append + bubble up.
- [ ] Delete root: last to root + heapify down.
- [ ] Build `[1,5,6,8,12,14,16]` → `[16,12,14,8,5,1,6]`.
- [ ] Heap sort: build max-heap, swap root with last, shrink, heapify.

**Huffman**

- [ ] Repeatedly combine the two smallest frequencies.
- [ ] Left = 0, right = 1; symbols only at leaves.
- [ ] Prefix-free; optimal weighted path length.
- [ ] CO-F24 WPL = 421.
- [ ] CO-R25 WPL = 1350.
- [ ] IC-R26 codes: A=0, B=101, C=100, D=111, E=1101, F=1100; WPL = 224.

**B-tree**

- [ ] Order `m` = max children; max keys `m−1`.
- [ ] All leaves same level; nodes at least `⌈m/2⌉` children.
- [ ] Overflow: split around median, promote median.
- [ ] Order 4 example final root `[7]`.
