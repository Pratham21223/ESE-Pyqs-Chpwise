# DBMS Past-Year Questions — Unit-wise Index

**Course:** Database Management System — `DJS23IPC202` (Theory) / `DJS23IPC202L` (Lab)
**Programme:** S.Y. B.Tech Information Technology, Semester III
**Scheme:** DJS23, Revision 4 (2025), effective AY 2025-2026
**Syllabus source:** `DJS23 SY B. Tech IT Version 2 Scheme and Syllabus (14 weeks).pdf`

This folder classifies the DJS23-scheme DBMS past-year questions into the **six syllabus units**, merges repeated/near-identical questions, and gives a full exam-style answer for every entry. Official synoptic answers were used where available; incomplete or missing answers were written out in full.

---

## 1. Files in this folder

| File | Unit | Main topics |
|---|---|---|
| [Unit_1_Introduction_to_Databases.md](Unit_1_Introduction_to_Databases.md) | 1 | DBMS characteristics, file system vs DBMS, schema/instance, data independence, users, DBA, architecture, storage manager |
| [Unit_2_Relational_Model_and_ER.md](Unit_2_Relational_Model_and_ER.md) | 2 | ER diagrams (10 scenarios), EER specialization/generalization/aggregation, constraints, keys, ER→relational mapping, relational algebra |
| [Unit_3_SQL.md](Unit_3_SQL.md) | 3 | SQL basics, constraints, 16 query sets, joins, views, triggers, authorization, DCL/TCL, aggregates, set/string operations |
| [Unit_4_Relational_Database_Design.md](Unit_4_Relational_Database_Design.md) | 4 | Normalization, FDs, 1NF/2NF/3NF/BCNF, 11 solved FD problems, candidate keys, decomposition |
| [Unit_5_Transaction_Management_and_Recovery.md](Unit_5_Transaction_Management_and_Recovery.md) | 5 | ACID, states, serializability (9 schedules), 2PL, timestamp ordering, validation, deadlock, recovery, shadow paging/ARIES |
| [Unit_6_Indexing.md](Unit_6_Indexing.md) | 6 | Indexing basics, primary/secondary, dense/sparse, clustered, single/multilevel, B-tree, B+ tree, hashing vs indexing |
| [DBMS_Last_Minute_Revision.md](DBMS_Last_Minute_Revision.md) | All 1–6 | **One-file cheat sheet:** formulas, syntax, methods, question templates, past-answer tables |

**Coverage:** 98 main question entries (many with multiple sub-questions). The SQL query bank alone contains 80+ individual queries; Unit 2 contains 10 ER scenarios, 6 paper-specific ER/EER mappings and 4 relational-algebra sets; Unit 4 contains 11 fully solved FD/normalization problems.

---

## 2. Papers included

All papers are from the **DJS23 scheme** (2024-25 and 2025-26 exam cycles), across branches. The questions overlap heavily; duplicates were merged and tagged with every source paper.

| Code | Paper | Course code | Date | Marks | Official key in `ANS/`? |
|---|---|---|---|---|---|
| **IT-F24** | IT — Final Examination 2024-25 | DJS23ICPC302 | 09 Dec 2024 | 60 | Yes (`ANS_Final-Exam_...DJS23ICPC302`) |
| **IT-R25** | IT — Re-Examination 2024-25 | DJS23ICPC302 | 22 Mar 2025 | 60 | Yes (`ANS_Re-Exam_...DJS23ICPC302`) |
| **CO-F24** | Computer Engineering — Final 2024-25 | DJS23CCPC303 | 09 Dec 2024 | 60 | Yes (`ANS_Final-Exam_...DJS23CCPC303`) |
| **CO-R25** | Computer Engineering — Re-Exam 2024-25 | DJS23CCPC303 | 22 Mar 2025 | 60 | Yes (`ANS_Re-Exam_...DJS23CCPC303`) |
| **IO-F24** | CSE (IOT & Cyber Security with Blockchain) — Final 2024-25 | DJS23BCPC303 | 09 Dec 2024 | 60 | Yes (`ANS_Final-Exam_...DJS23BCPC303`) |
| **DS-F24** | CSE (Data Science) — Final 2024-25 | DJS23DCPC302 | 09 Dec 2024 | 60 | No key in folder |
| **DS-R25** | CSE (Data Science) — Re-Exam 2024-25 | DJS23DCPC302 | 22 Mar 2025 | 60 | Yes (`ANS_Re-Exam_...DJS23DCPC302`) |
| **IT-F25** | IT — Final Examination 2025-26 | DJS23IPC202 | 04 Dec 2025 | 60 | Yes (`ANS-Sem-III-IT-...`) |
| **IT-R26** | IT — Re-Exam 2025-26 (also 2024-25 batch) | DJS23IPC202 | 16 Mar 2026 | 60 | No key |
| **AI-R26** | AI & DS — Re-Exam 2025-26 | — | 16 Mar 2026 | 60 | No key |
| **IC-R26** | CSE (IOT & Cyber Security with Blockchain) — Re-Exam 2025-26 | — | 16 Mar 2026 | 60 | No key |
| **CO-R26** | Computer Engineering — Re-Exam for 2024-25 batch | — | 16 Mar 2026 | 60 | No key |
| **CO-B26** | Comp/DS/AIDS/IT — Backlog Re-Exam 2023-24 | — | 16 Mar 2026 | 65 | No key |

