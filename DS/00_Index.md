# Data Structures — Past-Year Questions: Unit-wise Index

**Course:** Data Structures — `DJS23IPC201` (Theory)  
**Programme:** S.Y. B.Tech Information Technology, Semester III (DJS23 scheme)  
**Papers used:** DJS23 Semester-III Data Structures papers from 2023-24, 2024-25 and 2025-26, across IT, Computer, AIDS/AIML and CSE (ICB).

This folder groups every question from the included papers into the **six syllabus units**, merges repeated/near-identical questions, and gives a complete exam-style answer for each entry. Numerical answers are worked step by step; coding questions get an algorithm and, where the paper explicitly asks for a **program**, a complete C implementation.

---

## 1. Files in this folder

| File | Unit | Main topics |
|---|---|---|
| [Unit_1_Basics_of_Algorithms_and_Data_Structures.md](Unit_1_Basics_of_Algorithms_and_Data_Structures.md) | 1 | Algorithm characteristics, data-structure types, ADT, time/space complexity, Big-O/Omega/Theta, best/worst/average case, code-snippet analysis, file vs data structure |
| [Unit_2_List_Linked_Lists.md](Unit_2_List_Linked_Lists.md) | 2 | List ADT, array vs linked list, singly/circular/doubly linked lists, insert/delete/search/reverse/merge/split/duplicates/loop/median, polynomial arithmetic |
| [Unit_3_Stack_and_Queue.md](Unit_3_Stack_and_Queue.md) | 3 | Stack ADT and implementations, infix→postfix/prefix, postfix evaluation, expression trees, linear/circular/priority queue, DEQueue, queue applications |
| [Unit_4_Trees_BST_AVL_Heap.md](Unit_4_Trees_BST_AVL_Heap.md) | 4 | Tree terminology, traversals, tree construction, BST operations, AVL rotations/insert/delete, heap operations, Huffman tree, B-tree |
| [Unit_5_Graphs.md](Unit_5_Graphs.md) | 5 | Graph terminology, matrix vs list, BFS, DFS, BFS vs DFS, topological sort, cycle detection, strongly connected graphs, reverse graph, in/out degree |
| [Unit_6_Searching_Sorting_Hashing.md](Unit_6_Searching_Sorting_Hashing.md) | 6 | Linear/binary/Fibonacci search, bubble/selection/insertion/merge/quick/radix/heap sort, comparisons, hash functions, collision resolution, rehashing |
| [DS_Last_Minute_Revision.md](DS_Last_Minute_Revision.md) | All 1–6 | **One-file cheat sheet:** formulas, algorithms, complexities, numerical answers, question templates |

**Coverage:** 73 topic sections covering ~110 distinct question entries. Every question from the 14 included papers is mapped at least once; repeated questions list all source papers under **Asked in**.

---

## 2. Papers included

| Code | Paper | Date | Marks | Official key in `ANS/`? |
|---|---|---|---|---|
| **IT-F24** | IT — Final 2024-25 (`DJS23ICPC301`) | 07 Dec 2024 | 60 | Yes |
| **CO-F24** | Computer — Final 2024-25 (`DJS23CCPC302`) | 07 Dec 2024 | 60 | Yes |
| **IO-F24** | IOT — Final 2024-25 (`DJS23BCPC302`) | 07 Dec 2024 | 60 | Yes |
| **AI-F24** | AIDS/AIML — Final 2024-25 (`DJS23ACPC302` / `DJS23SCPC302`) | 07 Dec 2024 | 60 | Yes (SCPC key; ACPC key file is mislabeled — see §6) |
| **IT-R25** | IT — Re-Exam 2024-25 (`DJS23ICPC301`) | 24 Mar 2025 | 60 | Yes |
| **CO-R25** | Computer — Re-Exam 2024-25 (`DJS23CCPC302`) | 24 Mar 2025 | 60 | Yes |
| **AI-R25** | AIDS/AIML — Re-Exam 2024-25 (`DJS23ACPC302` / `DJS23SCPC302`) | 24 Mar 2025 | 60 | Yes (both are identical) |
| **P8 Jan-24** | Common — Final 2023-24 (all branches) | 08 Jan 2024 | 65 | No key in folder |
| **IT-F25** | IT — Final 2025-26 | 16 Dec 2025 | 60 | Yes |
| **IT-R26** | IT — Re-Exam 2025-26 (2024-25 & 2025-26 batches) | 18 Mar 2026 | 60 | No key |
| **AI-R26** | AIDS — Re-Exam 2025-26 | 18 Mar 2026 | 60 | No key |
| **IC-R26** | CSE (ICB) — Re-Exam 2025-26 | 18 Mar 2026 | 60 | No key |
| **CO-R26** | Computer — Re-Exam 2025-26 (2024-25 batch) | 18 Mar 2026 | 60 | No key |
| **CO-B26** | Comp/DS/AIDS/IT — Backlog Re-Exam 2023-24 | 18 Mar 2026 | 65 | No key |

