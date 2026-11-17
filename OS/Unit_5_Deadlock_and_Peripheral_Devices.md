# Unit 5 — Deadlock and Peripheral Devices

> Syllabus: principles of deadlock, deadlock prevention, avoidance, detection, Dining Philosopher problem; peripheral device management: programmed I/O, interrupt-driven I/O, DMA.

---

## 5.1 Deadlock and the four necessary conditions **[5]**

**Asked in:** P1 IT-F24 Q1(b); P2 AIML-F24 Q1(c); P5 IT-F25 Q1(B); P7 AIML-R25 Q3(B)(ii) (first half); P3 IoT-F24 Q4(B)(2) (first half).

**Questions (verbatim):**
> Explain the four necessary conditions for the occurrence of deadlock.

> Explain deadlock and the four necessary conditions for deadlock to occur.

> Explain the four necessary conditions required for a deadlock to occur. Describe why all four conditions must hold simultaneously for a deadlock to take place.

> Explain the four necessary conditions for deadlock. Illustrate each condition with a practical example from an operating system. how to prevent Deadlock?

> What is Deadlock? List the conditions that lead to deadlock. Analyze How Deadlock can be prevented with the help of example?

**Answer**

A **deadlock** is a state in which a set of processes are permanently blocked, each waiting for a resource held by another process in the set. No process can proceed, so the system makes no progress.

**Coffman conditions — all four must hold simultaneously**

| Condition | Meaning | OS example |
|---|---|---|
| **Mutual exclusion** | At least one resource is non-shareable; only one process can hold it | A printer or a file opened for writing |
| **Hold and wait** | A process holds at least one resource while waiting for another | Process holds a scanner and waits for a printer |
| **No pre-emption** | A resource cannot be forcibly taken away; it must be released voluntarily | The OS cannot snatch a locked file from a process |
| **Circular wait** | A closed chain `P0 → P1 → … → Pn → P0`, where each waits for a resource held by the next | P0 holds A and waits for B; P1 holds B and waits for A |

**Why all four:** if any one is broken, the cycle cannot close:

- No mutual exclusion → processes share the resource, no one waits.
- No hold-and-wait → a process releases what it holds before requesting more.
- Pre-emption allowed → the OS can take a resource and break the wait.
- No circular wait → there is a linear order, so some process can always finish.

**Resource-allocation graph (RAG):** processes are circles, resources are rectangles with one dot per instance; a request edge goes `Pi → Rj`, an assignment edge `Rj → Pi`. A cycle is necessary for deadlock; for single-instance resources, a cycle is sufficient.

---

## 5.2 Deadlock prevention **[5–10]**

**Asked in:** P3 IoT-F24 Q4(B)(2) (second half); P7 AIML-R25 Q3(B)(ii) (second half).

**Questions (verbatim — prevention part of the questions quoted in 5.1):**
> What is Deadlock? List the conditions that lead to deadlock. Analyze How Deadlock can be prevented with the help of example?

> Explain the four necessary conditions for deadlock. Illustrate each condition with a practical example from an operating system. how to prevent Deadlock?

**Answer**

Prevention attacks one of the four Coffman conditions so that deadlock is **structurally impossible**.

| Condition attacked | Technique | Example / cost |
|---|---|---|
| **Mutual exclusion** | Make resources shareable (read-only files, spooling) | Not possible for printers/tape drives; spooling can serialize |
| **Hold and wait** | Require a process to request **all** resources before it starts, or release all before requesting more | Low resource utilization; starvation; may not know future needs |
| **No pre-emption** | If a request cannot be granted, pre-empt (take back) resources from the holder; roll back the victim | Works for CPU/memory; hard for printers/files; rollback cost |
| **Circular wait** | Impose a **total ordering** on resource types; a process may request resources only in increasing order | Simple and practical; requires global numbering; may be restrictive |

**Ordering example:** number resources `R1 < R2 < R3`. A process may request `R1`, then `R2`, then `R3`, but never `R2` then `R1`. Therefore no cycle `P0 holds R2 waits R1` can coexist with `P1 holds R1 waits R2`.

