# Unit 4 — Memory Management

> Syllabus: fixed and variable partitioning, First Fit/Best Fit/Worst Fit, fragmentation, swapping, virtual memory, paging, segmentation, demand paging, page replacement policies.

---

## 4.1 Memory-management requirements **[5]**

**Asked in:** P2 AIML-F24 Q4(b)(1)(I).

**Question (verbatim):**
> Write short note on:
> I. Memory management requirements.

**Answer**

| Requirement | Meaning | Why it matters |
|---|---|---|
| **Relocation** | A process may be loaded anywhere in memory; addresses must be translated at run time | The compiler cannot know where the process will be placed |
| **Protection** | One process must not read/write another process’s memory | Isolation and security |
| **Sharing** | Processes that need common code/data (libraries, shared memory) must be able to share | Saves memory; enables IPC |
| **Logical organization** | Memory should be viewed as modules (code, data, stack, heap), not one linear array | Matches how programs are written; supports segmentation |
| **Physical organization** | The OS must manage main memory and secondary storage (swap) | Memory is limited; programs may exceed it |

Supporting mechanisms: base/limit registers, page tables, segment tables, TLB, valid/invalid bits, protection bits, demand paging and swapping.

---

## 4.2 Fixed vs variable partitioning; fragmentation **[5–7]**

**Asked in:** P3 IoT-F24 Q1(B)(1); P6 IT-R25 Q4(a)(ii).

**Questions (verbatim):**
> Elaborate fixed memory partitioning with advantages and disadvantages in memory management.

> Compare fixed and variable partitioning in memory management.

**Answer**

### Fixed partitioning

Memory is divided into a fixed number of partitions of fixed size at system-start time. Each partition holds one process.

| Advantages | Disadvantages |
|---|---|
| Simple to implement; low overhead | **Internal fragmentation**: a process smaller than its partition wastes the rest |
| Easy protection (base/limit per partition) | Partition size must be chosen in advance; too large wastes memory, too small limits process size |
| Fast allocation (first free partition) | Limits the **degree of multiprogramming** to the number of partitions |
| Predictable behaviour | A process larger than the largest partition cannot run |

**Internal fragmentation** = wasted space **inside** an allocated partition.

### Variable partitioning

Partitions are created dynamically, exactly the size of the arriving process; the free memory is one large hole that is split on allocation and merged on release.

| Advantages | Disadvantages |
|---|---|
| No internal fragmentation (allocation is exact) | **External fragmentation**: many small free holes, none large enough |
| Degree of multiprogramming is not fixed | Allocation is more complex (first/best/worst fit) |
| Large processes can be accommodated if a large enough hole exists | Needs **compaction** (expensive) or swapping |
| Better memory utilization in general | Holes must be coalesced on release |

**External fragmentation** = free memory is present but scattered in holes too small for a waiting process.

**Compaction:** move all occupied partitions together to create one large free hole; expensive (copying, address relocation) and only possible with relocation support.

**Comparison table**

| Point | Fixed partitioning | Variable partitioning |
|---|---|---|
| Partition size | Fixed at boot | Exactly process size |
| Fragmentation | Internal | External |
| Allocation | Simple/fast | First/Best/Worst Fit |
| Degree of multiprogramming | Fixed | Varies |
| Compaction | Not needed | Often needed |
| Protection | Base/limit per partition | Base/limit per process |

---

## 4.2A Jan-24 — dynamic partitioning + First Fit **[5]**

**Asked in:** P8 Jan-24 Q4(2)(2).

**Question (verbatim):**
> Discuss the advantages and disadvantages of Dynamic partitioning.
> Given memory partitions of 150k,500k,200k,300k,550k (in order) how would each of the first fit algorithm places the processes of 220k,430k,110k,425k (in order).

**Answer**

**Dynamic (variable) partitioning**

| Advantages | Disadvantages |
|---|---|
| Partitions are exactly the process size → **no internal fragmentation** | **External fragmentation**: many small free holes |
| Degree of multiprogramming is not fixed | Allocation is more complex (first/best/worst fit) |
| Large processes can use a large hole | Needs **compaction** or swapping; compaction is expensive |
| Better memory utilization than fixed partitions | Free holes must be coalesced on release |

**First Fit** (scan from the beginning, take the first hole large enough)

| Process | Hole chosen | Leftover |
|---|---|---|
| 220 | 500 | 280 |
| 430 | 550 | 120 |
| 110 | 150 | 40 |
| 425 | **cannot fit** (280, 200, 300, 120, 40 are all < 425) | — |

**Result:** 425k remains unallocated. This is the same input as P1 IT-F24 Q1(a)(ii): Best Fit allocates all four, while First Fit and Worst Fit leave 425k unallocated.

---

## 4.3 Allocation strategies — First Fit, Best Fit, Worst Fit, Next Fit **[10]**

**Applied in:** all memory-allocation numericals below (4.4–4.8).

**Rules**

