# Unit 2 — Process Management and CPU Scheduling

> Syllabus: process concept, process states, process control, threads, uni-processor scheduling, pre-emptive and non-pre-emptive scheduling, FCFS, SJF, Round Robin, Priority.

---

## 2.1 Process concept, states and Process Control Block **[5]**

**Asked in:** P2 AIML-F24 Q2(a); P1 IT-F24 Q3(b); P5 IT-F25 Q3(B); P6 IT-R25 Q3(b).

**Questions (verbatim):**
> Explain each state of process state model with a neat diagram.

> Explain the 5-state process model with suitable diagram.

> Explain the structure process control block (PCB) and its role in process management.

> Describe the Process Control Block with suitable diagram.

**Answer**

A **process** is a program in execution. It is an instance of a program running on a computer, an entity that can be assigned to and executed on a processor, and a unit of activity characterized by a single sequential thread of execution, a current state and an associated set of system resources (memory image, open files, locks, etc.). A program is a **passive** entity (an executable file); a process is an **active** entity with a program counter and associated resources.

**Process memory:** when a program is loaded into memory and becomes a process, it is divided into four sections — **stack, heap, text and data**.

**Five-state model**

```text
                 admitted                scheduler dispatch
   NEW ───────────────────► READY ───────────────────────► RUNNING
                              ▲                               │
                              │        interrupt / preempt     │
                              └───────────────────────────────┘
                              │                               │
                              │ exit                          │ wait for I/O/event
                              ▼                               ▼
                         TERMINATED ◄────── exit ────────  WAITING
                                                             │
                                                             │ I/O or event completes
                                                             └────────► READY
```

| State | Meaning |
|---|---|
| **New** | Process is being created; PCB is being built |
| **Ready** | In memory, waiting for the CPU |
| **Running** | Instructions are being executed on the CPU |
| **Waiting (Blocked)** | Waiting for an event: I/O completion, signal, resource |
| **Terminated** | Finished execution; resources are reclaimed |

Some texts add **Suspended Ready** and **Suspended Blocked** for processes swapped out to disk (7-state model).

**Process Control Block (PCB)**

The PCB is the kernel’s data structure for a process. It contains:

| Field | Contents |
|---|---|
| **Process state** | New/ready/running/waiting/terminated |
| **Program counter** | Address of the next instruction |
| **CPU registers** | General registers, stack pointer, flags — saved on context switch |
| **CPU scheduling information** | Priority, scheduling queue pointers, time slice |
| **Memory-management information** | Base/limit registers, page table, segment table |
| **Accounting information** | CPU time used, time limits, job/process IDs |
| **I/O status information** | Open files, allocated devices, pending I/O |

**Role of the PCB:** it identifies the process, stores everything needed to pause and resume it, lets the scheduler choose the next process, and lets the OS reclaim resources on termination. A **context switch** saves the running process’s PCB and restores the next process’s PCB.

**Process creation and termination:** a process is created by `fork()`/`CreateProcess()`; the parent may wait for the child. Termination releases memory, files, devices and PCB entries. Creation/termination are expensive because they involve kernel data structures, memory allocation, page-table setup and scheduler queue updates.

---

## 2.1A Schedulers and scheduling queues **[5]**


| Queue | Holds |
|---|---|
| **Job queue** | Set of **all** processes in the system |
| **Ready queue** | Processes residing in main memory that are ready and waiting to execute; stored as a linked list whose header points to the first and last PCB |
| **Device queue** | Processes waiting for a particular I/O device |

| Scheduler | Role  | Frequency |
|---|---|---|
| **Long-term scheduler** | Selects a job from the **job queue** and loads it into the **ready queue**; controls the **degree of multiprogramming**; selects a good mix of **CPU-bound** and **I/O-bound** processes; mostly used in batch systems | Executes much less frequently; may be invoked only when a process leaves the system |
| **Short-term scheduler (CPU scheduler)** | Selects a process from the **ready queue** and allocates the CPU to it | Executes at least once every **100 ms**; must be fast |
| **Medium-term scheduler** | Removes processes from memory (swapping out) and later reintroduces them (swapping in); reduces the degree of multiprogramming | Used in time-sharing systems when memory is overcommitted or the process mix needs changing |

**Queueing diagram:** rectangles are queues (ready queue and the set of device queues), circles are the resources that serve the queues, and arrows show the flow of processes. A running process may (1) issue an I/O request and enter an I/O queue, (2) create a subprocess and wait for its termination, or (3) be interrupted and put back in the ready queue.

**CPU–I/O burst cycle:** process execution alternates between a **CPU burst** (process executes on the CPU) and an **I/O burst** (process waits for I/O). It begins with a CPU burst, then an I/O burst, then another CPU burst, and so on.

---

## 2.2 Process vs thread **[5]**

**Asked in:** P4 AIML-R24 Q2(a).

**Question (verbatim):**
> Differentiate between Process and Thread.

**Answer**