**Other practical prevention:** lock ordering in code, `trylock` with timeout/backoff, single global lock (coarse), resource reservation, spooling.

**Prevention vs avoidance vs detection**

| Approach | Idea | When |
|---|---|---|
| Prevention | Break one condition statically | Design time |
| Avoidance | Grant a request only if the system remains safe (Banker’s) | Run time |
| Detection + recovery | Let deadlock happen, detect it, recover | Run time |

---

## 5.3 Deadlock avoidance — Banker’s algorithm and resource-request algorithm **[5–10]**

**Asked in:** P2 AIML-F24 Q4(a); plus the four Banker’s numericals in 5.4–5.7.

**Question (verbatim):**
> Explain Banker's Resource Request Algorithm with simple example.

**Answer**

**Safe state:** there exists at least one order in which every process can finish using the currently available resources plus the resources released by processes that finish earlier. **Unsafe state** does not necessarily mean deadlock, but the OS cannot guarantee that deadlock will not occur.

**Data structures** (n processes, m resource types):

```text
Available[j]      = number of free instances of resource j
Max[i][j]         = maximum demand of process i for resource j
Allocation[i][j]  = currently allocated to process i
Need[i][j]        = Max[i][j] − Allocation[i][j]
```

**Safety algorithm**

```text
Work = Available; Finish[i] = false for all i
repeat:
    find an i with Finish[i] == false and Need[i] <= Work (component-wise)
    if found:
        Work = Work + Allocation[i]
        Finish[i] = true
    else break
if all Finish[i] == true: safe, and the order is a safe sequence
else: unsafe
```

**Resource-request algorithm** for process `Pi` requesting `Request[i]`

```text
1. If Request[i] > Need[i]: error (exceeds maximum claim)
2. If Request[i] > Available: Pi must wait
3. Pretend to grant:
       Available   = Available − Request[i]
       Allocation[i] = Allocation[i] + Request[i]
       Need[i]     = Need[i] − Request[i]
4. Run the safety algorithm on the new state.
   If safe: grant the request.
   If unsafe: roll back (restore the old values) and make Pi wait.
```

**Simple example** (2 processes, 2 resources):

```text
Available = (3, 3)
P1: Allocation (1,0), Max (2,1) → Need (1,1)
P2: Allocation (1,1), Max (3,3) → Need (2,2)
P1 requests (1,0)
```

1. `(1,0) ≤ Need(P1) = (1,1)` → OK.
2. `(1,0) ≤ Available = (3,3)` → OK.
3. Tentative state: `Available = (2,3)`, `Allocation(P1) = (2,0)`, `Need(P1) = (0,1)`.
4. Safety: P1 need `(0,1) ≤ (2,3)` → P1 finishes, `Work = (4,3)`; P2 need `(2,2) ≤ (4,3)` → P2 finishes. **Safe → grant the request.**

If the tentative state had been unsafe, the OS would restore `Available`, `Allocation` and `Need` and make P1 wait. The four full numericals are next.

---

## 5.4 Banker’s Set 1 — IT-F24 (Need matrix + safe state) **[10]**

**Asked in:** P1 IT-F24 Q2(b).

**Question (verbatim):**
> Consider the following snapshot of a system:
>
> | Process | Allocation (A B C D) | Max (A B C D) | Available (A B C D) |
> |---|---|---|---|
> | P0 | 0 0 1 2 | 0 0 1 2 | 1 5 2 0 |
> | P1 | 1 0 0 0 | 1 7 5 0 | |
> | P2 | 1 3 5 4 | 2 3 5 6 | |
> | P3 | 0 6 3 2 | 0 6 5 2 | |
> | P4 | 0 0 1 4 | 0 6 5 6 | |
>
> Answer the following questions using the banker's algorithm:
> a. What is the content of the matrix Need?
> b. Is the system in a safe state?