| Algorithm | Rule | Typical result |
|---|---|---|
| **First Fit** | Scan from the beginning; take the first hole large enough | Fast; may leave small holes at the start |
| **Best Fit** | Take the smallest hole that fits | Leaves the smallest leftover; many tiny holes |
| **Worst Fit** | Take the largest hole | Leaves a large leftover; may waste a large hole |
| **Next Fit** | Continue from the last allocation point, wrap around | Spreads allocations; can be faster than First Fit |

**Fragmentation check:** after all allocations, add the sizes of the holes that are too small for any waiting process. That is the external fragmentation. Total free memory alone is not fragmentation.

---

## 4.4 Memory Set 1 — IT-F24 (Best Fit and Worst Fit) **[10]**

**Asked in:** P1 IT-F24 Q1(a)(ii).

**Question (verbatim):**
> Consider five memory partitions of size 150k,500k,200k,300k,550k. These partitions need to be allocated to four processes of sizes 220k,430k,110k,425k in that order. Perform the allocation of processes using-best fit and Worst Fit Algorithm.

### Best Fit

| Process | Hole chosen | Leftover |
|---|---|---|
| 220 | 300 | 80 |
| 430 | 500 | 70 |
| 110 | 150 | 40 |
| 425 | 550 | 125 |

All four processes are allocated. Final holes: `150→40`, `500→70`, `200` (untouched), `300→80`, `550→125`.

### Worst Fit

| Process | Hole chosen | Leftover |
|---|---|---|
| 220 | 550 | 330 |
| 430 | 500 | 70 |
| 110 | 330 (leftover of 550) | 220 |
| 425 | **cannot fit** (220, 200, 150, 125, 80, 70 are all too small) | — |

**Result:** 425k remains unallocated. Final holes (standard worst-fit): `150` (untouched), `70`, `200` (untouched), `300` (untouched), `220` (from 550). No single hole is ≥ 425. The official key places 110 in the 300k hole instead of the 330k leftover; the final outcome (425 unallocated) is the same.

**Conclusion:** Best Fit succeeds here; Worst Fit fails because it destroys the large 550k hole first.

---

## 4.5 Memory Set 2 — AIML-R24 (First, Best, Worst Fit) **[10]**

**Asked in:** P4 AIML-R24 Q4(b)(1).

**Question (verbatim):**
> Given six memory partitions of 300 KB, 600 KB, 350 KB, 200 KB, 750 KB, and 125 KB (in order), how would the first-fit, best-fit, and worst-fit algorithms place processes of size 115 KB, 500 KB, 358 KB, 200 KB, and 375 KB (in order)? Compare first-fit and worst-fit algorithms.

### First Fit

| Process | Hole | Leftover |
|---|---|---|
| 115 | 300 | 185 |
| 500 | 600 | 100 |
| 358 | 750 | 392 |
| 200 | 350 | 150 |
| 375 | 392 (leftover of 750) | 17 |

All allocated.

### Best Fit

| Process | Hole | Leftover |
|---|---|---|
| 115 | 125 | 10 |
| 500 | 600 | 100 |
| 358 | 750 | 392 |
| 200 | 200 | 0 |
| 375 | 392 | 17 |

All allocated.

### Worst Fit

| Process | Hole | Leftover |
|---|---|---|
| 115 | 750 | 635 |
| 500 | 635 | 135 |
| 358 | 600 | 242 |
| 200 | 350 | 150 |
| 375 | **cannot fit** (300, 242, 200, 150, 135, 125, 100, 17, 10) | — |

375 KB remains unallocated.

**Comparison First vs Worst**

| Point | First Fit | Worst Fit |
|---|---|---|
| Scan | Stops at first suitable hole | Scans all, picks largest |
| Speed | Faster | Slower |
| Large-hole preservation | Better here (750 kept until 358) | Destroys the 750 hole first |
| Result here | All 5 allocated | 375 unallocated |
| Fragmentation | Leaves 185, 100, 150, 17 | Leaves many medium holes, no large one |

---

## 4.6 Memory Set 3 — IT-F25 (First, Best, Worst; least external fragmentation) **[10]**

**Asked in:** P5 IT-F25 Q1(A)(i).

**Question (verbatim):**
> Apply memory allocation strategies First Fit, Best Fit, and Worst Fit to allocate the following process sizes (115 KB, 320 KB, 50 KB, 200 KB) into memory partitions (50 KB, 200 KB, 300 KB, 350 KB, 600 KB). Determine which strategy results in the least external fragmentation.

### First Fit

| Process | Hole | Leftover |
|---|---|---|
| 115 | 200 | 85 |
| 320 | 350 | 30 |
| 50 | 50 | 0 |
| 200 | 300 | 100 |

Final holes: `0, 85, 100, 30, 600`.

### Best Fit

| Process | Hole | Leftover |
|---|---|---|
| 115 | 200 | 85 |
| 320 | 350 | 30 |
| 50 | 50 | 0 |
| 200 | 300 | 100 |

Same placement as First Fit for this input. Final holes: `0, 85, 100, 30, 600`.

### Worst Fit

| Process | Hole | Leftover |
|---|---|---|
| 115 | 600 | 485 |
| 320 | 485 | 165 |
| 50 | 350 | 300 |
| 200 | 300 | 100 |