A **thread** is the **basic unit of CPU utilization** inside a process. It has its own **thread ID, program counter, register set and stack**, but **shares** the code section, data section and other OS resources (such as open files and signals) with other threads of the same process. A traditional process has a single thread of control; a multithreaded process can perform more than one task at a time.

**Comparison table**

| Comparison basis | Process | Thread |
|---|---|---|
| **Resource consumption** | Processes require more resources than threads | Threads generally need fewer resources |
| **Dependency** | Individual processes are independent of each other | Threads are parts of a process and are dependent |
| **Data and code sharing** | Processes have independent data and code segments | A thread shares the data segment, code segment and files with its peer threads |
| **Treatment by OS** | All different processes are treated separately by the OS | All user-level peer threads are treated as a single task by the OS |
| **Time for creation** | More time | Less time |
| **Time for termination** | More time | Less time |

**Additional differences (standard):**

| Process | Thread |
|---|---|
| Own address space and PCB | Shares code/data/heap; own stack and registers |
| Context switch is expensive (memory map, TLB, caches) | Context switch is cheap (registers/stack only) |
| Isolated; one crash usually does not kill another | Shares memory; one bad write can corrupt the process |
| IPC needs OS mechanisms | Threads communicate through shared memory directly |
| Good for isolation and fault containment | Good for parallelism and responsiveness |
| Example: Chrome tab as a process | Example: worker threads inside a browser tab |

**Thread types:** user-level threads (managed by a library; kernel sees one process) and kernel-level threads (managed by the OS; can run on different cores). Modern systems use a 1:1 or M:N mapping.

---

## 2.2A User-level vs kernel-level threads **[5]**

**Asked in:** P8 Jan-24 Q1(4)(1).

**Question (verbatim):**
> Compare User level and Kernel Level thread.

**Answer**

| Point | User-level threads (ULT) | Kernel-level threads (KLT) |
|---|---|---|
| Managed by | Thread library in user space | Operating-system kernel |
| Kernel awareness | Kernel sees one process; it does not know the individual threads | Kernel knows and schedules each thread |
| Creation/switch | Very fast; no system call/mode switch | Slower; system call and kernel scheduling |
| Blocking | If one ULT blocks on a system call, the **whole process** blocks (pure ULT) | Another thread of the same process can run |
| Multiprocessor use | Pure ULT cannot run on two cores at once | Threads can run on different cores in parallel |
| Scheduling | Application/library decides | Kernel decides; priorities/pre-emption |
| Portability | Portable across OSs | Depends on OS support |
| Example | Green threads, early Java threads | Windows threads, Linux `pthread` (1:1) |

**Models:** many-to-one (ULT), one-to-one (KLT), many-to-many (M:N). Modern Linux/Windows mostly use 1:1.

---

## 2.3 Why process creation/termination is expensive **[5]**

**Asked in:** P7 AIML-R25 Q3(A).

**Question (verbatim):**
> Analyze why process creation and termination are expensive operations.

**Answer**

**Creation costs**

1. **PCB allocation and initialization** — the kernel allocates and fills a process control block.
2. **Address-space creation** — new page tables, page directories, memory maps; pages may be copied (copy-on-write still needs page-table work).
3. **Program loading** — reading the executable, mapping text/data, setting up the stack and heap.
4. **File-descriptor table** — copying or duplicating open files.
5. **Scheduler/queue insertion** — the process must be admitted and made ready.
6. **Security/credential setup** — UID/GID, capabilities, audit records.
7. **Cache/TLB effects** — the new process starts with a cold TLB and cold caches.

**Termination costs**

1. **Reclaiming memory** — freeing pages, page tables and swap space.
2. **Closing files and releasing devices** — flushing buffers, updating metadata.
3. **Notifying the parent** — exit status, `wait()` wakeup, zombie cleanup.
4. **Removing from scheduler/queues** — unlinking from run/wait queues.
5. **Accounting and audit** — CPU time, resource usage, logs.
6. **Cleaning up IPC** — shared memory, semaphores, pipes, sockets.

**How OSs reduce the cost:** `fork()` + copy-on-write, `vfork()`/`posix_spawn()`, thread pools, process pools, `exec()` without copying, lazy loading, and slab allocators for PCBs. Still, a thread is much cheaper than a process because it shares the address space.

---

## 2.4 Scheduling criteria; pre-emptive vs non-pre-emptive **[5–10]**

**Asked in:** P3 IoT-F24 Q2(B)(2); P4 AIML-R24 Q2(b)(ii) (performance parameters); P7 AIML-R25 Q1(B)(ii) (response time/throughput).

**Questions (verbatim; the full table for the second question is in 2.9):**
> Elaborate preemptive scheduling Technique and discuss with justification how does it differ from non-preemptive scheduling?

> Explain performance evaluation parameters in scheduling policies. Calculate average waiting time and turnaround time for Priority preemptive algorithm (Lower the number, higher the priority)

**Answer**

**Scheduling criteria**