**Merged duplicates:** `DJS23ACPC302` and `DJS23SCPC302` papers are identical for both the 2024-25 final and re-exam; they are tagged **AI-F24** / **AI-R25** and counted once.

**Excluded papers:** the 2021-22 and 2022-23 papers (50/75-mark pre-DJS23 pattern) and the Sem-V **Advanced Data Structures** papers (`DJS22ITC502`, `DJS22AMC5012`). Their topics either repeat the DJS23 syllabus or belong to a different course; including them would duplicate entries and mix syllabi.

---

## 3. Unit coverage at a glance

| Unit | Hours | Sections | Recurring questions |
|---|---:|---:|---|
| 1 — Basics | 4 | 9 | algorithm characteristics, data-structure classification, ADT, time vs space complexity, Big-O/Omega/Theta |
| 2 — List | 7 | 16 | SLL/DLL/CLL operations, reverse, duplicates, merge, split, polynomial |
| 3 — Stack & Queue | 7 | 12 | stack ADT, infix→postfix/prefix, postfix evaluation, expression tree, circular queue, DEQueue, priority queue |
| 4 — Trees | 10 | 8 | traversals, tree construction, BST operations, AVL rotations, heap, Huffman, B-tree |
| 5 — Graphs | 6 | 10 | matrix vs list, BFS, DFS, topological sort, cycle detection, strong connectivity |
| 6 — Search/Sort/Hash | 8 | 18 | search traces, seven sorts, hash functions, linear/quadratic/double probing, chaining, rehashing |

---

## 4. How to use this material

1. **Start with the unit you are currently studying.** Each file opens with the exact syllabus wording and a source-code table.
2. **Read the question first, then the answer.** Every entry has a verbatim question block; the heading is only a navigation label.
3. **Prioritise repeated questions.** The highest-frequency topics are:
   - Linked-list operations (insert/delete/reverse/merge/duplicates) — every paper.
   - Infix→postfix/prefix and postfix evaluation — every paper.
   - AVL insertion/deletion with rotations — every paper.
   - BFS/DFS and topological sort — every paper.
   - Sorting traces (quick, merge, radix) and hashing numericals — every paper.
4. **Use the quick checklist** at the end of each unit for last-minute revision.
5. **Read the conventions below** before comparing with an official key.

---

## 5. Answer conventions

### Question text

- Every paper-derived entry starts with a **Question (verbatim)** block. Where the same question appears in several papers with different wording, each variant is listed and all sources are given under **Asked in**.
- Obvious OCR/printing errors in scanned papers are corrected silently (e.g. `qucue_rear` → `queue_rear`, `1317` → `13, 17`, `B↑C` → `B^C`). The intent is preserved; the correction is noted where it changes the answer.
- Marks are shown as printed. For OCR'd papers, marks are occasionally approximate because the scan merged columns.

### Algorithm vs program

The papers say **“program/algorithm”** in most coding questions. The answers give:

- a **language-independent algorithm/pseudocode** for every coding question, and
- a **complete C program** where the question explicitly says **“C program”**, **“complete program”**, or **“menu-driven program”**.

Where only an algorithm is asked, the answer stays with pseudocode plus a short C fragment if it clarifies the pointer updates.

### Numerical answers

- Every hash table, AVL tree, heap, sort pass and search trace was recomputed independently.
- Where a question is internally inconsistent, both self-consistent readings are shown and the recommended one is marked (see §6).

### Official answers

- Official synoptic keys were used as a starting point where available, then expanded and checked.
- Papers with **no key** (Jan-24, IT-R26, AI-R26, IC-R26, CO-R26, CO-B26) have fully written model answers.
- Where an official key is image-only (trees, tables), the answer was reconstructed from the question.

---

## 6. Discrepancies and OCR corrections

| Paper | Issue | How the bank handles it |
|---|---|---|
| **AI-F24 Q2(1)(ii)** | 8 keys `[3,2,9,6,11,13,7,12]` with “linear probing” but no table size. With `h(k)=k mod 5`, 8 keys cannot fit in 5 slots by open addressing. | Solved with **separate chaining** (matches the official key’s `k mod 5`). Also shows a 10-slot linear-probing alternative. |
| **IT-R26 Q4A** | `h(k)=k % 10` but “table size 5”; the function can return indices 5–9. | Shows both readings: `k mod 5` (5 slots) and `k mod 10` (10 slots); recommends `h(k)=k mod tableSize`. |
| **CO-F24 Q3B(ii)** | B-tree data printed as `3,12,7,32,8,1,5,15,4,9,1317,6,21` (missing comma). | Read as `…, 9, 13, 17, 6, 21`. |
| **IO-F24 Q3B** | Says “Depth-First Search **(BFS)**” — a typo. | Solved as **DFS** starting at A. |
| **IC-R26 Q1a** | Expression printed with `B ↑ C`; OCR can read it as `B + C`. | Treated as **`B^C`** (exponentiation), giving postfix `ABC^*DE/-FG/+` and value 50. |
| **ANS_Final … ACPC302** | The file labelled “Final” actually contains the **Re-Exam** key (dated 24 Mar 2025). | The 2024-25 final is covered by the SCPC key, which is the same paper. |
| **P8 Jan-24** | 65-mark paper with no key; some diagrams are images. | Fully solved; the BST and graph are reconstructed from the page images. |
| **CO-B26 Q1A(1)** | The paper prints `554 + 753` with output `1→2→0→7`, but `554 + 753 = 1307`. | Solved correctly as `1→3→0→7`; explains that `1207` would require the second list to be `6→5→3`. |

---

## 7. Topics outside the six units (included as extensions)

These appeared in included papers but are not printed in the DJS23IPC201 unit list. They are answered so nothing is silently dropped:

| Paper | Question | Topic | Where |
|---|---|---|---|
| CO-F24 Q2A(ii) | Huffman tree for weights 22, 5, 11, 19, 2, 11, 25, 5, 15, 20 | Huffman coding | Unit 4 §4.7 |
| CO-R25 Q2A(ii) | Huffman tree for 10…90 | Huffman coding | Unit 4 §4.7 |
| IC-R26 Q3A(ii) | Huffman codes for A45, B13, C12, D16, E9, F5 | Huffman coding | Unit 4 §4.7 |
| CO-F24 Q3B(ii) | B-tree of order 4 | B-tree | Unit 4 §4.8 |
| P8 Jan-24 Q3B(i) | File structure vs data structure | File organization | Unit 1 §1.8 |
| IT-F25 Q2B(1) | Strongly connected graph check | Graph algorithms | Unit 5 §5.1 |
| IT-F25 Q3B | DFS cycle detection | Graph algorithms | Unit 5 §5.7 |
| IT-R26 Q4A | Hashing vs rehashing | Dynamic hashing | Unit 6 §6.15 |
| Syllabus | Linear/extendible hashing | Dynamic hashing | Unit 6 §6.16 |
