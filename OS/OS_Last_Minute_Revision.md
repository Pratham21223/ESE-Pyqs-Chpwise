# Operating System — Last-Minute Revision (DJS23)

**One-file cheat sheet: formulas, methods, pseudocode, question types, past answers.**
Covers Units 1–6 of `DJS23IMD201`. Theory is compressed; use the unit files for full explanations.

> **Exam:** DJS23 papers are 60 marks, 2 hours, 4 questions × 15 marks with internal `OR`. The added **Jan-24** paper is 65 marks: Q1 = 20 (four 5-mark parts), Q2 = 15, Q3 = 15, Q4 = 15. Read both sides of every `OR` before choosing.

---

# 0. Question-type → answer template

| If the question says… | Write this |
|---|---|
| **Define / differentiate** (5) | 1-line definition + 5-row comparison table + 1 example |
| **Explain with example** (5/10) | Definition → diagram → step-by-step example → 1-line conclusion |
| **Explain OS type / architecture** (5/10) | Definition → labelled block diagram → 4–6 characteristics → 2–3 applications |
| **Scheduling numerical** (10) | Gantt chart → per-process table (AT, BT, CT, WT, TAT) → averages → 1-line comment |
| **Page replacement** (10) | Frames table per reference → mark F/H → total faults/hits → which is best |
| **Memory allocation** (10) | Table: process → hole chosen → leftover → unallocated → conclusion |
| **Banker’s** (10) | Need = Max − Allocation → work table → safe sequence or “unsafe” → request check |
| **Address translation** (5) | Split logical address → page/segment table lookup → form physical address → trap if invalid/out of limit |
| **Disk scheduling** (10) | Movement table (request, movement) → sequence → total seek time → compare |
| **I/O buffering / RAID** (5) | Table of types: technique, capacity/overlap, advantage, disadvantage, use |
| **Deadlock conditions** (5) | 4-row table + one OS example each + why all four must hold |
| **Synchronization** (5/10) | Shared variables → code/pseudocode → explain wait/signal order → deadlock note |
| **Compare X and Y** (5) | 6-row table: definition, unit, overhead, advantage, disadvantage, use |
| **Numerical** (10) | State assumptions → formula → substitution → result with units |

**Time plan:** 5 min read/choose → 20–25 min per 15-mark question → 10 min final check. Draw the Gantt/page/disk table first; it earns marks even if arithmetic slips.

## 0.1 Formula card (memorize)

```text
Scheduling:  TAT = CT − AT;  WT = TAT − BT;  RT = first start − AT
             Avg = Σ/n;  Throughput = n / total time;  CPU util = busy/total

Banker’s:    Need = Max − Allocation;  safe if Need[i] ≤ Work
             Request granted only if Request ≤ Need, Request ≤ Available, and new state is safe

Paging:      logical = [page | offset];  physical = frame × page_size + offset
             offset bits = log2(page_size)
             fault rate p = faults/refs;  hit ratio = 1 − p
             EAT = (1−p)×mem_time + p×fault_time

Disk:        access = seek + rotational latency + transfer
             avg rotational latency = 1/2 × (60/RPM) seconds
             total seek = Σ |head_now − head_next|
```

---

# 1. Unit 1 — OS overview

## 1.1 Definitions

| Term | One-line answer |
|---|---|
| **OS** | System software that manages hardware/software resources and provides services to programs |
| **Kernel** | Core resident part that runs in privileged mode |
| **System call** | Programmatic trap into the kernel for a service |
| **Shell** | Command interpreter / user interface |
| **Firmware** | Software stored in ROM (BIOS/UEFI) |
| **Multiprogramming** | Keep several jobs in memory; switch CPU when one waits |
| **Multitasking** | Time-sliced multiprogramming for interactive users |
| **Multiprocessing** | Two or more CPUs/cores execute concurrently |
| **Distributed OS** | Networked machines appear as one system |

## 1.2 Objectives and functions

**Objectives:** convenience, efficiency, ability to evolve.

**Functions:** process management, memory management, file management, device management, security/protection, error detection, accounting, user interface, networking.

**Services:** program execution, I/O, file-system operations, communication, error detection, resource allocation, accounting, protection.

## 1.3 Types of OS — one line each