| Criterion | Formula / meaning | Goal |
|---|---|---|
| **CPU utilization** | `busy time / total time × 100%` | Maximize |
| **Throughput** | `number of processes completed / total time` | Maximize |
| **Turnaround time (TAT)** | `completion time − arrival time` | Minimize |
| **Waiting time (WT)** | `TAT − burst time` | Minimize |
| **Response time** | `first CPU allocation − arrival time` | Minimize |
| **Fairness** | Every process gets a share; no starvation | Balance |
| **Predictability** | Same load gives similar response | Balance |

**When can the CPU be re-scheduled? (four circumstances)**

1. **Running → waiting** — e.g. an I/O request or `wait()` for a child.
2. **Running → ready** — e.g. an interrupt.
3. **Waiting → ready** — e.g. I/O completion.
4. **Terminates**.

For conditions 1 and 4 there is **no choice** — a new process must be selected. For conditions 2 and 3 there is a **choice** — continue the current process or select another. If scheduling takes place only under conditions 1 and 4, the system is **non-pre-emptive (cooperative)**; otherwise it is **pre-emptive**.

**Pre-emptive vs non-pre-emptive**

| Point | Pre-emptive | Non-pre-emptive |
|---|---|---|
| CPU can be taken away | Yes, on arrival of a higher-priority/shorter job or quantum expiry | No; running process keeps CPU until it blocks or finishes |
| Response time | Better for interactive systems | Can be poor (convoy effect) |
| Overhead | Context switches and scheduler overhead | Low overhead |
| Data consistency | Needs locks/synchronization around shared data | Simpler |
| Starvation | Possible with priority/SJF | Possible with SJF/priority |
| Examples | RR, SRTF, priority pre-emptive | FCFS, SJF non-pre-emptive, priority non-pre-emptive |
| Use | Time-sharing, real-time, desktop | Batch, simple embedded |

**Justification:** pre-emption improves responsiveness and fairness because a long job cannot monopolize the CPU; the cost is context-switch overhead and the need for synchronization. Non-pre-emption is simpler and has less overhead but suffers from the convoy effect and poor interactive response.

---

## 2.5 Scheduling algorithms — formulas and comparison **[10]**

**Asked in:** P2 AIML-F24 Q2(b)(ii) (compare algorithms + priority example).

**Question (verbatim):**
> Compare CPU scheduling algorithms with their merits and demerits along with example. Demonstrate priority scheduling with preemptive version with suitable example.

**Answer**

**Common formulas**

```text
Completion time (CT)  = time at which the process finishes
Turnaround time (TAT) = CT − Arrival time
Waiting time (WT)     = TAT − Burst time
Response time (RT)    = First CPU start − Arrival time
Average WT            = ΣWT / n
Average TAT           = ΣTAT / n
Throughput            = n / (last completion − first arrival)
CPU utilization       = busy time / total time
```

| Algorithm | Selection rule | Pre-emptive? | Merits | Demerits |
|---|---|---|---|---|
| **FCFS** | Earliest arrival first | No | Simple, no starvation, FIFO queue | Convoy effect, high average WT, poor for short jobs |
| **SJF** | Shortest burst first | No (classic) | Minimum average WT when bursts are known | Burst prediction, starvation of long jobs |
| **SRTF** | Shortest remaining time first | Yes | Minimum average WT, good response | More context switches, starvation, burst prediction |
| **RR** | FIFO with time quantum `q` | Yes | Fair, good response, no starvation | `q` too small → overhead; `q` too large → FCFS; not optimal TAT |
| **Priority** | Highest priority first | Either | Flexible, supports real-time | Starvation; aging needed; priority assignment is subjective |
| **Multilevel queue** | Different queues for different classes | Yes | Separates interactive/batch/system | Queue scheduling and starvation between queues |
| **Multilevel feedback** | Moves jobs between queues | Yes | Adapts to behavior, good general-purpose | Complex, tuning parameters |

**Comparison tables**

*Table 1 — comparison of FCFS, SJF, priority and RR *

| Parameter | FCFS | SJF | Priority | RR |
|---|---|---|---|---|
| **Pre-emption** | Non-pre-emptive | Pre-emptive  | Pre-emptive  | Pre-emptive |
| **Complexity** | Simplest | Difficult to understand and code | Difficult to understand | Performance depends heavily on time-quantum size |
| **Allocation** | CPU allocated in arrival order | CPU allocated to process with least CPU burst | Higher-priority job runs first | CPU allocated in arrival order, but with a fixed time slice |
| **Waiting time** | Large | Small compared to FCFS | Small compared to FCFS | Large compared to all three |

*Table 2 — overhead/performance *

| Algorithm | CPU overhead | Throughput | Turnaround time | Response time |
|---|---|---|---|---|
| **FCFS** | Low | Low | High | Low |
| **SJF** | Medium | High | Medium | Medium |
| **Priority** | Medium | Low | High | High |
| **RR** | High | Medium | Medium | High |

> **Convention:** plain “SJF” is solved as **non-pre-emptive** unless the question says “pre-emptive” or “SRTF”; plain “priority” follows the paper’s wording. If the comparison table treats SJF as pre-emptive, state the convention above the Gantt chart and solve the pre-emptive version as well.

