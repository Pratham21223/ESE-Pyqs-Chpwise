# Unit 3 — Linear Data Structures: Stack and Queue

> **Syllabus (DJS23IPC201, 7 hrs):** Stack as an ADT, operations, array and linked-list representation of stack, applications — reversing data, conversion of infix to prefix and postfix, evaluation of postfix and prefix expressions, balanced parentheses. Queue as an ADT, operations, implementation of linear queue, circular and priority queue using arrays and linked list, DEQueue. Applications — queue simulation.
>
> **Past-paper coverage:** stack ADT and implementations; infix→postfix and infix→prefix; postfix evaluation; expression trees; linear and circular queues; queue using linked list; priority queue; DEQueue variants; reversing/interleaving queues; stack using queue.

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

## 3.1 Stack as an ADT; array implementation **[5–10]**

**Asked in:** IO-F24 Q1B(1); IT-R25 Q3A(i); AI-R25 Q2(2) (Stack alternative); CO-R26 Q4A(i).

**Questions (verbatim):**
> Define Stack as a ADT.
>
> Discuss Stack ADT. Write a program/algorithm to implement Stack using Linked List.
>
> Explain Stack as ADT and their operations. How can a stack be implemented using an array?
>
> Write a menu driven program to implement stack using array.

**Answer**

A **stack** is a linear data structure that follows **LIFO** (Last In, First Out): insertion and deletion happen only at one end, called the **top**.

**Stack ADT**

```
ADT Stack
  push(x)   : insert x at the top
  pop()     : remove and return the top element
  peek()    : return the top element without removing it
  isEmpty() : true if the stack has no elements
  isFull()  : true if a fixed-size stack is full
  size()    : number of elements
```

The ADT fixes the behaviour; the implementation may be an array or a linked list.

**Array implementation**

Use an array `S[0…MAX−1]` and an integer `top`:

- `top = −1` means empty.
- `push`: if `top == MAX−1` → **overflow**; else `S[++top] = x`.
- `pop`: if `top == −1` → **underflow**; else return `S[top−−]`.
- `peek`: return `S[top]` if not empty.
- `isFull`: `top == MAX−1`.

**Diagram**

```
MAX = 5
index:   0    1    2    3    4
        [10] [20] [30] [  ] [ ]
                       ↑
                      top = 2

push(40): top = 3, S[3] = 40
pop():    returns 30, top = 2
```

**C implementation (core functions)**

```c
#define MAX 100
int stack[MAX];
int top = -1;

int isEmpty(void) { return top == -1; }
int isFull(void)  { return top == MAX - 1; }

void push(int x) {
    if (isFull()) { printf("Stack overflow\n"); return; }
    stack[++top] = x;
}

int pop(void) {
    if (isEmpty()) { printf("Stack underflow\n"); return -1; }
    return stack[top--];
}

int peek(void) {
    if (isEmpty()) { printf("Stack empty\n"); return -1; }
    return stack[top];
}
```

All four operations are **`O(1)`**. Space is `O(MAX)`.

**Menu-driven program**

```c
#include <stdio.h>
#define MAX 100

int stack[MAX], top = -1;

void push(int x) {
    if (top == MAX - 1) { printf("Overflow\n"); return; }
    stack[++top] = x;
}
int pop(void) {
    if (top == -1) { printf("Underflow\n"); return -1; }
    return stack[top--];
}
void display(void) {
    if (top == -1) { printf("Empty\n"); return; }
    for (int i = top; i >= 0; i--) printf("%d ", stack[i]);
    printf("\n");
}

int main(void) {
    int choice, x;
    do {
        printf("1.Push 2.Pop 3.Peek 4.Display 5.Exit\nChoice: ");
        scanf("%d", &choice);
        switch (choice) {
            case 1: printf("Value: "); scanf("%d", &x); push(x); break;
            case 2: x = pop(); if (x != -1) printf("Popped %d\n", x); break;
            case 3: if (top != -1) printf("Top %d\n", stack[top]); break;
            case 4: display(); break;
        }
    } while (choice != 5);
    return 0;
}
```

---

## 3.2 Stack using a linked list **[10]**

**Asked in:** IT-R25 Q3A(i); CO-R25 Q4A(i).

**Questions (verbatim):**
> Discuss Stack ADT. Write a program/algorithm to implement Stack using Linked List.
>
> Write a menu driven program to implement stack using linked list.

**Answer**

The **top** of the stack is the **head** of the linked list. Push inserts at the head; pop deletes the head. This gives `O(1)` operations and no fixed capacity (overflow only when the heap is exhausted).

**Node**

```c
struct Node {
    int data;
    struct Node *next;
};
```

**Algorithms**

```
PUSH(head, x):
1. new ← allocate Node; new.data ← x
2. new.next ← head
3. head ← new
4. return head

POP(head):
1. if head = NULL: print "underflow"; return NULL
2. temp ← head
3. head ← head.next
4. x ← temp.data
5. free(temp)
6. return x, head

PEEK(head):
1. if head = NULL: print "empty"
2. return head.data
```

**Diagram**

```
push 10:  top → [10|NULL]
push 20:  top → [20|•] → [10|NULL]
push 30:  top → [30|•] → [20|•] → [10|NULL]
pop:      returns 30; top → [20|•] → [10|NULL]
```

**C program**

```c
#include <stdio.h>
#include <stdlib.h>

struct Node { int data; struct Node *next; };
struct Node *top = NULL;

void push(int x) {
    struct Node *n = malloc(sizeof(struct Node));
    n->data = x; n->next = top; top = n;
}
int pop(void) {
    if (top == NULL) { printf("Underflow\n"); return -1; }
    struct Node *t = top; int x = t->data;
    top = top->next; free(t); return x;
}
void display(void) {
    struct Node *t = top;
    while (t) { printf("%d ", t->data); t = t->next; }
    printf("\n");
}
int main(void) {
    int ch, x;
    do {
        printf("1.Push 2.Pop 3.Peek 4.Display 5.Exit\nChoice: ");
        scanf("%d", &ch);
        switch (ch) {
            case 1: printf("Value: "); scanf("%d", &x); push(x); break;
            case 2: x = pop(); if (x != -1) printf("Popped %d\n", x); break;
            case 3: if (top != NULL) printf("Top %d\n", top->data); break;
            case 4: display(); break;
        }
    } while (ch != 5);
    return 0;
}
```