| Type | Key idea |
|---|---|
| **Serial** | No OS; sign-up sheet; setup time dominates |
| **Simple batch** | Monitor batches jobs; no user interaction |
| **Multiprogramming batch** | Several jobs in memory; CPU overlaps I/O |
| **Time-sharing** | Time quantum; interactive; response time matters |
| **Multiprocessing/parallel** | Multiple CPUs/cores; load balancing, synchronization |
| **Distributed** | Independent machines, single-system view; transparency, fault tolerance |
| **Real-time (RTOS)** | Deadline-driven; hard vs soft; deterministic, priority-based |
| **Network** | Each machine has its own OS; file/print sharing |
| **Embedded/mobile** | Small footprint, power-aware, fixed function |

**Five terms (Jan-24 Q1(1)):**

| Term | Key idea |
|---|---|
| **Uni-programming** | One program in memory; CPU idle during I/O |
| **Multi-programming** | Several programs; switch on I/O wait → CPU utilization |
| **Multi-tasking** | Time-slice; switch rapidly → response time |
| **Multi-processing** | Multiple CPUs/cores → true parallelism |
| **Multi-threading** | Multiple threads in one process; share code/data, own stack |

## 1.4 OS architectures

| Architecture | Structure | + / − |
|---|---|---|
| **Monolithic** | All services in kernel | Fast / hard to maintain |
| **Layered** | Layers 0…N | Modular / layer-crossing overhead |
| **Microkernel** | Minimal kernel + user-space servers | Reliable, secure / IPC overhead |
| **Modular** | Loadable kernel modules | Flexible / interface complexity |
| **Client–server** | Clients request services from servers | Distribution / message overhead |
| **VM/hypervisor** | Guest OSs on virtual hardware | Isolation / overhead |

**Microkernel diagram:** apps → user-space servers (file, driver, network) → microkernel (IPC, scheduling, basic memory, interrupts) → hardware. **Modularity:** add/replace a server without rebuilding the kernel; a crashed server does not crash the kernel; smaller trusted base. **Cost:** message passing/context switches.

## 1.5 System bus

| Bus | Carries |
|---|---|
| **Data** | Actual data (bidirectional) |
| **Address** | Memory/port address (usually CPU → memory) |
| **Control** | Read/write, interrupt, clock, bus grant |

## 1.6 Multiprocessor vs multicore

| Multiprocessor | Multicore |
|---|---|
| Multiple physical CPUs/sockets | Multiple cores on one die |
| Own caches; shared bus/NUMA | Shared last-level cache |
| Higher cost/power | Lower cost/power per core |
| Add sockets to scale | Add cores to scale |
| Higher inter-CPU latency | Low on-chip latency |

**OS design considerations (write any 6):** scheduling/load balancing, synchronization, cache coherence (MESI), memory management/NUMA, IPC, scalability, fault tolerance, energy efficiency, interrupt handling, thread affinity/migration, real-time latency.

## 1.7 System calls (syllabus gap)

| Type | Examples |
|---|---|
| Process control | `fork`, `exec`, `exit`, `wait`, `kill` |
| File management | `open`, `read`, `write`, `close`, `lseek` |
| Device management | `ioctl`, `read`, `write`, `mount` |
| Information | `getpid`, `alarm`, `time` |
| Communication | `pipe`, `socket`, `send`, `recv` |
| Protection | `chmod`, `chown`, `umask` |

**Parameter passing:** registers, memory block + address in register, stack. Kernel validates pointers/permissions.

---

# 2. Unit 2 — Process management and scheduling

## 2.1 Process states and PCB

```text
NEW ──admit──► READY ──dispatch──► RUNNING ──exit──► TERMINATED
                 ▲                   │
                 │   interrupt       │ wait (I/O/event)
                 └───────────────────┤
                                     ▼
                                  WAITING
                                     │ event completes
                                     └──► READY
```

**PCB fields:** state, program counter, CPU registers, scheduling info (priority, queue), memory info (base/limit, page table), accounting, I/O status (open files, devices).

**Process vs thread:** process has its own address space and is heavy/isolated; thread shares code/data/heap, has its own stack/registers, is light and fast to switch.

**ULT vs KLT (Jan-24):** ULT = library-managed, kernel sees one process, fast switch, one blocking call blocks all (pure ULT), cannot use multiple cores. KLT = kernel-managed, individually scheduled, blocking one does not block others, can run on multiple cores.

**Why creation/termination is expensive:** PCB, address space/page tables, program load, file table, scheduler queues; termination frees memory/files/devices, notifies parent, accounting, IPC cleanup.

## 2.2 Scheduling formulas

```text
CT  = completion time
TAT = CT − Arrival
WT  = TAT − Burst
RT  = first CPU start − Arrival
Avg = Σ / n
Throughput = n / (last completion − first arrival)
CPU utilization = busy time / total time
```

