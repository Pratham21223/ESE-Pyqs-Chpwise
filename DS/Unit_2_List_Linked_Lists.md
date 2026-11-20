# Unit 2 — Linear Data Structures: List

> **Syllabus (DJS23IPC201, 7 hrs):** List as an ADT, array-based implementation, linked-list implementation, singly linked lists, circular linked lists, doubly linked lists, all operations (insertion, deletion, merge, traversal, etc.) and their analysis, applications of linked lists — polynomial addition.
>
> **Past-paper coverage:** List ADT and applications; array vs linked representation; insertion/deletion/search/traversal in singly, circular and doubly linked lists; reverse; remove duplicates; merge; split; middle node; loop detection; median; polynomial arithmetic.

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

## 2.1 List as an ADT and applications of linked lists **[10]**

**Asked in:** AI-R25 Q1(1).

**Question (verbatim):**
> What is a List as an Abstract Data Type (ADT)? Discuss the various applications of linked lists in real-world scenarios. Explain how linked lists are used in different domains with appropriate examples.

**Answer**

**List ADT.** A list is an ordered collection of elements `L = (a₁, a₂, …, aₙ)` where each element has a position. The ADT specifies the operations, not the storage:

| Operation | Meaning |
|---|---|
| `insert(L, i, x)` | insert `x` at position `i` (or after a given element) |
| `delete(L, i)` | remove the element at position `i` |
| `search(L, x)` | return the position of `x`, or “not found” |
| `get(L, i)` / `set(L, i, x)` | read/write the element at position `i` |
| `length(L)` | number of elements |
| `traverse(L)` | visit every element once |

**Properties:** ordered (a first element and a last element), variable length, position-based access, duplicates allowed unless stated otherwise. The list can be **empty**.

**Implementations**

| Implementation | Access | Insert/delete at front | Insert/delete at position | Memory |
|---|---|---|---|---|
| Array | `O(1)` by index | `O(n)` (shift) | `O(n)` (shift) | contiguous, fixed/amortized |
| Singly linked list | `O(n)` | `O(1)` | `O(n)` search + `O(1)` relink | nodes scattered, extra pointer per node |
| Doubly linked list | `O(n)` | `O(1)` | `O(n)` search + `O(1)` relink | two pointers per node |

**Applications of linked lists**

1. **Dynamic memory management:** the OS keeps free/allocated memory blocks in linked lists; `malloc`/`free` remove and add blocks.
2. **Stacks and queues:** a singly linked list implements a stack (push/pop at head); a doubly linked list implements a deque/queue with `O(1)` operations at both ends.
3. **Undo/redo in editors:** each action is a node in a doubly linked list; `prev`/`next` move through history.
4. **Browser back/forward:** visited pages form a doubly linked list; one pointer moves back, one moves forward.
5. **Adjacency list of a graph:** every vertex stores a linked list of its neighbours; memory is `O(V + E)` instead of `O(V²)`.
6. **Round-robin scheduling:** processes form a circular linked list; the scheduler advances `current = current->next` and wraps around.
7. **Polynomial arithmetic:** each term `(coefficient, exponent)` is a node; addition/subtraction merge terms with equal exponents.
8. **Music/media playlists:** songs form a doubly or circular list; next/previous/shuffle are pointer operations.
9. **Hash tables with chaining:** each bucket is a linked list of collided keys.
10. **Sparse matrices:** only non-zero entries are stored as linked nodes, saving memory.

**Why a linked list is chosen:** unknown/variable size, frequent insert/delete in the middle, no need for random access, and no large contiguous block of memory.

---

## 2.1A Is a linked list linear, non-linear, or both? **[5]**

**Asked in:** CO-B26 Q3B(1).

**Question (verbatim):**
> Linked list data structure is Linear or Non-Linear or Both? Justify your answer.

**Answer**

A linked list is a **linear data structure**.

**Justification**

1. **Logical arrangement:** every node (except the first and last) has exactly **one predecessor** and **one successor**. This is the defining property of a linear structure.
2. **Traversal:** it is visited in a single sequence — `head → next → next → … → NULL` (or back to head in a circular list). There is no branching.
3. **Operations:** insert/delete/search act on one position or one key at a time; there is no parent/child or neighbour set.
4. **Comparison:** a tree or graph is non-linear because a node can have multiple successors/predecessors and traversal branches.

**Why the confusion arises:** the nodes are **physically non-contiguous** in memory (each node can be anywhere in the heap), and each node stores a pointer. That is a **memory-allocation** property, not a logical-shape property. "Linear" refers to the one-dimensional logical order, so the linked list remains linear.

**Verdict:** linear logically; non-contiguous physically. It is **not** both, and it is **not** non-linear.

---

## 2.2 Array vs linked representation of a list **[5]**

**Asked in:** AI-R25 Q1(1) (implementation half); IT-R26 Q1B (classification).

**Question (verbatim):**
> What is a List as an Abstract Data Type (ADT)? … Explain how linked lists are used in different domains with appropriate examples.

**Answer**

| Point | Array-based list | Linked-list |
|---|---|---|
| Memory | Contiguous block | Nodes anywhere in heap |
| Size | Fixed at creation (or resized/copied) | Grows/shrinks one node at a time |
| Access by index | `O(1)` — `a[i]` | `O(n)` — walk from head |
| Insert/delete at beginning | `O(n)` — shift elements | `O(1)` — relink |
| Insert/delete at end | `O(1)` amortized | `O(n)` for singly, `O(1)` with tail pointer |
| Insert/delete after a known node | `O(n)` shift | `O(1)` relink |
| Search | `O(n)` unsorted, `O(log n)` sorted + binary search | `O(n)` |
| Extra memory | No per-element pointer | One/two pointers per node |
| Cache locality | Good | Poor |
| Random access | Yes | No |
| Overflow | Possible (fixed capacity) | Only when heap is exhausted |

**Rule of thumb:** arrays for frequent index access and known size; linked lists for frequent insert/delete and unpredictable size.

---

## 2.2A Traversal of singly, doubly and circular linked lists **[10]**

**Asked in:** AI-R25 Q4(1) (traversal alternative).

