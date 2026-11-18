# Unit 3 — Concurrency Control

> Syllabus: principles of concurrency, mutual exclusion (software approaches, hardware support), semaphores, monitors, classical problems: Readers–Writers and Producer–Consumer.

---

## 3.1 Principles of concurrency **[5]**

**Asked in:** P2 AIML-F24 Q3(a)(i).

**Question (verbatim):**
> Explain the various principles of concurrency.

**Answer**

**Concurrency** means several processes/threads are making progress during the same time interval. On a single CPU they are interleaved; on multiple cores they may run truly in parallel. Concurrency introduces shared state, and therefore several principles must be respected.

** starting point: independent vs cooperative processes**

| Type | Meaning |
|---|---|
| **Independent process** | Does not share any data with another process; it cannot affect or be affected by other processes |
| **Cooperative process** | Shares a variable, memory, code or resource (keyboard, mouse, CPU, etc.) with another process |

Race example: `shared = 5`; P1 executes `x = shared; x++; sleep(1); shared = x;` and P2 executes `y = shared; y--; sleep(1); shared = y;`. Ideally `shared` should remain 5, but the interleaving can produce **4** (or 6) — an inconsistent value. Hence concurrent processes need **synchronization**.

**Principles (summary)**

| Principle | Meaning |
|---|---|
| **Atomicity** | An operation either completes fully or not at all; no other thread can observe a half-updated state |
| **Mutual exclusion** | At most one process/thread executes a critical section on a shared resource at a time |
| **Critical-section discipline** | Entry/exit protocol must satisfy mutual exclusion, progress and bounded waiting |
| **Ordering / synchronization** | Operations that depend on each other must occur in the required order (e.g. produce before consume) |
| **Visibility** | A write by one thread must become visible to others; needs memory barriers/volatile where applicable |
| **Deadlock freedom** | No set of processes may wait forever in a cycle |
| **Starvation freedom** | Every process eventually gets a turn; fairness/aging |
| **Livelock freedom** | Processes must not keep changing state without making progress |
| **Thread safety** | Shared data structures must be safe under concurrent access (locks, immutable data, thread-local data) |
| **Non-blocking progress** | Some algorithms (lock-free/wait-free) guarantee system-wide progress even if one thread stalls |
| **Coordination** | Semaphores, monitors, message passing, condition variables |

**Why concurrency matters:** better CPU utilization, responsiveness and throughput; but it also brings race conditions, data inconsistency, deadlock, starvation and debugging difficulty.

---

## 3.2 Race condition and hardware fix **[10]**

**Asked in:** P7 AIML-R25 Q2(B)(i).

**Question (verbatim):**
> Explain with a simple program example how race conditions can happen and how to fix it using hardware approach.

**Answer**

A **race condition** occurs when two or more processes access shared data concurrently and the final result depends on the order of their interleaving.

**Example —  shared counter**

```text
shared = 5

P1:  x = shared;   x++;   sleep(1);   shared = x;
P2:  y = shared;   y--;   sleep(1);   shared = y;
```

If both read `shared = 5` before either writes, P1 writes 6 and P2 writes 4 (or P2 writes 4 and P1 writes 6). The final value depends on the order, and the intended value 5 is lost. This is a **race condition**: the final value of shared data depends on which process finishes last.

**Hardware fix — atomic test-and-set / swap**

A hardware instruction performs read-modify-write atomically (no interrupt or other CPU can interleave in the middle).

```c
// test-and-set returns the old value and sets the word to 1
int test_and_set(int *lock) {
    int old = *lock;
    *lock = 1;
    return old;           // atomic in hardware
}

// spin lock
while (test_and_set(&lock))
    ;                     // busy wait

/* critical section */
balance = balance - 100;

lock = 0;                 // release
```

Equivalent **compare-and-swap**:

```c
while (compare_and_swap(&lock, 0, 1) != 0)
    ;                     // spin until lock was 0 and is now 1
```