**Example — priority pre-emptive** (used in the P4 numerical in 2.9):

| Process | Arrival | Burst | Priority |
|---|---|---|---|
| P1 | 0 | 2 | 2 |
| P2 | 1 | 1 | 1 |
| P3 | 2 | 8 | 4 |
| P4 | 3 | 4 | 5 |
| P5 | 4 | 5 | 3 |

Lower number = higher priority. Gantt: `0–1 P1 | 1–2 P2 | 2–3 P1 | 3–4 P3 | 4–9 P5 | 9–16 P3 | 16–20 P4`.
Average WT = 4.0, average TAT = 8.0. Full table is in 2.9.

**Priority details from the**

- **Internal priorities** are assigned by the OS using criteria such as average burst time, CPU-to-I/O ratio, system resource use and other kernel-visible factors.
- **External priorities** are assigned by users based on job importance, fees paid, politics, etc.
- Equal-priority processes are scheduled in **FCFS** order.
- **Starvation/indefinite blocking:** a low-priority task may wait forever while higher-priority jobs keep arriving. **Aging** solves it by gradually raising the priority of a waiting job.

---

## 2.6 Scheduling Set 1 — IT-F24 (SJF pre-emptive and non-pre-emptive) **[10]**

**Asked in:** P1 IT-F24 Q3(a)(i).

**Question (verbatim):**
> Consider the set of 5 processes whose arrival time and burst time are given below:
>
> | Process Id | Arrival time | Burst time |
> |---|---|---|
> | P1 | 3 | 1 |
> | P2 | 1 | 4 |
> | P3 | 4 | 2 |
> | P4 | 0 | 6 |
> | P5 | 2 | 3 |
>
> Apply SJF CPU scheduling with preemptive and Non Preemptive mode and calculate Average Waiting time and Turn around time. Draw the Gantt chart.

### (a) SJF — non-pre-emptive

At `t = 0` only P4 has arrived, so it runs to completion. At `t = 6`, choose the shortest burst among P1(1), P2(4), P3(2), P5(3).

**Gantt chart**

```text
0 ─────── 6 ─ 7 ─ 9 ── 12 ──── 16
   P4      P1  P3   P5      P2
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 3 | 1 | 7 | 3 | 4 |
| P2 | 1 | 4 | 16 | 11 | 15 |
| P3 | 4 | 2 | 9 | 3 | 5 |
| P4 | 0 | 6 | 6 | 0 | 6 |
| P5 | 2 | 3 | 12 | 7 | 10 |
| **Average** | | | | **4.80** | **8.00** |

### (b) SJF — pre-emptive (SRTF)

At `t = 1`, P2(4) is shorter than P4’s remaining 5 → pre-empt. At `t = 3`, P1(1) is shorter than P2’s remaining 2 → pre-empt. At `t = 4`, P3(2) and P2(2) tie; P2 arrived earlier → P2. Then P3, P5, P4.

**Gantt chart**

```text
0 ─ 1 ─── 3 ─ 4 ── 6 ── 8 ─── 11 ───── 16
  P4   P2   P1   P2   P3    P5       P4
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 3 | 1 | 4 | 0 | 1 |
| P2 | 1 | 4 | 6 | 1 | 5 |
| P3 | 4 | 2 | 8 | 2 | 4 |
| P4 | 0 | 6 | 16 | 10 | 16 |
| P5 | 2 | 3 | 11 | 6 | 9 |
| **Average** | | | | **3.80** | **7.00** |

**Conclusion:** pre-emptive SJF reduces average waiting time from 4.80 to 3.80 and average TAT from 8.00 to 7.00, at the cost of more context switches.

---

## 2.7 Scheduling Set 2 — AIML-F24 (FCFS and SJF pre-emptive) **[10]**

**Asked in:** P2 AIML-F24 Q2(b)(i).

**Question (verbatim):**
> Explain FCFS CPU scheduling algorithm along with its advantages and disadvantages. Consider the following processes with burst time. Calculate the average waiting time and average turnaround time for FCFS and SJF (preemptive).
>
> | Process id | Arrival time | Burst time/CPU execution time |
> |---|---|---|
> | P1 | 0 | 2 |
> | P2 | 1 | 3 |
> | P3 | 2 | 5 |
> | P4 | 3 | 4 |
> | P5 | 4 | 6 |

### (a) FCFS

**Gantt chart**

```text
0 ─ 2 ─── 5 ───── 10 ──── 14 ────── 20
  P1    P2      P3       P4        P5
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 2 | 2 | 0 | 2 |
| P2 | 1 | 3 | 5 | 1 | 4 |
| P3 | 2 | 5 | 10 | 3 | 8 |
| P4 | 3 | 4 | 14 | 7 | 11 |
| P5 | 4 | 6 | 20 | 10 | 16 |
| **Average** | | | | **4.20** | **8.20** |

### (b) SJF — pre-emptive (SRTF)

At `t = 2`, P1 finishes; P2(3) is shortest. At `t = 5`, P4(4) is shorter than P3(5) and P5(6). At `t = 9`, P3(5) is shorter than P5(6).

**Gantt chart**

```text
0 ─ 2 ─── 5 ─── 9 ───── 14 ────── 20
  P1    P2    P4      P3         P5
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 2 | 2 | 0 | 2 |
| P2 | 1 | 3 | 5 | 1 | 4 |
| P3 | 2 | 5 | 14 | 7 | 12 |
| P4 | 3 | 4 | 9 | 2 | 6 |
| P5 | 4 | 6 | 20 | 10 | 16 |
| **Average** | | | | **4.00** | **8.00** |