**Question (verbatim):**
> Describe how traversal works in different types of linked lists (Singly Linked List, Doubly Linked List, and Circular Linked List). Provide the algorithm for traversal in each type.

**Answer**

**Singly linked list**

Start at `head`, follow `next` until `NULL`. Only forward traversal is possible.

```
TRAVERSE_SLL(head):
1. temp ← head
2. while temp ≠ NULL:
       visit(temp)
       temp ← temp.next
```

**Doubly linked list**

`next` gives forward traversal (head → tail); `prev` gives backward traversal (tail → head).

```
TRAVERSE_DLL_FORWARD(head):
1. temp ← head
2. while temp ≠ NULL:
       visit(temp)
       temp ← temp.next

TRAVERSE_DLL_BACKWARD(head):
1. if head = NULL: return
2. temp ← head
3. while temp.next ≠ NULL: temp ← temp.next    /* go to tail */
4. while temp ≠ NULL:
       visit(temp)
       temp ← temp.prev
```

**Circular singly linked list**

The last node points to `head`, so a `while temp != NULL` loop would never end. Use `do…while` and stop when the pointer returns to `head`.

```
TRAVERSE_CLL(head):
1. if head = NULL: return
2. temp ← head
3. do:
       visit(temp)
       temp ← temp.next
   while temp ≠ head
```

**Circular doubly linked list** — same idea in both directions; stop when the pointer returns to the starting node.

```
TRAVERSE_CDLL_FORWARD(head):
1. if head = NULL: return
2. temp ← head
3. do:
       visit(temp)
       temp ← temp.next
   while temp ≠ head
```

**Comparison**

| List | Direction | Stop condition | Risk |
|---|---|---|---|
| Singly | forward only | `temp == NULL` | none |
| Doubly | forward and backward | `NULL` at each end | update both pointers on modify |
| Circular singly | forward (loops) | `temp == head` | infinite loop if condition is wrong |
| Circular doubly | both (loops) | back to start | infinite loop / broken circle |

**Complexity:** `O(n)` time and `O(1)` extra space for all four; a doubly list can be traversed twice (forward and backward) in `O(n)` each.

---

## 2.3 Singly linked list: insertion operations **[8–10]**

**Asked in:** IT-F24 Q1(a)(i) (insert after key); IT-R25 Q1(a)(i) (insert after key); AI-R26 Q2B(1) (beginning, specific position, end); IT-F25 Q1A(i) (insert beginning); IT-R26 Q1A(i) (insert end).

**Questions (verbatim):**
> Write a program/algorithm to perform following operation in singly linked list: 1. Insertion of an element x after key 2. Deletion in the beginning 3. Find the minimum element 4. Count the number of elements in linked list.
>
> Write a program/algorithm to perform following operation in Singly Linked List. 1. Insertion of an element x after key 2. Deletion of last element 3. Find the largest element in the Linked List 4. Find sum of all the elements in the Linked List.
>
> Write the algorithm/pseudocode for inserting a new node at the beginning, specific position, and end of a singly linked list with diagrams.

**Answer**

**Node definition**

```c
struct Node {
    int data;
    struct Node *next;
};
```

**Diagram — insertion at beginning**

```
Before:  head → [10|•] → [20|•] → [30|NULL]

Step 1:  newNode = [5|•]
Step 2:  newNode->next = head
Step 3:  head = newNode

After:   head → [5|•] → [10|•] → [20|•] → [30|NULL]
```

**Algorithm — insert at beginning**

```
INSERT_BEGIN(head, x):
1. new ← allocate Node
2. new.data ← x
3. new.next ← head
4. head ← new
5. return head
```

Time `O(1)`, space `O(1)`.

**Diagram — insertion at end**

```
Before: head → [10|•] → [20|•] → [30|NULL]

new = [40|NULL]
walk temp to 30 (temp->next == NULL)
temp->next = new

After:  head → [10|•] → [20|•] → [30|•] → [40|NULL]
```

**Algorithm — insert at end**

```
INSERT_END(head, x):
1. new ← allocate Node; new.data ← x; new.next ← NULL
2. if head = NULL: return new
3. temp ← head
4. while temp.next ≠ NULL: temp ← temp.next
5. temp.next ← new
6. return head
```

Time `O(n)` (or `O(1)` if a `tail` pointer is maintained).

**Diagram — insert after a given key**

```
List: head → [10|•] → [20|•] → [30|NULL], key = 20, x = 25

find 20; new = [25|•]
new->next = 20->next      (25 → 30)
20->next  = new           (20 → 25)

After: head → [10|•] → [20|•] → [25|•] → [30|NULL]
```

**Algorithm — insert after key**

```
INSERT_AFTER_KEY(head, key, x):
1. temp ← head
2. while temp ≠ NULL and temp.data ≠ key: temp ← temp.next
3. if temp = NULL: print "key not found"; return head
4. new ← allocate Node; new.data ← x
5. new.next ← temp.next
6. temp.next ← new
7. return head
```

Time `O(n)` to find the key, `O(1)` to relink.

**Diagram — insert at a specific position `pos` (1-based)**

```
List: head → [10|•] → [20|•] → [30|NULL], pos = 2, x = 15

walk to node at pos−1 = 1 (data 10)
new->next = 10->next
10->next  = new

After: head → [10|•] → [15|•] → [20|•] → [30|NULL]
```

**Algorithm — insert at position**

```
INSERT_POS(head, pos, x):
1. if pos < 1: error
2. if pos = 1: return INSERT_BEGIN(head, x)
3. temp ← head
4. for i ← 1 to pos−2:
       if temp = NULL: error "position out of range"
       temp ← temp.next
5. if temp = NULL: error
6. new ← allocate Node; new.data ← x
7. new.next ← temp.next
8. temp.next ← new
9. return head
```

**Edge cases:** empty list (`head == NULL`), `pos == 1`, `pos == length+1` (append), `pos > length+1` (reject). Always initialise `new->next` before linking.

---

## 2.4 Singly linked list: deletion operations **[8–10]**