**Criteria (P4 Q2b ii):** CPU utilization, throughput, turnaround time, waiting time, response time, fairness, predictability.

## 2.3 Algorithms — rule + trap

| Algorithm | Rule | Trap |
|---|---|---|
| **FCFS** | Earliest arrival first, non-pre-emptive | Convoy effect |
| **SJF** | Shortest burst first, non-pre-emptive | Needs burst prediction; starvation |
| **SRTF** | Shortest remaining time, pre-emptive | More context switches; starvation |
| **RR** | FIFO with quantum `q`, pre-emptive | `q` too small → overhead; too large → FCFS |
| **Priority** | Highest priority first; pre-emptive or not | Starvation; use aging |
| **MLFQ** | Multiple queues, feedback | Complex tuning |

**Tie-breakers:** earlier arrival; then smaller PID. **RR queue:** new arrival joins before a pre-empted process is re-queued (state this).

## 2.4 Past scheduling results (all solved in Unit 2)

| Paper | Question | Algorithm | Avg WT | Avg TAT |
|---|---|---|---:|---:|
| IT-F24 | P1(3,1) P2(1,4) P3(4,2) P4(0,6) P5(2,3) | SJF non-pre | 4.80 | 8.00 |
| IT-F24 | same | SRTF | 3.80 | 7.00 |
| AIML-F24 | P1(0,2) P2(1,3) P3(2,5) P4(3,4) P5(4,6) | FCFS | 4.20 | 8.20 |
| AIML-F24 | same | SRTF | 4.00 | 8.00 |
| AIML-R24 | P1(0,4) P2(1,5) P3(2,2) P4(3,1) P5(4,6) P6(6,3) | FCFS | 6.33 | 9.83 |
| AIML-R24 | same | RR q=2 | 7.33 | 10.83 |
| AIML-R24 | P1(0,2,pr2) P2(1,1,pr1) P3(2,8,pr4) P4(3,4,pr5) P5(4,5,pr3) | Priority pre | 4.00 | 8.00 |
| IT-F25 | P1(0,8) P2(1,20) P3(2,3) P4(3,6) P5(4,12) | FCFS | 18.80 | 28.60 |
| IT-F25 | same | SRTF | 10.40 | 20.20 |
| IT-R25 | burst 5,24,16,10,3 (all at 0) | FCFS | 26.80 | 38.40 |
| IT-R25 | same | SJF non-pre | 12.60 | 24.20 |
| AIML-R25 | P1(0,7) P2(1,5) P3(2,3) P4(3,1) P5(4,2) P6(5,1) | SJF non-pre | 5.67 | 8.83 |
| AIML-R25 | same | SRTF | 4.00 | 7.17 |
| AIML-R25 | P1(0,8) P2(1,4) P3(2,2) P4(3,1) P5(4,3) P6(5,2) | RR q=3 | 8.67 | 12.00 |
| AIML-R25 | same | Priority non-pre | 8.67 | 12.00 |
| **Jan-24** | **burst 10,29,3,7,12 (all at 0)** | **FCFS** | **28.00** | **40.20** |
| **Jan-24** | **same** | **SJF non-pre** | **13.00** | **25.20** |
| **Jan-24** | **same** | **RR q=10** | **23.00** | **35.20** |

**IT-F24 Gantt:** non-pre `P4(0–6) P1(6–7) P3(7–9) P5(9–12) P2(12–16)`; pre `P4(0–1) P2(1–3) P1(3–4) P2(4–6) P3(6–8) P5(8–11) P4(11–16)`.

**IT-F25 Gantt:** FCFS `P1(0–8) P2(8–28) P3(28–31) P4(31–37) P5(37–49)`; SRTF `P1(0–2) P3(2–5) P1(5–11) P4(11–17) P5(17–29) P2(29–49)`.

**Jan-24 Gantt:** FCFS `P1(0–10) P2(10–39) P3(39–42) P4(42–49) P5(49–61)`; SJF `P3(0–3) P4(3–10) P1(10–20) P5(20–32) P2(32–61)`; RR q=10 `P1(0–10) P2(10–20) P3(20–23) P4(23–30) P5(30–40) P2(40–50) P5(50–52) P2(52–61)`. SJF best (13), RR 23, FCFS 28.

---

# 3. Unit 3 — Concurrency

## 3.1 Definitions