**FCFS advantages:** simple, FIFO, no starvation. **Disadvantages:** convoy effect, long average waiting time, poor for short jobs. **SJF advantage:** minimum average waiting time; **disadvantage:** burst time must be known and long jobs may starve.

---

## 2.8 Scheduling Set 3 — AIML-R24 (FCFS and RR, q = 2) **[10]**

**Asked in:** P4 AIML-R24 Q2(b)(i).

**Question (verbatim):**
> Consider the set of 6 processes whose arrival time and burst time are given below. Calculate the average waiting time and average turnaround time for following algorithms:
> i. FCFS
> ii. Round Robin with time quantum = 2
>
> | Process Id | Arrival time | Burst time |
> |---|---|---|
> | P1 | 0 | 4 |
> | P2 | 1 | 5 |
> | P3 | 2 | 2 |
> | P4 | 3 | 1 |
> | P5 | 4 | 6 |
> | P6 | 6 | 3 |

### (a) FCFS

**Gantt chart**

```text
0 ── 4 ─── 9 ─ 11 ─ 12 ─── 18 ── 21
  P1    P2   P3   P4    P5    P6
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 4 | 4 | 0 | 4 |
| P2 | 1 | 5 | 9 | 3 | 8 |
| P3 | 2 | 2 | 11 | 7 | 9 |
| P4 | 3 | 1 | 12 | 8 | 9 |
| P5 | 4 | 6 | 18 | 8 | 14 |
| P6 | 6 | 3 | 21 | 12 | 15 |
| **Average** | | | | **6.33** | **9.83** |

### (b) Round Robin, q = 2

Ready queue order used: P1, P2, P3, P1, P4, P5, P2, P6, P5, P2, P6, P5. A newly arrived process joins before a pre-empted process is re-queued.

**Gantt chart**

```text
0─2 P1 | 2─4 P2 | 4─6 P3 | 6─8 P1 | 8─9 P4 | 9─11 P5 |
11─13 P2 | 13─15 P6 | 15─17 P5 | 17─18 P2 | 18─19 P6 | 19─21 P5
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 4 | 8 | 4 | 8 |
| P2 | 1 | 5 | 18 | 12 | 17 |
| P3 | 2 | 2 | 6 | 2 | 4 |
| P4 | 3 | 1 | 9 | 5 | 6 |
| P5 | 4 | 6 | 21 | 11 | 17 |
| P6 | 6 | 3 | 19 | 10 | 13 |
| **Average** | | | | **7.33** | **10.83** |

The official key rounds average TAT to **10.84**; the difference is only rounding. The completion times and per-process values are the same.

---

## 2.9 Scheduling Set 4 — AIML-R24 (priority pre-emptive) **[10]**

**Asked in:** P4 AIML-R24 Q2(b)(ii).

**Question (verbatim):**
> Explain performance evaluation parameters in scheduling policies. Calculate average waiting time and turnaround time for Priority preemptive algorithm (Lower the number, higher the priority)
>
> | Process | Arrival Time | Burst Time | Priority |
> |---|---|---|---|
> | P1 | 0 | 2 | 2 |
> | P2 | 1 | 1 | 1 |
> | P3 | 2 | 8 | 4 |
> | P4 | 3 | 4 | 5 |
> | P5 | 4 | 5 | 3 |

**Answer**

Performance parameters are defined in 2.4: CPU utilization, throughput, TAT, WT, response time, fairness and predictability.

**Trace**

- `t = 0`: P1 runs (priority 2).
- `t = 1`: P2 arrives with priority 1 → pre-empts P1; P2 finishes at 2.
- `t = 2`: P1 resumes (remaining 1) and finishes at 3.
- `t = 3`: P3 (priority 4) runs; at `t = 4`, P5 (priority 3) arrives and pre-empts P3.
- P5 runs 4–9; P3 resumes 9–16; P4 (priority 5) runs 16–20.

```text
0─1 P1 | 1─2 P2 | 2─3 P1 | 3─4 P3 | 4─9 P5 | 9─16 P3 | 16─20 P4
```

| Process | Arrival | Burst | Priority | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|---|
| P1 | 0 | 2 | 2 | 3 | 1 | 3 |
| P2 | 1 | 1 | 1 | 2 | 0 | 1 |
| P3 | 2 | 8 | 4 | 16 | 6 | 14 |
| P4 | 3 | 4 | 5 | 20 | 13 | 17 |
| P5 | 4 | 5 | 3 | 9 | 0 | 5 |
| **Average** | | | | | **4.00** | **8.00** |

