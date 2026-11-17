# Unit 1 — Operating System Overview

> Syllabus: OS objectives, basic functions and services, types of OS, system calls and types, multiprocessor and multicore organization, OS design considerations for multiprocessor/multicore.

---

## 1.1 Define OS; objectives; functions; services **[5–10]**

**Asked in:** P4 AIML-R24 Q1(a)(i); P5 IT-F25 Q4(B); P7 AIML-R25 Q1(A); P2 AIML-F24 Q1(a)(i) (definition half); P3 IoT-F24 Q2(B)(1) (services half).

**Questions (verbatim):**
> Explain Objectives and functions of operating system in detail.

> Define an Operating System (OS). Explain the major objectives of an OS and discuss its basic functions. Also describe the various types of Operating Systems available in the market today with suitable examples.

> Describe the major services and functions of an operating system with suitable examples.

> Define OS. Explain system bus and its types in operating system.

> **Note:** the P2 question has two parts. The OS-definition part is answered here; the **system bus** part is answered in **1.3**.

**Answer**

An **operating system (OS)** is system software that acts as an intermediary between the user and the computer hardware. It manages all hardware and software resources, provides an environment in which programs can execute, and offers services that application programs use.

**Objectives**

1. **Convenience** — make the computer easy and comfortable to use.
2. **Efficiency** — use CPU, memory, I/O and storage efficiently.
3. **Ability to evolve** — allow new hardware, services and features to be added without breaking existing applications.

**Basic functions**

| Function | What the OS does |
|---|---|
| **Process management** | Create/terminate processes, schedule CPU, synchronize, communicate, handle deadlock |
| **Memory management** | Allocate/deallocate memory, track free space, implement paging/segmentation, virtual memory |
| **File management** | Create/delete files and directories, map files to blocks, control access |
| **Device management** | Manage device drivers, buffering, caching, spooling, interrupts and DMA |
| **Security and protection** | Authenticate users, enforce access rights, isolate processes |
| **Error detection and handling** | Detect hardware/software errors, take corrective action, log errors |
| **Accounting** | Record resource usage for billing and performance tuning |
| **User interface** | CLI, GUI, batch interface |

**Services (what the OS offers to programs/users)**

- Program execution (load, run, terminate).
- I/O operations (read/write files and devices).
- File-system manipulation (create, delete, list, search).
- Communication (shared memory, message passing, pipes, sockets).
- Error detection and reporting.
- Resource allocation (CPU, memory, devices).
- Accounting and logging.
- Protection and security.
- Networking and distributed services (in modern OSs).

**Types of OS (short list for the 10-mark version)**

Serial processing, simple batch, multiprogramming batch, time-sharing (multi-tasking), multiprocessing/parallel, distributed, real-time (hard/soft), network, embedded, mobile, desktop/server. Details are in 1.2.

---

## 1.2 Types of OS — serial, batch, multiprogramming, time-sharing, real-time, parallel, distributed **[5–10]**

**Asked in:** P4 AIML-R24 Q1(a)(ii) (serial + simple batch); P4 AIML-R24 Q1(b) (distributed); P2 AIML-F24 Q1(b) (RTOS + parallel); P5 IT-F25 Q4(B) (types in general); P8 Jan-24 Q4(1) (distributed + real-time).

**Questions (verbatim):**
> Explain Serial Processing and Simple Batch Systems.

> Define and explain the characteristics of a distributed operating system.

> Discuss the characteristics and applications of the following operating systems:
> i. Real-time Operating System
> ii. Parallel Operating System

> Write a brief note on distributed & real-time Operating Systems

**Answer**

### (a) Serial processing

- No OS; the programmer interacted directly with the hardware (lights, switches, card reader, printer).
- A job was loaded in machine code; errors were shown by lights; output came on a printer.
- Problems:
  - **Scheduling:** users signed a hard-copy time sheet; a user might finish early (wasted time) or overrun (forced to stop).
  - **Setup time:** loading compiler, source, object program, tapes and card decks took a long time; one error forced the user to restart the setup.
- Users had access to the computer **in series**, hence “serial processing”.

### (b) Simple batch system

- A **monitor** (resident program) controls the sequence of jobs.
- The user submits jobs on cards/tape to an operator, who batches them; the user has no direct access to the processor.
- The monitor loads the next job automatically; each program branches back to the monitor on completion.
- Advantages: reduced setup time, better utilization than serial processing.
- Disadvantages: no interaction, CPU idle during I/O, difficult debugging, job scheduling is still manual.