**Need = Max − Allocation**

| Process | Allocation | Max | Need |
|---|---|---|---|
| P0 | 0 0 1 2 | 0 0 1 2 | **0 0 0 0** |
| P1 | 1 0 0 0 | 1 7 5 0 | **0 7 5 0** |
| P2 | 1 3 5 4 | 2 3 5 6 | **1 0 0 2** |
| P3 | 0 6 3 2 | 0 6 5 2 | **0 0 2 0** |
| P4 | 0 0 1 4 | 0 6 5 6 | **0 6 4 2** |

**Safety check** (Available = `1 5 2 0`)

| Step | Process | Need ≤ Work? | Work after finish |
|---|---|---|---|
| 1 | P0 | `0 0 0 0 ≤ 1 5 2 0` | `1 5 3 2` |
| 2 | P2 | `1 0 0 2 ≤ 1 5 3 2` | `2 8 8 6` |
| 3 | P3 | `0 0 2 0 ≤ 2 8 8 6` | `2 14 11 8` |
| 4 | P4 | `0 6 4 2 ≤ 2 14 11 8` | `2 14 12 12` |
| 5 | P1 | `0 7 5 0 ≤ 2 14 12 12` | `3 14 12 12` |

All processes finish → **safe**. One safe sequence is **`P0 → P2 → P3 → P4 → P1`**, which matches the official key. Other safe sequences also exist (e.g. `P0 → P2 → P3 → P1 → P4`); any valid one is acceptable if the work table is shown.

---

## 5.5 Banker’s Set 2 — IT-F25 (paper’s table is unsafe; request denied) **[10]**

**Asked in:** P5 IT-F25 Q2(B).

**Question (verbatim):**
> A system has 5 processes (P0–P4) and 3 resource types (A, B, C). The system snapshot is as follows:
>
> | Process | Allocation (A B C) | Max (A B C) | Available (A B C) |
> |---|---|---|---|
> | P0 | 0 0 1 | 7 5 3 | 3 3 2 |
> | P1 | 2 0 0 | 3 2 2 | |
> | P2 | 3 0 2 | 9 0 2 | |
> | P3 | 2 1 1 | 2 2 2 | |
> | P4 | 0 0 2 | 4 3 3 | |
>
> (a) Apply the Banker’s algorithm to check if the system is in a safe state.
> (b) If process P2 requests (1, 0, 0), determine whether the request can be safely granted.

**Need = Max − Allocation**

| Process | Allocation | Max | Need |
|---|---|---|---|
| P0 | 0 0 1 | 7 5 3 | **7 5 2** |
| P1 | 2 0 0 | 3 2 2 | **1 2 2** |
| P2 | 3 0 2 | 9 0 2 | **6 0 0** |
| P3 | 2 1 1 | 2 2 2 | **0 1 1** |
| P4 | 0 0 2 | 4 3 3 | **4 3 1** |

**(a) Safety check** (Available = `3 3 2`)

| Step | Process | Need ≤ Work? | Work after finish |
|---|---|---|---|
| 1 | P1 | `1 2 2 ≤ 3 3 2` | `5 3 2` |
| 2 | P3 | `0 1 1 ≤ 5 3 2` | `7 4 3` |
| 3 | P2 | `6 0 0 ≤ 7 4 3` | `10 4 5` |
| 4 | P4 | `4 3 1 ≤ 10 4 5` | `10 4 7` |
| 5 | P0 | `7 5 2 ≤ 10 4 7` → **B: 5 > 4** | — |

P0 cannot finish. The reason is visible from the totals: **total B in the system = ΣAllocation(B) + Available(B) = 1 + 3 = 4**, but P0 needs **5** B. No sequence can create the fifth B. Therefore the system is **not in a safe state**.

**(b) Request P2 → (1,0,0)**

1. `(1,0,0) ≤ Need(P2) = (6,0,0)` → OK.
2. `(1,0,0) ≤ Available = (3,3,2)` → OK.
3. Tentative state:
   - `Available = (2,3,2)`
   - `Allocation(P2) = (4,0,2)`
   - `Need(P2) = (5,0,0)`