Sample run: `1` → push 10, 20, 30, then `4` displays `30 20 10`; `2` prints `Popped 30`; `4` displays `20 10`.

**Array vs linked stack**

| Point | Array stack | Linked stack |
|---|---|---|
| Capacity | Fixed `MAX` | Grows until memory ends |
| Overflow | When `top == MAX−1` | When `malloc` fails |
| Push/pop | `O(1)` | `O(1)` |
| Memory overhead | None per element | One pointer per node |
| Cache | Better | Poorer |
| Implementation | `top` index | `top` pointer |

---

## 3.3 Infix to postfix conversion **[10]**

**Asked in:** IT-F24 Q3A(i); IO-F24 Q1A; CO-R26 Q2A(i); IT-F25 Q3A(i) (prefix and postfix); AI-R26 Q1A(1); IC-R26 Q1a.

**Questions (verbatim):**
> Write an Algorithm for implementing stack operations. Also illustrate the conversion of following infix expression to postfix expression using stack. `(a-b) / ((c*d)-(e+f))`
>
> Write Algorithm to convert infix expression to postfix expression. Demonstrate step by step for expression: `p*q^ r-s/t+(u/v)`
>
> Write a program/algorithm to convert infix expression to postfix expression. Also, convert following infix expression to postfix expression. `(A+B) * (C-D) + E`
>
> Using a stack, convert the following infix expression to prefix and postfix forms. Show each step of the conversion process clearly. Expression: `(A+B∗C−D)/(E+F)`
>
> Write pseudocode to convert infix expression to postfix expression using stack. Apply stack operations to convert following infix expression into postfix expression: `A + (B - C)*(D/E)+G`
>
> Convert given expression to postfix expression: `A * B ↑ C − (D / E) + [F / G]`. Evaluate converted postfix expression step by step for values: A=3, B=2, C=4, D=10, E=5, F=8, G=2.

**Answer**

**Precedence and associativity**

| Operator | Precedence | Associativity |
|---|---:|---|
| `^` (exponent) | 3 | right to left |
| `*`, `/`, `%` | 2 | left to right |
| `+`, `−` | 1 | left to right |
| `(` | 0 (sentinel) | — |

**Algorithm (shunting-yard)**

```
INFIX_TO_POSTFIX(infix):
1. create empty stack st and empty output
2. for each token c in infix:
     if c is an operand: append c to output
     else if c = '(': push c
     else if c = ')':
         while st not empty and top ≠ '(': output ← pop
         pop and discard '('
     else:                                  /* operator */
         while st not empty and top ≠ '(' and
               (prec(top) > prec(c) or
                (prec(top) = prec(c) and c is left-associative)):
             output ← pop
         push c
3. while st not empty: output ← pop
4. return output
```

Operands are single letters/digits in these questions; multi-digit numbers need a tokeniser.

**Example 1 — `(a-b)/((c*d)-(e+f))`**

| Token | Stack after | Output after |
|---|---|---|
| `(` | `(` | |
| `a` | `(` | `a` |
| `−` | `(−` | `a` |
| `b` | `(−` | `ab` |
| `)` | | `ab−` |
| `/` | `/` | `ab−` |
| `(` | `/(` | `ab−` |
| `(` | `/((` | `ab−` |
| `c` | `/((` | `ab−c` |
| `*` | `/((*` | `ab−c` |
| `d` | `/((*` | `ab−cd` |
| `)` | `/(` | `ab−cd*` |
| `−` | `/(−` | `ab−cd*` |
| `(` | `/(−(` | `ab−cd*` |
| `e` | `/(−(` | `ab−cd*e` |
| `+` | `/(−(+` | `ab−cd*e` |
| `f` | `/(−(+` | `ab−cd*ef` |
| `)` | `/(−` | `ab−cd*ef+` |
| `)` | `/` | `ab−cd*ef+−` |
| end | | **`ab−cd*ef+−/`** |

**Postfix: `ab−cd*ef+−/`**

**Example 2 — `p*q^r-s/t+(u/v)`**

| Token | Stack after | Output after |
|---|---|---|
| `p` | | `p` |
| `*` | `*` | `p` |
| `q` | `*` | `pq` |
| `^` | `*^` | `pq` |
| `r` | `*^` | `pqr` |
| `−` | `−` | `pqr^*` |
| `s` | `−` | `pqr^*s` |
| `/` | `−/` | `pqr^*s` |
| `t` | `−/` | `pqr^*st` |
| `+` | `+` | `pqr^*st/−` |
| `(` | `+(` | `pqr^*st/−` |
| `u` | `+(` | `pqr^*st/−u` |
| `/` | `+(/` | `pqr^*st/−u` |
| `v` | `+(/` | `pqr^*st/−uv` |
| `)` | `+` | `pqr^*st/−uv/` |
| end | | **`pqr^*st/−uv/+`** |

**Postfix: `pqr^*st/−uv/+`**

**Example 3 — `(A+B*C−D)/(E+F)`**

| Token | Stack after | Output after |
|---|---|---|
| `(` | `(` | |
| `A` | `(` | `A` |
| `+` | `(+` | `A` |
| `B` | `(+` | `AB` |
| `*` | `(+*` | `AB` |
| `C` | `(+*` | `ABC` |
| `−` | `(−` | `ABC*+` |
| `D` | `(−` | `ABC*+D` |
| `)` | | `ABC*+D−` |
| `/` | `/` | `ABC*+D−` |
| `(` | `/(` | `ABC*+D−` |
| `E` | `/(` | `ABC*+D−E` |
| `+` | `/(+` | `ABC*+D−E` |
| `F` | `/(+` | `ABC*+D−EF` |
| `)` | `/` | `ABC*+D−EF+` |
| end | | **`ABC*+D−EF+/`** |

**Postfix: `ABC*+D−EF+/`**

**Example 4 — `(A+B)*(C−D)+E`**

Trace: `A B + C D − * E +`. Postfix: **`AB+CD−*E+`**.

**Example 5 — `A + (B−C)*(D/E) + G`**

Trace: `A B C − D E / * + G +`. Postfix: **`ABC−DE/*+G+`**.

**Example 6 — `A*B↑C − (D/E) + [F/G]`**