Final holes: `50, 200, 100, 300, 165`.

**Least external fragmentation?**

All three algorithms allocate **all four processes**, so no process is left waiting. Total free memory is the same: `50 + 200 + 300 + 350 + 600 − (115 + 320 + 50 + 200) = 815 KB` for every strategy. First Fit and Best Fit produce identical placements; Worst Fit distributes the holes differently.

**Answer:** on this input there is **no clear winner** — all allocate every process and leave the same total free memory. If forced to choose, **Best Fit** usually leaves the smallest unusable holes, but here it is identical to First Fit. State the tie and show the numbers; that is what the official key does.

---

## 4.7 Memory Set 4 — IT-R25 (First, Best, Worst Fit) **[10]**

**Asked in:** P6 IT-R25 Q1(a)(ii).

**Question (verbatim):**
> Consider five memory partitions of size 150 KB, 400 KB, 350 KB, 450 KB, 500 KB. These partitions need to be allocated to four processes of 130 KB, 220 KB, 360 KB, 210 KB in that order. Perform the allocation of processes using- First Fit Algorithm. Best Fit Algorithm and Worst Fit Algorithm.

### First Fit

| Process | Hole | Leftover |
|---|---|---|
| 130 | 150 | 20 |
| 220 | 400 | 180 |
| 360 | 450 | 90 |
| 210 | 350 | 140 |

All allocated.

### Best Fit

| Process | Hole | Leftover |
|---|---|---|
| 130 | 150 | 20 |
| 220 | 350 | 130 |
| 360 | 400 | 40 |
| 210 | 450 | 240 |

All allocated.

### Worst Fit

| Process | Hole | Leftover |
|---|---|---|
| 130 | 500 | 370 |
| 220 | 450 | 230 |
| 360 | 400 | 40 |
| 210 | 370 | 160 |

All allocated.

**Conclusion:** all three strategies allocate all four processes. Best Fit leaves the smallest individual holes (20, 130, 40, 240); Worst Fit leaves a large 370→160 hole but also uses the 500 hole first. No process waits in any case.

---

## 4.8 Memory Set 5 — AIML-R25 (Next Fit and Worst Fit) **[10]**

**Asked in:** P7 AIML-R25 Q4(B)(ii). *(Next-fit is beyond the printed syllabus; see the index.)*

**Question (verbatim):**
> Given six memory partitions of 300 KB,600KB,350KB,200KB,750KB, and 125 KB (in order), how would the Next and Worst-fit algorithms place processes of size 115 KB,500KB,358KB,200KB,and 375 KB (in order)? Rank the algorithms in terms of how efficiently they use memory.

### Next Fit

Start at the first hole; after each allocation continue from the next hole, wrapping around.

| Process | Hole | Leftover |
|---|---|---|
| 115 | 300 | 185 |
| 500 | 600 | 100 |
| 358 | 750 | 392 |
| 200 | 350 | 150 |
| 375 | 750 (392 leftover, wrap/search forward) | 17 |

All allocated.

### Worst Fit

| Process | Hole | Leftover |
|---|---|---|
| 115 | 750 | 635 |
| 500 | 635 | 135 |
| 358 | 600 | 242 |
| 200 | 350 | 150 |
| 375 | **cannot fit** | — |

### Ranking

1. **Next Fit — best here:** all five processes are allocated; external fragmentation is 185+100+150+17 (plus untouched 200 and 125), but no process waits.
2. **Worst Fit — worse here:** 375 KB remains unallocated because the largest holes were consumed by smaller processes.

Next Fit is not always better; it can leave holes behind the pointer that are never revisited until wraparound. For this input, however, it uses memory more efficiently because it preserves the 750 KB hole long enough for the 358 KB process and still has 392 KB for 375 KB.

---

## 4.9 Paging, page table and address translation **[5]**

**Asked in:** P7 AIML-R25 Q4(A).

**Question (verbatim):**
> What is paging? Explain the concept of page table with suitable diagram.

**Answer**

**Paging** divides logical memory into fixed-size **pages** and physical memory into the same-size **frames**. Any page can be placed in any free frame, so there is no external fragmentation (only internal fragmentation in the last page).

**Address translation**

```text
Logical address = [ page number p | offset d ]
                       │
                       ▼
                 Page table[p] ──► frame number f
                       │
                       ▼
Physical address = [ frame number f | offset d ]
```

```text
CPU ── logical address ──► [ p | d ]
                              │
                              ▼
                     +-------------------+
                     |   Page table      |
                     |  p  | frame f     |
                     +-------------------+
                              │
                              ▼
                     physical = f × page_size + d
```

- **Page table** has one entry per page; each **PTE** contains the frame number plus valid/invalid, protection and dirty/reference bits.
- **Page size** is a power of 2 (e.g. 4 KB). Offset bits = `log2(page_size)`.
- **Example:** 32-bit logical address, 4 KB pages → 12 offset bits, 20 page-number bits. If page 5 maps to frame 9, logical address `5 × 4096 + 200 = 20680` maps to physical `9 × 4096 + 200 = 37064`.
- **TLB** caches recent page-table entries; a TLB hit avoids a memory access for the page table.
- **Effective access time:** `EAT = (1 − p) × (TLB + mem) + p × (TLB + 2 × mem)` (ignoring TLB search time), where `p` is the TLB miss rate.