4. Safety check: P1 and P3 can still finish, P2 can finish, P4 can finish, but P0 still needs 5 B and the total B is still 4. The tentative state is **unsafe**.
5. Therefore the request **cannot be safely granted**; P2 must wait.

> **Flag:** the paper prints `P0 Allocation = (0,0,1)`. The standard textbook version of this problem has `P0 Allocation = (0,1,0)`, which would give total B = 5 and a safe sequence `P1 → P3 → P4 → P0 → P2`. The official key follows the printed table and says “not safe / deny”, so this answer does the same. If your teacher uses the textbook table, mention both.

---

## 5.6 Banker’s Set 3 — IT-R25 (Need matrix + safe sequence) **[10]**

**Asked in:** P6 IT-R25 Q2(b).

**Question (verbatim):**
> Considering a system with five processes P0 through P4 and three resources of type A, B, C. Resource type A has 10 instances, B has 5 instances and type C has 7 instances. Suppose at time t0 following snapshot of the system has been taken:
>
> | Process | Allocation (A B C) | Max (A B C) | Available (A B C) |
> |---|---|---|---|
> | P0 | 0 1 0 | 7 5 3 | 3 3 2 |
> | P1 | 2 0 0 | 3 2 2 | |
> | P2 | 3 0 2 | 9 0 2 | |
> | P3 | 2 1 1 | 2 2 2 | |
> | P4 | 0 0 2 | 4 3 3 | |
>
> 1. What will be the content of the Need matrix?
> 2. Is the system in a safe state? If Yes, then what is the safe sequence?

**Need = Max − Allocation**

| Process | Allocation | Max | Need |
|---|---|---|---|
| P0 | 0 1 0 | 7 5 3 | **7 4 3** |
| P1 | 2 0 0 | 3 2 2 | **1 2 2** |
| P2 | 3 0 2 | 9 0 2 | **6 0 0** |
| P3 | 2 1 1 | 2 2 2 | **0 1 1** |
| P4 | 0 0 2 | 4 3 3 | **4 3 1** |

This is the standard textbook table (P0 allocation is `0 1 0`, not `0 0 1`). Total resources: A = 10, B = 5, C = 7; the allocation sums are A = 7, B = 2, C = 5, so `Available = (3,3,2)` is consistent.

**Safety check**

| Step | Process | Need ≤ Work? | Work after finish |
|---|---|---|---|
| 1 | P1 | `1 2 2 ≤ 3 3 2` | `5 3 2` |
| 2 | P3 | `0 1 1 ≤ 5 3 2` | `7 4 3` |
| 3 | P4 | `4 3 1 ≤ 7 4 3` | `7 4 5` |
| 4 | P0 | `7 4 3 ≤ 7 4 5` | `7 5 5` |
| 5 | P2 | `6 0 0 ≤ 7 5 5` | `10 5 7` |

All finish → **safe**. One safe sequence is **`P1 → P3 → P4 → P0 → P2`**. Other valid sequences include `P1 → P3 → P4 → P2 → P0` and `P3 → P1 → P4 → P0 → P2`.

---

## 5.7 Banker’s Set 4 — AIML-R25 (available vector, Need, three safe sequences) **[10]**

**Asked in:** P7 AIML-R25 Q3(B)(i).

**Question (verbatim):**
> Consider the following snapshot of a system.
> If the total available resources for R1=9, R2=3 and R3=6,
>
> | | Allocation Matrix | | | Claim Matrix | | |
> |---|---|---|---|---|---|---|
> | | R1 | R2 | R3 | R1 | R2 | R3 |
> | P1 | 1 | 0 | 0 | 3 | 2 | 2 |
> | P2 | 6 | 1 | 2 | 6 | 1 | 3 |
> | P3 | 2 | 1 | 1 | 3 | 1 | 4 |
> | P4 | 0 | 0 | 2 | 4 | 2 | 2 |
>
> Apply banker’s algorithm and solve the following.
> 1. Calculate the available vector
> 2. Find need matrix
> 3. Is the system in a safe state? If yes find and write any three safe sequences