| Term | Meaning |
|---|---|
| **Race condition** | Result depends on interleaving of accesses |
| **Critical section** | Code that accesses a shared resource |
| **Mutual exclusion** | At most one process in the CS |
| **Progress** | If CS free and someone wants in, one enters; outsiders cannot block |
| **Bounded waiting** | A waiting process gets a turn after a bounded number of entries |
| **Deadlock** | Cyclic waiting, no progress |
| **Starvation** | A process is indefinitely overlooked |
| **Livelock** | Processes keep changing state but make no progress |

## 3.2 Mutual exclusion — software/hardware

**Peterson (2 processes):**

```c
flag[i] = true; turn = j;
while (flag[j] && turn == j) ;
/* CS */
flag[i] = false;
```

**Test-and-set spinlock:**

```c
while (test_and_set(&lock)) ;   // atomic read+set
/* CS */
lock = 0;
```

**CAS:** `while (compare_and_swap(&lock,0,1) != 0);`

**Other software:** strict alternation (fails progress), Dekker, bakery (n processes, tickets).

**Hardware:** disable interrupts (uniprocessor only), test-and-set, compare-and-swap, atomic exchange, LL/SC.

## 3.3 Semaphores

```text
wait(S):   S = S − 1; if (S < 0) block and enqueue;
signal(S): S = S + 1; if (S <= 0) wake one from queue;
```

| Type | Value | Use |
|---|---|---|
| **Binary/mutex** | 0/1 | One resource / CS |
| **Counting** | 0…N | N identical resources / buffer slots |

**Which type? (Jan-24):** **binary semaphore (mutex)** for one shared resource / mutual exclusion; **counting** for N identical resources.

**Mutual exclusion:**

```c
semaphore mutex = 1;
wait(mutex);
/* CS */
signal(mutex);
```

**Ordering:** `P1: S1; signal(sync); P2: wait(sync); S2;`

**Implementation:** integer + queue; `wait/signal` atomic (disable interrupts or hardware instruction); block instead of busy-wait; `signal` wakes a waiter.

**Traps:** wrong wait order → deadlock; missing signal → permanent block; no ownership.

## 3.4 Monitors

- High-level construct: shared data + procedures; **only one thread inside at a time**.
- **Condition variables:** `wait(c)` releases monitor and blocks; `signal(c)` wakes one waiter. No memory — signal with no waiter is lost.
- **vs semaphore:** automatic mutual exclusion, built-in condition variables, easier/safer; semaphore is low-level/manual and error-prone.

**Monitor producer–consumer:**

```text
monitor BoundedBuffer {
  int buf[N], count=0, in=0, out=0;
  condition notFull, notEmpty;
  insert(item) { while (count==N) wait(notFull);
                 buf[in]=item; in=(in+1)%N; count++; signal(notEmpty); }
  remove()     { while (count==0) wait(notEmpty);
                 item=buf[out]; out=(out+1)%N; count--; signal(notFull); return item; }
}
```

## 3.5 Producer–Consumer with semaphores

```c
semaphore mutex=1, empty=N, full=0;

Producer: wait(empty); wait(mutex); insert(); signal(mutex); signal(full);
Consumer: wait(full);  wait(mutex); remove(); signal(mutex); signal(empty);
```

- `empty=0` → producer blocks (no overflow).
- `full=0` → consumer blocks (no underflow).
- `wait(empty/full)` must be **outside** `wait(mutex)`; reversing causes deadlock.

## 3.6 Readers–Writers (first)

```c
semaphore mutex=1, write=1; int readcount=0;

Reader: wait(mutex); readcount++;
        if (readcount==1) wait(write);
        signal(mutex);
        read;
        wait(mutex); readcount--;
        if (readcount==0) signal(write);
        signal(mutex);

Writer: wait(write); write; signal(write);
```

- First reader locks out writers; last reader releases.
- Readers-preference → writer starvation. Writers-preference adds a turnstile; fair solution uses FIFO.

## 3.7 IPC

| Shared memory | Message passing |
|---|---|
| Fast, direct access | Kernel copy, works across machines |
| Needs synchronization | Simpler protection |
| Same machine | Same/distributed |

**Mechanisms:** pipes, message queues, shared memory, semaphores, sockets, RPC, signals.

## 3.8 Computer-lab semaphore design

```c
semaphore lab = N;       // N systems
semaphore booking = 1;   // protects allocation log

wait(lab);               // take a system or block
wait(booking); update log; signal(booking);
use system;
wait(booking); update log; signal(booking);
signal(lab);             // release
```

Order `wait(lab)` before `wait(booking)` avoids holding the booking lock while waiting for a system.

---

# 4. Unit 4 — Memory management

## 4.1 Requirements

Relocation, protection, sharing, logical organization, physical organization.

## 4.2 Partitioning

