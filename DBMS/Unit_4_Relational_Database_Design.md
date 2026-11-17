# Unit 4 — Relational Database Design (Normalization)

> **Syllabus (DJS23IPC202, 6 hrs):** Pitfalls in relational-database designs, concept of normalization, functional dependencies, normal forms — 1NF, 2NF, 3NF, BCNF.
>
> **Past-paper coverage:** Need for normalization and design pitfalls, functional-dependency types and role, 1NF/2NF/3NF/BCNF definitions, candidate-key computation, highest normal form of a relation, step-by-step decomposition to 2NF/3NF/BCNF (12+ FD sets).

**Source codes**

| Code | Paper |
|---|---|
| IT-F24 | IT Final 2024-25 (Dec 2024) |
| IT-R25 | IT Re-Exam 2024-25 (Mar 2025) |
| CO-F24 | Comp Final 2024-25 (Dec 2024) |
| CO-R25 | Comp Re-Exam 2024-25 (Mar 2025) |
| IO-F24 | IOT Final 2024-25 (Dec 2024) |
| DS-F24 | DS Final 2024-25 (Dec 2024) |
| DS-R25 | DS Re-Exam 2024-25 (Mar 2025) |
| IT-F25 | IT Final 2025-26 (Dec 2025) |
| IT-R26 | IT Re-Exam 2025-26 (Mar 2026) |
| AI-R26 | AIDS Re-Exam 2025-26 (Mar 2026) |
| IC-R26 | CSE (ICB) Re-Exam 2025-26 (Mar 2026) |
| CO-R26 | Comp Re-Exam 2024-25 batch (Mar 2026) |
| CO-B26 | 2023-24 Backlog Re-Exam (Mar 2026) |

---

## 4.1 Why normalization? Design pitfalls and anomalies **[5–7]**

**Asked in:** CO-R26 Q1(B)(i) (need for normalization, redundancy/integrity); IO-F24 Q3(b)(i) (pitfalls); CO-F24 Q1(B)(i) (FD role).

**Questions (verbatim):**
> Explain the need for normalization in database design. Illustrate with an example how normalization helps reduce data redundancy and maintain data integrity.
>
> What are some common pitfalls in relational database design, and how can they impact database performance and data integrity?

**Answer**

**Normalization** is the process of organizing relations to reduce redundancy and eliminate insertion, update and deletion anomalies. It decomposes a large, poorly designed relation into smaller relations based on functional dependencies, without losing information.

**Problems in an unnormalized design**

| Anomaly | What happens | Example |
|---|---|---|
| **Insertion anomaly** | Cannot insert a fact without unrelated data | Cannot add a new department until an employee joins it |
| **Update anomaly** | Same fact stored many times; one update may be missed | Department name repeated for every employee; renaming updates some rows only |
| **Deletion anomaly** | Deleting one fact accidentally deletes another | Deleting the last employee of a department deletes the department's location |
| **Redundancy** | Wasted storage and inconsistent copies | Same address repeated in many rows |

**Common design pitfalls**

1. **Insufficient normalization** — redundancy and anomalies.
2. **Over-normalization** — too many small tables, excessive joins, slower queries.
3. **Improper primary key** — large/composite/natural keys, or no key at all.
4. **Missing/incorrect foreign keys** — orphaned records, broken referential integrity.
5. **Inadequate indexing** — full table scans, slow retrieval.
6. **Excessive NULLs** — complicated queries and weak integrity.
7. **Wrong data types/lengths** — wasted space, truncation, validation problems.
8. **No scalability plan** — performance degrades as data grows.

**Benefit of normalization:** controlled redundancy, consistent data, smaller storage, better integrity, and easier maintenance. The trade-off is more joins; in analytical systems, controlled denormalization (star schema) is sometimes preferred.

---

## 4.2 Functional dependencies — definition, types and role **[5–10]**

**Asked in:** CO-F24 Q1(B)(i) (definition + role in 2NF/3NF); AI-R26 Q3(A)(ii) (types); DS-F24 Q3(B)(1) (FD role); DS-R25 Q3(B)(1) (transitive dependency).

**Questions (verbatim):**
> Define functional dependency and explain its role in database normalization. Why is it important in achieving 2NF and 3NF?
>
> Describe various types of functional dependencies with the help of example.
>
> Analyze the term transitive dependency and explain how it impacts database normalization.

**Answer**

A **functional dependency (FD)** `X → Y` means: whenever two tuples agree on the attributes in `X`, they must also agree on the attributes in `Y`. `X` is the **determinant**; `Y` is the **dependent**.

**Example:** In `Student(StudentID, Name, DeptID, DeptName)`, `StudentID → Name, DeptID` and `DeptID → DeptName`.