**Why it works:** the test and the set happen as one indivisible hardware operation. Only one process can observe `old == 0`; all others spin until the lock is released. On a multiprocessor, the instruction also enforces cache coherence/memory ordering. Variants: `xchg`, `LDREX/STREX` (ARM), `CMPXCHG` (x86). Hardware locks are the basis of spinlocks, semaphores and monitors.

**'s Swap instruction**

```c
void Swap(boolean *a, boolean *b) {
    boolean temp = *a;
    *a = *b;
    *b = temp;
}

// Shared lock initialized to FALSE; each process has a local key
lock = FALSE;
do {
    key = TRUE;
    while (key == TRUE)
        Swap(&lock, &key);
    /* critical section */
    lock = FALSE;
    /* remainder section */
} while (TRUE);
```

**'s TestAndSet/Swap properties**

- Two `TestAndSet()` (or two `Swap()`) instructions **cannot run in parallel on two different CPUs**.
- The instruction is executed **automatically** and **cannot be interrupted**.
- **Mutual exclusion** is guaranteed by hardware.
- **Progress** is achieved (no blocking).
- **Bounded waiting is not met**: P1 can repeatedly enter the critical section while P2 never gets a chance. The same limitation applies to both TestAndSet and Swap.

**Caveats:** busy waiting wastes CPU; a ticket lock or queue is needed for bounded waiting; disabling interrupts works only on a uniprocessor and only for short critical sections.

---

## 3.3 Critical section and mutual exclusion; software/hardware approaches **[5–10]**

**Asked in:** P1 IT-F24 Q2(a)(i); P4 AIML-R24 Q3(a)(ii); P7 AIML-R25 Q2(B)(ii) (part).

**Questions (verbatim):**
> Explain the concept of mutual exclusion with a suitable example.

> Discuss what is mutual exclusion, and why is it essential in synchronization? How to achieve it?

> Explain the terms: Mutual Exclusion, critical section, Livelock. Explain the role of semaphores in solving synchronization and apply the same to design a solution that controls access to a computer lab with limited systems.

**Answer**

A **critical section (CS)** is the part of a program that accesses a shared resource (variable, file, printer, buffer). **Mutual exclusion (mutex)** is the requirement that **at most one** process executes its CS for that resource at any time.

** structure of a process**

```text
do {
    entry section       // request permission to enter
    critical section    // access shared resource atomically
    exit section        // release the lock / undo entry
    remainder section   // rest of the program
} while (1);
```

- **Entry section:** decides whether the process may enter the CS.
- **Critical section:** code that accesses shared resources and must execute as an atomic action.
- **Exit section:** releases the lock taken in the entry section.
- **Remainder section:** the remaining part of the program.

**Why it is essential:** without it, interleaved reads/writes cause race conditions, inconsistent data, lost updates and corrupted files. Mutual exclusion serializes the CS, so the shared state changes atomically from the point of view of other processes.

**Example:** two threads update the same bank balance. Both read `1000`; one writes `900` (withdraw 100), the other writes `1200` (deposit 200). The final value is `1200`, losing the withdrawal. With a mutex, only one thread enters the update section at a time: the first completes its read-modify-write, then the second reads the new value, giving the correct `1100`. Locks, semaphores and monitors enforce this.

**Requirements for a correct CS solution**

1. **Mutual exclusion** — at most one process in the CS.
2. **Progress** — if no process is in the CS and some want to enter, one must be allowed to enter; a process outside the CS cannot block others.
3. **Bounded waiting** — there is a limit on how many times others can enter before a waiting process gets its turn (no starvation).

**Software approaches**

| Approach | Idea | Problem |
|---|---|---|
| **Strict alternation** (`turn`) | Only the process whose turn it is may enter | Fails progress if the other process is not ready |
| **Peterson’s algorithm** (2 processes) | `flag[i] = true; turn = j; while (flag[j] && turn == j);` | Correct for 2 processes; busy waiting |
| **Dekker’s algorithm** | Combines `flag` and `turn` | Correct for 2 processes; complex |
| **Bakery algorithm** (n processes) | Each process takes a ticket; lowest ticket enters; ties by PID | Correct, bounded waiting; ticket overflow and busy waiting |