| Fixed | Variable |
|---|---|
| Fixed-size partitions | Holes sized to process |
| Internal fragmentation | External fragmentation |
| Simple, fixed degree of multiprogramming | Complex, needs compaction |
| No compaction | Compaction/coalescing |

**Internal fragmentation:** wasted inside allocation. **External:** free memory scattered in holes.

## 4.3 Allocation rules

| First Fit | Best Fit | Worst Fit | Next Fit |
|---|---|---|---|
| First hole that fits | Smallest hole that fits | Largest hole | Continue from last point |
| Fast | Small leftover | Large leftover | Spreads allocations |
| May fragment start | Many tiny holes | Destroys large holes | Can leave holes behind pointer |

## 4.4 Past allocation results

| Paper | Partitions | Processes | Result |
|---|---|---|---|
| IT-F24 | 150,500,200,300,550 | 220,430,110,425 | Best: all allocated; Worst: 425 unallocated |
| AIML-R24 | 300,600,350,200,750,125 | 115,500,358,200,375 | First/Best: all; Worst: 375 unallocated |
| IT-F25 | 50,200,300,350,600 | 115,320,50,200 | All strategies allocate all; total free 815; FF = BF |
| IT-R25 | 150,400,350,450,500 | 130,220,360,210 | All strategies allocate all |
| AIML-R25 | 300,600,350,200,750,125 | 115,500,358,200,375 | Next: all; Worst: 375 unallocated → Next better |
| **Jan-24** | **150,500,200,300,550** | **220,430,110,425** | **First Fit: 220→500, 430→550, 110→150, 425 unallocated** |

**IT-F24 Best Fit:** 220→300 (80), 430→500 (70), 110→150 (40), 425→550 (125).
**IT-F24 Worst Fit:** 220→550 (330), 430→500 (70), 110→330 (220), 425 **unallocated**.
**IT-F25:** FF/BF placements `[200,350,50,300]`; WF `[600,600,350,300]`; all leave 815 KB total → tie.

## 4.5 Paging and segmentation

**Paging**

```text
logical = [p | d]
physical = [f | d] = frame_table[p] × page_size + d
offset bits = log2(page_size)
```

**Segmentation**

```text
logical = [s | d]
if d ≥ limit[s] → trap
physical = base[s] + d
```

| Paging | Segmentation |
|---|---|
| Fixed pages/frames | Variable logical segments |
| Invisible to programmer | Visible (code/data/stack) |
| Internal fragmentation | External fragmentation |
| Page table (frame) | Segment table (base, limit) |
| Easy sharing per page | Natural sharing per module |

**TLB/EAT:** `EAT = (1−p)(t_TLB + t_mem) + p(t_TLB + 2t_mem)`.

## 4.6 Demand paging — 6 steps

1. Reference page → PTE invalid → page fault.
2. Trap to OS; save state.
3. Find free frame or evict victim (write back if dirty).
4. Load page from swap/disk.
5. Update page table/TLB; mark valid.
6. Restart the faulting instruction.

**Thrashing:** high page-fault rate; system spends more time paging than executing. Fix: working-set model, page-fault frequency, more memory, better locality.

## 4.7 Page replacement — formulas

```text
Fault rate p = faults / references
Hit ratio   = hits / references
EAT = (1−p) × memory_time + p × fault_time
```

| FIFO | LRU | Optimal |
|---|---|---|
| Earliest loaded | Least recently used | Farthest future use |
| Belady’s anomaly | Stack algorithm, no Belady | Minimum faults |
| Simple | Needs reference bits/stack | Theoretical only |

## 4.8 Past page-replacement results

| Paper | String (frames) | FIFO | LRU | OPT |
|---|---|---:|---:|---:|
| IT-F24 | 5,0,1,0,2,3,0,2,4,3,3,2,0,2,1,2,7,0,1,1,0 (3) | 11 | 12 | 9 |
| AIML-F24 | 6,0,1,2,0,3,6,0,3,5,6,2,4,0 (3) | 11 | — | 8 |
| IoT-F24 | 7,0,1,2,0,3,0,4,2,3,0,3,2 (3) | 10 | 9 | 7 |
| AIML-R24 | same as IT-F24 (3) | 11 | 12 | — |
| IT-F25 | 4,1,2,4,5,1,2,4,6,1,2,1,4,5,3,6 (3) | 12 | **14** | 9 |
| IT-R25 | 6,0,5,2,0,3,0,4,2,3,0,3,2,5,2,0,5,6,0,5 (3) | 15 | 12 | 9 |
| AIML-R25 | 1,2,3,4,5,3,4,1,6,7,8,7,8,9,7,8,9,5,4,5,4,2 (4) | 13 | — | 11 |
| **Jan-24** | **2,3,5,4,2,5,7,3,8,7 (3)** | **8** | **8** | — |