**Advantages:** no external fragmentation, simple allocation, easy sharing/protection. **Disadvantages:** internal fragmentation, extra memory access for the page table (mitigated by TLB), page-table size for large address spaces.

---

## 4.10 Segmentation and segmented allocation **[5–10]**

**Asked in:** P1 IT-F24 Q4(a)(ii); P2 AIML-F24 Q4(b)(1)(II); P5 IT-F25 Q4(A)(ii).

**Questions (verbatim):**
> Discuss what is segmentation in memory management, and how does it differ from paging?

> II. Segmented allocation with suitable example.

> Explain the concept of segmentation. Illustrate how segmented address can be translated into a physical address.

**Answer**

**Segmentation** divides a program into variable-size **logical segments** — code, data, stack, heap, library — each with a name/number. Each segment occupies a contiguous region of physical memory; the segment table stores its **base** and **limit**.

**Address translation**

```text
Logical address = [ segment number s | offset d ]
                         │
                         ▼
                Segment table[s] = (base, limit)
                         │
             if d ≥ limit → trap (segmentation fault)
             else physical = base + d
```

**Example**

| Segment | Base | Limit |
|---|---|---|
| 0 (code) | 1000 | 400 |
| 1 (data) | 2000 | 300 |
| 2 (stack) | 5000 | 500 |

- Logical `(1, 150)` → `2000 + 150 = 2150`.
- Logical `(2, 600)` → `600 ≥ 500` → **trap** (out of bounds).
- Logical `(0, 399)` → `1000 + 399 = 1399`.

**Segmentation vs paging**

| Point | Segmentation | Paging |
|---|---|---|
| Unit | Logical, variable-size segments | Fixed-size pages/frames |
| Visible to programmer? | Yes (segments have meaning) | No (transparent) |
| Fragmentation | External | Internal (last page) |
| Address | Segment number + offset | Page number + offset |
| Table | Segment table (base, limit) | Page table (frame number) |
| Sharing/protection | Natural per logical module | Per page |
| Size | Segment can grow/shrink | Page size fixed |

**Segmented allocation:** allocate each segment a contiguous hole (first/best/worst fit). It suffers from external fragmentation; compaction may be needed. Paging + segmentation combines both: segments are divided into pages, giving logical structure without external fragmentation.

---

## 4.11 Virtual memory and demand paging **[10]**

**Asked in:** P2 AIML-F24 Q4(b)(2) (with FIFO/Optimal numerical in Page Set 2); P8 Jan-24 Q1(4)(2).

**Questions (verbatim):**
> Discuss virtual memory management with paging. Demonstrate FIFO and Optimal Page replacement policy. Page references: 6, 0, 1, 2, 0, 3, 6, 0, 3, 5, 6, 2, 4, 0 page frames : 3

> Explain the concept of demand paging with neat and labelled diagram.

**Answer**

**Virtual memory** lets a process execute with only part of its address space in RAM. The rest stays on disk (swap/backing store). The OS maps virtual pages to physical frames on demand, so a program can be larger than physical memory and more processes can be resident.

**Demand paging**

1. The CPU references a page.
2. If the PTE is **valid**, translate and access.
3. If **invalid**, a **page fault** traps to the OS.
4. The OS finds a free frame (or evicts a victim).
5. If the victim is dirty, write it back; load the requested page from disk.
6. Update the page table and TLB; restart the faulting instruction.

**Support needed:** valid/invalid bit, dirty bit, reference bit, swap space, page-fault handler, frame allocator, replacement policy.

**Page-replacement policies**

| Policy | Rule | Notes |
|---|---|---|
| **FIFO** | Replace the page loaded earliest | Simple; suffers **Belady’s anomaly** (more frames can cause more faults) |
| **LRU** | Replace the least recently used page | Good approximation of future; stack algorithm, no Belady; needs hardware reference bits or stack |
| **Optimal (OPT/MIN)** | Replace the page not used for the longest future time | Lowest possible faults; theoretical benchmark only |
| **Clock / second chance** | FIFO + reference bit; give a second chance | Practical LRU approximation |
| **LFU/MFU** | Count references | Can retain stale pages |

**Performance:** page-fault rate `p`; effective access time `EAT = (1−p) × mem_time + p × fault_time`. If `p` is too high, **thrashing** occurs (the system spends more time paging than executing). Fixes: working-set model, page-fault frequency control, more memory, better locality.

The FIFO/Optimal numerical for this question is solved in **4.14 (Page Set 2)**: FIFO = 11 faults, Optimal = 8 faults with 3 frames.

---

## 4.12 Page replacement — FIFO, LRU, Optimal comparison **[5]**

**Question type:** “Differentiate FIFO, LRU and Optimal page replacement.”