**Peterson’s algorithm (2 processes)**

```c
flag[0] = flag[1] = false;
turn = 0;

// process i, other j = 1-i
do {
    flag[i] = true;
    turn = j;
    while (flag[j] && turn == j)
        ;                 // busy wait
    /* critical section */
    flag[i] = false;
    /* remainder section */
} while (1);
```

**Peterson's solution:** it is restricted to **two processes** that alternate between their critical and remainder sections. It uses two shared data items: `int turn` (whose turn it is to enter) and `boolean flag[2]` (whether a process is ready to enter). It satisfies **mutual exclusion, progress and bounded waiting**, and is a classic algorithmic description — but it **may not work correctly on modern computer architectures** because of instruction reordering/memory consistency.

**Hardware approaches**

| Approach | Mechanism | Notes |
|---|---|---|
| **Disable interrupts** | Kernel disables interrupts around CS | Only uniprocessor; unsafe if CS is long |
| **Test-and-set** | Atomically returns old value and sets lock | Simple spinlock; need bounded waiting |
| **Compare-and-swap (CAS)** | Atomically compares and swaps | Lock-free building block |
| **Atomic exchange** | Swaps register and memory atomically | Spinlock |
| **Load-linked / store-conditional** | LL loads, SC stores only if no intervening write | Used on ARM/PowerPC |

**Semaphore / mutex solution**

```c
semaphore mutex = 1;
wait(mutex);
/* critical section */
signal(mutex);
```

**Computer-lab design (P7 Q2B ii)**

A lab has `N` systems (say `N = 10`). Model each system as one unit of a counting semaphore:

```c
semaphore lab = N;        // N available systems
semaphore booking = 1;    // protects the booking list, if any

Student:
    wait(lab);            // if lab > 0, take a system; else sleep
    wait(booking);
        allocate a system / update log;
    signal(booking);
    use the system;
    wait(booking);
        release the system / update log;
    signal(booking);
    signal(lab);          // wake a waiting student
```

- If `lab = 0`, a student blocks instead of busy-waiting.
- `booking` gives mutual exclusion for the allocation list.
- The order `wait(lab)` before `wait(booking)` avoids a deadlock where a student holds the booking lock while waiting for a system.

**Livelock:** the processes are not blocked, but they keep changing state in response to each other and **never make progress**. Example: two people in a corridor step left, then right, then left, forever. Unlike deadlock, the CPU may be busy. Fix: randomized backoff, priority, or a tie-breaking rule.

---

## 3.4 Semaphores — definition, types, implementation and mutual exclusion **[5–10]**

**Asked in:** P2 AIML-F24 Q3(a)(ii); P3 IoT-F24 Q4(B)(1); P6 IT-R25 Q2(a)(i); P7 AIML-R25 Q2(B)(ii) (part); P8 Jan-24 Q4(3).

**Questions (verbatim):**
> What is semaphore? Explain its types.

> What are the semaphores, and how they are used to implement mutual exclusion?

> Explain how semaphores are implemented to solve synchronization problem.

> Analyze which type of semaphore is most suitable for controlling access to shared resources by concurrent processes.

**Answer**

A **semaphore** is an integer synchronization variable that is accessed only through two standard atomic operations: **wait()** and **signal()**. The classic names are **P** (from Dutch *proberen*, “to test”) and **V** (from Dutch *verhogen*, “to increment”).

**Definition (busy-wait version)**

```text
P(Semaphore S) {          // wait()
    while (S <= 0)
        ;                 // no operation (busy wait)
    S--;
}

V(Semaphore S) {          // signal()
    S++;
}
```

All modifications to the integer value in `wait()` and `signal()` must be executed **indivisibly**: when one process modifies the semaphore value, no other process can simultaneously modify the same value. This is the busy-wait version; use it when the question asks for the basic semaphore definition.

**OS implementation (blocking version — standard clarification)**

```text
wait(S):   S = S - 1;
           if (S < 0) block this process and put it in S's queue;

signal(S): S = S + 1;
           if (S <= 0) wake up one process from S's queue;
```

