# Unit 6 — I/O Management and Disk Scheduling

> Syllabus: I/O devices, I/O buffering, disk scheduling: FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK, RAID.

---

## 6.1 Role of I/O management in an operating system **[5]**

**Asked in:** P6 IT-R25 Q4(a)(i).

**Question (verbatim):**
> Summarize the role of I/O management in an operating system?

**Answer**

I/O management hides the diversity of devices behind a uniform interface and uses the hardware efficiently.

| Role | What it does |
|---|---|
| **Device independence** | Programs use logical names/handles, not port numbers or controller registers |
| **Uniform interface** | `open`, `read`, `write`, `close`, `ioctl` work across different devices |
| **Device drivers** | Device-specific code translates generic requests into controller commands |
| **Buffering** | Temporary storage smooths speed mismatch between CPU and device |
| **Caching** | Keep recently used blocks in memory to avoid repeated device access |
| **Spooling** | Queue jobs for a dedicated device (printer) so processes do not wait for it |
| **Interrupt handling** | ISR acknowledges completion/errors and wakes waiting processes |
| **DMA setup** | Program the DMA controller for block transfers |
| **Scheduling** | Order disk requests to minimize seek time (FCFS/SSTF/SCAN/…) |
| **Error handling** | Retry, log, mark bad blocks, report to the caller |
| **Protection/security** | Check permissions before allowing device access |
| **Power management** | Suspend/idle devices, runtime PM, wake-on-event |

**Layers**

```text
User process → system-call interface → I/O subsystem
            → device driver → device controller → physical device
```

**I/O hardware**

- A **device controller** is the electronics that operates a device; it has registers for data, status/control, and often a local buffer.
- The CPU talks to the controller through **I/O ports** (port-mapped I/O) or **memory-mapped I/O** (controller registers appear at memory addresses).
- A **device driver** is the kernel module that knows the controller’s register layout and command protocol.
- The controller signals completion by **interrupt** or by a status bit that the CPU **polls**; bulk transfers use **DMA**.
- **Block devices** (disks, SSDs) transfer fixed-size blocks and are usually cached; **character devices** (keyboard, serial, mouse) transfer byte streams and are usually unbuffered.

The I/O subsystem also manages **block devices** (disks, random access, cached) and **character devices** (keyboards, serial, byte streams, usually unbuffered).

---

## 6.2 I/O buffering — single, double, circular **[8]**

**Asked in:** P3 IoT-F24 Q3(A)(1).

**Question (verbatim):**
> What is I/O Buffering ? Elaborate in detail single buffering, double buffering, and circular buffering types.

**Answer**

**I/O buffering** uses a memory area between the producer (device or process) and consumer (process or device) to hold data temporarily. It compensates for:

- speed mismatch (fast CPU vs slow device),
- different transfer sizes (byte stream vs block),
- arrival jitter/bursts,
- the need to copy data before the device writes it (copy semantics).

### (a) Single buffering

```text
Device ──► [ buffer ] ──► Process
```

- The OS fills one buffer; the process consumes it.
- While the process is consuming, the device may fill the same buffer only after it is free; otherwise the device waits.
- Overlap is limited: fill and empty cannot fully overlap.
- Simple; one buffer per device/stream.

### (b) Double buffering

```text
Device ──► [ buffer A ] ──► Process
           [ buffer B ]
```

- Two buffers: while the device fills A, the process empties B; then roles swap.
- Better overlap and throughput; reduces producer/consumer waiting.
- Cost: twice the memory; still a fixed capacity.

### (c) Circular (ring) buffering

```text
        ┌──────────────────────────────┐
        │  b0  │  b1  │  b2  │ ... │ bn │
        └──────────────────────────────┘
          ▲ head (consumer)   ▲ tail (producer)
```

- An array of `n` buffers treated as a ring; `head` is the next to consume, `tail` is the next to fill.
- Producer advances `tail`; consumer advances `head`; both wrap modulo `n`.
- If `head == tail` → empty; if `(tail+1) % n == head` → full (or use a counter).
- Handles bursts and continuous streams (audio/video, network, keyboard).
- More complex pointer management; needs synchronization (semaphores/mutex) in a concurrent system.

**Comparison**

| Type | Buffers | Overlap | Memory | Use |
|---|---|---|---|---|
| Single | 1 | Limited | Lowest | Simple device, low rate |
| Double | 2 | Good | Medium | Printer/disk streams, graphics |
| Circular | n | Best for bursts | Highest | Multimedia, network, keyboard |