| Point | FIFO | LRU | Optimal |
|---|---|---|---|
| Victim | Earliest loaded | Least recently referenced | Used farthest in future |
| Implementation | Queue | Stack/counter/reference bits | Requires future string |
| Belady’s anomaly | Yes | No (stack algorithm) | No |
| Faults | Usually higher | Close to optimal | Minimum |
| Practical? | Yes | Yes (approx.) | No (benchmark) |
| Strength | Simple | Good locality | Best possible |
| Weakness | Ignores usage; anomaly | Hardware/overhead | Impossible online |

**Belady’s anomaly:** for some reference strings, increasing the number of frames increases FIFO faults (classic string `1,2,3,4,1,2,5,1,2,3,4,5`). LRU and Optimal do not exhibit it because they are stack algorithms.

---

## 4.13 Page Set 1 — IT-F24 (FIFO, LRU, Optimal) **[10]**

**Asked in:** P1 IT-F24 Q1(a)(i).

**Question (verbatim):**
> Consider the following reference string: 5, 0, 1, 0, 2, 3, 0, 2, 4, 3, 3, 2, 0, 2, 1, 2, 7, 0, 1, 1, 0. Calculate the number of Hits and Faults for the following page replacement algorithm: FIFO, LRU and Optimal strategy. Frame size=3.

**Trace** (frames after each reference; `F` = fault, `H` = hit)

| Ref | FIFO | LRU | OPT |
|---|---|---|---|
| 5 | F [5] | F [5] | F [5] |
| 0 | F [5, 0] | F [5, 0] | F [5, 0] |
| 1 | F [5, 0, 1] | F [5, 0, 1] | F [5, 0, 1] |
| 0 | H [5, 0, 1] | H [5, 0, 1] | H [5, 0, 1] |
| 2 | F [0, 1, 2] | F [0, 1, 2] | F [0, 1, 2] |
| 3 | F [1, 2, 3] | F [0, 2, 3] | F [0, 2, 3] |
| 0 | F [2, 3, 0] | H [0, 2, 3] | H [0, 2, 3] |
| 2 | H [2, 3, 0] | H [0, 2, 3] | H [0, 2, 3] |
| 4 | F [3, 0, 4] | F [0, 2, 4] | F [2, 3, 4] |
| 3 | H [3, 0, 4] | F [2, 4, 3] | H [2, 3, 4] |
| 3 | H [3, 0, 4] | H [2, 4, 3] | H [2, 3, 4] |
| 2 | F [0, 4, 2] | H [2, 4, 3] | H [2, 3, 4] |
| 0 | H [0, 4, 2] | F [2, 3, 0] | F [2, 4, 0] |
| 2 | H [0, 4, 2] | H [2, 3, 0] | H [2, 4, 0] |
| 1 | F [4, 2, 1] | F [2, 0, 1] | F [2, 0, 1] |
| 2 | H [4, 2, 1] | H [2, 0, 1] | H [2, 0, 1] |
| 7 | F [2, 1, 7] | F [2, 1, 7] | F [0, 1, 7] |
| 0 | F [1, 7, 0] | F [2, 7, 0] | H [0, 1, 7] |
| 1 | H [1, 7, 0] | F [7, 0, 1] | H [0, 1, 7] |
| 1 | H [1, 7, 0] | H [7, 0, 1] | H [0, 1, 7] |
| 0 | H [1, 7, 0] | H [7, 0, 1] | H [0, 1, 7] |

| Algorithm | Faults | Hits |
|---|---:|---:|
| **FIFO** | **11** | **10** |
| **LRU** | **12** | **9** |
| **Optimal** | **9** | **12** |

**Conclusion:** Optimal is best (9), FIFO next (11), LRU worst here (12). LRU is not guaranteed to beat FIFO on every string; its advantage is that it never suffers Belady’s anomaly.

---

## 4.14 Page Set 2 — AIML-F24 (FIFO and Optimal; virtual memory) **[10]**

**Asked in:** P2 AIML-F24 Q4(b)(2).

**Question (verbatim):**
> Discuss virtual memory management with paging. Demonstrate FIFO and Optimal Page replacement policy. Page references: 6, 0, 1, 2, 0, 3, 6, 0, 3, 5, 6, 2, 4, 0 page frames : 3

**Virtual memory in one line:** only the pages currently needed are kept in frames; the rest are on disk, and a page fault loads a page on demand (full mechanism in 4.11).

**Trace** (3 frames)

| Ref | FIFO | OPT |
|---|---|---|
| 6 | F [6] | F [6] |
| 0 | F [6, 0] | F [6, 0] |
| 1 | F [6, 0, 1] | F [6, 0, 1] |
| 2 | F [0, 1, 2] | F [6, 0, 2] |
| 0 | H [0, 1, 2] | H [6, 0, 2] |
| 3 | F [1, 2, 3] | F [6, 0, 3] |
| 6 | F [2, 3, 6] | H [6, 0, 3] |
| 0 | F [3, 6, 0] | H [6, 0, 3] |
| 3 | H [3, 6, 0] | H [6, 0, 3] |
| 5 | F [6, 0, 5] | F [6, 0, 5] |
| 6 | H [6, 0, 5] | H [6, 0, 5] |
| 2 | F [0, 5, 2] | F [0, 5, 2] |
| 4 | F [5, 2, 4] | F [0, 2, 4] |
| 0 | F [2, 4, 0] | H [0, 2, 4] |