**Types of functional dependencies**

1. **Trivial FD** — `Y ⊆ X`. Example: `{A, B} → A`. It always holds.
2. **Non-trivial FD** — `Y ⊄ X`. Example: `A → B`.
3. **Completely non-trivial FD** — `X ∩ Y = ∅`. Example: `StudentID → Name`.
4. **Full functional dependency** — `X → Y` and no proper subset of `X` determines `Y`. Example: `{StudentID, CourseID} → Grade`.
5. **Partial dependency** — a non-prime attribute depends on only part of a candidate key. Example: `{StudentID, CourseID} → StudentName`, while `StudentID → StudentName`. Violates 2NF.
6. **Transitive dependency** — `X → Y` and `Y → Z`, so `X → Z`, with `Y` not a candidate key. Example: `EmpID → DeptID`, `DeptID → DeptName`, so `EmpID → DeptName`. Violates 3NF.
7. **Multivalued dependency (MVD)** — `X →→ Y`: one value of `X` is associated with a set of `Y` values, independent of other attributes. Used in 4NF (beyond this syllabus, but good to mention).

**Role in normalization**

- FDs identify **candidate keys**: the closure of an attribute set tells us what it determines.
- They reveal **partial dependencies** (2NF) and **transitive dependencies** (3NF).
- Decomposition is driven by violating FDs; each new relation is built around a determinant.
- BCNF requires every non-trivial FD determinant to be a superkey.

---

## 4.3 Normal forms — definitions **[5–10]**

**Asked in:** IT-R25 Q2(A)(1); CO-R25 Q1(B)(i); IT-R26 Q2(A)(II); CO-R26 Q6(i); AI-R26 Q3(A)(i); IC-R26 Q4(b)(i); IO-F24 Q3(b)(ii); IT-F25 Q2(A)(II).

**Questions (verbatim):**
> Write the general definitions of 1NF, 2NF and 3NF.
>
> Write the general definitions of 2NF, 3NF and BCNF.
>
> What is Normalization? Explain 3 NF and BCNF with suitable examples.
>
> Explain Third Normal Form (3NF) with an example. How does it differ from 2NF?
>
> What is Boyce-Codd Normal Form (BCNF)? Explain with example.

**Answer**

| Normal form | Condition | Removes |
|---|---|---|
| **1NF** | All attributes are atomic; no repeating groups or multivalued attributes; each row is unique | Non-atomic values |
| **2NF** | 1NF + no **partial dependency**: no non-prime attribute depends on a proper subset of any candidate key | Partial dependencies |
| **3NF** | 2NF + no **transitive dependency**: for every non-trivial FD `X → A`, either `X` is a superkey or `A` is a prime attribute | Transitive dependencies |
| **BCNF** | For every non-trivial FD `X → Y`, `X` is a **superkey** | All FD-based redundancy (stricter than 3NF) |

**Prime attribute:** an attribute that is part of at least one candidate key.
**Non-prime attribute:** not part of any candidate key.

**2NF vs 3NF**

| Point | 2NF | 3NF |
|---|---|---|
| Removes | Partial dependencies | Transitive dependencies |
| Requirement | Must be in 1NF | Must be in 2NF |
| Condition | No non-prime attribute depends on part of a candidate key | For every FD `X → A`, `X` is a superkey or `A` is prime |
| Example violation | `{StudentID, CourseID} → StudentName`, but `StudentID → StudentName` | `EmpID → DeptID`, `DeptID → DeptName` |
| After decomposition | Separate the part-key dependency into its own relation | Separate the non-key determinant into its own relation |

**Example:** `Student(Student_ID, Course, Instructor, Instructor_Office)` with `{Student_ID, Course} → Instructor` and `Instructor → Instructor_Office`. The second FD is transitive, so the table is in 2NF but not 3NF. Decompose into `Student_Course(Student_ID, Course, Instructor)` and `Instructor(Instructor, Instructor_Office)`.

**3NF vs BCNF:** 3NF allows a non-superkey determinant if its right-hand side is prime; BCNF does not. BCNF is stricter and may lose dependency preservation.

**Classic BCNF example**

`R(Student, Course, Instructor)` with:
- `{Student, Course} → Instructor`
- `Instructor → Course`

Candidate keys: `{Student, Course}` and `{Student, Instructor}`. `Instructor → Course` has a determinant that is **not** a superkey (Instructor does not determine Student), so R is in 3NF but **not BCNF** (Course is prime). Decompose into:
- `R1(Instructor, Course)` — key Instructor
- `R2(Student, Instructor)` — key {Student, Instructor}

Both are BCNF.

---

# 4.4 Solved normalization problems