**Benefits:** fewer waits, better CPU/device utilization, smoother response, support for copy semantics and spooling. **Cost:** memory, copying overhead, and complexity.

---

## 6.3 Disk structure and performance parameters **[5]**

**Question type:** “Define seek time, rotational delay, transfer time; compute total access time.”

```text
Disk = platters + surfaces + tracks + sectors
Access time = seek time + rotational latency + transfer time
Seek time          = time to move the head to the target track
Rotational latency = time for the desired sector to rotate under the head
                   = 1/2 × (60 / RPM) seconds on average
Transfer time      = bytes / transfer rate
```

**Example:** 7200 RPM, average seek 8 ms, transfer rate 100 MB/s, 4 KB block.

- Rotation time = `60/7200 = 8.33 ms`; average rotational latency = `4.17 ms`.
- Transfer = `4096 / (100 × 10^6) ≈ 0.041 ms`.
- Total ≈ `8 + 4.17 + 0.041 ≈ 12.2 ms`.

**Disk scheduling** reorders the request queue to reduce **seek time**; rotational latency and transfer time are usually fixed for a given request.

---

## 6.4 Disk Set 1 — IT-F24 (SSTF and SCAN) **[10]**

**Asked in:** P1 IT-F24 Q3(a)(ii).

**Question (verbatim):**
> Suppose the order of disk request sequences are 176, 79, 34, 60, 92, 11, 41, 114 and the initial position of the Read-Write head is 50. Apply SSTF and SCAN to find the total seek time. (Assume that head moves in left direction)(0-199 tracks)

### SSTF

At each step, serve the request closest to the current head.

| Request | Movement |
|---|---|
| 41 | 9 |
| 34 | 7 |
| 11 | 23 |
| 60 | 49 |
| 79 | 19 |
| 92 | 13 |
| 114 | 22 |
| 176 | 62 |

**Sequence:** `50 → 41 → 34 → 11 → 60 → 79 → 92 → 114 → 176`
**Total seek time = 9 + 7 + 23 + 49 + 19 + 13 + 22 + 62 = 204 tracks.**

### SCAN (moving left)

Go left to track 0, servicing requests on the way; then reverse and go right.

| Request | Movement |
|---|---|
| 41 | 9 |
| 34 | 7 |
| 11 | 23 |
| 0 | 11 |
| 60 | 60 |
| 79 | 19 |
| 92 | 13 |
| 114 | 22 |
| 176 | 62 |

**Sequence:** `50 → 41 → 34 → 11 → 0 → 60 → 79 → 92 → 114 → 176`
**Total seek time = 9 + 7 + 23 + 11 + 60 + 19 + 13 + 22 + 62 = 226 tracks.**

> If your class uses **LOOK** (stop at the lowest request, 11, instead of going to 0), the total is `50→41→34→11→60→79→92→114→176 = 204`. The question says SCAN and 0–199, so the answer is **226**.

**Comparison:** SSTF is shorter here (204 vs 226) because SCAN makes the extra 0-track sweep, but SCAN gives bounded waiting and avoids starving far requests.

---

## 6.5 Disk Set 2 — IT-F25 (FCFS and LOOK) **[10]**

**Asked in:** P5 IT-F25 Q3(A)(ii).

**Question (verbatim):**
> Consider a disk queue with I/O requests on the following cylinders in their arriving order: 6, 10, 12, 54, 97, 73, 128, 15, 44, 110, 34, 45. The disk head is assumed to be at Cylinder 23 and moving in the direction of decreasing number of cylinders. The disk consists of total 150 cylinders. Calculate and show with diagram the disk head movement using FCFS and LOOK scheduling algorithms.

### FCFS

| Request | Movement |
|---|---|
| 6 | 17 |
| 10 | 4 |
| 12 | 2 |
| 54 | 42 |
| 97 | 43 |
| 73 | 24 |
| 128 | 55 |
| 15 | 113 |
| 44 | 29 |
| 110 | 66 |
| 34 | 76 |
| 45 | 11 |

**Sequence:** `23 → 6 → 10 → 12 → 54 → 97 → 73 → 128 → 15 → 44 → 110 → 34 → 45`
**Total = 482 tracks.**

### LOOK (moving down)

Service requests in decreasing order until the lowest request (6), then reverse and serve the rest in increasing order.