**1. Available vector**

The question gives **total instances**, not free instances:

```text
Total       = (9, 3, 6)
ΣAllocation = (1+6+2+0, 0+1+1+0, 0+2+1+2) = (9, 2, 5)
Available   = Total − ΣAllocation = (0, 1, 1)
```

**2. Need matrix** (`Need = Claim − Allocation`)

| Process | Allocation | Claim | Need |
|---|---|---|---|
| P1 | 1 0 0 | 3 2 2 | **2 2 2** |
| P2 | 6 1 2 | 6 1 3 | **0 0 1** |
| P3 | 2 1 1 | 3 1 4 | **1 0 3** |
| P4 | 0 0 2 | 4 2 2 | **4 2 0** |

**3. Safety check** (Available = `0 1 1`)

Only P2 fits initially (`0 0 1 ≤ 0 1 1`).

| Step | Process | Need ≤ Work? | Work after finish |
|---|---|---|---|
| 1 | P2 | `0 0 1 ≤ 0 1 1` | `6 2 3` |
| 2 | P1 | `2 2 2 ≤ 6 2 3` | `7 2 3` |
| 3 | P3 | `1 0 3 ≤ 7 2 3` | `9 3 4` |
| 4 | P4 | `4 2 0 ≤ 9 3 4` | `9 3 6` |

All finish → **safe**.

**Three safe sequences:**

1. **`P2 → P1 → P3 → P4`**
2. **`P2 → P3 → P1 → P4`**
3. **`P2 → P1 → P4 → P3`**

Check one: after P2, Work = `6 2 3`; P1 → `7 2 3`; P4 (`4 2 0`) → `7 2 5`; P3 (`1 0 3`) → `9 3 6`. Valid.

---

## 5.7A Banker’s Set 5 — Jan-24 (Need matrix + safe order) **[10]**

**Asked in:** P8 Jan-24 Q2(1)(1).

**Question (verbatim):**
> Consider the following snapshot of the system:
>
> | Process | Allocation (A B C D) | Max (A B C D) | Available (A B C D) |
> |---|---|---|---|
> | P0 | 0 1 1 0 | 0 2 1 0 | 1 5 2 0 |
> | P1 | 1 2 3 1 | 1 6 5 2 | |
> | P2 | 1 3 6 5 | 2 3 6 6 | |
> | P3 | 0 6 3 2 | 0 6 5 2 | |
> | P4 | 0 0 1 4 | 0 6 5 6 | |
> | **Total** | **2 12 14 12** | | |
>
> Apply Banker’s Algorithm:
> 1. Find the need matrix.
> 2. Is the system in safe state.? If yes, mention the safe order in which the resources could be allotted.

**Need = Max − Allocation**

| Process | Allocation | Max | Need |
|---|---|---|---|
| P0 | 0 1 1 0 | 0 2 1 0 | **0 1 0 0** |
| P1 | 1 2 3 1 | 1 6 5 2 | **0 4 2 1** |
| P2 | 1 3 6 5 | 2 3 6 6 | **1 0 0 1** |
| P3 | 0 6 3 2 | 0 6 5 2 | **0 0 2 0** |
| P4 | 0 0 1 4 | 0 6 5 6 | **0 6 4 2** |

**Safety check** (Available = `1 5 2 0`)

| Step | Process | Need ≤ Work? | Work after finish |
|---|---|---|---|
| 1 | P0 | `0 1 0 0 ≤ 1 5 2 0` | `1 6 3 0` |
| 2 | P3 | `0 0 2 0 ≤ 1 6 3 0` | `1 12 6 2` |
| 3 | P4 | `0 6 4 2 ≤ 1 12 6 2` | `1 12 7 6` |
| 4 | P1 | `0 4 2 1 ≤ 1 12 7 6` | `2 14 10 7` |
| 5 | P2 | `1 0 0 1 ≤ 2 14 10 7` | `3 17 16 12` |