## How to find candidate keys (exam method)

1. Compute the **closure** `X+` of an attribute set `X` using the given FDs.
2. Any attribute that never appears on the right-hand side of an FD must be part of every candidate key.
3. Start with those attributes; if their closure is not all attributes, add attributes one by one until the closure covers all.
4. Remove redundant attributes: a candidate key must be **minimal**.
5. An attribute is **prime** if it belongs to any candidate key; otherwise it is **non-prime**.

---

## Problem 1 — Big Patient Table (3NF) **[10]**

**Asked in:** IT-F24 Q2(A)(1).

**Question (verbatim):**
> Apply 3NF normalization to the following Big Patient Table 1. The functional dependencies are shown in Table 2. Show the result of each step in the normalization process.

**Given**

Attributes: `VisitNo, VisitDate, PatNo, PatAge, PatCity, ProvNo, ProvSpecialty, Diagnosis`.

FDs:
```text
PatNo → PatAge, PatCity
ProvNo → ProvSpecialty
VisitNo → PatNo, VisitDate, PatAge, PatCity
VisitNo, ProvNo → Diagnosis
```

**Step 1 — Candidate key**

`(VisitNo, ProvNo)+ = {VisitNo, ProvNo, PatNo, VisitDate, PatAge, PatCity, ProvSpecialty, Diagnosis}` = all attributes.

- `VisitNo+` does not include `ProvNo`, `ProvSpecialty`, `Diagnosis`.
- `ProvNo+` includes only `ProvNo, ProvSpecialty`.

So the only candidate key is **`(VisitNo, ProvNo)`**.
Prime: `VisitNo, ProvNo`. Non-prime: `VisitDate, PatNo, PatAge, PatCity, ProvSpecialty, Diagnosis`.

**Step 2 — 1NF**

All values are atomic (one diagnosis per visit/provider row), so the table is in 1NF.

**Step 3 — 2NF (remove partial dependencies)**

- `VisitNo → PatNo, VisitDate, PatAge, PatCity` — partial dependency (VisitNo is part of the key).
- `ProvNo → ProvSpecialty` — partial dependency (ProvNo is part of the key).
- `(VisitNo, ProvNo) → Diagnosis` — full dependency, keep.

Decompose:

```text
VISIT(VisitNo, VisitDate, PatNo, PatAge, PatCity)
PROVIDER(ProvNo, ProvSpecialty)
VISIT_DIAGNOSIS(VisitNo, ProvNo, Diagnosis)
```

**Step 4 — 3NF (remove transitive dependency)**

In `VISIT`, `PatNo → PatAge, PatCity`, and `PatNo` is non-prime. This is a transitive dependency. Decompose:

```text
PATIENT(PatNo, PatAge, PatCity)
VISIT(VisitNo, VisitDate, PatNo)
PROVIDER(ProvNo, ProvSpecialty)
VISIT_DIAGNOSIS(VisitNo, ProvNo, Diagnosis)
```

**Final 3NF schema**

| Relation | Key | FDs |
|---|---|---|
| PATIENT | PatNo | PatNo → PatAge, PatCity |
| VISIT | VisitNo | VisitNo → VisitDate, PatNo |
| PROVIDER | ProvNo | ProvNo → ProvSpecialty |
| VISIT_DIAGNOSIS | (VisitNo, ProvNo) | (VisitNo, ProvNo) → Diagnosis |

All four relations are in **BCNF** as well. The decomposition is lossless (common keys are candidate keys) and dependency-preserving (`VisitNo → PatAge, PatCity` is implied through `PatNo`).

**Sample data after decomposition**

PATIENT:

| PatNo | PatAge | PatCity |
|---|---|---|
| P1 | 35 | Denver |
| P3 | 17 | Englewood |
| P2 | 60 | Boulder |

VISIT:

| VisitNo | VisitDate | PatNo |
|---|---|---|
| V10020 | 1/13/2007 | P1 |
| V93030 | 1/20/2007 | P3 |
| V82110 | 1/18/2007 | P2 |

PROVIDER:

| ProvNo | ProvSpecialty |
|---|---|
| D1 | Internist |
| D2 | Nurse Practitioner |
| D3 | Cardiologist |

VISIT_DIAGNOSIS:

| VisitNo | ProvNo | Diagnosis |
|---|---|---|
| V10020 | D1 | Ear Infection |
| V10020 | D2 | Influenza |
| V93030 | D2 | Pregnancy |
| V82110 | D3 | Murmur |

---

## Problem 2 — R(ABCDEF), AB→C, C→DE, E→F, F→A **[10]**

**Asked in:** IT-R25 Q2(A)(1); CO-B26 Q1(A)(ii) (same FD set).