> **IT-F25 LRU trap:** official key says 10; correct trace gives **14**. FIFO 12 and OPT 9 match. Write the trace (Unit 4, Page Set 5).

## 4.9 Swapping

Move whole process between RAM and backing store. **+** frees memory, more multiprogramming; **−** slow disk I/O, context-switch cost, thrashing risk. Modern systems swap pages, not whole processes.

---

# 5. Unit 5 — Deadlock and peripheral devices

## 5.1 Four Coffman conditions

| Condition | Meaning | Example |
|---|---|---|
| **Mutual exclusion** | Resource non-shareable | Printer |
| **Hold and wait** | Hold one, wait for another | Hold scanner, wait printer |
| **No pre-emption** | Cannot forcibly take | Locked file |
| **Circular wait** | Closed chain `P0→P1→…→P0` | P0 holds A waits B; P1 holds B waits A |

All four must hold; break one → no deadlock.

## 5.2 Prevention / avoidance / detection

| Prevention | Avoidance | Detection + recovery |
|---|---|---|
| Break a condition statically | Banker’s: grant only if safe | Let it happen; detect cycle; abort/pre-empt |
| Resource ordering, all-at-once, pre-emption, shareable resources | Need ≤ Work; safe sequence | RAG/wait-for graph; victim selection |

**Resource ordering:** number resources; request only in increasing order → no circular wait.

## 5.3 Banker’s algorithm

```text
Need[i][j] = Max[i][j] − Allocation[i][j]

Work = Available; Finish = false
repeat:
  find i with Finish[i]==false and Need[i] <= Work
  if found: Work += Allocation[i]; Finish[i]=true
  else break
safe if all Finish true; order = safe sequence
```

**Request algorithm:**

```text
if Request > Need → error
if Request > Available → wait
pretend: Available -= Request; Allocation += Request; Need -= Request
if safe → grant; else rollback and wait
```

## 5.4 Past Banker’s results

| Paper | Key result |
|---|---|
| IT-F24 | Safe; sequence `P0 → P2 → P3 → P4 → P1` |
| IT-F25 | **Unsafe as printed**; P0 needs 5 B but total B = 4; request P2(1,0,0) denied |
| IT-R25 | Safe; `P1 → P3 → P4 → P0 → P2` (others possible) |
| AIML-R25 | Total (9,3,6) → Available (0,1,1); Need: P1(2,2,2), P2(0,0,1), P3(1,0,3), P4(4,2,0); safe `P2 → P1 → P3 → P4` |
| **Jan-24** | **Need P0(0,1,0,0), P1(0,4,2,1), P2(1,0,0,1), P3(0,0,2,0), P4(0,6,4,2); safe `P0 → P3 → P4 → P1 → P2`** |

> **IT-F25 trap:** paper prints P0 Allocation `001`. Standard textbook has `010`; with `010` the system is safe (`P1 → P3 → P4 → P0 → P2`). Answer the printed table, mention the likely typo.

## 5.5 Deadlock detection and recovery

- **RAG:** processes = circles, resources = rectangles with dots; request edge `Pi→Rj`, assignment `Rj→Pi`.
- Single instance: **cycle ⇔ deadlock**. Multiple instances: cycle is necessary but not sufficient; run detection algorithm.
- **Detection algorithm:** Work = Available; find `Request[i] ≤ Work`; finish and release; unfinished processes are deadlocked.
- **Recovery:** abort all; abort one at a time (re-detect); victim selection (priority, CPU time, resources, rollback count); resource pre-emption with rollback; ignore/reboot.
- **Jan-24 RAG:** P1 holds R1, P2 and P3 each hold one R2, P1 wants R2, P2 wants R1. Cycle `P1→R2→P2→R1→P1`, but **not necessarily deadlock** because P3 can release its R2 → safe `P3→P1→P2`.

## 5.6 Dining Philosophers

- 5 philosophers, 5 forks, need left + right; naive `wait(left); wait(right)` → all hold left → deadlock.
- **Solutions:** at most 4 at table (`room=4`); odd-even fork order; pick both forks atomically in a monitor; asymmetric ordering.
- **Monitor:** `state[5]` (THINKING/HUNGRY/EATING), `condition self[5]`; `pickup(i)` sets HUNGRY and calls `test(i)`; eats only if both neighbours not EATING; `putdown(i)` sets THINKING and tests neighbours.

