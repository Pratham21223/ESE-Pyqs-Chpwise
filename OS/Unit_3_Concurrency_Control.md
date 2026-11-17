# Unit 3 — Concurrency Control

> Syllabus: principles of concurrency, mutual exclusion (software approaches, hardware support), semaphores, monitors, classical problems: Readers–Writers and Producer–Consumer.

---

## 3.1 Principles of concurrency **[5]**

**Asked in:** P2 AIML-F24 Q3(a)(i).

**Question (verbatim):**
> Explain the various principles of concurrency.

**Answer**

**Concurrency** means several processes/threads are making progress during the same time interval. On a single CPU they are interleaved; on multiple cores they may run truly in parallel. Concurrency introduces shared state, and therefore several principles must be respected.

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

**Example — lost update**

```c
int balance = 1000;

void withdraw() {          void deposit() {
    int t = balance;           int t = balance;
    t = t - 100;               t = t + 200;
    balance = t;               balance = t;
}                          }
```

If both read `balance = 1000` before either writes:

```text
withdraw: read 1000 → compute 900
deposit : read 1000 → compute 1200
withdraw: write 900
deposit : write 1200

Final balance = 1200, but the correct value is 1100. The deposit overwrote the withdrawal.
```

**Hardware fix — atomic test-and-set / compare-and-swap**

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

**Caveats:** busy waiting wastes CPU; need bounded waiting (ticket lock) to avoid starvation; disable-interrupts works only on a uniprocessor and only for short critical sections.

---

## 3.3 Critical section and mutual exclusion; software/hardware approaches **[5–10]**

**Asked in:** P1 IT-F24 Q2(a)(i); P4 AIML-R24 Q3(a)(ii); P7 AIML-R25 Q2(B)(ii) (part).

**Questions (verbatim):**
> Explain the concept of mutual exclusion with a suitable example.

> Discuss what is mutual exclusion, and why is it essential in synchronization? How to achieve it?

> Explain the terms: Mutual Exclusion, critical section, Livelock. Explain the role of semaphores in solving synchronization and apply the same to design a solution that controls access to a computer lab with limited systems.

**Answer**

A **critical section (CS)** is the part of a program that accesses a shared resource (variable, file, printer, buffer). **Mutual exclusion (mutex)** is the requirement that **at most one** process executes its CS for that resource at any time.

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

A **semaphore** is an integer synchronization variable that is accessed only through two atomic operations:

```text
wait(S):   S = S - 1;
           if (S < 0) block this process and put it in S's queue;

signal(S): S = S + 1;
           if (S <= 0) wake up one process from S's queue;
```

(The classic names are `P`/`wait` and `V`/`signal`.)

**Types**

| Type | Range | Use |
|---|---|---|
| **Binary semaphore (mutex)** | 0 or 1 | Mutual exclusion for one resource/CS |
| **Counting semaphore** | 0…N | Manage N identical resources (printers, lab systems, buffer slots) |

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

**Condition variables:** `wait(c)` releases the monitor and blocks the caller; `signal(c)` wakes one waiter. Unlike a semaphore, a condition variable has **no memory** — a signal with no waiter is lost.

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
semaphore write = 1;      // exclusive writer / first reader
int readcount = 0;

Reader:
    wait(mutex);
    readcount++;
    if (readcount == 1)
        wait(write);      // first reader locks out writers
    signal(mutex);

    read the resource;

    wait(mutex);
    readcount--;
    if (readcount == 0)
        signal(write);    // last reader releases
    signal(mutex);

Writer:
    wait(write);
    write the resource;
    signal(write);
```

- The first reader locks `write`; the last reader unlocks it. Other readers only touch `readcount`.
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

- [ ] Race condition definition + one interleaving example.
- [ ] Critical section + 3 requirements (mutual exclusion, progress, bounded waiting).
- [ ] Peterson’s algorithm code and why it works.
- [ ] Test-and-set / compare-and-swap spinlock code.
- [ ] Semaphore `wait`/`signal` pseudocode; binary vs counting.
- [ ] Semaphore mutual-exclusion code; ordering semaphore code.
- [ ] Monitor definition + condition variables; 6 differences from semaphore.
- [ ] Producer–consumer: semaphore code (3 semaphores) and monitor code.
- [ ] Why `wait(empty)` must come before `wait(mutex)`.
- [ ] Readers–writers: first/second; reader/writer starvation.
- [ ] Dining philosophers: 4 conditions, deadlock, 3 solutions (Unit 5).
- [ ] IPC: shared memory vs message passing; 4 mechanisms.
- [ ] Livelock vs deadlock vs starvation.
- [ ] Which semaphore type: binary for one resource, counting for N identical resources (Jan-24).