**Note:** P5 has zero waiting time because it starts immediately on arrival; P4 waits the longest because it has the lowest priority. Aging would gradually raise P4’s priority to prevent starvation.

---

## 2.10 Scheduling Set 5 — IT-F25 (FCFS and SJF pre-emptive) **[10]**

**Asked in:** P5 IT-F25 Q3(A)(i).

**Question (verbatim):**
> Consider the following snapshot of the processes.
>
> | Process | Burst Time | Arrival Time | Priority |
> |---|---|---|---|
> | P1 | 8 | 0 | 1 |
> | P2 | 20 | 1 | 3 |
> | P3 | 3 | 2 | 2 |
> | P4 | 6 | 3 | 5 |
> | P5 | 12 | 4 | 4 |
>
> Calculate the average waiting time and turn-around time for FCFS, SJF(Pre-emptive) Scheduling algorithm.

### (a) FCFS

```text
0 ─── 8 ─────── 28 ─ 31 ── 37 ───── 49
   P1       P2      P3    P4       P5
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 8 | 8 | 0 | 8 |
| P2 | 1 | 20 | 28 | 7 | 27 |
| P3 | 2 | 3 | 31 | 26 | 29 |
| P4 | 3 | 6 | 37 | 28 | 34 |
| P5 | 4 | 12 | 49 | 33 | 45 |
| **Average** | | | | **18.80** | **28.60** |

### (b) SJF — pre-emptive (SRTF)

```text
0─2 P1 | 2─5 P3 | 5─11 P1 | 11─17 P4 | 17─29 P5 | 29─49 P2
```

At `t = 2`, P3(3) pre-empts P1 (remaining 6). At `t = 5`, P1 (remaining 6) and P4 (6) tie; P1 arrived earlier → P1. Then P4, P5, P2.

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 8 | 11 | 3 | 11 |
| P2 | 1 | 20 | 49 | 28 | 48 |
| P3 | 2 | 3 | 5 | 0 | 3 |
| P4 | 3 | 6 | 17 | 8 | 14 |
| P5 | 4 | 12 | 29 | 13 | 25 |
| **Average** | | | | **10.40** | **20.20** |

These match the official key exactly. SRTF cuts average waiting time from 18.8 to 10.4.

---

## 2.11 Scheduling Set 6 — IT-R25 (FCFS and SJF, all arrive at 0) **[10]**

**Asked in:** P6 IT-R25 Q3(a)(i).

**Question (verbatim):**
> Q2. Consider the set of 5 processes whose burst time are given below:
>
> | Process | Burst Time(ms) |
> |---|---|
> | P1 | 5 |
> | P2 | 24 |
> | P3 | 16 |
> | P4 | 10 |
> | P5 | 3 |
>
> Apply FCFS and SJF algorithm and calculate Average Waiting time and Turnaround time. Draw the Gantt chart.

**Assumption:** no arrival times are printed, so all processes arrive at `t = 0`.

### (a) FCFS (P1, P2, P3, P4, P5)

```text
0 ─ 5 ───── 29 ──── 45 ─── 55 ─ 58
  P1     P2      P3      P4    P5
```

| Process | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|
| P1 | 5 | 5 | 0 | 5 |
| P2 | 24 | 29 | 5 | 29 |
| P3 | 16 | 45 | 29 | 45 |
| P4 | 10 | 55 | 45 | 55 |
| P5 | 3 | 58 | 55 | 58 |
| **Average** | | | **26.80** | **38.40** |

### (b) SJF — non-pre-emptive

Order by burst: P5(3), P1(5), P4(10), P3(16), P2(24).

```text
0 ─ 3 ─ 8 ── 18 ─── 34 ───── 58
  P5   P1   P4     P3       P2
```

| Process | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|
| P1 | 5 | 8 | 3 | 8 |
| P2 | 24 | 58 | 34 | 58 |
| P3 | 16 | 34 | 18 | 34 |
| P4 | 10 | 18 | 8 | 18 |
| P5 | 3 | 3 | 0 | 3 |
| **Average** | | | **12.60** | **24.20** |

**Conclusion:** SJF halves the average waiting time because it runs the short P5 first instead of the long P2.

---

## 2.12 Scheduling Set 7 — AIML-R25 (SJF and SRTF) **[10]**

**Asked in:** P7 AIML-R25 Q1(B)(i).

**Question (verbatim):**
> Consider the set of 6 processes whose arrival time and burst time are given below:
>
> | Process Id | Arrival time | Burst time |
> |---|---|---|
> | P1 | 0 | 7 |
> | P2 | 1 | 5 |
> | P3 | 2 | 3 |
> | P4 | 3 | 1 |
> | P5 | 4 | 2 |
> | P6 | 5 | 1 |
>
> Apply CPU scheduling policy that is Shortest Job First and shortest remaining time first (SRTF), calculate the average TAT and WT.

### (a) SJF — non-pre-emptive

P1 is the only process at `t = 0`, so it runs to completion. At `t = 7`, shortest is P4(1), then P6(1), P5(2), P3(3), P2(5).

```text
0 ───── 7 ─ 8 ─ 9 ─ 11 ── 14 ─── 19
   P1     P4  P6   P5    P3     P2