**Asked in:** IT-F24 Q1(a)(i) (delete beginning); IT-R25 Q1(a)(i) (delete last); IT-F25 Q1A(i) (delete last); CO-R26 Q1A(ii) (delete beginning — C program).

**Questions (verbatim):**
> … 2. Deletion in the beginning …
>
> … 2. Deletion of last element …
>
> Write a program/algorithm to perform following operations in singly linked list: - Find length of a loop - Delete last element from linked list - Insert element x in the beginning of linked list
>
> Write a complete C program / Algorithm to DELETE a node from the BEGINNING of a Singly Linked List. Give a simple example.

**Answer**

**Delete at beginning**

```
DELETE_BEGIN(head):
1. if head = NULL: print "underflow"; return NULL
2. temp ← head
3. head ← head.next
4. free(temp)
5. return head
```

Time `O(1)`, space `O(1)`.

**Diagram**

```
Before: head → [10|•] → [20|•] → [30|NULL]
Step 1: temp = 10
Step 2: head = 20
Step 3: free(10)

After:  head → [20|•] → [30|NULL]
```

**Complete C program — delete from beginning**

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *next;
};

struct Node *deleteBegin(struct Node *head)
{
    if (head == NULL) {
        printf("List is empty (underflow)\n");
        return NULL;
    }
    struct Node *temp = head;   /* node to delete */
    head = head->next;          /* move head to second node */
    free(temp);                 /* release memory */
    return head;
}

void display(struct Node *head)
{
    while (head != NULL) {
        printf("%d -> ", head->data);
        head = head->next;
    }
    printf("NULL\n");
}

int main(void)
{
    /* create 10 -> 20 -> 30 */
    struct Node *head = malloc(sizeof(struct Node));
    head->data = 10;
    head->next = malloc(sizeof(struct Node));
    head->next->data = 20;
    head->next->next = malloc(sizeof(struct Node));
    head->next->next->data = 30;
    head->next->next->next = NULL;

    printf("Before: "); display(head);
    head = deleteBegin(head);
    printf("After : "); display(head);
    return 0;
}
```

Output:

```
Before: 10 -> 20 -> 30 -> NULL
After : 20 -> 30 -> NULL
```

**Delete last node**

```
DELETE_LAST(head):
1. if head = NULL: print "underflow"; return NULL
2. if head.next = NULL: free(head); return NULL
3. prev ← NULL; temp ← head
4. while temp.next ≠ NULL:
       prev ← temp
       temp ← temp.next
5. prev.next ← NULL
6. free(temp)
7. return head
```

Time `O(n)`, space `O(1)`. **Edge cases:** empty list and single-node list.

**Delete a node with a given key**

```
DELETE_KEY(head, key):
1. if head = NULL: return NULL
2. if head.data = key:
       temp ← head; head ← head.next; free(temp); return head
3. prev ← head; curr ← head.next
4. while curr ≠ NULL and curr.data ≠ key:
       prev ← curr; curr ← curr.next
5. if curr = NULL: print "not found"; return head
6. prev.next ← curr.next
7. free(curr)
8. return head
```

**Delete all occurrences of a key**

```
DELETE_ALL(head, key):
1. while head ≠ NULL and head.data = key:
       temp ← head; head ← head.next; free(temp)
2. curr ← head
3. while curr ≠ NULL and curr.next ≠ NULL:
       if curr.next.data = key:
           temp ← curr.next
           curr.next ← temp.next
           free(temp)
       else:
           curr ← curr.next
4. return head
```

Time `O(n)`, space `O(1)`.

---

## 2.5 Singly linked list: search, count, minimum, maximum, sum **[10]**

**Asked in:** IT-F24 Q1(a)(i) (minimum, count); IT-R25 Q1(a)(i) (largest, sum); IT-R26 Q1A(i) (display odd elements).

**Questions (verbatim):**
> … 3. Find the minimum element 4. Count the number of elements in linked list.
>
> … 3. Find the largest element in the Linked List. 4. Find sum of all the elements in the Linked List.
>
> Write a program/algorithm to perform following operations in singly linked list: - Delete all occurrences of duplicate keys in sorted linked list - Insert element x in the end of linked list - Display only odd elements in the linked list

**Answer**

All of these are one traversal, `O(n)` time and `O(1)` extra space.

**Search**

```
SEARCH(head, key):
1. pos ← 1; temp ← head
2. while temp ≠ NULL:
       if temp.data = key: return pos
       temp ← temp.next; pos ← pos + 1
3. return −1
```

**Count**

```
COUNT(head):
1. count ← 0; temp ← head
2. while temp ≠ NULL: count ← count + 1; temp ← temp.next
3. return count
```

**Minimum**

```
MINIMUM(head):
1. if head = NULL: return "empty"
2. min ← head.data; temp ← head.next
3. while temp ≠ NULL:
       if temp.data < min: min ← temp.data
       temp ← temp.next
4. return min
```

**Maximum** — same with `>` and `max`.

**Sum**

```
SUM(head):
1. sum ← 0; temp ← head
2. while temp ≠ NULL: sum ← sum + temp.data; temp ← temp.next
3. return sum
```

**Display only odd elements**

```
DISPLAY_ODD(head):
1. temp ← head
2. while temp ≠ NULL:
       if temp.data mod 2 ≠ 0: print temp.data
       temp ← temp.next
```

**Trace** on `head → 7 → 12 → 5 → 20 → 9`:

| Operation | Result |
|---|---|
| count | 5 |
| sum | 53 |
| minimum | 5 |
| maximum | 20 |
| odd elements | 7, 5, 9 |

---

## 2.6 Find the length of a loop in a singly linked list **[10]**

**Asked in:** IT-F25 Q1A(i).

**Question (verbatim):**
> Write a program/algorithm to perform following operations in singly linked list: - Find length of a loop - Delete last element from linked list - Insert element x in the beginning of linked list

**Answer**

A loop exists when some node's `next` points back to an earlier node. **Floyd's cycle detection** uses two pointers:

1. `slow` moves one node at a time, `fast` moves two.
2. If they meet, a loop exists.
3. Keep one pointer at the meeting point and move the other around the loop until it returns; count the steps.

```
LOOP_LENGTH(head):
1. slow ← head; fast ← head
2. while fast ≠ NULL and fast.next ≠ NULL:
       slow ← slow.next
       fast ← fast.next.next
       if slow = fast:                     /* meeting point */
           count ← 1
           temp ← slow.next
           while temp ≠ slow:
               count ← count + 1
               temp ← temp.next
           return count