### (c) Multiprogramming batch

- Several jobs are kept in memory; when the running job waits for I/O, the OS switches the CPU to another job.
- Goals: maximize CPU utilization and throughput.
- Needs memory protection, job scheduling and I/O management.

### (d) Time-sharing (multi-tasking)

- The CPU switches rapidly between jobs using a **time slice (quantum)**.
- Each user gets the impression of a dedicated machine; response time is the key criterion.
- Needs pre-emptive scheduling, memory protection and synchronization.

### (e) Real-time operating system (RTOS)

- Correctness depends on **both the result and the time** at which it is produced.
- **Hard real-time:** missing a deadline is a failure (airbag, flight control).
- **Soft real-time:** missing a deadline degrades quality but is not catastrophic (video streaming).
- Characteristics: determinism/predictability, priority-based pre-emptive scheduling, bounded interrupt latency, small overhead, reliability, resource efficiency.
- Applications: embedded systems, medical devices, industrial control, aerospace, robotics.

### (f) Parallel operating system

- Manages multiple processors/cores executing parts of a program simultaneously.
- Characteristics: parallel processing, synchronization and communication, load balancing, fault tolerance, scalability.
- Applications: supercomputing, scientific simulation, big-data analytics, cloud, real-time data processing.

### (g) Distributed operating system

- Manages a group of independent, networked computers and makes them appear as **one system**.
- Characteristics:
  - **Transparency** — user does not see the distribution.
  - **Resource sharing** — processors, memory, storage, printers across machines.
  - **Fault tolerance** — replication and redundancy.
  - **Scalability** — add machines without redesign.
  - **Concurrency** — processes on different machines run and synchronize concurrently.
- Examples: cloud platforms, Hadoop-style clusters, distributed Android device systems.

### (h) Other types

Network OS (each machine has its own OS, file/print sharing), embedded OS (fixed function, small footprint), mobile OS (Android/iOS), desktop/server OS (Windows/Linux/macOS).

---

## 1.2A Uni-programming vs multi-programming vs multi-tasking vs multi-processing vs multi-threading **[5]**

**Asked in:** P8 Jan-24 Q1(1).

**Question (verbatim):**
> Describe the terms uni-programming, multi-programming, multi-tasking, multi-processing, and multi- threading

**Answer**

| Term | Meaning | Key point |
|---|---|---|
| **Uni-programming** | Only **one program** is in memory at a time; the CPU runs it until it finishes or blocks | CPU sits idle during I/O; poor utilization |
| **Multi-programming** | Several programs are kept in memory; when the running program waits for I/O, the CPU switches to another | Increases **CPU utilization** and throughput |
| **Multi-tasking** | A time-sharing variant: the CPU switches rapidly between tasks using a **time quantum** | Improves **response time** for interactive users; each task gets a slice |
| **Multi-processing** | Two or more **CPUs/cores** execute processes/threads simultaneously | True hardware parallelism; needs scheduling, synchronization and cache coherence |
| **Multi-threading** | Multiple **threads** inside one process share code/data/heap but have their own stack/registers | Concurrent execution within a process; cheap context switch |

**One-line distinctions**

- Multi-programming vs multi-tasking: multi-programming maximizes CPU utilization by overlapping I/O; multi-tasking minimizes response time by time-slicing.
- Multi-processing is a **hardware** property (multiple processors); multi-threading is a **program/OS** property (multiple execution flows).
- Multi-threading can run on a single CPU (interleaved) or on multiple cores (parallel); multi-processing always requires multiple CPUs/cores.

---

## 1.3 System bus and its types **[5]**

**Asked in:** P2 AIML-F24 Q1(a)(i).

**Question (verbatim):**
> Define OS. Explain system bus and its types in operating system.

**Answer**

An **operating system (OS)** is system software that manages the hardware and software resources of a computer and provides services to application programs and users. It acts as the intermediary between the user and the hardware.

The **system bus** is the set of communication lines that connects the CPU, main memory and I/O devices. It is the shared pathway over which data, addresses and control signals travel. The OS relies on it for every instruction fetch, memory access and device transfer.

| Bus | Carries | Direction / role |
|---|---|---|
| **Data bus** | Actual data between CPU, memory and I/O | Bidirectional; width determines how many bits move at once |
| **Address bus** | Address of the memory location or I/O port | Usually unidirectional (CPU → memory/I/O); width determines addressable space |
| **Control bus** | Read/write, interrupt, clock, reset, bus-request/grant signals | Coordinates and synchronizes the transfer |