A real OS normally avoids busy waiting by blocking the process and moving it to the semaphore's wait queue; `signal` wakes one waiter. Note that the counting example treats the semaphore as a **signed counter**: `7 − 20 = −13`, then `−13 + 18 = 5`. That matches the blocking implementation's negative-value convention; the busy-wait `while (S <= 0)` version would instead keep the waiter spinning and would not decrement below zero.

**Types **

| Type | Range | Use |
|---|---|---|
| **Binary semaphore (mutex)** | 0 or 1 | Mutual exclusion for one resource/CS; on some systems called a mutex lock |
| **Counting semaphore** | Unrestricted domain (0…N, or negative when waiters are queued) | Controls access to a resource with **multiple instances** (printers, lab systems, buffer slots) |

**Solved examples**

1. A counting semaphore is initialized to **12**. Then 10 `P` (wait) and 4 `V` (signal) operations are performed:
   `S = 12 − 10 + 4 = 6`.
2. At a particular time the value of a counting semaphore is **7**. Then 20 `P` operations and `x` `V` operations are completed, and the final value is **5**:
   `7 − 20 + x = 5` → `x = 18`.

**Important note on the three requirements:** a semaphore achieves **mutual exclusion** and **progress**, but **bounded waiting is not guaranteed** — one process can keep re-entering the critical section while another never gets a chance. A FIFO queue/ticket lock is needed to guarantee bounded waiting.

**Mutual exclusion with a binary semaphore**

```c
semaphore mutex = 1;

do {
    wait(mutex);          // enter
    /* critical section */
    signal(mutex);        // leave
    /* remainder section */
} while (1);
```

Only one process can decrement `mutex` from 1 to 0; the next process blocks until the first calls `signal`.

**Ordering / synchronization**

```c
// Want statement S1 in P1 to execute before S2 in P2
semaphore sync = 0;

P1: S1; signal(sync);
P2: wait(sync); S2;
```

**Implementation**

- The semaphore value and a queue of blocked processes are kept in the kernel.
- `wait`/`signal` must be **atomic**: the OS disables interrupts briefly on a uniprocessor, or uses a hardware atomic instruction/spinlock on a multiprocessor.
- If `wait` finds the value negative, the process is moved to the semaphore’s **wait queue** and the scheduler runs another process (blocking, not busy waiting).
- `signal` increments the value; if there are waiters, one is moved to the ready queue.

**Problems:** incorrect order of `wait` operations causes deadlock; a missing `signal` causes permanent blocking; priority inversion; semaphores are low-level and error-prone (unlike monitors).

---

## 3.4A Which semaphore type is most suitable for shared resources? **[5]**

**Asked in:** P8 Jan-24 Q4(3).

**Question (verbatim):**
> Analyze which type of semaphore is most suitable for controlling access to shared resources by concurrent processes.

**Answer**

It depends on **how many instances** of the resource exist:

| Resource situation | Best semaphore | Why |
|---|---|---|
| **One** shared resource / critical section (printer, file, counter, shared variable) | **Binary semaphore (mutex)**, initial value 1 | Guarantees mutual exclusion: only one process can hold it |
| **N identical** resources (N printers, N lab systems, N buffer slots) | **Counting semaphore**, initial value N | Allows up to N concurrent holders; the (N+1)-th process blocks |
| **Ordering** between two processes (S1 before S2) | Binary semaphore initialized to 0 | `wait` blocks until the other process signals |

For the typical “shared resource” wording, the expected answer is a **binary semaphore (mutex)**, because it enforces mutual exclusion. Use a **counting semaphore** when the resource has multiple identical instances. A monitor is an even higher-level alternative, but the question asks specifically about semaphore type.

---

## 3.5 Monitors vs semaphores **[5]**

**Asked in:** P1 IT-F24 Q2(a)(ii); P5 IT-F25 Q2(A)(i); P8 Jan-24 Q3(2).

**Questions (verbatim):**
> Explain the concept of monitors as a synchronization mechanism. How do they differ from semaphores?

> Compare semaphores with monitors as synchronization mechanisms.