| Request | Movement |
|---|---|
| 15 | 8 |
| 12 | 3 |
| 10 | 2 |
| 6 | 4 |
| 34 | 28 |
| 44 | 10 |
| 45 | 1 |
| 54 | 9 |
| 73 | 19 |
| 97 | 24 |
| 110 | 13 |
| 128 | 18 |

**Sequence:** `23 → 15 → 12 → 10 → 6 → 34 → 44 → 45 → 54 → 73 → 97 → 110 → 128`
**Total = 8 + 3 + 2 + 4 + 28 + 10 + 1 + 9 + 19 + 24 + 13 + 18 = 139 tracks.**

These match the official key (FCFS 482, LOOK 139). LOOK is much better because it avoids the long jump to 0 and the wild FCFS swings.

---

## 6.6 Disk Set 3 — IT-R25 (SCAN and C-SCAN) **[10]**

**Asked in:** P6 IT-R25 Q3(a)(ii).

**Question (verbatim):**
> Suppose the order of disk request sequence are 70, 140, 50, 125, 30, 25, 160 in a disk with 170 tracks and the initial position of the Read-Write head is 60. And it is given that the disk arm should move towards the larger value. Apply SCAN and C-SCAN disk scheduling algorithm to calculate the total seek time.

**Assumption:** 170 tracks = tracks `0–169`.

### SCAN (moving up)

Go up to 169, then reverse and go down.

| Request | Movement |
|---|---|
| 70 | 10 |
| 125 | 55 |
| 140 | 15 |
| 160 | 20 |
| 169 | 9 |
| 50 | 119 |
| 30 | 20 |
| 25 | 5 |

**Sequence:** `60 → 70 → 125 → 140 → 160 → 169 → 50 → 30 → 25`
**Total = 10 + 55 + 15 + 20 + 9 + 119 + 20 + 5 = 253 tracks.**

### C-SCAN (moving up)

Service upward to 169, then jump to 0 **without service**, and service upward again.

| Request | Movement |
|---|---|
| 70 | 10 |
| 125 | 55 |
| 140 | 15 |
| 160 | 20 |
| 169 | 9 |
| 0 (jump) | 169 |
| 25 | 25 |
| 30 | 5 |
| 50 | 20 |

**Sequence:** `60 → 70 → 125 → 140 → 160 → 169 → 0 → 25 → 30 → 50`
**Total = 10 + 55 + 15 + 20 + 9 + 169 + 25 + 5 + 20 = 328 tracks.**

> If your class treats “170 tracks” as 0–170, add 1 to the upward travel and 1 to the jump: SCAN = 255, C-SCAN = 330. State the convention.

**Comparison:** SCAN 253 is shorter here because it services the low requests on the return pass; C-SCAN 328 has a longer jump but gives more uniform wait times.

---

## 6.7 Disk Set 4 — IoT-F24 (SCAN with clustered requests) **[8]**

**Asked in:** P3 IoT-F24 Q3(A)(2).

**Question (verbatim):**
> Consider Performance analysis for clustered requests: Request queue: [10, 12, 15, 17, 20, 190, 195] Disk head starts at 50. Disk tracks: 0 to 200. Head moves initially towards the lower end. Compute and comment on the seek time efficiency of SCAN in this scenario.

**SCAN (moving down to 0, then up)**

| Request | Movement |
|---|---|
| 20 | 30 |
| 17 | 3 |
| 15 | 2 |
| 12 | 3 |
| 10 | 2 |
| 0 | 10 |
| 190 | 190 |
| 195 | 5 |

**Sequence:** `50 → 20 → 17 → 15 → 12 → 10 → 0 → 190 → 195`
**Total = 30 + 3 + 2 + 3 + 2 + 10 + 190 + 5 = 245 tracks.**

**Comment on efficiency**

- The requests are **clustered at both ends**: 10–20 and 190–195, with nothing in the middle.
- SCAN still travels all the way to **0** even though the lowest request is **10**, then crosses the entire disk to 190. The 10-track trip from 10 to 0 and the 190-track trip from 0 to 190 dominate the cost.
- **LOOK** would stop at 10 and reverse: `50→20→17→15→12→10→190→195` = `40 + 185 = 225` tracks, saving **20 tracks (about 8%)**.
- So SCAN is **not efficient** for this clustered pattern; LOOK is better. SCAN’s advantage is bounded worst-case wait and no starvation, not minimum seek time for every queue.
- **C-SCAN** would be even worse for these clusters (it would jump from 200 back to 0 and service 10–20 last).

---