**Why it matters to the OS:** the OS schedules processes so that the CPU keeps the bus busy; it programs DMA controllers to move blocks without CPU involvement; it handles interrupts generated by devices on the control bus; and it must arbitrate bus access when several processors or DMA engines compete for it.

---

## 1.4 Microkernel architecture; microkernel vs monolithic **[5–10]**

**Asked in:** P2 AIML-F24 Q1(a)(ii); P7 AIML-R25 Q2(A).

**Questions (verbatim):**
> Describe architecture of a typical microkernel along with suitable diagram.

> Describe the microkernel architecture and explain how it improves modularity compared to monolithic OS.

**Answer**

A **microkernel** keeps only the minimum essential mechanisms in kernel mode:

- low-level memory management,
- basic inter-process communication (IPC),
- CPU scheduling,
- interrupt/exception handling.

All other services — file systems, device drivers, networking, paging policies — run as **user-space server processes**. They communicate with each other and with the kernel by **message passing**.

```text
+--------------------------------------------------+
|  User applications                                |
+--------------------------------------------------+
| File server | Device driver | Network server | ... |  <- user space
+--------------------------------------------------+
|              Microkernel (IPC, scheduling,        |
|              basic memory, interrupts)            |  <- kernel space
+--------------------------------------------------+
|              Hardware                             |
+--------------------------------------------------+
```

**How it improves modularity compared with a monolithic OS**

| Point | Microkernel | Monolithic |
|---|---|---|
| Kernel size | Small; only essential mechanisms | Large; most services in kernel |
| Service location | User-space servers | Kernel procedures |
| Adding a service | Add/replace a server; no kernel rebuild | Recompile/rebuild kernel, often reboot |
| Fault isolation | A crashed server can be restarted; kernel survives | A driver bug can crash the whole system |
| Security | Smaller trusted computing base | Large attack surface |
| Communication | Message passing (IPC overhead) | Direct procedure calls (fast) |
| Performance | More context switches and copying | Faster for tightly coupled services |
| Debugging | Easier; services are ordinary processes | Harder; kernel debugging |

**Trade-off:** microkernels are more modular, secure and reliable, but message-passing overhead can reduce performance. Examples: Mach, QNX, L4, seL4; hybrid designs (Windows NT, macOS XNU) keep some services in the kernel for speed.

---

## 1.5 Multiprocessor vs multicore; OS design considerations **[10]**

**Asked in:** P1 IT-F24 Q4(b); P6 IT-R25 Q4(b).

**Questions (verbatim):**
> Explain some of the key design considerations for an operating system when managing a multiprocessor or multicore system?

> Explain the difference between a multiprocessor and a multicore system.

**Answer**

### Multiprocessor vs multicore

| Aspect | Multiprocessor | Multicore |
|---|---|---|
| What it is | Two or more **separate physical processors** (sockets) on one or more boards | Two or more **cores on a single chip/die** |
| Memory | May be shared (SMP) or distributed (NUMA); each CPU may have its own cache | Usually shared last-level cache and shared main memory |
| Communication | Through the system bus/interconnect; higher latency | On-chip interconnect; much lower latency |
| Cost/power | Higher cost, more board space, more power | Lower cost per core, better power efficiency |
| Failure | A processor can fail independently | A core failure may affect the whole chip |
| Scaling | Scale by adding sockets/boards | Scale by adding cores within a chip |
| Example | Dual-socket Xeon server | 8-core Ryzen / Apple M-series |

### Design considerations for the OS

1. **Processor scheduling and load balancing** — distribute runnable threads evenly; avoid one core idle while another is overloaded; consider affinity.
2. **Synchronization and coordination** — locks, semaphores and atomic instructions must work across processors; minimize contention and false sharing.
3. **Cache coherence** — keep per-core caches consistent (e.g. MESI: Modified, Exclusive, Shared, Invalid).
4. **Memory management** — shared page tables, TLB shootdowns, NUMA-aware allocation, memory barriers.
5. **Inter-process communication** — efficient shared memory/message passing; avoid excessive copying.
6. **Scalability** — per-CPU data structures, fine-grained locks, avoid a single global lock.
7. **Fault tolerance and reliability** — detect a failed processor/core, migrate its work, degrade gracefully.
8. **Thread management** — kernel threads, thread affinity, gang scheduling for parallel programs.
9. **Interrupt handling** — route interrupts to cores, balance interrupt load, avoid interrupt storms.
10. **Energy efficiency** — dynamic voltage/frequency scaling, idle core parking, big.LITTLE scheduling.
11. **Process/thread migration** — move work to idle cores while preserving cache locality.
12. **Real-time constraints** — bounded latency and priority inheritance across cores.