**Excluded papers:** 2021-22 and 2022-23 (pre-DJS23) papers, the Sem V Advanced DBMS paper, and 2023-24 regular papers that do not match the DJS23 IPC202 syllabus. They were used only where a question directly matches a syllabus topic.

---

## 3. Unit coverage at a glance

| Unit | Hours | Main entries | Recurring questions |
|---|---|---|---|
| 1 — Introduction | 4 | 8 | DBMS characteristics, file system vs DBMS, schema vs instance, data independence, DBA, three-level architecture |
| 2 — Relational Model | 10 | 22 | ER diagrams (library, hospital, banking, hotel, airline, music, college, university, pharmacy), EER concepts, keys, mapping, relational algebra |
| 3 — SQL | 11 | 24 | 16 query sets, constraints, joins, views, triggers, GRANT/REVOKE, DCL/TCL, aggregates, set/string operations |
| 4 — Relational Design | 6 | 15 | Normalization basics, FD types, 1NF–BCNF definitions, 11 FD sets solved step by step |
| 5 — Transactions & Recovery | 8 | 22 | ACID, states, 9 serializability schedules, 3 timestamp schedules, 2PL, validation, deadlock, log recovery, shadow paging/ARIES |
| 6 — Indexing | 3 | 7 | Index types, B-tree/B+ tree insertion, search, CRUD, hashing vs indexing, clustered index |

---

## 4. How to use this material

1. **Start with the unit matching your current lecture.** Each file begins with the exact syllabus wording and a source table.
2. **Read the question first, then the answer.** Questions are numbered (`Q1.1`, `2.1`, `3.1`, `Problem 1`, `Schedule 1`, etc.) and tagged with the paper code(s) where they appeared.
3. **Practise the repeated questions first.** The highest-frequency topics are:
   - ER diagram + ER→relational mapping (Units 2)
   - SQL query sets with joins/group by/nested queries (Unit 3)
   - Normalization with candidate keys (Unit 4)
   - Conflict/view serializability + 2PL/timestamp (Unit 5)
   - B-tree/B+ tree insertion (Unit 6)
4. **Use the quick checklist** at the end of each file for last-minute revision.
5. **Check the conventions section** (below) before comparing with an official key.

---

## 5. Answer sourcing and conventions

### Question text is now verbatim

Every entry starts with a **Question (verbatim)** / **Questions (verbatim)** block that quotes the wording printed in the paper. The heading above it is only a short label for navigation — it is not a replacement for the question. Where the same question appeared in several papers with different wording, each variant is listed. Where questions were merged, all source papers are listed under "Asked in".

For image-based questions (ER/EER diagrams, schedules, normalization tables), the text is quoted and the diagram/table is reconstructed or mapped in the answer.

### Official answers vs written answers

- Where an official synoptic key had a usable text answer, it was used as the basis and expanded for completeness.
- Several official keys were **image-only** for diagrams, schedules and normalization tables. Those answers were reconstructed/solved from the question paper.
- Papers with **no key** (DS-F24, IT-R26, AI-R26, IC-R26, CO-R26, CO-B26) have fully written answers. These are model answers, not official keys.

### Marks

- Marks are shown as printed on the paper. For OCR'd papers, marks are occasionally approximate because the scan/OCR merged columns.
- Where a question says "any one" / "OR", both alternatives are covered where they fit the syllabus.

### B-tree/B+ tree convention used

- **Order `m` = maximum number of children**; maximum keys = `m−1`.
- On overflow (`m` keys), the node splits: left part keeps `⌈m/2⌉−1` keys, the median is promoted (B-tree) or copied up (B+ tree), and the right part keeps the rest.
- If your class defines order as "maximum keys" instead, the same method applies; only the split point shifts. State your convention in the exam.