3. return 0                                 /* no loop */
```

**Trace:** `1 → 2 → 3 → 4 → 5 → 3` (5 points back to 3).

- slow/fast meet at node 4.
- Starting from 4: `4 → 5 → 3 → 4` takes 3 steps.
- Loop length = **3** (nodes 3, 4, 5).

Time `O(n)`, space `O(1)`. To find the **start** of the loop: after meeting, reset `slow = head`; move both one step at a time; the meeting point is the loop start.

---

## 2.7 Reverse a singly linked list **[10]**

**Asked in:** CO-F24 Q3A; P8 Jan-24 Q1A(1)(b).

**Questions (verbatim):**
> Write a program / algorithm to a Reverse a Singly Linked List.
>
> Write algorithm/functions in C for below operations on singly linked list. … b) to reverse a singly linked list

**Answer**

**Iterative three-pointer method**

```
REVERSE(head):
1. prev ← NULL
2. curr ← head
3. while curr ≠ NULL:
       next ← curr.next     /* save the rest */
       curr.next ← prev     /* reverse the link */
       prev ← curr          /* advance prev */
       curr ← next          /* advance curr */
4. return prev              /* new head */
```

**Trace** on `1 → 2 → 3 → NULL`

| Step | prev | curr | next | List so far |
|---|---|---|---|---|
| start | NULL | 1 | 2 | 1→2→3 |
| 1 | 1 | 2 | 3 | 1→NULL |
| 2 | 2 | 3 | NULL | 2→1→NULL |
| 3 | 3 | NULL | — | 3→2→1→NULL |

Result: `3 → 2 → 1 → NULL`. Time `O(n)`, space `O(1)`.

**Recursive version**

```
REVERSE_REC(curr):
1. if curr = NULL or curr.next = NULL: return curr
2. newHead ← REVERSE_REC(curr.next)
3. curr.next.next ← curr
4. curr.next ← NULL
5. return newHead
```

Time `O(n)`, space `O(n)` recursion stack.

**C function**

```c
struct Node *reverse(struct Node *head)
{
    struct Node *prev = NULL, *curr = head, *next;
    while (curr != NULL) {
        next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}
```

---

## 2.8 Remove duplicate nodes from a linked list **[8–10]**

**Asked in:** IO-F24 Q2B(1); P8 Jan-24 Q1A(1)(a); IT-R26 Q1A(i).

**Questions (verbatim):**
> Develop an algorithm to remove repetitive node in singly linked list.
>
> Write algorithm/functions in C for below operations on singly linked list. a) to remove duplicates from an unsorted linked list
>
> Write a program/algorithm to perform following operations in singly linked list: - Delete all occurrences of duplicate keys in sorted linked list …

**Answer**

**Case 1 — sorted list, `O(n)`**

In a sorted list, duplicates are adjacent. Keep one node and delete the next while its data equals the current data.

```
REMOVE_DUP_SORTED(head):
1. curr ← head
2. while curr ≠ NULL and curr.next ≠ NULL:
       if curr.data = curr.next.data:
           temp ← curr.next
           curr.next ← temp.next
           free(temp)
       else:
           curr ← curr.next
3. return head
```

**Trace:** `1 → 1 → 2 → 3 → 3 → 3 → 4` becomes `1 → 2 → 3 → 4`.

**Case 2 — unsorted list, `O(n²)` without extra memory**

For every node `curr`, scan the rest and delete any node with the same data.

```
REMOVE_DUP_UNSORTED(head):
1. curr ← head
2. while curr ≠ NULL:
       prev ← curr; temp ← curr.next
       while temp ≠ NULL:
           if temp.data = curr.data:
               prev.next ← temp.next
               free(temp)
               temp ← prev.next
           else:
               prev ← temp
               temp ← temp.next
       curr ← curr.next
3. return head
```

**Trace:** `10 → 20 → 10 → 30 → 20` becomes `10 → 20 → 30`.

**Case 3 — unsorted list with a hash table, average `O(n)`**

Store each seen value in a hash set; if `temp.data` is already present, unlink it. Extra space `O(n)`.

**Complexity comparison**

| Method | Time | Extra space |
|---|---|---|
| Sorted list, adjacent deletion | `O(n)` | `O(1)` |
| Unsorted, nested scan | `O(n²)` | `O(1)` |
| Unsorted, hash set | `O(n)` average | `O(n)` |

---

## 2.9 Merge two sorted singly linked lists **[8–10]**

**Asked in:** IC-R26 Q2b(1); CO-R26 Q3A.

**Questions (verbatim):**
> Write an algorithm/function to merge two sorted singly linked lists (in ascending order) into a single linked list. Illustrate your answer with a neat diagram.
>
> Write a program / algorithm merge two Sorted Singly Linked Lists so that resultant linked list is also sorted.

**Answer**

Use a **dummy head** to avoid special-casing the first node, then splice the smaller node each time.

```
MERGE(A, B):
1. dummy ← new Node; tail ← dummy
2. while A ≠ NULL and B ≠ NULL:
       if A.data ≤ B.data:
           tail.next ← A; A ← A.next
       else:
           tail.next ← B; B ← B.next
       tail ← tail.next
3. if A ≠ NULL: tail.next ← A
4. else: tail.next ← B
5. return dummy.next
```

**Diagram**

```
A: 1 → 4 → 7 → NULL
B: 2 → 3 → 8 → NULL

Compare 1,2 → take 1
Compare 4,2 → take 2
Compare 4,3 → take 3
Compare 4,8 → take 4
Compare 7,8 → take 7
A empty     → append 8

Result: 1 → 2 → 3 → 4 → 7 → 8 → NULL
```

**Trace table**

| Step | A pointer | B pointer | Chosen | Result so far |
|---|---|---|---|---|
| 1 | 1 | 2 | 1 | 1 |
| 2 | 4 | 2 | 2 | 1→2 |
| 3 | 4 | 3 | 3 | 1→2→3 |
| 4 | 4 | 8 | 4 | 1→2→3→4 |
| 5 | 7 | 8 | 7 | 1→2→3→4→7 |
| 6 | NULL | 8 | 8 | 1→2→3→4→7→8 |

Time `O(m+n)`, space `O(1)` (nodes are relinked, not copied). The result is stable if ties take from `A` first.

---

## 2.10 Split a linked list into two halves; find the middle **[10 / 5]**

**Asked in:** P8 Jan-24 Q4A(2); CO-B26 Q4A(2).

**Questions (verbatim):**
> Write an algorithm/function in C to split a given linked list into two halves. If the length of the linked list is odd, make the first half one node longer than the second.
>
> Write a program/algorithm to - i. find the middle of the linked list. ii. check whether the given linked list length is even or odd?

**Answer**

**Slow/fast pointer rule**

- `slow` moves one node, `fast` moves two.
- When `fast` reaches the end, `slow` is at the middle.
- For an **odd** length `2k+1`, `slow` lands on the `(k+1)`-th node — the extra node belongs to the first half.
- For an **even** length `2k`, `slow` lands on the `k`-th node (first middle) if the loop stops at `fast->next == NULL`, or on the `(k+1)`-th if it stops at `fast == NULL`. State the convention.

**Split algorithm (first half longer for odd)**

```
SPLIT(head):
1. if head = NULL: return (NULL, NULL)
2. slow ← head; fast ← head
3. while fast.next ≠ NULL and fast.next.next ≠ NULL:
       slow ← slow.next
       fast ← fast.next.next
4. second ← slow.next
5. slow.next ← NULL
6. return (head, second)
```

**Trace — odd `1 → 2 → 3 → 4 → 5`**

| Iteration | slow | fast |
|---|---|---|
| start | 1 | 1 |
| 1 | 2 | 3 |
| 2 | 3 | 5 |

`fast.next == NULL`, stop. `second = 4`; first half `1→2→3`, second `4→5`. First half has 3 nodes, second 2.

**Trace — even `1 → 2 → 3 → 4`**

| Iteration | slow | fast |
|---|---|---|
| start | 1 | 1 |
| 1 | 2 | 3 |

Next check: `fast.next = 4 ≠ NULL`, but `fast.next.next = NULL`, so the loop stops. `slow = 2`, first half `1→2`, second `3→4`. Equal halves.

**Middle node**

```
MIDDLE(head):
1. slow ← head; fast ← head
2. while fast ≠ NULL and fast.next ≠ NULL:
       slow ← slow.next
       fast ← fast.next.next
3. return slow
```

For odd `1→2→3→4→5`, returns 3. For even `1→2→3→4`, returns 3 (second middle); if the first middle is required, start `fast = head.next` or stop one step earlier.

**Even/odd length**

```
IS_EVEN(head):
1. temp ← head
2. while temp ≠ NULL and temp.next ≠ NULL:
       temp ← temp.next.next
3. if temp = NULL: return true   /* even */
4. else: return false            /* odd */
```

Time `O(n)`, space `O(1)`.

---

## 2.11 Circular singly linked list **[8–10]**

**Asked in:** CO-R25 Q1A(ii) (insert beginning — C program); AI-F24 Q4(1)(ii) (search in CLL).

**Questions (verbatim):**
> Write a complete C program / Algorithm to insert a node at the BEGINNING in a Circular Linked List. Give a simple example.
>
> Write the pseudocode and explain how you would search for a value in a Circular Singly Linked List. How does the search operation differ from a standard singly linked list, and what are the key challenges in implementing this operation?

**Answer**

In a **circular singly linked list (CLL)**, the last node's `next` points back to the first node instead of `NULL`. If `head` is the first node, `head->next` eventually returns to `head`.

**Insert at beginning**

```
INSERT_BEGIN_CLL(head, x):
1. new ← allocate Node; new.data ← x
2. if head = NULL:
       new.next ← new
       return new
3. last ← head
4. while last.next ≠ head: last ← last.next
5. new.next ← head
6. last.next ← new
7. head ← new
8. return head
```

Time `O(n)` because the last node must be found. With a `tail` pointer, it is `O(1)`.

**Diagram**

```
Before: head → [10|•] → [20|•] → [30|•]
                                      ↑
                                      └──── back to 10

Insert 5:
new = [5|•]
last = 30; new.next = 10; 30.next = 5; head = 5

After:  head → [5|•] → [10|•] → [20|•] → [30|•]
                                            ↑
                                            └── back to 5
```

**Complete C program — insert at beginning in CLL**

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *next;
};

struct Node *insertBeginCLL(struct Node *head, int x)
{
    struct Node *newNode = malloc(sizeof(struct Node));
    newNode->data = x;

    if (head == NULL) {          /* first node points to itself */
        newNode->next = newNode;
        return newNode;
    }

    struct Node *last = head;
    while (last->next != head)   /* find the last node */
        last = last->next;

    newNode->next = head;        /* new node points to old head */
    last->next = newNode;        /* last node points to new head */
    return newNode;              /* new node becomes head */
}

void displayCLL(struct Node *head)
{
    if (head == NULL) { printf("Empty\n"); return; }
    struct Node *temp = head;
    do {
        printf("%d -> ", temp->data);
        temp = temp->next;
    } while (temp != head);
    printf("(back to %d)\n", head->data);
}

int main(void)
{
    struct Node *head = NULL;
    head = insertBeginCLL(head, 30);
    head = insertBeginCLL(head, 20);
    head = insertBeginCLL(head, 10);
    head = insertBeginCLL(head, 5);
    displayCLL(head);            /* 5 -> 10 -> 20 -> 30 -> (back to 5) */
    return 0;
}
```

**Search in a CLL**

```
SEARCH_CLL(head, key):
1. if head = NULL: return "not found"
2. temp ← head
3. do:
       if temp.data = key: return temp
       temp ← temp.next
   while temp ≠ head
4. return "not found"
```

**Differences from a singly linked list**

| Point | Singly linked list | Circular singly linked list |
|---|---|---|
| Last node's `next` | `NULL` | Points to head |
| End test | `temp == NULL` | `temp == head` (after at least one full round) |
| Traversal | Simple `while` | `do…while` to avoid skipping the head |
| Infinite loop risk | None | Yes, if the stop condition is wrong |
| Insert/delete at end | `O(n)` | `O(n)` without tail, `O(1)` with tail |
| Use | General list | Round-robin, circular buffers, playlists |

**Key challenges:** (1) an empty list has `head == NULL`, but a one-node list has `head->next == head`; (2) never use `while (temp != NULL)`; (3) always keep the last node's link consistent after insert/delete, otherwise the circle breaks or a node becomes unreachable.

---

## 2.12 Doubly linked list: insertion and deletion **[8–10]**

**Asked in:** AI-F24 Q1(1) (insert beginning, delete end, search); IT-F24 Q1(a)(ii) (reverse DLL); CO-F24 Q1A(ii) (insert beginning — C program); IO-F24 Q2B(2) (insert end); IT-F25 Q1A(ii) (swap first/last, insert end, delete at index); IT-R26 Q1A(ii) (delete kth from end, display reverse, insert after y); IT-R25 Q1A(ii) (median); AI-R26 Q4A(1) (insert beginning/after 30/delete 40).

**Questions (verbatim):**
> Implement a doubly linked list with the following operations: 1 Insertion Node at the beginning 2 Deletion Node from the end 3 Search for a node in the list
>
> Write a function/algorithm to reverse the order of elements in a doubly linked list and display the reversed Doubly Linked List.
>
> Write a complete C program / Algorithm to insert a node at the BEGINNING in a Doubly Linked List. Give a simple example.
>
> Write a algorithm / function to insert a node at the end of double linked list.
>
> Write a program/algorithm to perform following operations in Doubly linked list with complexity analysis - Swap first and last element of doubly linked list - Insert element x in the end - Delete element at index i
>
> Write a program/algorithm to perform following operations in Doubly linked list along with complexity analysis - Delete kth element from end - Display the elements in reverse order - Insert element x after element y
>
> Write down a function/algorithm to find the Median of a sorted Doubly Linked List containing odd/even number of elements.
>
> Given a doubly linked list containing the sequence: 10 20 30 40 50 … Write pseudocode for the operations to modify the list: 1. Insert 15 at the beginning. 2. Insert 35 after the node containing 30. 3. Delete the node containing 40.

**Answer**

**Node definition**

```c
struct DNode {
    int data;
    struct DNode *prev;
    struct DNode *next;
};
```

**Diagram**

```
NULL ← [10|prev|next] ⇄ [20|prev|next] ⇄ [30|prev|next] → NULL
         ↑ head                                      ↑ tail
```

**Insert at beginning**

```
INSERT_BEGIN_DLL(head, x):
1. new ← allocate DNode; new.data ← x
2. new.prev ← NULL
3. new.next ← head
4. if head ≠ NULL: head.prev ← new
5. head ← new
6. return head
```

Time `O(1)`.

**Complete C program — insert at beginning in DLL**

```c
#include <stdio.h>
#include <stdlib.h>

struct DNode {
    int data;
    struct DNode *prev;
    struct DNode *next;
};

struct DNode *insertBeginDLL(struct DNode *head, int x)
{
    struct DNode *newNode = malloc(sizeof(struct DNode));
    newNode->data = x;
    newNode->prev = NULL;
    newNode->next = head;

    if (head != NULL)
        head->prev = newNode;

    return newNode;              /* new node is the new head */
}

void displayForward(struct DNode *head)
{
    while (head != NULL) {
        printf("%d <-> ", head->data);
        head = head->next;
    }
    printf("NULL\n");
}

int main(void)
{
    struct DNode *head = NULL;
    head = insertBeginDLL(head, 30);
    head = insertBeginDLL(head, 20);
    head = insertBeginDLL(head, 10);
    displayForward(head);        /* 10 <-> 20 <-> 30 <-> NULL */
    return 0;
}
```

**Insert at end**

```
INSERT_END_DLL(head, x):
1. new ← allocate DNode; new.data ← x; new.next ← NULL
2. if head = NULL: new.prev ← NULL; return new
3. temp ← head
4. while temp.next ≠ NULL: temp ← temp.next
5. temp.next ← new
6. new.prev ← temp
7. return head
```

Time `O(n)` without a tail pointer, `O(1)` with one.

**Insert after a given node `y`**

```
INSERT_AFTER_DLL(head, y, x):
1. temp ← head
2. while temp ≠ NULL and temp.data ≠ y: temp ← temp.next
3. if temp = NULL: print "not found"; return head
4. new ← allocate DNode; new.data ← x
5. new.next ← temp.next
6. new.prev ← temp
7. if temp.next ≠ NULL: temp.next.prev ← new
8. temp.next ← new
9. return head
```

**Delete at beginning**

```
DELETE_BEGIN_DLL(head):
1. if head = NULL: return NULL
2. temp ← head
3. head ← head.next
4. if head ≠ NULL: head.prev ← NULL
5. free(temp)
6. return head
```

**Delete at end**

```
DELETE_END_DLL(head):
1. if head = NULL: return NULL
2. if head.next = NULL: free(head); return NULL
3. temp ← head
4. while temp.next ≠ NULL: temp ← temp.next
5. temp.prev.next ← NULL
6. free(temp)
7. return head
```

**Delete at index `i` (1-based)**

```
DELETE_INDEX_DLL(head, i):
1. if head = NULL or i < 1: return head
2. temp ← head; pos ← 1
3. while temp ≠ NULL and pos < i:
       temp ← temp.next; pos ← pos + 1
4. if temp = NULL: print "out of range"; return head
5. if temp.prev ≠ NULL: temp.prev.next ← temp.next
6. else: head ← temp.next
7. if temp.next ≠ NULL: temp.next.prev ← temp.prev
8. free(temp)
9. return head
```

**Delete the k-th node from the end**

Use two pointers separated by `k` nodes:

```
DELETE_KTH_FROM_END(head, k):
1. if head = NULL or k < 1: return head
2. fast ← head; slow ← head
3. for i ← 1 to k:
       if fast = NULL: return head        /* k > length */
       fast ← fast.next
4. if fast = NULL:                         /* delete head */
       temp ← head; head ← head.next
       if head ≠ NULL: head.prev ← NULL
       free(temp); return head
5. while fast ≠ NULL:
       fast ← fast.next; slow ← slow.next
6. temp ← slow.next                        /* node to delete */
7. slow.next ← temp.next
8. if temp.next ≠ NULL: temp.next.prev ← slow
9. free(temp)
10. return head
```

Time `O(n)`, space `O(1)`.

**Reverse display (tail to head)**

```
DISPLAY_REVERSE_DLL(head):
1. if head = NULL: return
2. temp ← head
3. while temp.next ≠ NULL: temp ← temp.next    /* go to tail */
4. while temp ≠ NULL:
       print temp.data
       temp ← temp.prev
```

Time `O(n)`, space `O(1)`.

**Reverse the links of a DLL**

```
REVERSE_DLL(head):
1. temp ← NULL; curr ← head
2. while curr ≠ NULL:
       temp ← curr.prev
       curr.prev ← curr.next
       curr.next ← temp
       curr ← curr.prev          /* move to the next original node */
3. if temp ≠ NULL: head ← temp.prev
4. return head
```

**Swap first and last elements (by data or by links)**

The simplest correct version swaps **data**:

```
SWAP_FIRST_LAST(head):
1. if head = NULL or head.next = NULL: return head
2. first ← head
3. last ← head
4. while last.next ≠ NULL: last ← last.next
5. swap(first.data, last.data)
```

If the exam wants pointer surgery, relink `prev/next` of the first and last nodes and update `head`; handle the two-node case separately. State which version you are writing.

**Search**

```
SEARCH_DLL(head, key):
1. temp ← head; pos ← 1
2. while temp ≠ NULL:
       if temp.data = key: return pos
       temp ← temp.next; pos ← pos + 1
3. return −1
```

**Median of a sorted DLL**

For a sorted DLL, the median is the middle by position (not by value).

```
MEDIAN_DLL(head):
1. if head = NULL: return "empty"
2. count ← 0; temp ← head
3. while temp ≠ NULL: count ← count + 1; temp ← temp.next
4. mid ← (count + 1) / 2          /* ceil for odd, lower middle for even */
5. temp ← head
6. for i ← 1 to mid−1: temp ← temp.next
7. if count is odd: return temp.data
8. else: return (temp.data + temp.next.data) / 2.0
```

**Trace:** `10 ⇄ 20 ⇄ 30 ⇄ 40 ⇄ 50` → count 5, mid 3 → median 30. For `10 ⇄ 20 ⇄ 30 ⇄ 40` → count 4, lower middle 2, median `(20+30)/2 = 25`.

**Worked example — AI-R26 Q4A(1)**

Start: `10 ⇄ 20 ⇄ 30 ⇄ 40 ⇄ 50`.

1. Insert 15 at beginning → `15 ⇄ 10 ⇄ 20 ⇄ 30 ⇄ 40 ⇄ 50`.
2. Insert 35 after 30 → `15 ⇄ 10 ⇄ 20 ⇄ 30 ⇄ 35 ⇄ 40 ⇄ 50`.
3. Delete node 40 → `15 ⇄ 10 ⇄ 20 ⇄ 30 ⇄ 35 ⇄ 50`.

Pointer checks: `35.prev = 30`, `35.next = 40`; after deleting 40, `35.next = 50` and `50.prev = 35`.

---

## 2.12A Sum of two linked-list numbers using a stack **[10]**

**Asked in:** CO-B26 Q1A(1).

**Question (verbatim):**
> Write an algorithm/program to find the sum of two linked lists using stack. The digits of a number are stored in node as shown below. Input: First List: 5->5->4; Second List: 7->5->3; Output Resultant list: 1->2->0->7.

**Answer**

Each list stores one decimal digit per node, **most significant digit first**. To add from the least significant end, push the digits of both lists onto two stacks, then pop and add with a carry.

**Algorithm**

```
ADD_LISTS_STACK(L1, L2):
1. S1 ← empty stack; S2 ← empty stack
2. for each node p in L1: push(S1, p.data)
3. for each node q in L2: push(S2, q.data)
4. carry ← 0; result ← empty list
5. while S1 not empty or S2 not empty or carry ≠ 0:
       a ← 0 if S1 empty else pop(S1)
       b ← 0 if S2 empty else pop(S2)
       sum ← a + b + carry
       digit ← sum mod 10
       carry ← floor(sum / 10)
       new ← Node(digit)
       new.next ← result        /* prepend: result is MSB-first */
       result ← new
6. return result
```

Prepending each digit builds the result in the same most-significant-first order as the inputs. Time `O(m+n)`, space `O(m+n)` for the two stacks and the result.

**Trace with the printed digits**

`L1 = 5→5→4` (554), `L2 = 7→5→3` (753).

| Pop from S1 | Pop from S2 | Carry in | Sum | Digit | Carry out | Result so far |
|---:|---:|---:|---:|---:|---:|---|
| 4 | 3 | 0 | 7 | 7 | 0 | `7` |
| 5 | 5 | 0 | 10 | 0 | 1 | `0→7` |
| 5 | 7 | 1 | 13 | 3 | 1 | `3→0→7` |
| — | — | 1 | 1 | 1 | 0 | `1→3→0→7` |

**Result: `1→3→0→7` (1307).**

**Note on the printed output:** `554 + 753 = 1307`, so the correct resultant list is `1→3→0→7`. The paper prints `1→2→0→7` (1207); that would require the second list to be `6→5→3` (653), because `554 + 653 = 1207`. The algorithm is unchanged — only the input digits differ. In the exam, show the addition explicitly so the arithmetic is verifiable.

**C function (core)**

```c
struct Node *addLists(struct Node *l1, struct Node *l2)
{
    int s1[100], s2[100], t1 = 0, t2 = 0;
    for (struct Node *p = l1; p; p = p->next) s1[t1++] = p->data;
    for (struct Node *q = l2; q; q = q->next) s2[t2++] = q->data;

    int carry = 0;
    struct Node *result = NULL;
    while (t1 > 0 || t2 > 0 || carry) {
        int a = (t1 > 0) ? s1[--t1] : 0;
        int b = (t2 > 0) ? s2[--t2] : 0;
        int sum = a + b + carry;
        carry = sum / 10;
        struct Node *n = malloc(sizeof(struct Node));
        n->data = sum % 10;
        n->next = result;          /* prepend */
        result = n;
    }
    return result;
}
```

---

## 2.13 Polynomial representation and addition **[10]**

**Asked in:** CO-R25 Q3A.

**Question (verbatim):**
> Create function to perform polynomial addition or subtraction using linked lists. (Assume linked list is already created)

**Answer**

Each node stores one term:

```c
struct Term {
    int coeff;      /* coefficient */
    int exp;        /* exponent */
    struct Term *next;
};
```

Terms are kept in **decreasing exponent** order. Example: `5x³ + 4x² − 3x + 7` is

```
[5|3] → [4|2] → [-3|1] → [7|0] → NULL
```

**Addition algorithm (merge by exponent)**

```
ADD_POLY(P, Q):
1. dummy ← new Term; tail ← dummy
2. while P ≠ NULL and Q ≠ NULL:
       if P.exp > Q.exp:
           append copy of P; P ← P.next
       else if P.exp < Q.exp:
           append copy of Q; Q ← Q.next
       else:
           c ← P.coeff + Q.coeff
           if c ≠ 0: append (c, P.exp)
           P ← P.next; Q ← Q.next
3. append all remaining terms of P
4. append all remaining terms of Q
5. return dummy.next
```

**Trace**

```
P = 5x³ + 4x² + 7        → [5|3] → [4|2] → [7|0]
Q = 3x³ − 2x² + x − 5    → [3|3] → [-2|2] → [1|1] → [-5|0]

exp 3: 5+3 = 8  → 8x³
exp 2: 4−2 = 2  → 2x²
exp 1: only Q   → x
exp 0: 7−5 = 2  → 2

Result: 8x³ + 2x² + x + 2
```

**Subtraction** is the same algorithm with every coefficient of `Q` negated before merging (`P − Q = P + (−Q)`). Do not negate the exponents.

**C function**

```c
struct Term *addPoly(struct Term *p, struct Term *q)
{
    struct Term dummy = {0, 0, NULL};
    struct Term *tail = &dummy;

    while (p != NULL && q != NULL) {
        struct Term *t = malloc(sizeof(struct Term));
        if (p->exp > q->exp) {
            t->coeff = p->coeff; t->exp = p->exp; p = p->next;
        } else if (p->exp < q->exp) {
            t->coeff = q->coeff; t->exp = q->exp; q = q->next;
        } else {
            t->coeff = p->coeff + q->coeff; t->exp = p->exp;
            p = p->next; q = q->next;
        }
        t->next = NULL;
        if (t->coeff != 0) { tail->next = t; tail = t; }
        else free(t);
    }
    while (p != NULL) {                 /* append remaining terms of p */
        struct Term *t = malloc(sizeof(struct Term));
        t->coeff = p->coeff; t->exp = p->exp; t->next = NULL;
        tail->next = t; tail = t; p = p->next;
    }
    while (q != NULL) {                 /* append remaining terms of q */
        struct Term *t = malloc(sizeof(struct Term));
        t->coeff = q->coeff; t->exp = q->exp; t->next = NULL;
        tail->next = t; tail = t; q = q->next;
    }
    return dummy.next;
}
```

**Complexity:** `O(m + n)` time because each term of either list is visited once; `O(m + n)` new nodes in the result (or `O(1)` extra if the input nodes are reused).

---

## Quick revision checklist — Unit 2

**List ADT and basics**

- [ ] List ADT operations: insert, delete, search, get, set, length, traverse.
- [ ] Array vs linked list: access, insert/delete, memory, cache.
- [ ] Applications: dynamic memory, stacks/queues, undo/redo, browser, adjacency list, round-robin, polynomial, playlist, hash chaining, sparse matrix.
- [ ] Node definition: `data` + `next` (and `prev` for DLL).

**Singly linked list**

- [ ] Insert beginning `O(1)`, end `O(n)`, after key `O(n)`, position `O(n)`.
- [ ] Delete beginning `O(1)`, last `O(n)`, key `O(n)`.
- [ ] Empty list and single-node edge cases.
- [ ] Search/count/min/max/sum: one traversal `O(n)`.
- [ ] Reverse: three pointers, `O(n)` time, `O(1)` space.
- [ ] Floyd cycle detection; loop length; loop start.
- [ ] Remove duplicates sorted `O(n)`; unsorted `O(n²)` or hash `O(n)`.
- [ ] Merge sorted lists: dummy node, `O(m+n)`.
- [ ] Split halves: slow/fast; odd → first half longer.
- [ ] Middle: slow/fast; even convention.
- [ ] Even/odd length without counting.

**Circular and doubly linked lists**

- [ ] CLL: last node points to head; traversal uses `do…while`.
- [ ] CLL insert beginning: find last, `new->next = head`, `last->next = new`, `head = new`.
- [ ] CLL search stops when `temp == head`.
- [ ] DLL node: `prev` + `data` + `next`.
- [ ] DLL insert beginning: `new->next = head`, `head->prev = new`.
- [ ] DLL insert end: walk to last, `last->next = new`, `new->prev = last`.
- [ ] DLL delete beginning: update `head` and `head->prev`.
- [ ] DLL delete index/k-th from end: two pointers.
- [ ] DLL reverse display from tail using `prev`.
- [ ] Median: count then walk to middle; average for even.
- [ ] Swap first/last: data swap or pointer surgery.

**Polynomial**

- [ ] Node: coefficient + exponent.
- [ ] Keep decreasing exponent order.
- [ ] Add equal exponents, copy unequal ones.
- [ ] Subtraction negates coefficients, not exponents.
- [ ] Complexity `O(m+n)`.