All processes finish → **safe**. One valid safe order is **`P0 → P3 → P4 → P1 → P2`**. Other safe orders exist (e.g. `P0 → P3 → P2 → P4 → P1`); any valid one is acceptable if the work table is shown.

---

## 5.8 Deadlock detection using resource-allocation graphs **[5]**

**Asked in:** P4 AIML-R24 Q1(c); P8 Jan-24 Q2(1)(2).

**Questions (verbatim):**
> Describe how resource allocation graphs can be used to detect deadlocks. Explain with example.

> Consider a system with 3 process (P1,P2 and P3 ) and 2 resources (R1 and R2). There is 1 instance of R1 and 2 instances of R2. 1 Instance of R1 is held by P1 , 1 instance of R2 is held by P2 and 1 instance of R2 is held by P3. P1 is requesting 1 instance of R2 and P2 is requesting 1 instance of R1. Draw the resource allocation graph for the given scenario. Find if the system is in a deadlock state otherwise find a safe sequence.

**Answer**

A **resource-allocation graph (RAG)** has:

- **Process nodes** (circles) `P1, P2, …`
- **Resource nodes** (rectangles) `R1, R2, …`, each with one dot per instance
- **Request edge** `Pi → Rj` (process waits for resource)
- **Assignment edge** `Rj → Pi` (instance allocated to process)

**Detection rules**

| Graph | Deadlock? |
|---|---|
| No cycle | No deadlock |
| Cycle, **single instance** per resource | Deadlock (cycle is necessary and sufficient) |
| Cycle, **multiple instances** | Possible deadlock; must run the detection algorithm |

**Example (single instance)**

```text
P1 → R1 → P2 → R2 → P1
```

- P1 holds R1 and requests R2.
- P2 holds R2 and requests R1.
- The cycle `P1 → R2 → P2 → R1 → P1` means both wait forever → **deadlock**.

**Example (multiple instances)** — a cycle may not be deadlock if another instance can satisfy a request. Run:

```text
Work = Available
Finish[i] = false for all i
repeat:
    find Pi with Finish[i] == false and Request[i] <= Work
    if found: Work = Work + Allocation[i]; Finish[i] = true
    else break
if some Finish[i] == false: those processes are deadlocked
```

**Jan-24 worked example — 3 processes, 2 resources**

Given:

- `R1` has **1** instance; it is held by **P1**.
- `R2` has **2** instances; one is held by **P2**, one by **P3**.
- `P1` requests one instance of `R2`.
- `P2` requests one instance of `R1`.

RAG edges:

```text
R1 → P1          (P1 holds R1)
R2 → P2          (P2 holds one R2)
R2 → P3          (P3 holds the other R2)
P1 → R2          (P1 waits for R2)
P2 → R1          (P2 waits for R1)
```

There is a cycle:

```text
P1 → R2 → P2 → R1 → P1
```

**Is it a deadlock?** Not necessarily, because `R2` has **two instances**. `P3` holds the second instance but is **not waiting** for anything; it can finish and release its `R2`. Then:

1. P3 finishes → one `R2` instance becomes free.
2. P1 gets `R2`, finishes, releases `R1`.
3. P2 gets `R1`, finishes.

**Safe sequence:** `P3 → P1 → P2`. No deadlock, **provided P3 eventually releases its R2**. If P3 is also blocked forever (never releases), then the cycle becomes a deadlock. This is exactly why a cycle in a multi-instance RAG is not sufficient proof of deadlock.

**Recovery after detection:** abort all deadlocked processes, abort one at a time until the cycle breaks, or pre-empt resources (with rollback). Victim selection should consider priority, CPU time, resources held and remaining work to avoid starvation.

---

## 5.9 Deadlock recovery **[5]**

**Asked in:** P6 IT-R25 Q1(b).