```

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 7 | 7 | 0 | 7 |
| P2 | 1 | 5 | 19 | 13 | 18 |
| P3 | 2 | 3 | 14 | 9 | 12 |
| P4 | 3 | 1 | 8 | 4 | 5 |
| P5 | 4 | 2 | 11 | 5 | 7 |
| P6 | 5 | 1 | 9 | 3 | 4 |
| **Average** | | | | **5.67** | **8.83** |

### (b) SRTF (pre-emptive SJF)

```text
0─1 P1 | 1─2 P2 | 2─3 P3 | 3─4 P4 | 4─6 P3 | 6─7 P6 | 7─9 P5 | 9─13 P2 | 13─19 P1
```

At `t = 4`, P3 (remaining 2, arrived 2) and P5 (2, arrived 4) tie → P3. At `t = 5`, P6 (1) arrives but P3 has 1 left and arrived earlier → P3 finishes first.

| Process | Arrival | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|---|
| P1 | 0 | 7 | 19 | 12 | 19 |
| P2 | 1 | 5 | 13 | 7 | 12 |
| P3 | 2 | 3 | 6 | 1 | 4 |
| P4 | 3 | 1 | 4 | 0 | 1 |
| P5 | 4 | 2 | 9 | 3 | 5 |
| P6 | 5 | 1 | 7 | 1 | 2 |
| **Average** | | | | **4.00** | **7.17** |

SRTF lowers average WT from 5.67 to 4.00.

---

## 2.13 Scheduling Set 8 — AIML-R25 (RR q = 3 and priority non-pre-emptive) **[10]**

**Asked in:** P7 AIML-R25 Q1(B)(ii).

**Question (verbatim):**
> Consider the set of 6 processes whose arrival time and burst time are given below. Draw Gantt chart & calculate average Turnaround Time, Waiting Time, response time and throughput for following CPU Scheduling algorithms: RR with TQ= 3ms and priority based non preemptive. Assume priority 0,1,2,3,4,5 respectively and consider lower the number highest the priority.
>
> | Process ID | AT | BT |
> |---|---|---|
> | P1 | 0 | 8 |
> | P2 | 1 | 4 |
> | P3 | 2 | 2 |
> | P4 | 3 | 1 |
> | P5 | 4 | 3 |
> | P6 | 5 | 2 |

Priority assignment: P1 = 0 (highest), P2 = 1, P3 = 2, P4 = 3, P5 = 4, P6 = 5 (lowest).

### (a) RR, q = 3

```text
0─3 P1 | 3─6 P2 | 6─8 P3 | 8─9 P4 | 9─12 P1 |
12─15 P5 | 15─17 P6 | 17─18 P2 | 18─20 P1
```

| Process | AT | BT | Completion | WT | TAT | Response |
|---|---|---|---|---|---|---|
| P1 | 0 | 8 | 20 | 12 | 20 | 0 |
| P2 | 1 | 4 | 18 | 13 | 17 | 2 |
| P3 | 2 | 2 | 8 | 4 | 6 | 4 |
| P4 | 3 | 1 | 9 | 5 | 6 | 5 |
| P5 | 4 | 3 | 15 | 8 | 11 | 8 |
| P6 | 5 | 2 | 17 | 10 | 12 | 10 |
| **Average** | | | | **8.67** | **12.00** | **4.83** |

**Throughput** = `6 / 20 = 0.30` processes per ms.

### (b) Priority — non-pre-emptive

Order: P1, P2, P3, P4, P5, P6.

```text
0 ─── 8 ─── 12 ─ 14 ─ 15 ── 18 ─ 20
   P1     P2    P3   P4    P5    P6
```

| Process | AT | BT | Priority | Completion | WT | TAT | Response |
|---|---|---|---|---|---|---|---|
| P1 | 0 | 8 | 0 | 8 | 0 | 8 | 0 |
| P2 | 1 | 4 | 1 | 12 | 7 | 11 | 7 |
| P3 | 2 | 2 | 2 | 14 | 10 | 12 | 10 |
| P4 | 3 | 1 | 3 | 15 | 11 | 12 | 11 |
| P5 | 4 | 3 | 4 | 18 | 11 | 14 | 11 |
| P6 | 5 | 2 | 5 | 20 | 13 | 15 | 13 |
| **Average** | | | | | **8.67** | **12.00** | **8.67** |

**Throughput** = `6 / 20 = 0.30` processes per ms.

**Comparison:** both give the same average TAT and WT here, but RR gives much better **response time** (4.83 vs 8.67) and is fairer; priority non-pre-emptive is simpler and respects importance but can starve low-priority jobs.

---

## 2.13A Scheduling Set 9 — Jan-24 (FCFS, SJF, RR q=10) **[10]**

**Asked in:** P8 Jan-24 Q3(1)(1).

> 
**Question (verbatim):**
> Consider the set of 5 processes whose burst time are given below. All Processes arrived at time 0.
>
> | Processes | Burst Time |
> |---|---|
> | P1 | 10 |
> | P2 | 29 |
> | P3 | 3 |
> | P4 | 7 |
> | P5 | 12 |
>
> Apply FCFS, SJF, Round Robin (quantum=10ms) CPU scheduling in Non Preemptive mode and calculate Average Waiting time and Average Turn around time. Draw the Gantt chart. Also comment which scheduling algorithm is better in terms of waiting time.

**Assumption:** FCFS and SJF are non-pre-emptive. RR is pre-emptive by quantum by definition; the phrase “Non Preemptive mode” is applied to FCFS/SJF, while RR uses `q = 10 ms`.

### (a) FCFS

```text
0 ─ 10 ───── 39 ─ 42 ─ 49 ─ 61
   P1       P2    P3    P4    P5