**Question (verbatim):**
> Given the relation R(ABCDEF) having functional dependencies, AB-> C, C->DE, E->F and F->A, Perform the following.
> 1. Write the general definitions of 1NF, 2NF and 3NF
> 2. Find the candidate keys.
> 3. Find the Highest normal form followed by the relation R.

**Given**

```text
R(A, B, C, D, E, F)
AB → C
C → D, E
E → F
F → A
```

**Step 1 — Candidate keys**

- `(AB)+ = A, B, C, D, E, F` → **AB is a candidate key**.
- `(BC)+ = B, C, D, E, F, A` → **BC is a candidate key** (B alone does not determine anything; C alone gives C, D, E, F, A but not B).
- `A+ = A`, `B+ = B`, `C+ = C, D, E, F, A` (not B), `D+ = D`, `E+ = E, F, A` (not B, C, D), `F+ = F, A` (not B, C, D, E).

Candidate keys: **AB, BC**.
Prime attributes: **A, B, C**. Non-prime: **D, E, F**.

**Step 2 — Highest normal form**

- 1NF: yes.
- 2NF: For key `BC`, `C → D, E` is a **partial dependency** (C is a proper subset of BC, and D, E are non-prime). Therefore **not 2NF**.
- Highest normal form: **1NF**.

**Step 3 — 3NF decomposition (dependency-preserving)**

```text
R1(A, B, C)      -- AB → C
R2(C, D, E)      -- C → D, E
R3(E, F)         -- E → F
R4(F, A)         -- F → A
```

- R1 contains the candidate key AB.
- R2 key C, R3 key E, R4 key F.
- This is a lossless, dependency-preserving **3NF** decomposition (standard synthesis algorithm).
- R1 is **3NF but not BCNF**: `C → A` is implied (C→E→F→A), C is not a superkey of R1, but A is prime.

**Step 4 — BCNF decomposition (if asked)**

Decompose R1(A,B,C) further on `C → A`:

```text
R1a(C, A)        -- C → A
R1b(B, C)        -- key BC
R2(C, D, E)      -- C → D, E
R3(E, F)         -- E → F
R4(F, A)         -- F → A
```

All five relations are in **BCNF**. The decomposition is lossless, but `AB → C` is **not preserved** (A is in R1a, B/C in R1b). This is the usual trade-off: BCNF may sacrifice dependency preservation.

---

## Problem 3 — R(ABCDEF), AB→C, C→D, C→E, E→F, F→A (BCNF) **[10]**

**Asked in:** CO-F24 Q4(A)(i).

**Question (verbatim):**
> Given the functional dependencies, AB-> C, C->D, C->E, E->F, F->A for the relation R(ABCDEF). Check whether the relation is in BCNF or not. If not normalize it into BCNF.

**Given**

```text
R(A, B, C, D, E, F)
AB → C
C → D
C → E
E → F
F → A
```

**Step 1 — Candidate keys**

The closure is identical to Problem 2: `C → D, E` and `E → F → A`, so:

- `(AB)+ = all` → **AB** is a key.
- `(BC)+ = all` → **BC** is a key.

Prime: A, B, C. Non-prime: D, E, F.

**Step 2 — Highest normal form**

For key BC, `C → D` and `C → E` are partial dependencies (C is part of BC). Therefore the relation is **1NF**, not 2NF.

**Step 3 — BCNF decomposition**

```text
R1(C, A)         -- C → A (implied via C→E→F→A)
R2(B, C)         -- key BC
R3(C, D, E)      -- C → D, E
R4(E, F)         -- E → F
R5(F, A)         -- F → A
```

Verify each relation:

| Relation | Key | Non-trivial FDs | BCNF? |
|---|---|---|---|
| R1(C,A) | C | C → A | Yes |
| R2(B,C) | BC | none | Yes |
| R3(C,D,E) | C | C → D, E | Yes |
| R4(E,F) | E | E → F | Yes |
| R5(F,A) | F | F → A | Yes |

**Lossless:** joins are on C (key of R3), E (key of R4), F (key of R5), and C (key of R1), so no spurious tuples.
**Dependency preservation:** `C→D,E`, `E→F`, `F→A` are preserved; `AB→C` is lost. For a dependency-preserving design, use the 3NF decomposition `ABC, CDE, EF, FA` instead.

---

## Problem 4 — R(A,B,C,D,E,P,G), AB→CD, DE→P, C→E, P→C, B→G **[10]**

**Asked in:** IT-R26 Q2(A)(II); CO-R26 Q6(i) (same FD set).