**Question (verbatim):**
> Discuss the methods used in deadlock recovery.

**Answer**

Once detection finds a deadlock, the OS must break it.

**(a) Process termination**

1. **Abort all deadlocked processes** — simple, but loses a lot of work.
2. **Abort one process at a time** — after each abort, re-run detection; continue until the cycle disappears. Overhead is repeated detection, but less work is lost.
3. **Victim selection** — choose by:
   - priority of the process,
   - CPU time consumed / remaining time,
   - resources held and needed,
   - whether the process is interactive or batch,
   - number of times it has already been rolled back (avoid starvation).

**(b) Resource pre-emption**

- Take a resource from a victim and give it to another process.
- The victim may need to **roll back** to a checkpoint (or restart).
- Issues: choosing a victim, rollback cost, starvation (use a bounded number of pre-emptions or aging).

**(c) Rollback**

- **Total rollback:** abort the process and restart it.
- **Checkpoint rollback:** restore the process to a saved checkpoint, releasing later resources.

**(d) Ignore / manual**

- The “ostrich algorithm”: ignore deadlock if it is rare; reboot or let the operator kill a process. Used by some general-purpose OSs for simplicity.

**Prevention of recurrence:** lock ordering, timeouts, `trylock`, resource ordering, reducing hold-and-wait.

---

## 5.10 Dining Philosophers problem and monitor solution **[5–10]**

**Asked in:** P4 AIML-R24 Q4(a).

**Question (verbatim):**
> Describe the Dining Philosophers Problem and its solution using monitor.

**Answer**

**Problem:** five philosophers sit around a circular table. Between each pair is one chopstick (five chopsticks total). A philosopher alternates between thinking and eating; to eat, they need **both** the left and right chopstick.

**Naive semaphore solution**

```c
semaphore chopstick[5] = {1,1,1,1,1};

Philosopher i:
    wait(chopstick[i]);            // left
    wait(chopstick[(i+1)%5]);      // right
    eat();
    signal(chopstick[i]);
    signal(chopstick[(i+1)%5]);
    think();
```

**Deadlock:** if all five pick up their left chopstick simultaneously, each holds one and waits for the right one → circular wait.

**Monitor solution**

```text
monitor DiningPhilosophers {
    enum { THINKING, HUNGRY, EATING } state[5];
    condition self[5];

    procedure pickup(int i) {
        state[i] = HUNGRY;
        test(i);
        if (state[i] != EATING)
            self[i].wait();
    }

    procedure putdown(int i) {
        state[i] = THINKING;
        test((i + 4) % 5);       // left neighbour
        test((i + 1) % 5);       // right neighbour
    }

    procedure test(int i) {
        if (state[(i + 4) % 5] != EATING &&
            state[i] == HUNGRY &&
            state[(i + 1) % 5] != EATING) {
            state[i] = EATING;
            self[i].signal();
        }
    }
}
```

A philosopher eats only when **both neighbours are not eating**. The monitor guarantees mutual exclusion on the state array, so the “pick both forks” decision is atomic. This prevents the circular wait that causes deadlock.

**Other solutions**

1. **At most four at the table:** a counting semaphore `room = 4`; at least one philosopher can always get both forks.
2. **Odd–even:** odd philosophers pick left then right; even pick right then left. This breaks the symmetric cycle.
3. **Atomic pick-up:** request both forks in one critical section (monitor or `wait` on a mutex); if either is unavailable, release and retry.
4. **Asymmetric:** one philosopher picks right first, the rest pick left first.

**Starvation:** even without deadlock, a philosopher may never get a turn. Solutions: FIFO ordering, aging, or a fair monitor queue.

---

## 5.11 I/O transfers — programmed I/O, interrupt-driven I/O, DMA **[5–10]**

**Asked in:** P5 IT-F25 Q4(A)(i) (programmed I/O); P1 IT-F24 Q4(a)(i) (DMA vs interrupt-driven).

**Questions (verbatim):**
> Describe the working mechanism of programmed I/O data transfer.