## 6.7A Disk Set 5 — Jan-24 (FCFS, SSTF, C-SCAN) **[10]**

**Asked in:** P8 Jan-24 Q3(1)(2).

**Question (verbatim):**
> Suppose that a disk drive has 5,000 cylinders, numbered 0 to 4,999. The drive is currently serving a request at cylinder 2,150, and the previous request was at cylinder 1,805. The queue of pending requests, in FIFO order, is: 2,069, 1,212, 2,296, 2,800, 544, 1,618, 356, 1,523, 4,965, 3681. Starting from the current head position, what is the total distance (in cylinders) that the disk arm moves to satisfy all the pending requests for each of the following disk-scheduling algorithms?
> FCFS, SSTF and C-SCAN.

**Direction:** previous request 1805 < current 2150 → head is moving **upward**. C-SCAN therefore services upward first, jumps to 0, then services the low requests.

### FCFS

| Request | Movement |
|---|---|
| 2,069 | 81 |
| 1,212 | 857 |
| 2,296 | 1,084 |
| 2,800 | 504 |
| 544 | 2,256 |
| 1,618 | 1,074 |
| 356 | 1,262 |
| 1,523 | 1,167 |
| 4,965 | 3,442 |
| 3,681 | 1,284 |
| **Total** | **13,011** |

### SSTF

| Request | Movement |
|---|---|
| 2,069 | 81 |
| 2,296 | 227 |
| 2,800 | 504 |
| 3,681 | 881 |
| 4,965 | 1,284 |
| 1,618 | 3,347 |
| 1,523 | 95 |
| 1,212 | 311 |
| 544 | 668 |
| 356 | 188 |
| **Total** | **7,586** |

### C-SCAN

| Request | Movement |
|---|---|
| 2,296 | 146 |
| 2,800 | 504 |
| 3,681 | 881 |
| 4,965 | 1,284 |
| 4,999 | 34 |
| 0 (jump, no service) | 4,999 |
| 356 | 356 |
| 544 | 188 |
| 1,212 | 668 |
| 1,523 | 311 |
| 1,618 | 95 |
| 2,069 | 451 |
| **Total** | **9,917** |

**Comparison:** SSTF is shortest (**7,586**), FCFS is longest (**13,011**), C-SCAN is **9,917**. SSTF can starve far requests; C-SCAN gives more uniform waiting because it always sweeps in one direction.

---

## 6.8 Disk scheduling algorithms — comparison **[5]**

| Algorithm | Rule | Advantage | Disadvantage |
|---|---|---|---|
| **FCFS** | Serve in arrival order | Simple, fair, no starvation | Long seek times, wild head movement |
| **SSTF** | Shortest seek first | Good throughput, simple | Far requests may starve; not optimal |
| **SCAN** | Move to one end, then reverse (elevator) | No starvation, bounded wait | Extra travel to the end even with no requests |
| **C-SCAN** | Service only in one direction; jump back to 0 without service | Uniform wait time; good for heavy load | Long jump; extra travel |
| **LOOK** | SCAN but stop at the last request | Less travel than SCAN | Slightly more complex |
| **C-LOOK** | C-SCAN but jump to the lowest pending request | Least travel of the circular variants | More complex |

**Formulas**

```text
Total seek time = Σ |head_now − head_next|
Average seek time = total seek time / number of requests
Throughput = requests / total time
```

**Which to choose?** FCFS for simplicity/fairness; SSTF for light, random load; SCAN/C-SCAN for heavy load with fairness; LOOK/C-LOOK when the request range is much smaller than the disk range.

---

## 6.9 RAID levels **[5–8]**

**Asked in:** P8 Jan-24 Q2(2).

**Question (verbatim):**
> Explain different RAID levels with an example.

**RAID = Redundant Array of Independent Disks.** It combines multiple disks for performance, reliability, or both.

| Level | Technique | Redundancy | Capacity | Fault tolerance | Typical use |
|---|---|---|---|---|---|
| **RAID 0** | Block striping | None | N disks | 0 failures | Speed, scratch/temp |
| **RAID 1** | Mirroring | Full copy | N/2 | 1 disk (per mirror) | OS, small critical data |
| **RAID 2** | Bit striping + Hamming ECC | ECC disks | Less | 1 disk | Obsolete |
| **RAID 3** | Byte striping + dedicated parity | Parity disk | N−1 | 1 disk | Large sequential transfers |
| **RAID 4** | Block striping + dedicated parity | Parity disk | N−1 | 1 disk | Reads; parity disk bottleneck on writes |
| **RAID 5** | Block striping + **distributed** parity | Distributed parity | N−1 | 1 disk | General-purpose servers |
| **RAID 6** | Block striping + **double** parity | Two parity blocks | N−2 | 2 disks | Large arrays, high availability |
| **RAID 10** | Mirror + stripe (1+0) | Mirroring | N/2 | 1 per mirror pair | Databases, high IOPS |