**Question (verbatim):**
> Let R (A, B, C, D, E, P, G) be a relational schema in which the following functional dependencies hold:
> AB→CD, DE→P, C→E, P→C and B→G.
> Perform the following.
> 1. Write the general definitions of 2NF, 3NF and BCNF
> 2. Find the candidate keys
> 3. Find the Highest normal form followed by the relation

**Given**

```text
R(A, B, C, D, E, P, G)
AB → C, D
DE → P
C → E
P → C
B → G
```

**Step 1 — Candidate key**

- `(AB)+ = A, B, C, D, E, P, G` → all attributes.
- `A+ = A`; `B+ = B, G`; `C+ = C, E`; `D+ = D`; `E+ = E`; `P+ = P, C, E`; `G+ = G`.
- No proper subset of AB determines all attributes, so **AB is the only candidate key**.

Prime: **A, B**. Non-prime: **C, D, E, P, G**.

**Step 2 — Highest normal form**

- 1NF: yes.
- 2NF: `B → G` is a **partial dependency** (B is a proper subset of AB, G is non-prime). Therefore **not 2NF**.
- Highest normal form: **1NF**.

**Step 3 — 2NF decomposition**

Remove `B → G`:

```text
R1(B, G)                  -- B → G
R2(A, B, C, D, E, P)      -- AB → CD, DE → P, C → E, P → C
```

R2 is now 2NF: its key is AB and there are no partial dependencies (C→E, P→C, DE→P are transitive, not partial).

**Step 4 — 3NF/BCNF decomposition**

Use the synthesis algorithm on the minimal cover:

```text
R1(B, G)          -- B → G
R2(A, B, C, D)    -- AB → C, D
R3(D, E, P)       -- DE → P
R4(C, E)          -- C → E
R5(P, C)          -- P → C
```

All five relations are in **BCNF**:
- R2 key AB; R3 key DE; R4 key C; R5 key P; R1 key B.
- Lossless: R2 contains the candidate key AB; the remaining joins use determinants that are keys.
- Dependency-preserving: every FD is represented in one relation.

> **Exam tip:** If the question only asks "highest normal form", write: candidate key = AB, `B → G` is partial, so highest NF = **1NF**. Add the decomposition for full marks.

---

## Problem 5 — Book and Collection (highest normal form) **[10]**

**Asked in:** CO-R25 Q4(A)(i); IT-F25 Q2(A)(II).

**Question (verbatim — CO-R25 wording):**
> Determine the highest normal forms of the following schemas.
> Book (Title, Author, Catalog_no, Publisher, Year, Price)
> Collection (Title, Author, Catalog_no)
> with the following functional dependencies:
> I. Title, Author → Catalog_no
> II. Catalog_no → Title, Author, Publisher, Year
> III. Publisher, Title, Year → Price
> Assume {Author, Title} is the key for both schemas.

**Also asked as (IT-F25):**
> What is normalization? Write the general definitions of 2NF, 3NF. Find the highest normal forms of the following relations: Book and Collection.

**Given**

```text
Book(Title, Author, Catalog_no, Publisher, Year, Price)
Collection(Title, Author, Catalog_no)

FDs:
I.  Title, Author → Catalog_no
II. Catalog_no → Title, Author, Publisher, Year
III. Publisher, Title, Year → Price

Assume {Author, Title} is the key for both schemas.
```

### Collection(Title, Author, Catalog_no)

- `(Title, Author)+ = Title, Author, Catalog_no` → key.
- `Catalog_no+ = Catalog_no, Title, Author` → `Catalog_no` is also a candidate key.
- Every attribute (Title, Author, Catalog_no) is **prime**.
- Both FDs have a candidate key as determinant → **BCNF**.

> **Note on the official key:** It marks Collection as 3NF. Technically it is BCNF because `Catalog_no → Title, Author` makes `Catalog_no` a candidate key. Writing "3NF (in fact BCNF)" is safe in the exam.

### Book(Title, Author, Catalog_no, Publisher, Year, Price)

Candidate keys:
- `(Title, Author)+ = all` → key.
- `Catalog_no → Title, Author, Publisher, Year`. Because `Catalog_no` determines `Publisher`, `Title` and `Year`, the FD `Publisher, Title, Year → Price` gives `Catalog_no → Price` transitively. Therefore `Catalog_no+ = all` and **Catalog_no is a candidate key**. Candidate keys: **{Title, Author}** and **{Catalog_no}**.

Prime: Title, Author, Catalog_no. Non-prime: Publisher, Year, Price.

- 1NF: yes.
- 2NF: For key {Title, Author}, no partial dependency (no FD from Title alone or Author alone). For key Catalog_no (single), no partial dependency. → **2NF**.
- 3NF: FD III `Publisher, Title, Year → Price`: determinant is not a superkey (it does not determine Author/Catalog_no), and Price is non-prime → violates 3NF.
- Highest normal form: **2NF**.