> Differentiate between DMA and Interrupt-Driven I/O.

**Answer**

### (a) Programmed I/O (polling)

1. The CPU executes a program that issues a command to the device controller (read/write, address, count).
2. The CPU repeatedly reads the controller’s **status register** and waits until the device is ready.
3. When ready, the CPU reads/writes one data word through the data register.
4. It updates the address/count and repeats until the whole block is transferred.

```text
CPU: issue command
loop: read status
      if not ready: goto loop
      transfer one word
      update address/count
      if count > 0: goto loop
```

- **Advantage:** simple, no interrupt hardware.
- **Disadvantage:** CPU is busy-waiting (“polling”) and cannot do useful work; very inefficient for slow devices and large blocks.

### (b) Interrupt-driven I/O

1. The CPU issues a command and continues executing other work.
2. The device controller interrupts the CPU when it is ready for the next word or when the operation completes.
3. The CPU saves its state, runs the interrupt service routine (ISR), transfers one word, and returns.
4. The cycle repeats for each word/block.

- **Advantage:** CPU is not busy-waiting; better utilization than polling.
- **Disadvantage:** an interrupt per word/block causes overhead; still the CPU participates in every transfer.

### (c) DMA (Direct Memory Access)

1. The CPU programs the **DMA controller** with the memory address, block count, direction and device.
2. The DMA controller transfers the whole block directly between the device and memory, without CPU involvement.
3. It competes for the bus (“cycle stealing”) and interrupts the CPU only when the block is complete (or on error).
4. The CPU handles the completion interrupt and continues.

- **Advantage:** highest throughput for large transfers; CPU is free during the transfer.
- **Disadvantage:** extra hardware (DMA controller); bus contention; cache-coherence issues.

**Comparison**

| Point | Programmed I/O | Interrupt-driven I/O | DMA |
|---|---|---|---|
| CPU involvement | Busy-waits for every word | Interrupt per word/block | Only setup + completion |
| CPU utilization | Worst | Better | Best |
| Hardware | Simple | Interrupt controller | DMA controller |
| Data transfer | CPU ↔ device | CPU ↔ device (ISR) | Device ↔ memory directly |
| Best for | Very simple/slow devices | Moderate-speed devices | Disk, network, large blocks |
| Overhead | Polling loop | Interrupt overhead | Bus arbitration/cycle stealing |

---

## 5.12 Out-of-syllabus items in this unit

- **P3 IoT-F24 Q4(A): Program Counter and Stack Pointer** — computer-organization content; a short answer is in `00_Index.md` Appendix A3.
- **P3 IoT-F24 Q1(A): fixed-point arithmetic** — computer-organization content; solution in Appendix A1.
- **P4 AIML-R24 Q1(c): RAG** — included above because deadlock detection is in the syllabus.

---

## Unit 5 — quick revision checklist

- [ ] Deadlock definition + 4 Coffman conditions + one OS example each.
- [ ] Why all four must hold; RAG cycle rule (single vs multiple instances).
- [ ] Prevention: one technique per condition; resource ordering example.
- [ ] Avoidance: safe/unsafe state; Need = Max − Allocation; safety algorithm.
- [ ] Resource-request algorithm: 4 steps; rollback if unsafe.
- [ ] Banker’s: be able to compute Need, run the work table, list a safe sequence, and handle a request.
- [ ] Detection: RAG, cycle, single vs multiple instances, detection algorithm.
- [ ] Jan-24 RAG: cycle `P1→R2→P2→R1→P1` with 2 R2 instances → not deadlock if P3 releases; safe `P3→P1→P2`.
- [ ] Recovery: abort all/one, victim selection, pre-emption, rollback.
- [ ] Dining philosophers: naive deadlock, monitor code, 4 alternative solutions.
- [ ] Programmed I/O: polling loop; interrupt-driven: ISR per word; DMA: block transfer + completion interrupt.
- [ ] DMA vs interrupt-driven: 6 differences.