Treat `↑` as exponentiation `^` and `[ ]` as `( )`.

Trace: `A B C ^ * D E / − F G / +`. Postfix: **`ABC^*DE/−FG/+`**.

**Example 7 — `A*B+C−(D/E)+(F/G)`**

Postfix: **`AB*C+DE/−FG/+`**.

**Example 8 — `A−B/(C*D)+(E*F)`**

Postfix: **`ABCD*/−EF*+`**.

**Complexity:** each token is pushed and popped at most once → `O(n)` time, `O(n)` stack space.

---

## 3.4 Infix to prefix conversion **[10]**

**Asked in:** CO-R25 Q2A(i); IT-F25 Q3A(i).

**Questions (verbatim):**
> Write and explain algorithm/program for conversion of Infix expression to prefix form. Apply the same for following example. `A/B^C+D*E-F*G`
>
> Using a stack, convert the following infix expression to prefix and postfix forms. Show each step of the conversion process clearly. Expression: `(A+B∗C−D)/(E+F)`

**Answer**

**Direct right-to-left algorithm**

1. Scan the infix expression from **right to left**.
2. If the token is an operand, append it to the output.
3. If it is `)`, push it.
4. If it is `(`, pop and output until `)` is on top; discard the `)`.
5. If it is an operator, pop while the stack top has **strictly greater** precedence, then push the operator.
6. After the scan, pop everything.
7. Reverse the output.

Strict `>` (not `≥`) is what makes equal-precedence operators group correctly for right-to-left scanning.

```
INFIX_TO_PREFIX(infix):
1. output ← empty; st ← empty
2. for c from rightmost to leftmost token of infix:
     if operand: output ← output + c
     else if c = ')': push c
     else if c = '(':
         while top ≠ ')': output ← output + pop
         pop and discard ')'
     else:
         while st not empty and top ≠ ')' and prec(top) > prec(c):
             output ← output + pop
         push c
3. while st not empty: output ← output + pop
4. return reverse(output)
```

**Example 1 — `A/B^C + D*E − F*G`**

Scan from right to left:

| Token | Stack after | Output after |
|---|---|---|
| `G` | | `G` |
| `*` | `*` | `G` |
| `F` | `*` | `GF` |
| `−` | `−` | `GF*` |
| `E` | `−` | `GF*E` |
| `*` | `−*` | `GF*E` |
| `D` | `−*` | `GF*ED` |
| `+` | `−+` | `GF*ED*` |
| `C` | `−+` | `GF*ED*C` |
| `^` | `−+^` | `GF*ED*C` |
| `B` | `−+^` | `GF*ED*CB` |
| `/` | `−+/` | `GF*ED*CB^` |
| `A` | `−+/` | `GF*ED*CB^A` |
| end | | `GF*ED*CB^A/+−` |

Reverse the output: **`−+/A^BC*DE*FG`**.

Check the parse: `− (+ (/ A (^ B C)) (* D E)) (* F G)` = `((A/B^C) + (D*E)) − (F*G)`, which matches the original.

**Example 2 — `(A+B*C−D)/(E+F)`**

Scan from right to left:

| Token | Stack after | Output after |
|---|---|---|
| `)` | `)` | |
| `F` | `)` | `F` |
| `+` | `)+` | `F` |
| `E` | `)+` | `FE` |
| `(` | | `FE+` |
| `/` | `/` | `FE+` |
| `)` | `/)` | `FE+` |
| `D` | `/)` | `FE+D` |
| `−` | `/−` | `FE+D` |
| `C` | `/−` | `FE+DC` |
| `*` | `/−*` | `FE+DC` |
| `B` | `/−*` | `FE+DCB` |
| `+` | `/−+` | `FE+DCB*` |
| `A` | `/−+` | `FE+DCB*A` |
| `(` | `/` | `FE+DCB*A+−` |
| end | | `FE+DCB*A+−/` |

Reverse: **`/−+A*BCD+EF`**.

**Postfix of the same expression:** **`ABC*+D−EF+/`** (from §3.3).

**Complexity:** `O(n)` time, `O(n)` stack space.

---

## 3.5 Evaluation of a postfix expression **[5–10]**

**Asked in:** P8 Jan-24 Q4A(1); IT-R26 Q3A(i); IC-R26 Q1a (evaluation half).

**Questions (verbatim):**
> Write a program or algorithm to evaluate the post fix expression. Also evaluate the following postfix expression using stack. `6523+8*+3+*`
>
> Describe the procedure for evaluation of a postfix expression using a stack. Evaluate the following postfix expression step-by-step and show stack contents after each step. Expression: `AB+CD*+E-`. Assume A=2, B=3, C=4, D=5, E=6.
>
> Convert given expression to postfix expression: … Evaluate converted postfix expression step by step for values: A=3, B=2, C=4, D=10, E=5, F=8, G=2.

**Answer**

**Procedure**

1. Scan the postfix expression left to right.
2. If the token is an operand, **push** its value.
3. If it is an operator, **pop two** values: first pop is the right operand `b`, second pop is the left operand `a`. Push `a op b`.
4. At the end, the stack contains exactly one value — the result.

```
EVAL_POSTFIX(postfix):
1. st ← empty
2. for each token c:
     if c is operand: push value(c)
     else:
         b ← pop; a ← pop
         push (a c b)
3. return pop
```

**Example 1 — `6523+8*+3+*`**

Digits are single operands.

| Token | Operation | Stack after |
|---|---|---|
| `6` | push 6 | 6 |
| `5` | push 5 | 6, 5 |
| `2` | push 2 | 6, 5, 2 |
| `3` | push 3 | 6, 5, 2, 3 |
| `+` | 2 + 3 = 5 | 6, 5, 5 |
| `8` | push 8 | 6, 5, 5, 8 |
| `*` | 5 × 8 = 40 | 6, 5, 40 |
| `+` | 5 + 40 = 45 | 6, 45 |
| `3` | push 3 | 6, 45, 3 |
| `+` | 45 + 3 = 48 | 6, 48 |
| `*` | 6 × 48 = 288 | 288 |

**Result = 288.**

**Example 2 — `AB+CD*+E−` with A=2, B=3, C=4, D=5, E=6**