| Algorithm | Faults | Hits |
|---|---:|---:|
| **FIFO** | **11** | **3** |
| **Optimal** | **8** | **6** |

**Virtual-memory discussion:** see 4.11. The Optimal policy is a benchmark; FIFO is simple but can evict a page that is still hot. Here Optimal saves 3 faults.

---

## 4.15 Page Set 3 — IoT-F24 (FIFO, LRU, Optimal; policy not named) **[7]**

**Asked in:** P3 IoT-F24 Q1(B)(2).

**Question (verbatim):**
> Consider page reference string Reference String: 7, 0, 1, 2, 0, 3, 0, 4, 2, 3, 0, 3, 2
> Number of Frames: 3. Find the number of page faults by Using Page replacement policies.

> **Note:** the paper does not name the policies. The answer solves **FIFO, LRU and Optimal**; write the algorithm name above each table in the exam.

**Trace** (13 references, 3 frames)

| Ref | FIFO | LRU | OPT |
|---|---|---|---|
| 7 | F [7] | F [7] | F [7] |
| 0 | F [7, 0] | F [7, 0] | F [7, 0] |
| 1 | F [7, 0, 1] | F [7, 0, 1] | F [7, 0, 1] |
| 2 | F [0, 1, 2] | F [0, 1, 2] | F [0, 1, 2] |
| 0 | H [0, 1, 2] | H [0, 1, 2] | H [0, 1, 2] |
| 3 | F [1, 2, 3] | F [0, 2, 3] | F [0, 2, 3] |
| 0 | F [2, 3, 0] | H [0, 2, 3] | H [0, 2, 3] |
| 4 | F [3, 0, 4] | F [0, 3, 4] | F [2, 3, 4] |
| 2 | F [0, 4, 2] | F [0, 4, 2] | H [2, 3, 4] |
| 3 | F [4, 2, 3] | F [4, 2, 3] | H [2, 3, 4] |
| 0 | F [2, 3, 0] | F [2, 3, 0] | F [2, 3, 0] |
| 3 | H [2, 3, 0] | H [2, 3, 0] | H [2, 3, 0] |
| 2 | H [2, 3, 0] | H [2, 3, 0] | H [2, 3, 0] |

| Algorithm | Faults | Hits |
|---|---:|---:|
| **FIFO** | **10** | **3** |
| **LRU** | **9** | **4** |
| **Optimal** | **7** | **6** |

---

## 4.16 Page Set 4 — AIML-R24 (FIFO and LRU) **[10]**

**Asked in:** P4 AIML-R24 Q4(b)(2).

**Question (verbatim):**
> Consider a main memory with three page frames and the following sequence of page references:
> 5, 0, 1, 0, 2, 3, 0, 2, 4, 3, 3, 2, 0, 2, 1, 2, 7, 0, 1, 1, 0
> Apply FIFO and Least Recently Used (LRU) page replacement policy and calculate the total number of faults, hits, miss ratio and hit ratio.

**Answer**

This is the **same reference string as Page Set 1**. The full FIFO/LRU trace is in 4.13.

| Algorithm | Faults | Hits | Miss ratio | Hit ratio |
|---|---:|---:|---:|---:|
| **FIFO** | **11** | **10** | **11/21** | **10/21** |
| **LRU** | **12** | **9** | **12/21** | **9/21** |

These match the official key exactly (FIFO 11, LRU 12). In exam, write “same trace as IT-F24 Q1(a)(i)” only if you have already written it; otherwise reproduce the table.

---

## 4.17 Page Set 5 — IT-F25 (FIFO, LRU, Optimal) **[10]**

**Asked in:** P5 IT-F25 Q1(A)(ii).

**Question (verbatim):**
> A system uses three memory frames and processes the following reference string: 4, 1, 2, 4, 5, 1, 2, 4, 6, 1, 2, 1, 4, 5, 3, 6. Apply the FIFO (First-In-First-Out), LRU (Least Recently Used), and Optimal page replacement algorithms to this reference string. Calculate the number of page faults for each algorithm. Determine which algorithm performs best for this reference pattern.

**Trace** (3 frames)

