# Operating System (DJS23) — Past-Year Question Bank

**Unit-wise question bank with answers, built from DJS23-scheme papers plus the requested 2023-24 paper.**
Every entry quotes the question **verbatim** from the paper; the heading is only a navigation label.

Syllabus used: **Operating System (DJS23IMD201)**, S.Y. B.Tech IT, Semester III, DJS23 Version 2 scheme (14 weeks).

> The companion file for last-minute revision is **[OS_Last_Minute_Revision.md](OS_Last_Minute_Revision.md)**.

---

## 1. Syllabus at a glance

| Unit | Topics | Hours |
|---|---|---:|
| **1** | OS objectives, basic functions and services, types of OS, system calls and types, multiprocessor and multicore organization, OS design considerations for multiprocessor/multicore | 05 |
| **2** | Process concept, process states, process control, threads, uni-processor scheduling, pre-emptive/non-pre-emptive, FCFS, SJF, RR, Priority | 06 |
| **3** | Principles of concurrency, mutual exclusion (software approaches, hardware support), semaphores, monitors, Readers–Writers, Producer–Consumer | 04 |
| **4** | Fixed and variable partitioning, First Fit/Best Fit/Worst Fit, fragmentation, swapping, virtual memory, paging, segmentation, demand paging, page replacement | 05 |
| **5** | Principles of deadlock, prevention, avoidance, detection, Dining Philosophers; peripheral device management: programmed I/O, interrupt-driven I/O, DMA | 04 |
| **6** | I/O devices, I/O buffering, disk scheduling: FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK, RAID | 04 |

**Evaluation:** DJS23 papers: 60 marks, 2 hours, 4 questions × 15 marks with internal `OR`. The added 2023-24 paper: **65 marks**, 4 questions (20 + 15 + 15 + 15).

---

## 2. Papers used (7 DJS23 papers + the requested 2023-24 paper)

| # | Short name | Programme | Date | Type | Marks | Official key? |
|---|---|---|---|---|---|---|
| P1 | **IT-F24** | B.Tech IT | 20 Dec 2024 | Final 2024-25 | 60 | Yes (`ANS/DJS23ICMD301`) |
| P2 | **AIML-F24** | B.Tech AIML | 20 Dec 2024 | Final 2024-25 | 60 | Yes (`ANS/DJS23ACMD301`) |
| P3 | **IoT-F24** | B.Tech CSE (IoT & Cyber Security with Blockchain) | 20 Dec 2024 | Final 2024-25 | 60 | No |
| P4 | **AIML-R24** | B.Tech AIML | 26 Mar 2025 | Re-exam 2024-25 | 60 | Yes (`ANS/DJS23ACMD301`) |
| P5 | **IT-F25** | B.Tech IT | 13 Dec 2025 | Final 2025-26 | 60 | Yes (`ANS/Sem-III-IT`) |
| P6 | **IT-R25** | B.Tech IT | 01 Apr 2026 | Re-exam 2025-26 (paper printed “RE EXAMINATION 2024-2025”) | 60 | No |
| P7 | **AIML-R25** | B.Tech AIML | 01 Apr 2026 | Re-exam 2024-25 & 2025-26 | 60 | No |
| **P8** | **Jan-24** | B.Tech AIDS / AIML / IoT / IT | 12 Jan 2024 | Final 2023-24 (previous batch) | **65** | No |

**Official keys used:** P1, P2, P4, P5. For P3, P6, P7 and P8 the answers are fully written/re-solved. Where an official key answer was an image, the answer was re-solved and checked.

> **Code note:** the syllabus PDF lists the IT course as **DJS23IMD201**, while the 2024-25 papers carry branch codes **DJS23ICMD301 / DJS23ACMD301 / DJS23BCMD301** (IT / AIML / IoT-Cyber). The unit content is the same six-unit OS syllabus, so all DJS23 papers are used. The 2025-26 papers carry no code on the first page but follow the same pattern.
>
> **P8 note:** the Jan 2024 paper is from the **2023-24** exam (previous batch) and has a 65-mark pattern. It is included because you asked for it; its questions are tagged **Jan-24** and are mixed into the same unit files. It has no official key in the folder, so its answers are fully written.

### Papers deliberately excluded

These files are in `OS/QP/` but are **pre-2023-24** papers, so they were not mixed into the bank:

| File | Why excluded |
|---|---|
| `IT Sem III_OS_Feb 2022.pdf` | Course code DJ19ITC305 — pre-DJS23 |
| `Operating System QP IT.pdf` (27 Jan 2023) | 2022-23 scheme |
| `Operating Systems QP AIML & AIDS.pdf` (27 Jan 2023) | 2022-23 scheme |
| `Operating Systems - QP AIDS & AIML.pdf` (31 Mar 2023) | 2022-23 re-exam |

They are still useful as extra practice, but their questions and unit split do not match the DJS23 syllabus exactly. The **Jan 2024** paper is now included as P8 above.

---

## 3. Unit map — where each question lives

| Unit | File | What is covered |
|---|---|---|
| 1 | [Unit_1_OS_Overview.md](Unit_1_OS_Overview.md) | OS definition/objectives/functions/services, types of OS, uni/multi-programming terms, serial/batch, microkernel, system bus, OS architecture, multiprocessor vs multicore, design considerations |
| 2 | [Unit_2_Process_Management_and_Scheduling.md](Unit_2_Process_Management_and_Scheduling.md) | Process states, PCB, process vs thread, ULT vs KLT, process creation/termination, pre-emptive vs non-pre-emptive, FCFS, SJF/SRTF, RR, Priority, comparison of algorithms |
| 3 | [Unit_3_Concurrency_Control.md](Unit_3_Concurrency_Control.md) | Principles of concurrency, race conditions, critical section, mutual exclusion, hardware support, semaphores, monitors, Producer–Consumer, Readers–Writers, IPC |
| 4 | [Unit_4_Memory_Management.md](Unit_4_Memory_Management.md) | Fixed/variable partitioning, fragmentation, First/Best/Worst/Next Fit, swapping, paging, segmentation, virtual memory, demand paging, page replacement |
| 5 | [Unit_5_Deadlock_and_Peripheral_Devices.md](Unit_5_Deadlock_and_Peripheral_Devices.md) | Deadlock conditions, prevention, avoidance (Banker’s), detection (RAG), recovery, Dining Philosophers, programmed I/O, interrupt-driven I/O, DMA |
| 6 | [Unit_6_IO_Management_and_Disk_Scheduling.md](Unit_6_IO_Management_and_Disk_Scheduling.md) | I/O management role, I/O buffering, disk structure/performance, FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK, RAID (asked in Jan-24) |
| All | [OS_Last_Minute_Revision.md](OS_Last_Minute_Revision.md) | One-file cheat sheet: formulas, methods, question templates, past-answer tables |

---

## 4. Verbatim-question convention

- Every entry starts with **Question (verbatim)** or **Questions (verbatim)**.
- The heading above it is a short label, not a replacement for the question.
- Where the same question appears in several papers with different wording, **each variant is quoted**.
- Where duplicate questions were merged, every source paper is listed under **Asked in**.
- For image-based questions (tables, schedules, diagrams), the text is quoted and the table/diagram is reconstructed in the answer.
- OCR papers (P6, P7 and the scanned 2025-26 files) may have minor character-level artefacts; obvious OCR errors were cleaned, wording was preserved.

---

## 5. Official-key discrepancies and assumptions

These are the places where the official key and a correct re-solution do not agree, or where the paper is ambiguous. **Read these before the exam** — they are the most likely places to lose marks by copying a wrong key.

### 5.1 P5 IT-F25 — LRU page faults (official key says 10; correct is 14)

- Reference string: `4, 1, 2, 4, 5, 1, 2, 4, 6, 1, 2, 1, 4, 5, 3, 6`, 3 frames.
- Official key: FIFO = 12, **LRU = 10**, Optimal = 9.
- Re-solution: FIFO = 12, **LRU = 14**, Optimal = 9.
- FIFO and Optimal match the key; the LRU figure does not. The step-by-step LRU trace is in Unit 4 (Page Set 5). **Write the trace, not just the number** — that is what earns marks.

### 5.2 P5 IT-F25 — Banker’s table is unsafe as printed

- The paper prints `P0 Allocation = (0, 0, 1)`, `Max = (7, 5, 3)`, so `Need(P0) = (7, 5, 2)`.
- Total B in the system = allocated B (1) + available B (3) = **4**, but P0 needs **5** B. Therefore **no safe sequence exists**. The official key also concludes “not safe” and “request cannot be granted”.
- If the intended standard textbook table had `P0 Allocation = (0, 1, 0)`, the system would be safe (e.g. `P1 → P3 → P4 → P0 → P2`). The paper as printed is unsafe; the answer follows the printed table and flags the likely typo.