**BCNF/3NF decomposition**

```text
R1(Title, Author, Catalog_no)          -- Title, Author → Catalog_no and Catalog_no → Title, Author
R2(Catalog_no, Publisher, Year)        -- Catalog_no → Publisher, Year
R3(Publisher, Title, Year, Price)      -- Publisher, Title, Year → Price
```

Verify:
- R1: keys {Title, Author} and {Catalog_no}; both FDs have candidate-key determinants → BCNF.
- R2: key Catalog_no → BCNF.
- R3: key {Publisher, Title, Year} → BCNF.

Lossless: R1 ⋈ R2 on Catalog_no (key of R2), then ⋈ R3 on {Publisher, Title, Year} (key of R3).
Dependency-preserving: all three FDs are represented.

---

## Problem 6 — Student table to 3NF **[10]**

**Asked in:** DS-F24 Q3(B)(1).

**Question (verbatim):**
> Given the table:
> Student_ID | Course_ID | Instructor_ID | Instructor_Name | Course_Name
> With the following functional dependencies:
> · Student_ID, Course_ID → Instructor_ID, Course_Name
> · Instructor_ID → Instructor_Name
> Normalize the table to 3NF. Analyse the concept of functional dependency and its role in database normalization.

**Given**

```text
Student(Student_ID, Course_ID, Instructor_ID, Instructor_Name, Course_Name)

FDs:
Student_ID, Course_ID → Instructor_ID, Course_Name
Instructor_ID → Instructor_Name
```

**Step 1 — Candidate key**

`(Student_ID, Course_ID)+ = all` → key is **{Student_ID, Course_ID}**.
Prime: Student_ID, Course_ID. Non-prime: Instructor_ID, Instructor_Name, Course_Name.

**Step 2 — Highest normal form**

- 1NF: yes.
- 2NF: no FD from Student_ID alone or Course_ID alone → **2NF**.
- 3NF: `Instructor_ID → Instructor_Name`; Instructor_ID is non-prime and Instructor_Name is non-prime → **transitive dependency**, violates 3NF.
- Highest: **2NF**.

**Step 3 — 3NF decomposition**

```text
R1(Student_ID, Course_ID, Instructor_ID, Course_Name)   -- key {Student_ID, Course_ID}
R2(Instructor_ID, Instructor_Name)                       -- key Instructor_ID
```

Both are BCNF. Lossless (common Instructor_ID is the key of R2), dependency-preserving.

---

## Problem 7 — Book table to 2NF **[10]**

**Asked in:** DS-F24 Q3(B)(2).

**Question (verbatim):**
> Define partial dependency and discuss why removing it is essential for achieving Second Normal Form (2NF). For the table below, identify partial dependencies and normalize it to 2NF:
> Book_ID | Title | Publisher | Author | Author_Age
> Functional dependencies:
> · Book_ID → Title, Publisher
> · Author → Author_Age

**Given**

```text
Book(Book_ID, Title, Publisher, Author, Author_Age)

FDs:
Book_ID → Title, Publisher
Author → Author_Age
```

**Assumption:** A book can have multiple authors, so the candidate key is **{Book_ID, Author}**. (If each book has exactly one author, Book_ID alone is the key; state your assumption.)

**Step 1 — Highest normal form**

- 1NF: yes.
- 2NF:
  - `Book_ID → Title, Publisher` is a **partial dependency** (Book_ID is part of the key).
  - `Author → Author_Age` is a **partial dependency** (Author is part of the key).
- Highest: **1NF**.

**Step 2 — 2NF decomposition**

```text
Book(Book_ID, Title, Publisher)
Author(Author, Author_Age)
Book_Author(Book_ID, Author)
```

- Book key Book_ID; Author key Author; Book_Author key {Book_ID, Author}.
- All three are BCNF; lossless and dependency-preserving.

---

## Problem 8 — Employee table to 3NF **[10]**

**Asked in:** DS-R25 Q3(B)(1).

**Question (verbatim):**
> Analyze the term transitive dependency and explain how it impacts database normalization. Normalize the following table to Third Normal Form (3NF):
> Employee_ID | Department_ID | Department_Name | Manager_ID | Manager_Name
> Assume the following functional dependencies are given:
> 1. Employee_ID → Department_ID, Manager_ID
> 2. Department_ID → Department_Name, Manager_ID
> 3. Manager_ID → Manager_Name

**Given**

```text
Employee(Employee_ID, Department_ID, Department_Name, Manager_ID, Manager_Name)

FDs:
1. Employee_ID → Department_ID, Manager_ID
2. Department_ID → Department_Name, Manager_ID
3. Manager_ID → Manager_Name
```