| Token | Operation | Stack after |
|---|---|---|
| `A` | push 2 | 2 |
| `B` | push 3 | 2, 3 |
| `+` | 2 + 3 = 5 | 5 |
| `C` | push 4 | 5, 4 |
| `D` | push 5 | 5, 4, 5 |
| `*` | 4 × 5 = 20 | 5, 20 |
| `+` | 5 + 20 = 25 | 25 |
| `E` | push 6 | 25, 6 |
| `−` | 25 − 6 = 19 | 19 |

**Result = 19.**

**Example 3 — `ABC^*DE/−FG/+` with A=3, B=2, C=4, D=10, E=5, F=8, G=2**

| Token | Operation | Stack after |
|---|---|---|
| `A` | push 3 | 3 |
| `B` | push 2 | 3, 2 |
| `C` | push 4 | 3, 2, 4 |
| `^` | 2⁴ = 16 | 3, 16 |
| `*` | 3 × 16 = 48 | 48 |
| `D` | push 10 | 48, 10 |
| `E` | push 5 | 48, 10, 5 |
| `/` | 10 / 5 = 2 | 48, 2 |
| `−` | 48 − 2 = 46 | 46 |
| `F` | push 8 | 46, 8 |
| `G` | push 2 | 46, 8, 2 |
| `/` | 8 / 2 = 4 | 46, 4 |
| `+` | 46 + 4 = 50 | 50 |

**Result = 50.**

**Complexity:** `O(n)` time, `O(n)` stack space.

**Prefix evaluation** is the same procedure scanning **right to left**; when an operator is found, the first pop is the left operand and the second pop is the right operand.

---

## 3.6 Expression tree from a postfix expression **[10]**

**Asked in:** P8 Jan-24 Q1A(2); IT-R25 Q4A(i); CO-R25 Q3B(2).

**Questions (verbatim):**
> Write an algorithm/steps for construction of an expression tree. Construct the expression tree for the postfix expression: `abc/+def/-*`. Show the steps of construction.
>
> Write down the algorithm for construction of expression tree from given postfix expression. Construct a binary expression tree using the following expression `(5*(2+2)-7)*(2+7)+10-(6+8)*1`.
>
> Explain the expression tree with algorithm/ program.

**Answer**

An **expression tree** is a binary tree whose:

- **internal nodes** are operators,
- **leaf nodes** are operands,
- left and right subtrees are the operands of the operator.

An **inorder** traversal gives the infix form, **postorder** gives the postfix form, and **preorder** gives the prefix form.

**Construction algorithm (from postfix)**

```
BUILD_EXPR_TREE(postfix):
1. st ← empty stack of tree nodes
2. for each token c in postfix:
     if c is operand:
         node ← new Node(c); push node
     else:
         right ← pop
         left  ← pop
         node ← new Node(c, left, right)
         push node
3. return pop                       /* root */
```

**Example 1 — `abc/+def/−*`**

| Token | Action | Stack (top at right) |
|---|---|---|
| `a` | leaf a | a |
| `b` | leaf b | a, b |
| `c` | leaf c | a, b, c |
| `/` | pop c (right), b (left) → `/` | a, `(b/c)` |
| `+` | pop `(b/c)` (right), a (left) → `+` | `(a+(b/c))` |
| `d` | leaf d | `(a+(b/c))`, d |
| `e` | leaf e | `(a+(b/c))`, d, e |
| `f` | leaf f | `(a+(b/c))`, d, e, f |
| `/` | pop f, e → `/` | `(a+(b/c))`, d, `(e/f)` |
| `−` | pop `(e/f)`, d → `−` | `(a+(b/c))`, `(d−(e/f))` |
| `*` | pop `(d−(e/f))`, `(a+(b/c))` → `*` | `((a+(b/c))*(d−(e/f)))` |

**Tree**

```
                *
              /   \
             +     −
            / \   / \
           a   / d   /
              / \   / \
             b   c e   f
```

Inorder: `a + b / c * d − e / f`; postorder: `a b c / + d e f / − *` (the original).

**Example 2 — `(5*(2+2)-7)*(2+7)+10-(6+8)*1`**

First convert to postfix: **`522+*7−27+*10+68+1*−`**.

Build step by step:

| Token | Stack after |
|---|---|
| `5` | 5 |
| `2` | 5, 2 |
| `2` | 5, 2, 2 |
| `+` | 5, `(2+2)` |
| `*` | `(5*(2+2))` |
| `7` | `(5*(2+2))`, 7 |
| `−` | `((5*(2+2))−7)` |
| `2` | `((5*(2+2))−7)`, 2 |
| `7` | `((5*(2+2))−7)`, 2, 7 |
| `+` | `((5*(2+2))−7)`, `(2+7)` |
| `*` | `(((5*(2+2))−7)*(2+7))` |
| `10` | `(((5*(2+2))−7)*(2+7))`, 10 |
| `+` | `((((5*(2+2))−7)*(2+7))+10)` |
| `6` | …, 6 |
| `8` | …, 6, 8 |
| `+` | …, `(6+8)` |
| `1` | …, `(6+8)`, 1 |
| `*` | …, `((6+8)*1)` |
| `−` | `(((((5*(2+2))−7)*(2+7))+10)−((6+8)*1))` |

**Tree (top levels)**

```
                    −
                  /   \
                 +     *
                / \   / \
               *   10  +  1
              / \     / \
             *   7   6   8
            / \
           5   +
              / \
             2   2
```

Evaluation: `5*(2+2)=20`; `20−7=13`; `2+7=9`; `13*9=117`; `117+10=127`; `6+8=14`; `14*1=14`; `127−14=113`.

**Complexity:** `O(n)` time and `O(n)` stack space for `n` tokens.

---

## 3.7 Queue as an ADT; linear queue using an array **[5–10]**

**Asked in:** IC-R26 Q1b(i); AI-F24 Q4(1); IT-R25 Q3A(ii).

**Questions (verbatim):**
> Describe Queue as ADT.
>
> What are the advantages of a circular queue over a linear queue? How can you implement a linear queue using an array?
>
> Compare Linear Queue and Circular Queue. Write an Algorithm / Program to implement linear queue using arrays. Write down time complexity of all operations.

**Answer**

A **queue** is a linear data structure that follows **FIFO** (First In, First Out): insertion (**enqueue**) happens at the **rear**, deletion (**dequeue**) happens at the **front**.