## 5.7 I/O transfers

| Programmed I/O | Interrupt-driven | DMA |
|---|---|---|
| CPU polls status, transfers each word | Device interrupts per word/block; ISR transfers | DMA controller transfers block; interrupt at end |
| Busy wait, worst CPU use | Better, still CPU per word | Best for large blocks; CPU free |
| Simple | Needs interrupt controller | Needs DMA controller; cycle stealing |
| Slow/simple devices | Moderate devices | Disk, network, large transfers |

**Programmed I/O loop:** issue command → read status → wait ready → transfer word → update address/count → repeat.

**DMA setup:** CPU writes memory address, count, direction, device → DMA transfers → completion interrupt.

---

# 6. Unit 6 — I/O management and disk scheduling

## 6.1 I/O management roles

Device independence, uniform interface, drivers, buffering, caching, spooling, interrupt handling, DMA, disk scheduling, error handling, protection, power management.

**Layers:** user → system call → I/O subsystem → driver → controller → device.

**I/O hardware:** controller registers (data/status/control), port-mapped or memory-mapped I/O, interrupt or polling, DMA for blocks. Block devices (disk/SSD) vs character devices (keyboard/serial).

## 6.2 Buffering

| Single | Double | Circular |
|---|---|---|
| One buffer | Two buffers, alternate | Ring of `n` buffers |
| Limited overlap | Good overlap | Best for bursts |
| Low memory | Medium | High memory/complexity |
| Simple device | Printer/disk/graphics | Multimedia/network/keyboard |

**Benefits:** smooth speed mismatch, overlap CPU/device, handle bursts, copy semantics. **Cost:** memory, copying, synchronization.

## 6.3 Disk performance

```text
Access time = seek + rotational latency + transfer
Average rotational latency = 1/2 × (60 / RPM) seconds
Transfer time = bytes / transfer rate
Total seek time = Σ |head_now − head_next|
```

**Example:** 7200 RPM → rotation 8.33 ms → average latency 4.17 ms.

## 6.4 Disk algorithms

| Algorithm | Rule | + / − |
|---|---|---|
| **FCFS** | Arrival order | Simple, fair / long seeks |
| **SSTF** | Shortest seek first | Good throughput / starvation |
| **SCAN** | Go to one end, reverse (elevator) | No starvation / extra end travel |
| **C-SCAN** | Service one direction; jump to 0 | Uniform wait / long jump |
| **LOOK** | SCAN, stop at last request | Less travel / slightly complex |
| **C-LOOK** | C-SCAN, jump to lowest pending | Least travel / complex |

## 6.5 Past disk results

| Paper | Queue, head, range | Algorithm | Total |
|---|---|---|---|
| IT-F24 | 176,79,34,60,92,11,41,114; head 50; 0–199; left | SSTF | 204 |
| IT-F24 | same | SCAN | 226 |
| IT-F25 | 6,10,12,54,97,73,128,15,44,110,34,45; head 23; 0–149; down | FCFS | 482 |
| IT-F25 | same | LOOK | 139 |
| IT-R25 | 70,140,50,125,30,25,160; head 60; 0–169; up | SCAN | 253 |
| IT-R25 | same | C-SCAN | 328 |
| IoT-F24 | 10,12,15,17,20,190,195; head 50; 0–200; down | SCAN | 245 |
| IoT-F24 | same | LOOK (comparison) | 225 |
| **Jan-24** | **2069,1212,2296,2800,544,1618,356,1523,4965,3681; head 2150; 0–4999; up** | **FCFS** | **13011** |
| **Jan-24** | **same** | **SSTF** | **7586** |
| **Jan-24** | **same** | **C-SCAN** | **9917** |

**IT-F24 sequences:** SSTF `50→41→34→11→60→79→92→114→176`; SCAN `50→41→34→11→0→60→79→92→114→176`.
**IT-F25:** FCFS `23→6→10→12→54→97→73→128→15→44→110→34→45`; LOOK `23→15→12→10→6→34→44→45→54→73→97→110→128`.
**IT-R25:** SCAN `60→70→125→140→160→169→50→30→25` (253); C-SCAN `60→70→125→140→160→169→0→25→30→50` (328).
**Jan-24:** FCFS `2150→2069→1212→2296→2800→544→1618→356→1523→4965→3681`; SSTF `2150→2069→2296→2800→3681→4965→1618→1523→1212→544→356`; C-SCAN `2150→2296→2800→3681→4965→4999→0→356→544→1212→1523→1618→2069`.