| Ref | FIFO | LRU | OPT |
|---|---|---|---|
| 4 | F [4] | F [4] | F [4] |
| 1 | F [4, 1] | F [4, 1] | F [4, 1] |
| 2 | F [4, 1, 2] | F [4, 1, 2] | F [4, 1, 2] |
| 4 | H [4, 1, 2] | H [4, 1, 2] | H [4, 1, 2] |
| 5 | F [1, 2, 5] | F [4, 2, 5] | F [1, 2, 5] |
| 1 | H [1, 2, 5] | F [4, 5, 1] | H [1, 2, 5] |
| 2 | H [1, 2, 5] | F [5, 1, 2] | H [1, 2, 5] |
| 4 | F [2, 5, 4] | F [1, 2, 4] | F [1, 2, 4] |
| 6 | F [5, 4, 6] | F [2, 4, 6] | F [1, 2, 6] |
| 1 | F [4, 6, 1] | F [4, 6, 1] | H [1, 2, 6] |
| 2 | F [6, 1, 2] | F [6, 1, 2] | H [1, 2, 6] |
| 1 | H [6, 1, 2] | H [6, 1, 2] | H [1, 2, 6] |
| 4 | F [1, 2, 4] | F [1, 2, 4] | F [2, 6, 4] |
| 5 | F [2, 4, 5] | F [1, 4, 5] | F [6, 4, 5] |
| 3 | F [4, 5, 3] | F [4, 5, 3] | F [6, 5, 3] |
| 6 | F [5, 3, 6] | F [5, 3, 6] | H [6, 5, 3] |

| Algorithm | Faults | Hits |
|---|---:|---:|
| **FIFO** | **12** | **4** |
| **LRU** | **14** | **2** |
| **Optimal** | **9** | **7** |

**Best policy:** **Optimal (9 faults)**. FIFO is second (12); LRU is worst on this particular string (14).

> **Official-key discrepancy:** the key gives LRU = **10** faults. A step-by-step LRU trace gives **14**. FIFO (12) and Optimal (9) match the key. Write the trace; if your teacher follows the key, show both and explain that LRU’s victim choice depends on the recency order. See `00_Index.md` §5.1.

---

## 4.18 Page Set 6 — IT-R25 (FIFO, LRU, Optimal) **[10]**

**Asked in:** P6 IT-R25 Q1(a)(i).

**Question (verbatim):**
> Calculate number of page faults for the page replacement policies FIFO, LRU and Optimal page replacement algorithm for given reference string 6, 0, 5, 2, 0, 3, 0, 4, 2, 3, 0, 3, 2, 5, 2, 0, 5, 6, 0, 5 (assuming three frame size).

**Trace** (3 frames)

| Ref | FIFO | LRU | OPT |
|---|---|---|---|
| 6 | F [6] | F [6] | F [6] |
| 0 | F [6, 0] | F [6, 0] | F [6, 0] |
| 5 | F [6, 0, 5] | F [6, 0, 5] | F [6, 0, 5] |
| 2 | F [0, 5, 2] | F [0, 5, 2] | F [0, 5, 2] |
| 0 | H [0, 5, 2] | H [0, 5, 2] | H [0, 5, 2] |
| 3 | F [5, 2, 3] | F [0, 2, 3] | F [0, 2, 3] |
| 0 | F [2, 3, 0] | H [0, 2, 3] | H [0, 2, 3] |
| 4 | F [3, 0, 4] | F [0, 3, 4] | F [2, 3, 4] |
| 2 | F [0, 4, 2] | F [0, 4, 2] | H [2, 3, 4] |
| 3 | F [4, 2, 3] | F [4, 2, 3] | H [2, 3, 4] |
| 0 | F [2, 3, 0] | F [2, 3, 0] | F [2, 3, 0] |
| 3 | H [2, 3, 0] | H [2, 3, 0] | H [2, 3, 0] |
| 2 | H [2, 3, 0] | H [2, 3, 0] | H [2, 3, 0] |
| 5 | F [3, 0, 5] | F [2, 3, 5] | F [2, 0, 5] |
| 2 | F [0, 5, 2] | H [2, 3, 5] | H [2, 0, 5] |
| 0 | H [0, 5, 2] | F [2, 5, 0] | H [2, 0, 5] |
| 5 | H [0, 5, 2] | H [2, 5, 0] | H [2, 0, 5] |
| 6 | F [5, 2, 6] | F [5, 0, 6] | F [0, 5, 6] |
| 0 | F [2, 6, 0] | H [5, 0, 6] | H [0, 5, 6] |
| 5 | F [6, 0, 5] | H [5, 0, 6] | H [0, 5, 6] |

| Algorithm | Faults | Hits |
|---|---:|---:|
| **FIFO** | **15** | **5** |
| **LRU** | **12** | **8** |
| **Optimal** | **9** | **11** |

---

## 4.19 Page Set 7 — AIML-R25 (FIFO and Optimal, 4 frames) **[10]**

**Asked in:** P7 AIML-R25 Q4(B)(i).

**Question (verbatim):**
> Consider the following data points. Page frame size is 4. Apply the FIFO and optimal page replacement algorithms. Compute page fault and page hit for the algorithm and find which pages resides in memory.
> 1,2,3,4,5,3,4,1,6,7,8,7,8,9,7,8,9,5,4,5,4,2.

**Trace** (4 frames)