**Queue ADT**

```
ADT Queue
  enqueue(x) : insert x at the rear
  dequeue()  : remove and return the front element
  front()    : read the front element
  rear()     : read the rear element
  isEmpty()  : true if no elements
  isFull()   : true if fixed capacity is reached
  size()     : number of elements
```

**Linear queue using an array**

Use `Q[0…MAX−1]`, `front = 0`, `rear = −1`:

- **empty:** `front > rear` (or `front == −1` with a count).
- **full:** `rear == MAX−1`.
- **enqueue:** if full → overflow; else `Q[++rear] = x`.
- **dequeue:** if empty → underflow; else return `Q[front++]`.

```
ENQUEUE(Q, x):
1. if rear = MAX−1: print "overflow"; return
2. rear ← rear + 1
3. Q[rear] ← x

DEQUEUE(Q):
1. if front > rear: print "underflow"; return
2. x ← Q[front]
3. front ← front + 1
4. return x
```

**Diagram**

```
MAX = 6
enqueue 10, 20, 30:
index:  0    1    2    3    4    5
       [10] [20] [30] [  ] [  ] [  ]
        ↑              ↑
      front=0        rear=2

dequeue twice:
index:  0    1    2    3    4    5
       [  ] [  ] [30] [  ] [  ] [  ]
                  ↑    ↑
               front=2 rear=2

enqueue 40, 50, 60, 70:
rear reaches 5, then the queue reports FULL
even though slots 0 and 1 are free — false overflow.
```

**Drawback:** after deletions, the free slots at the beginning cannot be reused once `rear` reaches `MAX−1`. This is called **false overflow** (or the "drifting queue" problem). Solutions: shift elements (`O(n)`), use a circular queue, or use a linked list.

**Complexities (linear queue)**

| Operation | Time |
|---|---|
| enqueue | `O(1)` |
| dequeue | `O(1)` |
| front/rear | `O(1)` |
| isEmpty/isFull | `O(1)` |
| search | `O(n)` |

---

## 3.8 Circular queue: advantages and operations **[10]**

**Asked in:** IT-F24 Q3A(ii); AI-R25 Q4(1); AI-R26 Q4A(2); CO-R26 Q4a.

**Questions (verbatim):**
> What is meant by Linear Data structures and enlist them. Why one need a circular queue as a data structure? What are it's applications? Write an algorithm to perform Insert() and Delete() operation on circular queue.
>
> What is a circular queue? Write pseudocodes for all of its operations.
>
> State Drawback of Circular Queue. Write code to implement Linear queue using linked list and its operations (enqueue, dequeue, queue_front, queue_rear) & display function to display modified queue after every operation.
>
> Write an algorithm/function to perform enqueue and dequeue operations on circular queue.

**Answer**

**Linear data structures** store elements in a single sequence, so each element has at most one predecessor and one successor. Examples: array, linked list, stack, queue, deque. A **circular queue** is a linear queue whose array is treated as a ring: after index `MAX−1`, the next index is `0`. This reuses the slots freed by dequeue and avoids false overflow.

**Why a circular queue is needed:** in a linear array queue, `rear` eventually reaches `MAX−1` even though slots freed at the front are still empty. This **false overflow** wastes memory. A circular queue wraps `rear` and `front` with `% MAX`, so the freed slots are reused and every operation stays `O(1)`.

**Pointer rules (capacity `MAX`)**

- `front` = index of the first element.
- `rear` = index of the last element.
- **empty:** `front == −1`.
- **full:** `(rear + 1) % MAX == front`.
- **enqueue:** `rear = (rear + 1) % MAX; Q[rear] = x`.
- **dequeue:** `x = Q[front]; front = (front + 1) % MAX`.
- When the last element is removed, reset `front = rear = −1`.

Because empty is a separate sentinel (`front == −1`), all `MAX` slots can be used. If you instead define empty as `front == rear`, you must keep one slot free and the capacity becomes `MAX−1`; state the convention in the exam.

**Diagram**

```
MAX = 6
After enqueue 10,20,30,40,50:
index:  0    1    2    3    4    5
       [10] [20] [30] [40] [50] [  ]
        ↑                        ↑
      front=0                 rear=4

dequeue 10, 20; enqueue 60 (slot 5), then 70 (wraps to slot 0):
index:  0    1    2    3    4    5
       [70] [20] [30] [40] [50] [60]
         ↑    ↑
       rear=0 front=2

enqueue 80 → rear=(0+1)%6=1; check (0+1)%6 = 1 ≠ front=2, so insert at slot 1:
index:  0    1    2    3    4    5
       [70] [80] [30] [40] [50] [60]
              ↑    ↑
            rear=1 front=2

Now (rear+1)%6 = 2 == front, so the queue is full (all six slots used).
```

**Algorithms**

```
ENQUEUE_CQ(x):
1. if front = −1:                  /* empty */
       front ← 0; rear ← 0; Q[0] ← x; return
2. if (rear + 1) mod MAX = front:
       print "overflow"; return
3. rear ← (rear + 1) mod MAX
4. Q[rear] ← x

DEQUEUE_CQ():
1. if front = −1: print "underflow"; return
2. x ← Q[front]
3. if front = rear:                /* last element */
       front ← −1; rear ← −1
4. else:
       front ← (front + 1) mod MAX
5. return x

FRONT_CQ(): return Q[front] if not empty
REAR_CQ():  return Q[rear] if not empty
```

**C implementation**

```c
#include <stdio.h>
#define MAX 100

int Q[MAX], front = -1, rear = -1;

void enqueue(int x) {
    if (front == -1) { front = rear = 0; Q[0] = x; return; }
    if ((rear + 1) % MAX == front) { printf("Overflow\n"); return; }
    rear = (rear + 1) % MAX;
    Q[rear] = x;
}
int dequeue(void) {
    if (front == -1) { printf("Underflow\n"); return -1; }
    int x = Q[front];
    if (front == rear) front = rear = -1;
    else front = (front + 1) % MAX;
    return x;
}
void display(void) {
    if (front == -1) { printf("Empty\n"); return; }
    int i = front;
    while (1) {
        printf("%d ", Q[i]);
        if (i == rear) break;
        i = (i + 1) % MAX;
    }
    printf("\n");
}
```