## 6.6 RAID

| Level | Technique | Capacity | Faults | Use |
|---|---|---|---|---|
| 0 | Striping | N | 0 | Speed/temp |
| 1 | Mirroring | N/2 | 1 per mirror | OS/critical |
| 2 | Bit stripe + Hamming | Less | 1 | Obsolete |
| 3 | Byte stripe + parity | N−1 | 1 | Sequential |
| 4 | Block stripe + dedicated parity | N−1 | 1 | Reads |
| 5 | Block stripe + distributed parity | N−1 | 1 | General server |
| 6 | Double parity | N−2 | 2 | Large arrays |
| 10 | Mirror + stripe | N/2 | 1 per pair | Database/IOPS |

**Parity** = XOR; can reconstruct one lost block. **RAID 6** uses two parity blocks. **Write penalty** on RAID 5/6. **Hot spare** rebuilds automatically.

**Jan-24 example (4 × 1 TB disks):** RAID 0 = 4 TB, no fault; RAID 1/10 = 2 TB, mirror survives; RAID 5 = 3 TB, survives 1; RAID 6 = 2 TB, survives 2.

---

# 7. “If you see this, do this”

| Question wording | Action |
|---|---|
| “Draw Gantt chart, find average WT/TAT” | Draw timeline → table CT/WT/TAT → averages. State pre-emptive/non-pre-emptive and tie rule. |
| “SJF pre-emptive” | Use SRTF; re-check at every arrival. |
| “RR q = k” | Show ready queue; new arrivals before re-queued process. |
| “Page replacement” | Table with frames after each ref; F/H; total faults/hits; best policy. |
| “Frame size = 3/4” | Frames, not pages; show the resident set. |
| “First/Best/Worst Fit” | Table process→hole→leftover; list unallocated; conclusion. |
| “Least external fragmentation” | Compare holes, not just total free; if all allocated, say so. |
| “Banker’s” | Need table → work table → safe sequence; if no sequence, prove unsafe. |
| “Request (a,b,c)” | Check ≤ Need, ≤ Available, pretend grant, re-run safety, rollback if unsafe. |
| “Deadlock conditions” | 4-row table + one example each + “all four must hold”. |
| “Prevent deadlock” | Break one condition per row; resource ordering example. |
| “Detect deadlock” | RAG; single-instance cycle = deadlock; multiple-instance run algorithm. A cycle with multiple instances is **not automatically** a deadlock (Jan-24). |
| “Explain RAID levels” | Table 0/1/5/6/10 + 4-disk example + parity vs mirroring (Jan-24). |
| “Dining philosophers” | Naive deadlock; monitor/4-at-table/odd-even; starvation note. |
| “Disk scheduling” | Movement table → sequence → total; compare SCAN vs LOOK. |
| “SCAN with clustered requests” | Note travel to 0 may be wasteful; compare LOOK. |
| “I/O buffering” | Single/double/circular table + diagram + benefits. |
| “DMA vs interrupt-driven” | 6-row table; CPU involvement, transfer unit, hardware, use. |
| “Programmed I/O” | Polling loop steps; busy wait disadvantage. |
| “Microkernel” | Diagram + modularity/security + IPC overhead. |
| “Multiprocessor vs multicore” | 5-row table + 6 design considerations. |

---

# 8. Final checklist before handing over

- [ ] Gantt chart has time labels and process names; table has AT, BT, CT, WT, TAT.
- [ ] Averages computed with the correct denominator; units stated.
- [ ] Page table marks every F/H; fault count = number of F; hit count = references − faults.
- [ ] Banker’s shows `Need = Max − Allocation` and the work/release column.
- [ ] If unsafe, state **why** (e.g. total B = 4, P0 needs 5).
- [ ] Disk answer shows each movement and the sum; state track range (0–199 / 0–169).
- [ ] Allocation answer lists leftover holes and any unallocated process.
- [ ] Synchronization code has `wait` before CS and `signal` after; order of `wait(empty/full)` vs `mutex` is correct.
- [ ] Monitor answer mentions condition variables and automatic mutual exclusion.
- [ ] Deadlock answer has all four conditions, not just a definition.
- [ ] RAID answer distinguishes mirroring vs parity and states capacity/faults.
- [ ] Assumptions written at the top: all-arrive-0, lower-number-higher-priority, track convention, page policy.
- [ ] Diagrams labelled; tables have headers; pseudocode in code blocks.
- [ ] Attempt every sub-part; a correct method/table earns partial marks even if the final number is wrong.

**Good luck.**