```

| Process | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|
| P1 | 10 | 10 | 0 | 10 |
| P2 | 29 | 39 | 10 | 39 |
| P3 | 3 | 42 | 39 | 42 |
| P4 | 7 | 49 | 42 | 49 |
| P5 | 12 | 61 | 49 | 61 |
| **Average** | | | **28.00** | **40.20** |

### (b) SJF — non-pre-emptive

Order: P3(3), P4(7), P1(10), P5(12), P2(29).

```text
0 ─ 3 ─ 10 ─ 20 ── 32 ───── 61
  P3   P4   P1    P5       P2
```

| Process | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|
| P1 | 10 | 20 | 10 | 20 |
| P2 | 29 | 61 | 32 | 61 |
| P3 | 3 | 3 | 0 | 3 |
| P4 | 7 | 10 | 3 | 10 |
| P5 | 12 | 32 | 20 | 32 |
| **Average** | | | **13.00** | **25.20** |

### (c) Round Robin, q = 10

```text
0─10 P1 | 10─20 P2 | 20─23 P3 | 23─30 P4 | 30─40 P5 |
40─50 P2 | 50─52 P5 | 52─61 P2
```

| Process | Burst | Completion | Waiting | Turnaround |
|---|---|---|---|---|
| P1 | 10 | 10 | 0 | 10 |
| P2 | 29 | 61 | 32 | 61 |
| P3 | 3 | 23 | 20 | 23 |
| P4 | 7 | 30 | 23 | 30 |
| P5 | 12 | 52 | 40 | 52 |
| **Average** | | | **23.00** | **35.20** |

**Comment:** SJF is best in terms of average waiting time (**13.00**), then RR (**23.00**), then FCFS (**28.00**). SJF is optimal for average waiting time when burst times are known; RR is fairer and better for response time; FCFS suffers from the convoy effect.

---

## Unit 2 — quick revision checklist

**Process fundamentals**

- [ ] Process definition; program passive vs process active; process memory = stack, heap, text, data.
- [ ] 5-state diagram + one line per state; 7-state extension (suspended ready/blocked).
- [ ] PCB fields (7 rows): state, PC, registers, scheduling, memory, accounting, I/O status.
- [ ] Job queue vs ready queue vs device queue.
- [ ] Long-term vs short-term vs medium-term scheduler; degree of multiprogramming; CPU-bound/I/O-bound mix.
- [ ] CPU–I/O burst cycle.
- [ ] Process vs thread: 6-row table + extra differences.
- [ ] ULT vs KLT; many-to-one, one-to-one, many-to-many models.
- [ ] Why process creation/termination is expensive.

**Scheduling concepts**

- [ ] Scheduling criteria: CPU utilization, throughput, TAT, WT, response time (+ fairness/predictability).
- [ ] Four scheduling circumstances: running→waiting, running→ready, waiting→ready, terminate.
- [ ] Pre-emptive vs non-pre-emptive: 6 differences.
- [ ] Formulas: CT, TAT = CT − AT, WT = TAT − BT, RT, averages, throughput, CPU utilization.
- [ ] Convoy effect, starvation, aging.

**Algorithms**

- [ ] FCFS: rule, non-pre-emptive, convoy effect.
- [ ] SJF non-pre-emptive and SRTF; burst prediction; tie by arrival/FCFS.
- [ ] Priority: pre-emptive/non-pre-emptive, internal vs external, equal priority = FCFS, aging.
- [ ] RR: quantum, circular ready queue, q too small → overhead, q too large → FCFS.
- [ ] Multilevel queue and multilevel feedback queue (standard).
- [ ] comparison tables: SJF/priority marked pre-emptive; CPU overhead/throughput/TAT/response.
- [ ] Be able to draw the Gantt chart and compute averages for any table.
- [ ] RR: show the ready queue; state whether a new arrival joins before a re-queued process.
- [ ] SJF: state whether pre-emptive or non-pre-emptive; for ties use arrival time.
- [ ] Jan-24 scheduling: FCFS 28.00/40.20, SJF 13.00/25.20, RR q=10 23.00/35.20 (FCFS/RR values match).