> Discuss Monitor in synchronization.

**Answer**

A **monitor** is a high-level synchronization construct that encapsulates shared data and the procedures that operate on it. Only **one process/thread can be active inside the monitor at a time** — the compiler/runtime enforces mutual exclusion automatically.

```text
monitor BankAccount {
    int balance = 0;
    condition notEmpty, notFull;

    procedure deposit(int x) {
        balance = balance + x;
        signal(notEmpty);
    }

    procedure withdraw(int x) {
        while (balance < x) wait(notEmpty);
        balance = balance - x;
    }
}
```

** components of a monitor**

| Component | Role |
|---|---|
| **Initialization code** | Runs once when the monitor is created |
| **Private data** | Shared variables hidden from outside; only monitor procedures can access them |
| **Monitor procedures** | Functions that can be called from outside the monitor |
| **Monitor entry queue** | Holds threads/processes waiting to enter the monitor |

The schematic shows **shared data** at the top, **operations/procedures** in the middle, **initialization code** at the bottom, and an **entry queue** of processes waiting to get in. With condition variables, there are additional queues associated with `x` and `y`.

**Condition variables:** `wait(c)` releases the monitor and blocks the caller; `signal(c)` wakes one waiter. Unlike a semaphore, a condition variable has **no memory** — a signal with no waiter is lost. If no `x.wait()` is pending, `x.signal()` has no effect.

**Signal semantics:** if process P calls `x.signal()` and process Q is suspended in `x.wait()`, both cannot run in parallel. Options:
- **Signal and wait** — P waits until Q leaves the monitor or waits on another condition.
- **Signal and continue** — Q waits until P leaves the monitor or waits on another condition.
- Concurrent Pascal uses a compromise: P immediately leaves the monitor and Q resumes. Mesa, C# and Java use signal-and-continue.

| Aspect | Monitor | Semaphore |
|---|---|---|
| Level | High-level language construct | Low-level OS primitive |
| Mutual exclusion | Automatic: one thread inside the monitor | Manual: programmer writes `wait/signal` |
| Data | Shared data + procedures encapsulated | Just an integer + queue |
| Condition variables | Built-in (`wait`/`signal`) | No built-in condition variables |
| Ease of use | Easier, safer | Error-prone (wrong order → deadlock) |
| Risk | Condition-variable semantics can be subtle | Deadlock, missed signal, priority inversion |
| Scope | Language/runtime support (Java `synchronized`, Ada) | OS/systems programming |
| Example | Java monitor, `synchronized` methods | `sem_t` in POSIX, `mutex` |

**Semaphore vs mutex:** a binary semaphore can act as a mutex, but a mutex usually has ownership (only the locking thread can unlock) and priority inheritance; a semaphore has no owner.

---

## 3.6 Producer–Consumer problem — semaphores and monitor **[5–10]**

**Asked in:** P2 AIML-F24 Q3(b) (monitor); P5 IT-F25 Q2(A)(ii) (bounded buffer); P6 IT-R25 Q2(a)(ii) (semaphores).

**Questions (verbatim):**
> Discuss producer consumer problem and provide the solution to the problem using monitor.

> Explain the working of the bounded buffer solution to the Producer–Consumer problem and illustrate how does it prevent buffer overflow and underflow conditions.

> Discuss how the Producer-Consumer problem can be solved using semaphores.

**Answer**

**Problem:** a producer inserts items into a bounded buffer of size `N`; a consumer removes them. The producer must wait when the buffer is full (**overflow**); the consumer must wait when it is empty (**underflow**). Access to the buffer must be mutually exclusive.

### (a) Semaphore solution

```c
semaphore mutex = 1;      // protect the buffer
semaphore empty = N;      // number of empty slots
semaphore full  = 0;      // number of filled slots

Producer:
    while (true) {
        item = produce();
        wait(empty);          // wait for a free slot
        wait(mutex);          // enter CS
        insert(item);
        signal(mutex);        // leave CS
        signal(full);         // one more item
    }

Consumer:
    while (true) {
        wait(full);           // wait for an item
        wait(mutex);          // enter CS
        item = remove();
        signal(mutex);        // leave CS
        signal(empty);        // one more free slot
        consume(item);
    }
```