**Step 1 — Candidate key**

`Employee_ID+ = Employee_ID, Department_ID, Manager_ID, Department_Name, Manager_Name` = all.
`Department_ID+` does not include Employee_ID; `Manager_ID+` does not include Employee_ID. So **Employee_ID** is the only candidate key.
Prime: Employee_ID. Non-prime: Department_ID, Department_Name, Manager_ID, Manager_Name.

**Step 2 — Highest normal form**

- 1NF: yes.
- 2NF: single-attribute key, so no partial dependency → **2NF**.
- 3NF:
  - `Department_ID → Department_Name, Manager_ID`: Department_ID is non-prime → transitive dependency.
  - `Manager_ID → Manager_Name`: Manager_ID is non-prime → transitive dependency.
- Highest: **2NF**.

**Step 3 — 3NF decomposition**

```text
Employee(Employee_ID, Department_ID)
Department(Department_ID, Department_Name, Manager_ID)
Manager(Manager_ID, Manager_Name)
```

- Employee key Employee_ID; Department key Department_ID; Manager key Manager_ID.
- `Employee_ID → Manager_ID` is implied through `Department_ID`.
- All BCNF; lossless (Department_ID is a key of Department, Manager_ID is a key of Manager); dependency-preserving.

---

## Problem 9 — Student/Course table to 2NF **[10]**

**Asked in:** DS-R25 Q3(B)(2).

**Question (verbatim):**
> Describe the Second Normal Form (2NF) and analyze how it differs from 1NF in terms of dependency elimination. For the table below normalize it to 2NF:
> Student_ID | Course | Instructor | Instructor_Office | Course_Hours
> Functional dependencies:
> · Student_ID, Course → Instructor, Instructor_Office, Course_Hours
> · Course → Instructor, Instructor_Office, Course_Hours

**Given**

```text
Student(Student_ID, Course, Instructor, Instructor_Office, Course_Hours)

FDs:
Student_ID, Course → Instructor, Instructor_Office, Course_Hours
Course → Instructor, Instructor_Office, Course_Hours
```

**Step 1 — Candidate key**

`(Student_ID, Course)+ = all` → key = **{Student_ID, Course}**.
Prime: Student_ID, Course. Non-prime: Instructor, Instructor_Office, Course_Hours.

**Step 2 — Highest normal form**

`Course → Instructor, Instructor_Office, Course_Hours` is a **partial dependency** (Course is part of the key). Therefore **not 2NF**; highest = **1NF**.

**Step 3 — 2NF decomposition**

```text
Student_Course(Student_ID, Course)
Course_Info(Course, Instructor, Instructor_Office, Course_Hours)
```

- Student_Course key {Student_ID, Course}; Course_Info key Course.
- Both BCNF; lossless (Course is the key of Course_Info); dependency-preserving.

---

## Problem 10 — R(X,Y,Z,W,P) to BCNF **[10]**

**Asked in:** IC-R26 Q4(b)(ii).

**Question (verbatim):**
> Consider the relation R(X, Y, Z, W, P) with the following functional dependencies:
> X → Y
> Y → Z
> {X, P} → W
> (a) Find all candidate keys of R. (2)
> (b) Normalize the relation into 1NF, 2NF, 3NF, and BCNF, showing each step of decomposition.

**Given**

```text
R(X, Y, Z, W, P)
X → Y
Y → Z
{X, P} → W
```

**Step 1 — Candidate key**

- `(X, P)+ = X, P, Y, Z, W` = all → **XP is a candidate key**.
- `X+ = X, Y, Z` (not W, P); `P+ = P`; `Y+ = Y, Z`.
- XP is the only candidate key. Prime: X, P. Non-prime: Y, Z, W.

**Step 2 — Highest normal form**

`X → Y` is a **partial dependency** (X is part of XP, Y is non-prime) → **not 2NF**. Highest = **1NF**.

**Step 3 — Normalize step by step**

2NF:
```text
R1(X, Y, Z)      -- X → Y, Y → Z
R2(X, P, W)      -- XP → W
```

3NF/BCNF (remove `Y → Z` from R1):
```text
R1a(X, Y)        -- X → Y
R1b(Y, Z)        -- Y → Z
R2(X, P, W)      -- XP → W
```

- Keys: X, Y, XP respectively.
- All BCNF; lossless (X is a key of R1a, Y is a key of R1b); dependency-preserving.

---

## Problem 11 — R(A,B,C,D,E), CE→D, D→B, C→A **[10]**

**Asked in:** AI-R26 Q3(A)(i).