### 5.3 P1 IT-F24 — Worst Fit placement of 110 KB

- Official key: 220 → 550 (left 330), 430 → 500 (left 70), **110 → 300**, 425 unallocated.
- Standard worst-fit (including the 330 KB leftover): 220 → 550 (left 330), 430 → 500 (left 70), **110 → 330** (left 220), 425 unallocated.
- The final result (425 unallocated) is the same. The worked answer uses the standard rule and notes the key’s variant.

### 5.4 P1 IT-F24 — Banker’s safe sequence

- The official key gives `<P0, P2, P3, P4, P1>`. This is valid. Several other safe sequences also exist; any one is acceptable if the work table is shown.

### 5.5 P6 IT-R25 — “170 tracks”

- `170 tracks` is interpreted as tracks `0–169`. With C-SCAN the arm goes to 169, jumps to 0, then services the low requests.
- If your class uses `0–170` (171 tracks), every SCAN/C-SCAN total increases by 1–2. State your assumption.

### 5.6 P6 IT-R25 — SJF table has no arrival times

- The scheduling table gives only burst times. The answer assumes **all processes arrive at time 0**, which is the only interpretation that allows FCFS/SJF to be computed. State this assumption.

### 5.7 P7 AIML-R25 — “total available resources”

- The question gives **total instances** `R1=9, R2=3, R3=6` and an allocation matrix. The available vector is therefore
  `Available = Total − ΣAllocation = (9,3,6) − (9,2,5) = (0,1,1)`.
- Do **not** use `(9,3,6)` directly as Available.

### 5.8 P3 IoT-F24 — page-replacement policy not named

- Q1(B)(2) says only “Using Page replacement policies”, with no algorithm list. The answer solves **FIFO, LRU and Optimal**, and quotes all three counts. In the exam, write the algorithm name above each table.

### 5.9 P3 IoT-F24 — linked allocation lacks block/pointer size

- The question gives 800 blocks and a 200-block file but no block size or pointer size. The answer gives the standard formula and both common interpretations (pointer stored in each block vs separate pointer block). This question is out of the DJS23 syllabus anyway (file allocation).

### 5.10 Further assumptions used throughout

| Situation | Assumption |
|---|---|
| Scheduling table has no arrival column | All arrive at `t = 0` |
| “SJF” without “pre-emptive” | Non-pre-emptive SJF |
| “SJF (pre-emptive)” / SRTF | Pre-emptive shortest remaining time |
| Priority | **Lower number = higher priority** only where the paper says so; otherwise state it |
| RR | Newly arrived processes join the ready queue before a pre-empted process is re-queued |
| Page replacement | Frames shown as the resident set after the reference; `F` = fault, `H` = hit |
| Disk scheduling | Track range as printed; SCAN goes to the end of the range, LOOK stops at the last request |
| Banker’s | Need = Max − Allocation; request is granted only if the tentative state is safe |

### 5.11 P8 Jan-24 — pattern and assumptions

- **Pattern:** 65 marks, Q1 = 20 (four 5-mark parts), Q2 = 15 (10 + 5), Q3 = 15 (10 + 5), Q4 = 15 (5 + 5 + 5). This is **not** the 60-mark DJS23 pattern.
- **Q3(1) “Round Robin … in Non Preemptive mode”:** RR is pre-emptive by quantum by definition. The answer treats FCFS and SJF as non-pre-emptive and runs **RR with q = 10**; state this interpretation.
- **Q2(1)(2) RAG:** R2 has two instances. The cycle `P1 → R2 → P2 → R1 → P1` is **not automatically a deadlock** because P3 holds the second R2 instance and is not waiting. The answer assumes P3 can finish and release R2; then the safe sequence is `P3 → P1 → P2`. If P3 never releases, the cycle can deadlock.
- **Q3(1)(2) disk:** the previous request 1805 < current 2150, so the head is moving **upward**; C-SCAN services upward, jumps to 0, then services the low requests.
- **Q1(1) scheduling:** all processes arrive at `t = 0`.
- **Q4(2)(2) first fit:** 425 KB remains unallocated.

---

## 6. Out-of-syllabus questions (listed, not dropped)