- `empty = 0` → producer blocks: **no overflow**.
- `full = 0` → consumer blocks: **no underflow**.
- `mutex` ensures only one process touches the buffer at a time.
- **Order matters:** `wait(empty)`/`wait(full)` must be outside `wait(mutex)`. Reversing them causes deadlock (producer holds mutex and waits for empty, consumer cannot enter to free a slot).

**Trace (N = 5, so empty = 5, full = 0, mutex = 1)**

| Operation | `empty` | `full` | `mutex` | Result |
|---|---:|---:|---:|---|
| Produce item x | 5 → 4 | 0 | 1 → 0 → 1 | x inserted, then full 0 → 1 |
| Produce item y | 4 → 3 | 1 → 2 | 1 → 0 → 1 | y inserted |
| Consume item y | 3 → 4 | 2 → 1 | 1 → 0 → 1 | y removed |
| Buffer full | 0 | 5 | 1 | Producer's `wait(empty)` blocks — no overflow |
| Buffer empty | 5 | 0 | 1 | Consumer's `wait(full)` blocks — no underflow |

### (b) Monitor solution

```text
monitor BoundedBuffer {
    int buffer[N];
    int count = 0, in = 0, out = 0;
    condition notFull, notEmpty;

    procedure insert(item) {
        while (count == N) wait(notFull);
        buffer[in] = item;
        in = (in + 1) % N;
        count++;
        signal(notEmpty);
    }

    procedure remove() {
        while (count == 0) wait(notEmpty);
        item = buffer[out];
        out = (out + 1) % N;
        count--;
        signal(notFull);
        return item;
    }
}
```

The monitor guarantees mutual exclusion automatically; `notFull` and `notEmpty` handle the full/empty conditions. `while` (not `if`) is used to re-check after wakeup (spurious wakeups / barging).

---

## 3.7 Readers–Writers problem **[10]**

**Asked in:** P4 AIML-R24 Q3(b).

**Question (verbatim):**
> Discuss Readers/Writers problem and provide the solution to the problem using semaphore.

**Answer**

**Problem:** a shared resource (file/database) is read by many readers and written by writers.

- Any number of **readers** may access the resource simultaneously.
- A **writer** needs exclusive access: no other reader or writer.
- Readers and writers must not overlap.

**First readers–writers (readers preference)**

```c
semaphore mutex = 1;      // protects readcount
semaphore wrt = 1;        // exclusive writer / first reader
int readcount = 0;

Reader:
    wait(mutex);
    readcount++;
    if (readcount == 1)
        wait(wrt);        // first reader locks out writers
    signal(mutex);

    read the resource;

    wait(mutex);
    readcount--;
    if (readcount == 0)
        signal(wrt);      // last reader releases
    signal(mutex);

Writer:
    wait(wrt);
    write the resource;
    signal(wrt);
```

- The first reader locks `wrt`; the last reader unlocks it. Other readers only touch `readcount`.
- **Trace:** first reader: `mutex 1→0`, `readcount 0→1`, `wrt 1→0` (writer is locked out), `mutex 0→1`. A second reader can then enter: `mutex`, `readcount 1→2`, `mutex`; both read together. As readers leave, `readcount` decreases; when it reaches 0, `wrt` is set back to 1 so a writer can enter.
- **Problem:** if readers keep arriving, `readcount` never reaches 0 and the writer **starves**.

**Second readers–writers (writers preference):** add a `rw_mutex` so that once a writer is waiting, new readers block behind it. Writers do not starve, but readers can.

**Fair solution:** use a FIFO queue/turnstile so readers and writers are served in arrival order.

---

## 3.8 Dining Philosophers (cross-reference)

The full problem and the monitor/semaphore solutions are in **Unit 5, section 5.10**, because the DJS23 syllabus places “Dining Philosopher problem” in the deadlock unit. The concurrency primitives used are the semaphores and monitors of this unit.