| Ref | FIFO | OPT |
|---|---|---|
| 1 | F [1] | F [1] |
| 2 | F [1, 2] | F [1, 2] |
| 3 | F [1, 2, 3] | F [1, 2, 3] |
| 4 | F [1, 2, 3, 4] | F [1, 2, 3, 4] |
| 5 | F [2, 3, 4, 5] | F [1, 3, 4, 5] |
| 3 | H [2, 3, 4, 5] | H [1, 3, 4, 5] |
| 4 | H [2, 3, 4, 5] | H [1, 3, 4, 5] |
| 1 | F [3, 4, 5, 1] | H [1, 3, 4, 5] |
| 6 | F [4, 5, 1, 6] | F [3, 4, 5, 6] |
| 7 | F [5, 1, 6, 7] | F [4, 5, 6, 7] |
| 8 | F [1, 6, 7, 8] | F [4, 5, 7, 8] |
| 7 | H [1, 6, 7, 8] | H [4, 5, 7, 8] |
| 8 | H [1, 6, 7, 8] | H [4, 5, 7, 8] |
| 9 | F [6, 7, 8, 9] | F [5, 7, 8, 9] |
| 7 | H [6, 7, 8, 9] | H [5, 7, 8, 9] |
| 8 | H [6, 7, 8, 9] | H [5, 7, 8, 9] |
| 9 | H [6, 7, 8, 9] | H [5, 7, 8, 9] |
| 5 | F [7, 8, 9, 5] | H [5, 7, 8, 9] |
| 4 | F [8, 9, 5, 4] | F [5, 8, 9, 4] |
| 5 | H [8, 9, 5, 4] | H [5, 8, 9, 4] |
| 4 | H [8, 9, 5, 4] | H [5, 8, 9, 4] |
| 2 | F [9, 5, 4, 2] | F [8, 9, 4, 2] |

| Algorithm | Faults | Hits |
|---|---:|---:|
| **FIFO** | **13** | **9** |
| **Optimal** | **11** | **11** |

**Pages resident at the end (last state):**

- FIFO: `9, 5, 4, 2`.
- Optimal: `8, 9, 4, 2`.

---

## 4.19A Page Set 8 — Jan-24 (FIFO and LRU) **[5]**

**Asked in:** P8 Jan-24 Q4(2)(1).

**Question (verbatim):**
> Apply FIFO and LRU Page replacement policies for the following page sequence (2,3,5,4,2,5,7,3,8,7). Assume page frame size is 3. Calculate number of hits and page faults.

**Trace** (3 frames; `F` = fault, `H` = hit)

| Ref | FIFO | LRU |
|---|---|---|
| 2 | F [2] | F [2] |
| 3 | F [2, 3] | F [2, 3] |
| 5 | F [2, 3, 5] | F [2, 3, 5] |
| 4 | F [3, 5, 4] | F [3, 5, 4] |
| 2 | F [5, 4, 2] | F [5, 4, 2] |
| 5 | H [5, 4, 2] | H [5, 4, 2] |
| 7 | F [4, 2, 7] | F [5, 2, 7] |
| 3 | F [2, 7, 3] | F [5, 7, 3] |
| 8 | F [7, 3, 8] | F [7, 3, 8] |
| 7 | H [7, 3, 8] | H [7, 3, 8] |

| Algorithm | Faults | Hits |
|---|---:|---:|
| **FIFO** | **8** | **2** |
| **LRU** | **8** | **2** |

Both policies produce the same counts on this short string, although the resident sets differ at references 7 and 3.

---

## 4.20 Swapping (syllabus topic) **[5]**

**Question type:** “What is swapping? What are its advantages and disadvantages?”

**Answer:** **Swapping** moves an entire process between main memory and a **backing store** (disk) so that more processes can be multiplexed than fit in memory. The OS swaps out a blocked/low-priority process and swaps in a ready one. It is usually combined with virtual memory (page-level swapping rather than whole-process swapping).

| Advantage | Disadvantage |
|---|---|
| Frees memory for other processes | Disk I/O is very slow compared with RAM |
| Allows more multiprogramming | Context-switch/swap time is high |
| Helps a process grow beyond memory | Needs a large backing store |
| Simple concept | Can cause thrashing if overused |

Modern systems swap **pages** (paging) rather than whole processes; whole-process swapping is rare but still used for long-idle processes.

---

## Unit 4 — quick revision checklist

- [ ] 5 memory-management requirements.
- [ ] Fixed vs variable partitioning: 6 differences; internal vs external fragmentation.
- [ ] First/Best/Worst/Next Fit rules + one solved layout.
- [ ] Paging address translation formula; page table + TLB; EAT.
- [ ] Segmentation address translation; segment table `(base, limit)`; trap if offset ≥ limit.
- [ ] Segmentation vs paging: 7 differences.
- [ ] Demand paging: 6-step page-fault handling.
- [ ] FIFO/LRU/Optimal table; Belady’s anomaly; stack algorithm.
- [ ] Page-fault/hit/miss ratio formulas.
- [ ] Virtual memory benefits and thrashing.
- [ ] Swapping: definition + 2 advantages + 2 disadvantages.
- [ ] Jan-24: first fit on 150/500/200/300/550 with 220/430/110/425 → 425 unallocated.
- [ ] Jan-24: FIFO and LRU on 2,3,5,4,2,5,7,3,8,7 (3 frames) → both 8 faults / 2 hits.
- [ ] Be able to reproduce any of the 8 page traces and 6 allocation layouts.