These appeared in the included papers but are outside the six units of `DJS23IMD201`. They are collected here so nothing is silently omitted. Short answers/solutions are in the **Appendix** at the end of this file.

| Paper | Question | Why out of syllabus |
|---|---|---|
| P2 AIML-F24 | Q1(a)(i): “Define OS. Explain system bus and its types…” | **Partly out:** the OS definition is in Unit 1; the system bus is computer-organization content. It is still answered in Unit 1 §1.3. |
| P3 IoT-F24 | Q1(A): fixed-point addition/subtraction (`010010.1 + 0110.110`, etc.) | Computer organization / arithmetic, not OS |
| P3 IoT-F24 | Q3(B): linked allocation, 800 blocks, 200-block file, total space and average accesses | File systems / file allocation, not in the six units |
| P3 IoT-F24 | Q4(A): Program Counter (PC) and Stack Pointer (SP) | Computer organization / CPU architecture |
| P3 IoT-F24 | Q2(A): “File management and I/O Management” | Only the **I/O management** half is in Unit 6; file management is not a syllabus unit |
| **P8 Jan-24** | **Q1(2): “Discuss pile and sequential file organization methods with their advantages.”** | File organization / file systems, not in the six OS units |
| P7 AIML-R25 | Q4(B)(ii): **Next-fit** allocation | Syllabus lists only First/Best/Worst Fit; Next-fit is an extension |

### Verbatim text of the out-of-syllabus questions

> **P2 AIML-F24 Q1(a)(i):** Define OS. Explain system bus and its types in operating system.

> **P3 IoT-F24 Q1(A):** Discuss how an arithmetic operation is performed on fixed-point numbers with following examples.
> i. Addition: 010010.1 with 0110.110
> ii Subtraction: 0110.110 from 010010.1

> **P3 IoT-F24 Q3(B):** A disk has 800 blocks, and a file requires 200 blocks. If the file is allocated using linked allocation, where each block points to the next block , calculate:
> • The total space used for storing the file (including pointer space).
> • The average number of disk accesses to retrieve a block from this file.

> **P3 IoT-F24 Q4(A):** Elaborate the importance of the Program Counter (PC) and Stack Pointer (SP) in the CPU’s operation. How do they contribute to program execution?

> **P3 IoT-F24 Q2(A):** Distinguish the important key aspects of File management and I/O Management.

> **P8 Jan-24 Q1(2):** Discuss pile and sequential file organization methods with their advantages.

> **P7 AIML-R25 Q4(B)(ii):** Given six memory partitions of 300 KB,600KB,350KB,200KB,750KB, and 125 KB (in order), how would the Next and Worst-fit algorithms place processes of size 115 KB,500KB,358KB,200KB,and 375 KB (in order)? Rank the algorithms in terms of how efficiently they use memory.

Everything else is mapped into Units 1–6.

---

## 7. Syllabus topics not directly asked in these papers

These are still examinable because they are printed in the syllabus. The revision file gives the minimum needed:

- **Unit 1:** system calls and their types (process control, file, device, information, communication, protection).
- **Unit 2:** user-level vs kernel-level threads were not asked in the DJS23 papers, but **P8 Jan-24 Q1(4)(1)** asks them; the table is in Unit 2 §2.2A.
- **Unit 4:** swapping; TLB/effective access time; Belady’s anomaly. Demand paging is now asked in **P8 Jan-24 Q1(4)(2)**.
- **Unit 5:** full detection algorithm for multiple instances (P8’s RAG question is analysed in Unit 5 §5.8, but the formal algorithm is still worth revising).
- **Unit 6:** C-LOOK; I/O devices and device controllers. RAID is now asked in **P8 Jan-24 Q2(2)** and covered in Unit 6 §6.9.

---

## 8. How to use this bank

1. **First pass:** read the unit file for the topic, cover the answer, try the verbatim question.
2. **Numericals:** redo the Gantt chart / page table / Banker’s table on paper without looking. The files show every intermediate step.
3. **Last night:** use `OS_Last_Minute_Revision.md` only — formulas, methods, past-answer tables and question templates.
4. **Before the exam:** read Section 5 (discrepancies) again; those are the traps.

---

## Appendix — short answers to out-of-syllabus questions

### A1. P3 Q1(A) — fixed-point addition and subtraction

**Addition: `010010.1 + 0110.110`**

Align the binary points:

```text
  010010.100
+ 000110.110
-----------
  011001.010
```

Result: **`011001.010`** (binary) = 25.25 decimal.

**Subtraction: `010010.1 − 0110.110`**

```text
  010010.100
− 000110.110
-----------
  001011.110
```

Result: **`001011.110`** (binary) = 11.75 decimal.

Method: align the points, pad with zeros, then add/subtract column by column with carry/borrow; the point stays in the same column.

### A2. P3 Q3(B) — linked allocation

Given: 800 disk blocks, file = 200 blocks, linked allocation (each block points to the next).

- **Space for the file:** the 200 data blocks are needed. The pointer is normally stored **inside each block**, so the file still occupies **200 blocks**; the usable data per block is reduced by the pointer size. If the pointer is kept in a **separate** pointer block for every data block, the file occupies `200 + 200 = 400` blocks.
- **Average disk accesses to retrieve a block:** sequential search from the first block takes `1, 2, …, 200` accesses; average = `(200 + 1)/2 = 100.5 ≈ 101` block accesses. This is the main disadvantage of linked allocation: no direct access.

### A3. P3 Q4(A) — Program Counter and Stack Pointer

- **Program Counter (PC):** holds the address of the **next instruction** to fetch. After each fetch it is incremented (or loaded with a branch target), so the CPU always knows where to continue. During a context switch the PC is saved in the PCB so the process can resume at the same instruction.
- **Stack Pointer (SP):** points to the **top of the current stack**. It is used for function calls/returns, saving return addresses, local variables and interrupt/context frames. A call pushes the return address and decrements/increments the SP; a return pops it.
- Together they implement **sequential execution, procedure calls, returns, interrupts and context switching**. They are CPU-architecture registers, not OS data structures, which is why this question is out of the OS syllabus.

### A4. P3 Q2(A) — file management vs I/O management (I/O half only)

| File management | I/O management |
|---|---|
| Logical view: files, directories, naming, access rights | Physical view: devices, controllers, drivers, interrupts, DMA |
| Tracks file allocation (contiguous/linked/indexed) and free space | Tracks device queues, buffering, caching, spooling |
| Provides `open`, `read`, `write`, `close`, `seek` | Converts logical requests into device commands and handles completion |
| Concerned with protection and sharing | Concerned with device utilization, throughput and latency |

The **file-management** half (allocation methods, directory structures) is not part of `DJS23IMD201`; only the I/O-management half is examinable in Unit 6.

### A5. P7 Q4(B)(ii) — Next-fit

Next-fit keeps a pointer to the last allocated hole and continues searching **from that point**, wrapping around to the beginning. It does not restart at the first hole like First-fit.

For partitions `300, 600, 350, 200, 750, 125` and processes `115, 500, 358, 200, 375`:

| Process | Next-fit placement | Hole left |
|---|---|---|
| 115 | 300 | 185 |
| 500 | 600 | 100 |
| 358 | 750 | 392 |
| 200 | 350 (wrap/search forward) | 150 |
| 375 | 750 (392 hole) | 17 |

All processes are allocated. Worst-fit gives: 115 → 750 (635), 500 → 635 (135), 358 → 600 (242), 200 → 350 (150), **375 cannot be allocated**. Hence **Next-fit is more efficient for this input** because it leaves no process waiting, although it may fragment memory elsewhere.

### A6. P8 Jan-24 Q1(2) — pile vs sequential file organization

| Pile | Sequential |
|---|---|
| Records are stored in **arrival order**, with no key ordering | Records are stored in **sorted order** of a key field |
| No search structure; search is a linear scan `O(n)` | Binary search on the key is possible (`O(log n)`), and sequential scan is fast |
| Insert is very fast (append at the end) | Insert/delete is expensive: records must be shifted or the file reorganized |
| Deleted records leave gaps unless compacted | Periodic reorganization/overflow areas are needed |
| Good for **bulk storage, archival, variable-length records**, and when all records are processed | Good for **batch processing, range queries, tape storage**, and ordered reports |
| Space can be wasted by deleted/stale records | Better locality for sequential access |

**Advantages of pile:** simplest, fastest insert, no key overhead, handles variable-length records.
**Advantages of sequential:** fast ordered scan, binary search, efficient range/batch processing.
**Disadvantages:** pile has slow search; sequential has expensive insert/delete and needs periodic reorganization.