---

## 3.9 Inter-process communication (IPC) and a hardware mutual-exclusion approach **[5]**

**Asked in:** P4 AIML-R24 Q3(a)(i); P8 Jan-24 Q1(3).

**Questions (verbatim):**
> Discuss Inter Process communication in brief. Explain any one hardware approach to achieve mutual exclusion.

> Discuss Inter-process communication in brief.

**Answer**

**IPC** lets processes exchange data and coordinate actions. Two basic models:

| Model | How it works | Advantages | Disadvantages |
|---|---|---|---|
| **Shared memory** | OS creates a shared region; processes map it and read/write directly | Fast (no kernel copy per access) | Needs explicit synchronization; protection is harder |
| **Message passing** | Processes send/receive messages through the kernel | Works across machines; no shared address space | Kernel copy/context-switch overhead |

**Mechanisms:** pipes (anonymous/named), message queues, shared memory segments, semaphores, sockets, remote procedure calls (RPC), signals.

**Hardware approach — test-and-set spinlock**

```c
int lock = 0;
while (test_and_set(&lock))
    ;
/* critical section */
lock = 0;
```

`test_and_set` atomically reads the old value and sets the lock to 1. The first process sees 0 and enters; others see 1 and spin. On a multiprocessor the instruction also serializes cache lines. Alternatives: compare-and-swap, atomic exchange, load-linked/store-conditional, disabling interrupts (uniprocessor only).

---

## Unit 3 — quick revision checklist

**Concurrency basics**

- [ ] Independent vs cooperative processes.
- [ ] Race condition definition + shared-counter example.
- [ ] Critical section + 3 requirements: mutual exclusion, progress, bounded waiting.
- [ ] Entry / critical / exit / remainder structure.

**Mutual exclusion**

- [ ] Software approaches: strict alternation (fails progress), Peterson (2 processes), Dekker, Bakery (n processes).
- [ ] Peterson code + note (may fail on modern architectures).
- [ ] Hardware: disable interrupts (uniprocessor), TestAndSet, Swap, CAS, LL/SC.
- [ ] Test-and-set / Swap code + properties (atomic, mutual exclusion, progress, **no bounded wait**).
- [ ] Busy waiting vs blocking; ticket lock for bounded waiting.

**Semaphores**

- [ ] busy-wait `while(S<=0); S--;` + blocking OS implementation.
- [ ] Binary vs counting semaphore; range and use.
- [ ] examples: `12, 10P, 4V → 6`; `7, 20P, xV → 5 ⇒ x = 18`.
- [ ] note: semaphore gives mutual exclusion + progress, **not** bounded waiting.
- [ ] Semaphore mutual-exclusion code; ordering semaphore code.
- [ ] Implementation: atomic wait/signal, wait queue, signal wakes waiter.
- [ ] Which semaphore type: binary for one resource, counting for N identical resources (Jan-24).

**Monitors**

- [ ] Monitor definition; only one process active inside.
- [ ] Components: initialization code, private data, monitor procedures, entry queue.
- [ ] Condition variables `x.wait`/`x.signal`; signal with no waiter has no effect.
- [ ] Signal-and-wait vs signal-and-continue; Concurrent Pascal/Mesa/C#/Java.
- [ ] Monitor vs semaphore: automatic vs manual mutual exclusion, condition variables, ease of use.

**Classical problems**

- [ ] Producer–consumer: semaphore code (3 semaphores), monitor code, trace.
- [ ] Why `wait(empty)`/`wait(full)` must come before `wait(mutex)`; overflow/underflow prevention.
- [ ] Readers–writers: `mutex`, `wrt`, `readcount`; trace; first/second/fair.
- [ ] Reader starvation vs writer starvation.
- [ ] Computer-lab semaphore design: counting `lab = N`, booking mutex, wait order.
- [ ] Dining philosophers: naive deadlock, monitor solution, 4 alternatives.
- [ ] IPC: shared memory vs message passing; pipes, queues, shared memory, sockets, RPC, signals.
- [ ] Livelock vs deadlock vs starvation.