**Key ideas**

- **Striping** spreads data across disks → parallel I/O, higher throughput.
- **Mirroring** duplicates data → fast reads, survives a disk failure, 50% capacity cost.
- **Parity** stores XOR of data blocks → can reconstruct one failed disk; RAID 6 uses two independent parity blocks for two failures.
- **Hot spare:** an idle disk automatically replaces a failed one and rebuilds.
- **Write penalty:** RAID 5/6 writes require read-modify-write of parity; RAID 10 writes are simpler.
- **Rebuild time:** larger disks take longer; RAID 6 reduces the risk during rebuild.

**Example (4 disks of 1 TB each)**

| Level | Usable capacity | What happens on one disk failure |
|---|---|---|
| RAID 0 | 4 TB | Data lost (no redundancy) |
| RAID 1 | 2 TB | Mirror copy survives; rebuild the failed disk |
| RAID 5 | 3 TB | Parity reconstructs the lost disk; array continues degraded |
| RAID 6 | 2 TB | Two parity blocks; survives **two** disk failures |
| RAID 10 | 2 TB | Each mirror pair survives one failure; rebuild from its mirror |

**One-line answer for the exam:** RAID 0 = speed only; RAID 1 = mirroring; RAID 5 = striping + distributed parity (survives 1 failure); RAID 6 = double parity (survives 2); RAID 10 = mirror + stripe (high performance and reliability, 50% capacity).

---

## 6.10 File management vs I/O management **[5]**

**Asked in:** P3 IoT-F24 Q2(A).

**Question (verbatim):**
> Distinguish the important key aspects of File management and I/O Management.

**Answer**

| File management | I/O management |
|---|---|
| Logical view: files, directories, names, attributes | Physical view: devices, controllers, drivers, interrupts |
| File allocation (contiguous/linked/indexed), free-space management | Device scheduling, buffering, caching, spooling |
| Access control and sharing | Device protection and permissions |
| Operations: create, open, read, write, seek, close, delete | Operations: issue command, transfer, acknowledge, retry |
| Concerned with **what** data is stored and **who** can access it | Concerned with **how** data moves between memory and device |
| Uses the block I/O layer | Implements the block/character device layer |

> The **file-management** half (allocation methods, directory structures) is not part of `DJS23IMD201`. Only the I/O-management half is examinable in Unit 6. A short linked-allocation answer is in `00_Index.md` Appendix A2.

---

## 6.11 Out-of-syllabus: linked allocation **[7]**

**Asked in:** P3 IoT-F24 Q3(B). Full solution in `00_Index.md` Appendix A2. Key points:

- 200 data blocks; pointer stored in each block → file occupies **200 blocks** (usable data per block is reduced by the pointer).
- If a separate pointer block is used per data block → **400 blocks**.
- Average access to a random block = `(200+1)/2 = 100.5 ≈ 101` block reads, because linked allocation has no direct access.

---

## Unit 6 — quick revision checklist

- [ ] 10 roles of I/O management; block vs character devices.
- [ ] Single/double/circular buffering: diagram, overlap, memory, use.
- [ ] Disk access time = seek + rotational + transfer; average rotational latency = 1/2 rotation.
- [ ] FCFS/SSTF/SCAN/C-SCAN/LOOK/C-LOOK: rule + one advantage + one disadvantage.
- [ ] Compute total seek time for a request queue; show the movement table.
- [ ] SCAN vs LOOK: SCAN goes to the end, LOOK stops at the last request.
- [ ] C-SCAN vs C-LOOK: jump to 0 vs jump to the lowest pending request.
- [ ] RAID 0/1/5/6/10: technique, capacity, faults tolerated, use. **Jan-24 asked RAID explicitly.**
- [ ] Parity vs mirroring; write penalty; rebuild.
- [ ] Jan-24 disk: FCFS 13011, SSTF 7586, C-SCAN 9917 (head 2150, previous 1805, 0–4999).
- [ ] File management vs I/O management: 6 differences.