**Question (verbatim):**
> Consider relation: R = (A, B, C, D, E) with the set of functional dependencies:
> CE → D, D → B, C → A
> Find all candidate keys.
> If the relation is not in 3NF, decompose it until it becomes 3NF.

**Given**

```text
R(A, B, C, D, E)
CE → D
D → B
C → A
```

**Step 1 — Candidate key**

- `(CE)+ = C, E, D, B, A` = all → **CE is a candidate key**.
- `C+ = C, A` (not B, D, E); `E+ = E`; `D+ = D, B`.
- CE is the only candidate key. Prime: C, E. Non-prime: A, B, D.

**Step 2 — Highest normal form**

`C → A` is a **partial dependency** (C is part of CE, A is non-prime) → **not 2NF**. Highest = **1NF**.

**Step 3 — 3NF/BCNF decomposition**

```text
R1(C, E, D)      -- CE → D
R2(C, A)         -- C → A
R3(D, B)         -- D → B
```

- Keys: CE, C, D respectively.
- All BCNF; lossless (C is a key of R2, D is a key of R3); dependency-preserving.

---

# 4.5 Summary table — all FD sets in past papers

| # | Relation and FDs | Candidate key(s) | Highest NF | 3NF/BCNF decomposition |
|---|---|---|---|---|
| 1 | Big Patient: PatNo→PatAge,PatCity; ProvNo→ProvSpecialty; VisitNo→PatNo,VisitDate,PatAge,PatCity; VisitNo,ProvNo→Diagnosis | (VisitNo, ProvNo) | 1NF (partials; 3NF after decomposition) | Patient, Visit, Provider, VisitDiagnosis |
| 2 | R(ABCDEF): AB→C, C→DE, E→F, F→A | AB, BC | 1NF (C→D,E partial on BC) | 3NF: ABC, CDE, EF, FA; BCNF: AC, BC, CDE, EF, FA |
| 3 | R(ABCDEF): AB→C, C→D, C→E, E→F, F→A | AB, BC | 1NF (C→D,E partial on BC) | BCNF: AC, BC, CDE, EF, FA |
| 4 | R(ABCDEFG): AB→CD, DE→P, C→E, P→C, B→G | AB | 1NF (B→G partial) | BG, ABCD, DEP, CE, PC |
| 5 | Book: Title,Author→Catalog_no; Catalog_no→Title,Author,Publisher,Year; Publisher,Title,Year→Price | {Title,Author}, {Catalog_no} | 2NF (FD III) | TitleAuthorCatalog, CatalogPublisherYear, PublisherTitleYearPrice |
| 6 | Collection: Title,Author→Catalog_no; Catalog_no→Title,Author | {Title,Author}, {Catalog_no} | BCNF (official: 3NF) | Already BCNF |
| 7 | Student: Student_ID,Course_ID→Instructor_ID,Course_Name; Instructor_ID→Instructor_Name | (Student_ID, Course_ID) | 2NF (transitive Instructor_ID) | StudentCourseInstructor, Instructor |
| 8 | Book: Book_ID→Title,Publisher; Author→Author_Age | (Book_ID, Author) | 1NF (partials) | Book, Author, Book_Author |
| 9 | Employee: Employee_ID→Department_ID,Manager_ID; Department_ID→Department_Name,Manager_ID; Manager_ID→Manager_Name | Employee_ID | 2NF (transitive Dept/Mgr) | Employee, Department, Manager |
| 10 | Student: Student_ID,Course→Instructor,Instructor_Office,Course_Hours; Course→Instructor,Instructor_Office,Course_Hours | (Student_ID, Course) | 1NF (Course partial) | Student_Course, Course_Info |
| 11 | R(X,Y,Z,W,P): X→Y, Y→Z, XP→W | XP | 1NF (X→Y partial) | XY, YZ, XPW |
| 12 | R(A,B,C,D,E): CE→D, D→B, C→A | CE | 1NF (C→A partial) | CED, CA, DB |

---

## Unit 4 — quick revision checklist

- [ ] Anomalies: insertion, update, deletion; redundancy
- [ ] FD definition; trivial/non-trivial/full/partial/transitive
- [ ] 1NF, 2NF, 3NF, BCNF definitions in words
- [ ] How to compute a candidate key using closures
- [ ] Prime vs non-prime attributes
- [ ] Partial dependency → 2NF; transitive dependency → 3NF
- [ ] BCNF condition: every determinant is a superkey
- [ ] Solve the Big Patient Table, R(ABCDEF), R(ABCDEFG), Book/Collection
- [ ] Solve the Student/Employee/Book 2NF/3NF problems
- [ ] Lossless join and dependency preservation (conceptual)
- [ ] 3NF vs BCNF trade-off