### OCR caveat

The 2025-26 re-exam papers (IT-R26, AI-R26, IC-R26) and the 2024-25/2023-24 backlog papers (CO-R26, CO-B26) are scanned images. Their text was OCR'd and cross-checked against the page images for schedules, tables and FD sets. Minor wording/marks differences may remain; the question intent and answers are unaffected.

---

## 6. Official-key discrepancies (important)

While cross-checking, three official keys disagree with a careful re-solution. The unit files explain each; note them before comparing:

1. **IT-R25 Q1(A)(1) schedule** — the official key marks it non-serializable. Tracing the printed schedule gives edges `T3→T1`, `T1→T2`, `T3→T2` — an **acyclic** graph, so it is conflict serializable to `T3→T1→T2`.
2. **IT-F25 Q1(A)(I) S1/S2** — the official key marks S2 conflict-serializable. Both S1 and S2 have cycles (`T1↔T2`), so **neither is conflict serializable**. S2 is, however, **view serializable** to `T2→T1` because of the blind write on Y. This is the classic conflict-vs-view distinction.
3. **CO-R25 Book/Collection** — the official key marks Collection as 3NF. Technically `Catalog_no → Title, Author` makes Catalog_no a candidate key, so Collection is **BCNF** (and therefore also 3NF). The file gives both the rigorous answer and the official convention.

These are flagged in the relevant unit files, not hidden.

---

## 7. Questions outside the six units

The following appeared in the included papers but are **not part of the DJS23IPC202 six-unit syllabus**. They are listed here so nothing is silently dropped. Some are briefly noted inside a unit where they are closely related (e.g., hashing vs indexing in Unit 6).

| Paper | Question | Topic | Status |
|---|---|---|---|
| DS-F24 Q4(A) | Explain how data is structured in a graph database; give an application | Graph databases / NoSQL | Out of syllabus |
| DS-F24 Q4(B)(1) | Role of dimension tables in a STAR schema; design a healthcare STAR schema | Data warehousing | Out of syllabus |
| DS-F24 Q4(B)(2) | OLAP characteristics; slice and dice | OLAP | Out of syllabus |
| DS-R25 Q4(A) | ACID vs BASE | BASE / NoSQL | ACID part covered in Unit 5.7; BASE is out of syllabus |
| DS-R25 Q4(B)(1) | STAR schema design for a retail chain | Data warehousing | Out of syllabus |
| DS-R25 Q4(B)(2) | ROLAP/MOLAP/HOLAP; roll-up | OLAP | Out of syllabus |
| AI-R26 Q4(B) | Linear hashing and overflow handling | Dynamic hashing | Out of syllabus (brief note in Unit 6.6) |
| CO-B26 Q4(B)(ii) | Dynamic hashing techniques | Dynamic hashing | Out of syllabus (brief note in Unit 6.6) |
| CO-B26 Q4(B)(i) | Cluster index with example | Indexing (related) | Included in Unit 6.7 as an extension |
| CO-F24 Q4(B) | Hashing vs indexing | Hashing (related) | Included in Unit 6.6 as a comparison |
| IO-F24 Q4(a) | Shadow paging vs ARIES | Recovery (ARIES beyond syllabus) | Included in Unit 5.6.3 as a comparison |
| IC-R26 Q1(b)(i) | Super/candidate/primary/foreign/surrogate keys | Keys (surrogate beyond) | Included in Unit 2.15 |
| CO-B26 Q2(B)(i)/(ii) | View levels; storage-manager components | DBMS architecture | Included in Unit 1.7/1.8 |

---

## 8. Quick “most expected” list for the exam

Based on frequency across the 13 papers:

1. **ER diagram** for a given scenario + **map ER/EER to relational** (Units 2).
2. **SQL queries**: joins, `GROUP BY`/`HAVING`, nested subqueries, `ALTER TABLE`, views (Unit 3).
3. **Normalization**: find candidate keys, highest NF, decompose to 3NF/BCNF (Unit 4).
4. **Conflict vs view serializability**: draw a precedence graph, find an equivalent serial schedule (Unit 5).
5. **2PL / strict 2PL** lock sequence; **timestamp ordering** RTS/WTS (Unit 5).
6. **B-tree/B+ tree insertion** with splits; B+ tree search/CRUD (Unit 6).
7. **ACID + transaction states**, **log-based recovery** (deferred vs immediate), **shadow paging** (Unit 5).
8. **Data independence, DBA, DBMS architecture** short answers (Unit 1).

Good luck — and if a question in the exam is worded differently, classify it by the **operation/concept** it tests, not by the exact table names.