**Advantages over a linear queue**

1. Reuses freed slots; no false overflow.
2. Fixed memory, no shifting; enqueue/dequeue stay `O(1)`.
3. Better cache locality than a linked queue.
4. Natural for round-robin scheduling, buffering and streaming.

**Applications:** CPU round-robin scheduling, keyboard/input buffers, streaming and network packet buffers, printer spooling, traffic-light controllers, and any bounded producer–consumer buffer.

**Linear vs circular queue**

| Point | Linear queue | Circular queue |
|---|---|---|
| Rear movement | `rear++` until `MAX−1` | `rear=(rear+1)%MAX` |
| Reuse of freed slots | No | Yes |
| False overflow | Yes | No |
| Full condition | `rear == MAX−1` | `(rear+1)%MAX == front` |
| Empty condition | `front > rear` (or `front==−1`) | `front == −1` |
| Operations | `O(1)` | `O(1)` |
| Use | simple buffer | streaming, scheduling, bounded buffer |

**Drawbacks of a circular queue**

1. **Fixed capacity** — the array cannot grow; a full queue rejects insertion.
2. **Full/empty ambiguity** — if only `front` and `rear` are stored and `front==rear` is used for empty, one slot must be kept free (capacity `MAX−1`) or a `count`/flag is needed.
3. **Slightly more complex** — every index update needs `% MAX`.
4. **Traversal** must stop at `rear`, not at `MAX−1`; a wrong loop condition can cycle forever.

**Capacity and convention:** with `front == −1` as the empty sentinel, all `MAX` slots are usable. If your implementation defines empty as `front == rear`, it must keep one slot free and the capacity becomes `MAX−1`, or maintain a separate `count`. A full queue is otherwise indistinguishable from an empty one when only `front` and `rear` are compared.

---

## 3.9 Queue using a linked list **[10]**

**Asked in:** CO-F24 Q4A(1); AI-R26 Q4A(2); CO-B26 Q2B(2).

**Questions (verbatim):**
> Write a menu driven program to implement queue using linked list.
>
> State Drawback of Circular Queue. Write code to implement Linear queue using linked list and its operations (enqueue, dequeue, queue_front, queue_rear) & display function to display modified queue after every operation.
>
> If a Linked List is used to represent a Linear Queue. Write an algorithms/program to implement Insert () and Delete () operations with example.

**Answer**

Maintain two pointers:

- `front` — first node (next to dequeue),
- `rear` — last node (where enqueue happens).

**Node**

```c
struct Node { int data; struct Node *next; };
struct Node *front = NULL, *rear = NULL;
```

**Algorithms**

```
ENQUEUE(x):
1. new ← allocate Node; new.data ← x; new.next ← NULL
2. if rear = NULL:                 /* empty queue */
       front ← rear ← new
3. else:
       rear.next ← new
       rear ← new

DEQUEUE():
1. if front = NULL: print "underflow"; return
2. temp ← front
3. x ← temp.data
4. front ← front.next
5. if front = NULL: rear ← NULL   /* queue became empty */
6. free(temp)
7. return x

QUEUE_FRONT(): return front.data if not empty
QUEUE_REAR():  return rear.data if not empty
```

Both enqueue and dequeue are `O(1)`; display is `O(n)`.

**Diagram**

```
enqueue 10:  front → [10] ← rear
enqueue 20:  front → [10] → [20] ← rear
enqueue 30:  front → [10] → [20] → [30] ← rear
dequeue:     returns 10; front → [20] → [30] ← rear
```

**Menu-driven C program**

```c
#include <stdio.h>
#include <stdlib.h>

struct Node { int data; struct Node *next; };
struct Node *front = NULL, *rear = NULL;

void enqueue(int x) {
    struct Node *n = malloc(sizeof(struct Node));
    n->data = x; n->next = NULL;
    if (rear == NULL) front = rear = n;
    else { rear->next = n; rear = n; }
}
int dequeue(void) {
    if (front == NULL) { printf("Underflow\n"); return -1; }
    struct Node *t = front;
    int x = t->data;
    front = front->next;
    if (front == NULL) rear = NULL;
    free(t);
    return x;
}
void display(void) {
    struct Node *t = front;
    if (!t) { printf("Empty\n"); return; }
    while (t) { printf("%d ", t->data); t = t->next; }
    printf("\n");
}
int main(void) {
    int ch, x;
    do {
        printf("1.Enqueue 2.Dequeue 3.Front 4.Rear 5.Display 6.Exit\n");
        scanf("%d", &ch);
        switch (ch) {
            case 1: printf("Value: "); scanf("%d", &x); enqueue(x); break;
            case 2: x = dequeue(); if (x != -1) printf("Deleted %d\n", x); break;
            case 3: if (front) printf("Front %d\n", front->data); break;
            case 4: if (rear)  printf("Rear %d\n", rear->data); break;
            case 5: display(); break;
        }
    } while (ch != 6);
    return 0;
}
```

**Linked queue vs circular array queue**

| Point | Linked queue | Circular array |
|---|---|---|
| Capacity | Unlimited (heap) | Fixed `MAX` |
| Overflow | `malloc` failure | `(rear+1)%MAX == front` |
| Enqueue/dequeue | `O(1)` | `O(1)` |
| Memory per element | Data + pointer | Data only |
| Cache locality | Poor | Good |
| Implementation | Two pointers | Two indices |

---

## 3.10 Priority queue: insert and delete **[5–7]**

**Asked in:** IO-F24 Q4A.

**Question (verbatim):**
> Design a algorithm to perform insert and delete operation on priority queue.

**Answer**

A **priority queue** stores elements with a priority; `delete` always removes the element with the **highest priority** (or lowest value, depending on convention). It is not FIFO.

**Implementation choices**

| Implementation | Insert | Delete-max/min | Remarks |
|---|---|---|---|
| Unsorted array | `O(1)` | `O(n)` scan | simple |
| Sorted array | `O(n)` shift | `O(1)` | good if deletes dominate |
| Linked list sorted | `O(n)` | `O(1)` | dynamic |
| **Binary heap** | `O(log n)` | `O(log n)` | **best general choice** |
| Balanced BST | `O(log n)` | `O(log n)` | ordered traversal |