---

## 1.6 OS architecture with services — monolithic, layered, microkernel, modular **[10]**

**Asked in:** P3 IoT-F24 Q2(B)(1).

**Question (verbatim):**
> Draw and Explain Operating System Architecture with significant operating system services.

**Answer**

```text
+-----------------------------------------------------------+
|                    User applications                       |
+-----------------------------------------------------------+
|  System-call interface / API (library calls, traps)        |
+-----------------------------------------------------------+
|  OS services: process, memory, file, device, protection,   |
|               networking, accounting                       |
+-----------------------------------------------------------+
|  Kernel: scheduling, IPC, memory manager, drivers, FS      |
+-----------------------------------------------------------+
|  Hardware: CPU, memory, disks, controllers, devices        |
+-----------------------------------------------------------+
```

**Architectural styles**

| Architecture | Structure | Advantage | Disadvantage |
|---|---|---|---|
| **Monolithic** | All services in one kernel, direct calls | Fast | Large, hard to maintain, one bug can crash all |
| **Layered** | Layers 0…N; each layer uses only lower layers | Modular, easy to debug | Layer crossing overhead; hard to define layers |
| **Microkernel** | Minimal kernel + user-space servers | Reliable, secure, extensible | IPC overhead |
| **Modular** | Loadable kernel modules | Add drivers without reboot | Module interface/versioning complexity |
| **Client–server** | Clients request services from servers | Distribution, isolation | Network/message overhead |
| **Virtual machine** | Hypervisor runs guest OSs | Isolation, consolidation | Resource overhead |

**Significant services** (same list as 1.1): program execution, I/O, file-system operations, communication, error detection, resource allocation, accounting, protection and security. The **system-call interface** is the boundary: user programs call library routines, which trap into the kernel; the kernel validates arguments and performs the operation.

---

## 1.7 System calls and their types **[5]** (syllabus topic, not directly asked in the included papers)

**Question type:** “What is a system call? Explain its types with examples.”

**Answer**

A **system call** is the programmatic interface through which a user process requests a service from the kernel. The process executes a trap/software interrupt, switches to kernel mode, and the kernel performs the operation and returns a result. Examples: `fork()`, `exec()`, `open()`, `read()`, `write()`, `wait()`, `malloc()` (which may call `brk`/`mmap`).

| Type | Purpose | Examples |
|---|---|---|
| **Process control** | Create/terminate/wait/load processes | `fork`, `exec`, `exit`, `wait`, `kill` |
| **File management** | Create/open/read/write/close files | `open`, `read`, `write`, `close`, `lseek`, `unlink` |
| **Device management** | Request/release/read/write devices | `ioctl`, `read`, `write`, `mount`, `umount` |
| **Information maintenance** | Get/set time, process attributes, system data | `getpid`, `alarm`, `sleep`, `time` |
| **Communication** | Create/connect/send/receive messages | `pipe`, `socket`, `send`, `recv`, `shmget` |
| **Protection** | Get/set permissions | `chmod`, `chown`, `umask` |

**Parameter passing methods:** registers, a block/table in memory whose address is in a register, or the stack. The kernel must validate every pointer and permission before using user-supplied addresses.

---

## Unit 1 — quick revision checklist

- [ ] OS definition + 3 objectives + 8 functions.
- [ ] Difference between function (what the OS does) and service (what it offers).
- [ ] Serial → simple batch → multiprogramming → time-sharing evolution; what problem each solved.
- [ ] RTOS: hard vs soft, determinism, priority, bounded latency; 3 applications.
- [ ] Parallel OS: load balancing, synchronization, scalability; 3 applications.
- [ ] Distributed OS: transparency, resource sharing, fault tolerance, scalability, concurrency.
- [ ] Microkernel diagram + 4 advantages + IPC overhead trade-off.
- [ ] System bus: data/address/control; one line each.
- [ ] Multiprocessor vs multicore: 5 differences.
- [ ] 10 OS design considerations for multiprocessor/multicore.
- [ ] System-call types table (6 rows) + parameter passing.
- [ ] Uni-programming vs multi-programming vs multi-tasking vs multi-processing vs multi-threading (Jan-24).