**Array-based priority queue (max-priority)**

Store `(element, priority)` pairs. For insertion into an **unsorted** array, append at the end; for deletion, scan for the maximum priority, swap it with the last element, and shrink.

```
INSERT(item, p):
1. if count = MAX: print "overflow"; return
2. a[count].data ← item; a[count].priority ← p
3. count ← count + 1

DELETE_MAX():
1. if count = 0: print "underflow"; return
2. maxIndex ← 0
3. for i ← 1 to count−1:
       if a[i].priority > a[maxIndex].priority: maxIndex ← i
4. result ← a[maxIndex]
5. a[maxIndex] ← a[count−1]
6. count ← count − 1
7. return result
```

**Trace**

Insert `(A,2), (B,5), (C,1), (D,4)`:

```
index: 0      1      2      3
      (A,2)  (B,5)  (C,1)  (D,4)
```

`DELETE_MAX` scans priorities `2,5,1,4` → index 1 (B,5). Replace with last `(D,4)` and shrink:

```
(A,2)  (D,4)  (C,1)
```

Next delete returns D (priority 4), then A, then C.

**Heap-based priority queue** (better complexity): insert at the end and **heapify up**; delete the root, move the last element to the root and **heapify down**. Both `O(log n)`.

```
INSERT_HEAP(x):
1. n ← n + 1; heap[n] ← x; i ← n
2. while i > 1 and heap[i] > heap[parent(i)]:
       swap(heap[i], heap[parent(i)]); i ← parent(i)

DELETE_MAX_HEAP():
1. max ← heap[1]
2. heap[1] ← heap[n]; n ← n − 1
3. HEAPIFY_DOWN(1)
4. return max
```

**Applications:** CPU scheduling (priority scheduling), Dijkstra/Prim, Huffman coding, event simulation, bandwidth management.

---

## 3.11 Double-ended queue (DEQueue) **[5–10]**

**Asked in:** IT-F24 Q4B(2); CO-F24 Q2A(1); IT-F25 Q2B(2); IT-R26 Q2B(2); IT-R25 Q4B(2).

**Questions (verbatim):**
> Explain the concept of double-ended queue. State various operations that can be performed on it with suitable example.
>
> Illustrate an algorithm/program to perform Insert and Delete operation in Input Restricted DEQUEUE with suitable example?
>
> Write a function in C or an algorithm to implement input-restricted DEQueue.
>
> Write a function in C or an algorithm to implement an output-restricted DEQueue.
>
> Write an algorithm/function to implement double-ended queue for insertion and deletion operation form the front end.

**Answer**

A **DEQueue (double-ended queue)** allows insertion and deletion at **both** ends, but not in the middle.

**Operations**

| Operation | Meaning |
|---|---|
| `insertFront(x)` | add `x` at the front |
| `insertRear(x)` | add `x` at the rear |
| `deleteFront()` | remove and return the front |
| `deleteRear()` | remove and return the rear |
| `getFront()` / `getRear()` | read without removing |
| `isEmpty()` / `isFull()` | status |

**Variants**

| Variant | Insertion | Deletion |
|---|---|---|
| **Input-restricted** | only at **rear** | both front and rear |
| **Output-restricted** | both front and rear | only at **front** |
| **Unrestricted** | both | both |

**Input-restricted DEQueue**

```
INSERT_REAR(x):
1. if (rear + 1) mod MAX = front: overflow
2. if front = −1: front ← rear ← 0
3. else: rear ← (rear + 1) mod MAX
4. Q[rear] ← x

DELETE_FRONT():
1. if front = −1: underflow
2. x ← Q[front]
3. if front = rear: front ← rear ← −1
4. else: front ← (front + 1) mod MAX
5. return x

DELETE_REAR():
1. if front = −1: underflow
2. x ← Q[rear]
3. if front = rear: front ← rear ← −1
4. else: rear ← (rear − 1 + MAX) mod MAX
5. return x
```

**Trace** with `MAX = 5`:

```
insertRear(10), insertRear(20), insertRear(30):
[10] [20] [30] [ ] [ ]
 ↑              ↑
front          rear

deleteFront() → 10; deleteRear() → 30:
[ ] [20] [ ] [ ] [ ]
     ↑
  front=rear=1
```

**Output-restricted DEQueue**

```
INSERT_FRONT(x):
1. if full: overflow
2. if front = −1: front ← rear ← 0
3. else: front ← (front − 1 + MAX) mod MAX
4. Q[front] ← x

INSERT_REAR(x):   same as input-restricted
DELETE_FRONT():   same as input-restricted
```

**Trace:** `insertRear(10), insertRear(20), insertFront(5), insertFront(1)` gives `1, 5, 10, 20`; `deleteFront()` returns `1`, then `5`.

**Array implementation details**

- Use a circular array so both ends wrap in `O(1)`.
- Use the `front == −1` sentinel (all slots usable) or maintain a `count` to distinguish full from empty.
- A linked list with `head` and `tail` gives the same operations without a capacity limit.

**Applications:** sliding-window maximum, undo/redo, browser history, job-stealing schedulers, palindrome checking.

---

## 3.12 Queue applications: reverse, rotate and interleave **[10]**

**Asked in:** AI-F24 Q2(2)(i); P8 Jan-24 Q2B(1); IT-F25 Q3A(2); IT-R26 Q3A(2).

**Questions (verbatim):**
> How can a stack be implemented using a linked list? Write a program to reverse the order of elements in a queue using a stack.
>
> Given an integer k and a queue of integers, write down the algorithm to reverse the order of the first k elements of the queue, leaving the other elements in the same relative order. … Time Complexity O(n), Space Complexity O(n).
>
> Given a queue of integers, write a function/algorithm to rearrange the elements by interleaving the first half of the list with the second half of the list. … [1,2,3,4,5,6,7,8,9,10] → [1,6,2,7,3,8,4,9,5,10].
>
> Write an algorithm for implementing stack using queue data structure. Show the working … PUSH(5), PUSH(10), PUSH(15), POP(), PUSH(20), PUSH(25), PUSH(30), POP().

**Answer**

### 3.12.1 Reverse a queue using a stack

```
REVERSE_QUEUE(Q):
1. st ← empty stack
2. while Q not empty:
       st.push(Q.dequeue())
3. while st not empty:
       Q.enqueue(st.pop())
4. return Q
```

**Trace:** `Q = [10, 20, 30, 40]`

- Pop into stack: `10, 20, 30, 40` → stack top 40.
- Pop stack into queue: `40, 30, 20, 10`.

Time `O(n)`, space `O(n)`.

### 3.12.2 Reverse the first `k` elements of a queue

```
REVERSE_FIRST_K(Q, k):
1. if k ≤ 0 or k > size(Q): return Q
2. st ← empty stack
3. for i ← 1 to k: st.push(Q.dequeue())
4. while st not empty: Q.enqueue(st.pop())
5. for i ← 1 to size(Q) − k:
       Q.enqueue(Q.dequeue())
6. return Q
```

**Trace:** `Q = [10,20,30,40,50,60,70,80,90]`, `k = 4`

- Dequeue 4 into stack: stack `10,20,30,40` (top 40); `Q = [50,60,70,80,90]`.
- Pop stack into rear: `Q = [50,60,70,80,90,40,30,20,10]`.
- Rotate the first `9−4 = 5` elements to the rear: `50,60,70,80,90` move behind → `Q = [40,30,20,10,50,60,70,80,90]`.

Time `O(n)`, space `O(k)` (the question allows `O(n)`).

### 3.12.3 Interleave the two halves of a queue

```
INTERLEAVE(Q):
1. n ← size(Q); half ← n / 2
2. Q1 ← empty; Q2 ← empty
3. for i ← 1 to half: Q1.enqueue(Q.dequeue())
4. for i ← 1 to n − half: Q2.enqueue(Q.dequeue())
5. while Q1 or Q2 not empty:
       if Q1 not empty: Q.enqueue(Q1.dequeue())
       if Q2 not empty: Q.enqueue(Q2.dequeue())
6. return Q
```

**Trace:** `[1,2,3,4,5,6,7,8,9,10]`

- `Q1 = [1,2,3,4,5]`, `Q2 = [6,7,8,9,10]`.
- Alternate: `1,6,2,7,3,8,4,9,5,10`.

Time `O(n)`, space `O(n)`.

### 3.12.4 Stack using a queue (push-costly)

Use one queue. `push` enqueues the new element and then rotates the previous elements behind it, so the newest element is always at the front.

```
PUSH(x):
1. Q.enqueue(x)
2. for i ← 1 to size(Q) − 1:
       Q.enqueue(Q.dequeue())

POP():
1. if Q empty: underflow
2. return Q.dequeue()
```

**Trace**

| Operation | Queue after | Output |
|---|---|---|
| `PUSH(5)` | `[5]` | |
| `PUSH(10)` | `[10,5]` | |
| `PUSH(15)` | `[15,10,5]` | |
| `POP()` | `[10,5]` | 15 |
| `PUSH(20)` | `[20,10,5]` | |
| `PUSH(25)` | `[25,20,10,5]` | |
| `PUSH(30)` | `[30,25,20,10,5]` | |
| `POP()` | `[25,20,10,5]` | 30 |

**Outputs: 15 and 30.**

**Complexity:** `push O(n)`, `pop O(1)`. The alternative (pop-costly) makes `push O(1)` and `pop O(n)` by moving all but the last element to the rear before deleting.

### 3.12.5 Stack applications (summary)

1. **Reversing data** — push all, pop all.
2. **Expression conversion** — infix to postfix/prefix.
3. **Expression evaluation** — postfix/prefix.
4. **Balanced parentheses** — push opening brackets; on a closing bracket, pop and match.
5. **Function calls/recursion** — the run-time stack stores return addresses and locals.
6. **Undo/backtracking** — most recent action is undone first.
7. **DFS** — the recursive/iterative stack holds the path.
8. **Syntax parsing** — operator and operand stacks.

---

## Quick revision checklist — Unit 3

**Stack**

- [ ] LIFO; top; push/pop/peek/isEmpty/isFull.
- [ ] Array stack: `top = −1`; overflow at `MAX−1`; `O(1)`.
- [ ] Linked stack: push/pop at head; `O(1)`; no fixed capacity.
- [ ] Stack applications: reverse, conversion, evaluation, parentheses, recursion, DFS.
- [ ] Infix→postfix: precedence `^ > * / > + −`; `^` right-assoc.
- [ ] Stack trace for `(a−b)/((c*d)−(e+f))` = `ab−cd*ef+−/`.
- [ ] `p*q^r−s/t+(u/v)` = `pqr^*st/−uv/+`.
- [ ] `(A+B*C−D)/(E+F)` postfix = `ABC*+D−EF+/`.
- [ ] Infix→prefix: scan right to left; strict `>`; reverse output.
- [ ] `A/B^C+D*E−F*G` prefix = `−+/A^BC*DE*FG`.
- [ ] Postfix evaluation: pop right then left; `6523+8*+3+* = 288`.
- [ ] `AB+CD*+E− = 19`; `ABC^*DE/−FG/+ = 50`.
- [ ] Expression tree: operands leaves, operators internal; inorder = infix, postorder = postfix, preorder = prefix.
- [ ] Build expression tree with a stack of nodes.

**Queue**

- [ ] FIFO; enqueue at rear, dequeue at front.
- [ ] Linear queue array: `front=0, rear=−1`; full at `rear=MAX−1`; false overflow.
- [ ] Circular queue: `rear=(rear+1)%MAX`; full `(rear+1)%MAX==front`; empty `front==−1`.
- [ ] Circular queue: `front == −1` empty; all `MAX` slots usable; full when `(rear+1)%MAX == front`.
- [ ] Queue using linked list: `front` and `rear`; both operations `O(1)`.
- [ ] Priority queue: delete highest priority; heap gives `O(log n)`.
- [ ] DEQueue: insert/delete at both ends.
- [ ] Input-restricted: insert rear only; delete both.
- [ ] Output-restricted: delete front only; insert both.
- [ ] Reverse queue using stack.
- [ ] Reverse first `k`: dequeue k → stack → enqueue → rotate `n−k`.
- [ ] Interleave halves with two auxiliary queues.
- [ ] Stack using queue: push enqueues then rotates; pop dequeues.
- [ ] `PUSH 5,10,15; POP; PUSH 20,25,30; POP` outputs 15 and 30.
