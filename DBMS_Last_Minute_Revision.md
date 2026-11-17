# DBMS Last-Minute Revision — DJS23IPC202

**One-file cheat sheet: formulas, methods, syntax, question types.**
Covers Units 1–6. Theory is compressed; use the unit files for full explanations.

> **Exam:** 60 marks, 2 hours. Papers usually have 4–5 questions with internal `OR` choices. Marks are roughly: Unit 5 (15), Unit 4 or 2 (15), Unit 2 + 3 (15), Unit 5/6 + 3 (15). Always read both sides of every `OR` before choosing.

---

# 0. Question-type → answer template

| If the question says… | Write this |
|---|---|
| **Define / differentiate** (5) | 1-line definition + 4–5 differences in a table + 1 example |
| **Explain with example** (5/10) | Definition → diagram/table → step-by-step example → 1-line conclusion |
| **Draw ER/EER** (10) | Entities + attributes (underline PK) → relationships → cardinalities/participation → diagram → 2-line justification |
| **Convert ER/EER to relational** (10) | State mapping rule used for each relation → schema with PK/FK → 1-line justification |
| **Normalize** (10) | Candidate key → highest NF + violating FD → step-by-step decomposition → lossless/dependency-preserving check |
| **Schedule** (10) | Conflict list → precedence graph → cycle? → serial schedule → view check |
| **SQL queries** (10) | One query per sub-question; add 1-line comment; use consistent table aliases |
| **B-tree/B+ tree insertion** (10) | Table of each insertion → show splits → final tree diagram → state order convention |
| **Compare** (5) | Table with 5–6 rows (definition, structure, search, range, use) |
| **Architecture/diagram** (5) | Labelled block diagram + 1 line per component |

**Time plan:** 5 min read/choose → 20–25 min per 15-mark question → 10 min final check. Draw diagrams first; they carry marks even if text is short.

---

# 1. Introduction — rapid points

## 1.1 Definitions

| Term | One-line answer |
|---|---|
| **DBMS** | Software to store, manage, retrieve and control access to a database |
| **Database** | Organized collection of related data |
| **Schema** | Logical design (tables, attributes, keys, constraints); changes rarely |
| **Instance** | Actual data at a particular moment; changes frequently |
| **Data independence** | Change schema at one level without changing the next higher level |
| **DBA** | Person/team managing design, security, backup, performance, users |

**Characteristics of a database system (write any 5):** data abstraction/multiple views, reduced redundancy, data independence, concurrency control, ACID transactions, integrity constraints, security/authorization, backup/recovery, efficient access, centralized control.

## 1.2 File system vs DBMS — 6 quick differences

| File system | DBMS |
|---|---|
| Data duplicated in separate files | Central shared database |
| High redundancy/inconsistency | Controlled redundancy, constraints |
| No concurrency control | Locks/timestamps |
| Application-level security | GRANT/REVOKE, roles, views |
| Manual recovery | Log-based recovery, checkpoints |
| Program knows file layout | Logical/physical data independence |

## 1.3 Data independence

- **Logical:** change conceptual schema (add column, split table, add relationship) → external views/apps unchanged.
- **Physical:** change internal schema (index, file organization, disk) → conceptual schema unchanged.

## 1.4 Three-level architecture

```text
External level (views)  →  Conceptual level (tables/constraints)  →  Internal level (files/indexes)
        ↑ external/conceptual mapping                 ↑ conceptual/internal mapping
```

**Storage manager components:** file manager, buffer manager, disk manager, authorization/integrity manager, transaction manager, recovery manager, index manager.

## 1.5 Database users

DBA, database designers, application programmers, end users (casual / naive-parametric / sophisticated), system analysts.

## 1.6 DBA responsibilities (write any 5)

Schema design, storage/access strategy, security/authorization, integrity constraints, backup/recovery, performance tuning, concurrency control, user support, capacity planning.

---

# 2. ER / EER + relational model

## 2.1 ER notation

| Symbol | Meaning |
|---|---|
| Rectangle | Entity |
| Double rectangle | Weak entity |
| Ellipse | Attribute |
| Underlined attribute | Primary key |
| Dashed ellipse | Derived attribute |
| Double ellipse | Multivalued attribute |
| Diamond | Relationship |
| Double diamond | Identifying relationship |
| `1`, `N`, `M` on edges | Cardinality |
| Single line | Partial participation |
| Double line | Total participation |

**Attribute types:** simple, composite, single-valued, multivalued, derived, key. **Weak entity:** no key of its own; has a partial key and an identifying relationship with an owner entity.

## 2.2 Cardinality + participation — write both

- **1:1** — one entity relates to at most one; e.g., Employee–Wife.
- **1:N** — one side relates to many; e.g., Department–Employee.
- **M:N** — many on both sides; e.g., Student–Course.
- **Total participation:** every entity must participate (double line).
- **Partial participation:** some may not (single line).

## 2.3 EER concepts

| Concept | Direction | Example |
|---|---|---|
| **Specialization** | Top-down: superclass → subclasses | PERSON → STUDENT, FACULTY |
| **Generalization** | Bottom-up: subclasses → superclass | CAR, TRUCK → VEHICLE |
| **Aggregation** | Treat relationship as an entity | EMPLOYEE works_on PROJECT; MANAGER monitors the works_on assignment |

**Subclass constraints**

- **Total/partial:** all or some superclass entities belong to a subclass.
- **Disjoint (`d`):** at most one subclass (SAVINGS vs CURRENT).
- **Overlapping (`o`):** can belong to multiple subclasses (STUDENT + EMPLOYEE).
- **Hierarchy:** one superclass; **lattice:** multiple inheritance.
- **Union type:** subclass whose members come from a union of superclasses (OWNER = PERSON ∪ COMPANY).

## 2.4 Keys — formula-style

| Key | Rule | Example |
|---|---|---|
| **Super key** | Any attribute set that uniquely identifies a tuple | {StudentID}, {StudentID, Name} |
| **Candidate key** | Minimal super key | {StudentID}, {Email} |
| **Primary key** | Chosen candidate key | StudentID |
| **Alternate key** | Candidate key not chosen | Email |
| **Composite key** | Key with ≥2 attributes | (StudentID, CourseID) |
| **Foreign key** | References another relation's PK | DeptID → DEPARTMENT |
| **Surrogate key** | System-generated ID | AUTO_INCREMENT |
| **Partial key** | Distinguishes weak entities under owner | DependentName |

## 2.5 ER/EER → relational mapping — 10 rules

1. **Strong entity → relation.** Simple attributes; flatten composites; choose a candidate key as PK.
2. **Weak entity → relation.** Owner PK as FK; PK = (owner PK + partial key).
3. **1:1 →** FK on the total-participation side (add `UNIQUE`), or merge relations.
4. **1:N →** PK of the “1” side as FK on the “N” side; relationship attributes go on the N side.
5. **M:N →** new relation with both PKs as FKs; composite PK; add relationship attributes.
6. **Multivalued attribute →** new relation (owner PK, attribute); composite PK.
7. **Ternary relationship →** new relation with all participating PKs.
8. **Specialization/generalization →**
   - Single table: all attributes + type discriminator (NULLs for inapplicable).
   - Table per subclass: superclass + each subclass with superclass PK as FK (best for total).
   - Table per hierarchy: each subclass gets all inherited + specific attributes.
   - Overlapping → separate subclass tables; partial → some superclass rows have no subclass row.
9. **Aggregation →** map the aggregated relationship as a relation, then link it to the higher-level entity.
10. **Derived attribute →** omit (compute when needed).

**Lossless check for a decomposition:** `R1 ∩ R2` must be a key of `R1` or `R2` (or use the chase test).

## 2.6 Relational algebra — syntax

| Operator | Symbol | Meaning | Example |
|---|---|---|---|
| Selection | `σ` | Filter rows | `σ_salary > 50000(Employee)` |
| Projection | `π` | Select columns | `π_name, salary(Employee)` |
| Rename | `ρ` | Rename relation/attribute | `ρ_E(Employee)` |
| Union | `∪` | Rows in either (union-compatible) | `A ∪ B` |
| Difference | `−` | Rows in first but not second | `A − B` |
| Cartesian product | `×` | All combinations | `A × B` |
| Natural join | `⋈` | Join on same-named attributes | `Student ⋈ Department` |
| Theta join | `⋈_θ` | Join with condition | `A ⋈_{A.id=B.id} B` |
| Division | `÷` | “For all” queries | `R ÷ S` |
| Aggregation | `γ` | Group + aggregate | `γ_dept, AVG(salary)(Employee)` |

**Query templates**

```text
Names of all red parts:                 π_pName(σ_colour='red'(Parts))
Prices of red or green parts:           π_price(σ_colour='red' ∨ colour='green'(Parts) ⋈ Catalog)
Suppliers of red/green parts:           π_sName(Suppliers ⋈ Catalog ⋈ σ_colour='red' ∨ colour='green'(Parts))
Customers with ≥1 transaction:          π_cname(customer ⋈ account ⋈ transaction)
Customers with no transaction:          π_cname(customer) − π_cname(customer ⋈ account ⋈ transaction)
Patients with appointment but no med:   π_pname(patient ⋈ appointment) − π_pname(patient ⋈ appointment ⋈ prescription)
```

**“For all” pattern:** `R ÷ S` or `π_x(R) − π_x((π_x(R) × S) − R)`.

## 2.7 ER diagram checklist (before you draw)

- [ ] All entities, weak entities marked
- [ ] PK underlined; partial key dashed-underlined
- [ ] Attributes on correct entity/relationship
- [ ] Cardinality `1/N/M` on every edge
- [ ] Total participation double lines where required
- [ ] Specialization/generalization with `d/o` and total/partial
- [ ] Relationship attributes (date, quantity, hours, price)
- [ ] No many-to-many drawn as a single FK

---

# 3. SQL — syntax + patterns

## 3.1 Command categories

| Category | Commands |
|---|---|
| **DDL** | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` |
| **DML** | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `MERGE` |
| **DCL** | `GRANT`, `REVOKE` |
| **TCL** | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

## 3.2 DDL syntax

```sql
CREATE TABLE Department (
    DeptID INT PRIMARY KEY,
    DeptName VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE Employee (
    EmpID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Email VARCHAR(80) UNIQUE,
    Salary DECIMAL(10,2) CHECK (Salary > 0),
    DeptID INT,
    JoinDate DATE DEFAULT CURRENT_DATE,
    FOREIGN KEY (DeptID) REFERENCES Department(DeptID)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

ALTER TABLE Employee ADD CONSTRAINT fk_dept
    FOREIGN KEY (DeptID) REFERENCES Department(DeptID);

ALTER TABLE Accounts ADD CONSTRAINT pk_accounts PRIMARY KEY (account_id);

DROP TABLE Employee;
TRUNCATE TABLE Employee;
```

**Constraint types:** PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK, DEFAULT, domain (data type/range).

## 3.3 DML syntax

```sql
INSERT INTO Employee (EmpID, Name, Salary, DeptID)
VALUES (101, 'Aarav', 60000, 10);

UPDATE Employee
SET Salary = Salary * 1.10
WHERE DeptID = 6;

DELETE FROM Employee WHERE EmpID = 104;
```

## 3.4 SELECT — clause order vs execution order

**Write order:**

```sql
SELECT [DISTINCT] columns / aggregates
FROM table
JOIN table2 ON condition
WHERE row_condition
GROUP BY columns
HAVING group_condition
ORDER BY columns [ASC|DESC]
LIMIT n;
```

**Logical execution order:** `FROM/JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT`.

> Consequence: you cannot use a `SELECT` alias in `WHERE` (usually), but you can in `ORDER BY`; `HAVING` filters groups, `WHERE` filters rows.

## 3.5 Joins

```sql
-- INNER: only matches
SELECT s.Name, d.DeptName
FROM Student s
INNER JOIN Department d ON s.DeptID = d.DeptID;

-- LEFT: all left rows + matches
SELECT s.Name, d.DeptName
FROM Student s LEFT JOIN Department d ON s.DeptID = d.DeptID;

-- RIGHT: all right rows + matches
SELECT s.Name, d.DeptName
FROM Student s RIGHT JOIN Department d ON s.DeptID = d.DeptID;

-- FULL: all rows from both
SELECT s.Name, d.DeptName
FROM Student s FULL OUTER JOIN Department d ON s.DeptID = d.DeptID;

-- NATURAL: join on all same-named columns, duplicate columns removed
SELECT Name, DeptName FROM Student NATURAL JOIN Department;

-- CROSS: Cartesian product
SELECT * FROM Student CROSS JOIN Department;
```

## 3.6 Aggregates + GROUP BY/HAVING

```sql
SELECT DeptID,
       COUNT(*) AS Employees,
       SUM(Salary) AS TotalSalary,
       AVG(Salary) AS AvgSalary,
       MIN(Salary) AS MinSalary,
       MAX(Salary) AS MaxSalary
FROM Employee
GROUP BY DeptID
HAVING AVG(Salary) > 50000;
```

- `COUNT(*)` counts rows; `COUNT(col)` ignores NULLs.
- Every non-aggregated `SELECT` column must appear in `GROUP BY`.
- `WHERE` before grouping; `HAVING` after.

## 3.7 Subqueries

```sql
-- Scalar subquery
SELECT * FROM Employee
WHERE Salary > (SELECT AVG(Salary) FROM Employee);

-- IN / NOT IN
SELECT * FROM Employee
WHERE DeptID IN (SELECT DeptID FROM Department WHERE DeptName = 'HR');

-- EXISTS / NOT EXISTS
SELECT * FROM Department d
WHERE EXISTS (SELECT 1 FROM Employee e WHERE e.DeptID = d.DeptID);

SELECT * FROM Department d
WHERE NOT EXISTS (SELECT 1 FROM Employee e WHERE e.DeptID = d.DeptID);

-- Correlated: above-average salary in own department
SELECT * FROM Employee e
WHERE Salary > (SELECT AVG(Salary) FROM Employee e2 WHERE e2.DeptID = e.DeptID);
```

## 3.8 Set operations + strings

```sql
SELECT city FROM Student
UNION          -- duplicates removed
SELECT city FROM Faculty;

SELECT city FROM Student
UNION ALL      -- duplicates kept
SELECT city FROM Faculty;

SELECT city FROM Student
INTERSECT
SELECT city FROM Faculty;

SELECT city FROM Student
EXCEPT            -- MINUS in Oracle
SELECT city FROM Faculty;
```

| String operation | Example |
|---|---|
| `LIKE` `%` | `WHERE Name LIKE 'A%'` |
| `LIKE` `_` | `WHERE Name LIKE '_a%'` |
| Concatenate | `CONCAT(Fname,' ',Lname)` or `Fname || ' ' || Lname` |
| Substring | `SUBSTR(Name,1,3)` |
| Length | `LENGTH(Name)` |
| Case | `UPPER(Name)`, `LOWER(Name)` |
| Trim | `TRIM(Name)` |
| Replace | `REPLACE(Name,'a','A')` |

## 3.9 Views

```sql
CREATE VIEW HighEarningEmployees AS
SELECT EID, Fname, Lname, Salary, DeptID
FROM Employee
WHERE Salary > 100000;

SELECT * FROM HighEarningEmployees WHERE DeptID = 5;

CREATE OR REPLACE VIEW v AS SELECT ...;
DROP VIEW v;
```

**Updatable view:** single table, no `DISTINCT`, `GROUP BY`, `HAVING`, aggregates or set operations, includes PK. Otherwise use `INSTEAD OF` triggers.

## 3.10 Triggers

```sql
CREATE OR REPLACE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON table_name
[FOR EACH ROW]
[WHEN (condition)]
BEGIN
    -- :NEW.col / :OLD.col
END;
/
```

| BEFORE trigger | AFTER trigger |
|---|---|
| Fires before DML | Fires after DML |
| Validate/reject/transform | Audit/log/cascade |
| Can modify `:NEW` | Cannot modify `:NEW` |
| Example: reject salary < 1000 | Example: insert into audit table |

**Backup-before-delete trigger:**

```sql
CREATE OR REPLACE TRIGGER t1
BEFORE DELETE ON main
FOR EACH ROW
BEGIN
    INSERT INTO backup(id, salary)
    VALUES (:OLD.id, :OLD.salary);
END;
/
```

**Salary validation:**

```sql
CREATE OR REPLACE TRIGGER check_salary
BEFORE INSERT OR UPDATE OF Salary ON Employee
FOR EACH ROW
BEGIN
    IF :NEW.Salary < 1000 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary must be at least 1000');
    END IF;
END;
/
```

**Audit:**

```sql
CREATE OR REPLACE TRIGGER log_salary
AFTER UPDATE OF Salary ON Employee
FOR EACH ROW
BEGIN
    INSERT INTO SalaryAudit(EmpID, OldSalary, NewSalary, ChangedOn)
    VALUES (:OLD.EID, :OLD.Salary, :NEW.Salary, SYSDATE);
END;
/
```

- Row-level: `FOR EACH ROW`; statement-level: omit it.
- MySQL uses `NEW.col`/`OLD.col` and `SIGNAL SQLSTATE`.

## 3.11 Procedures (asked in AI-R26)

```sql
CREATE OR REPLACE PROCEDURE update_customer_city(p_city IN VARCHAR2) AS
BEGIN
    UPDATE CUSTOMER SET City = p_city WHERE CustomerID = 2;
    COMMIT;
END;
/

EXEC update_customer_city('Mumbai');
```

## 3.12 Authorization / DCL

```sql
GRANT SELECT, UPDATE ON Employee TO A, B;
GRANT UPDATE (amount) ON loan TO U4;
GRANT UPDATE ON Branch TO U5 WITH GRANT OPTION;
REVOKE SELECT ON Employee FROM U3;
REVOKE UPDATE ON Branch FROM U5 CASCADE;

CREATE ROLE manager;
GRANT SELECT, UPDATE ON Employee TO manager;
GRANT manager TO user1, user2;
```

- `WITH GRANT OPTION` lets the grantee pass the privilege on.
- `CASCADE` on revoke also removes privileges granted onward.
- **Security vs authorization:** security protects data; authorization grants specific privileges.

## 3.13 TCL

```sql
BEGIN TRANSACTION;
UPDATE Accounts SET balance = balance - 100 WHERE account_id = 101;
SAVEPOINT after_debit;
UPDATE Accounts SET balance = balance + 100 WHERE account_id = 102;
ROLLBACK TO after_debit;
COMMIT;
```

- `COMMIT` — make permanent; `ROLLBACK` — undo; `SAVEPOINT` — partial rollback point.

## 3.14 SQL query patterns (very likely)

| Ask | Pattern |
|---|---|
| **Nth highest salary** | `SELECT DISTINCT Salary FROM Employee e1 WHERE N-1 = (SELECT COUNT(DISTINCT Salary) FROM Employee e2 WHERE e2.Salary > e1.Salary);` |
| **Duplicates** | `SELECT col, COUNT(*) FROM T GROUP BY col HAVING COUNT(*) > 1;` |
| **Rows with no match** | `LEFT JOIN ... WHERE right.pk IS NULL` or `NOT EXISTS` |
| **Second highest** | `SELECT MAX(Salary) FROM Employee WHERE Salary < (SELECT MAX(Salary) FROM Employee);` |
| **Above department average** | correlated subquery with `e2.DeptID = e.DeptID` |
| **Count per group** | `GROUP BY` + `COUNT(*)` |
| **Top N per group** | window function `ROW_NUMBER() OVER (PARTITION BY ... ORDER BY ...)` (if allowed) |
| **Create table + FK** | `CREATE TABLE` with `PRIMARY KEY`, `FOREIGN KEY ... REFERENCES` |
| **Add PK/FK later** | `ALTER TABLE ... ADD CONSTRAINT ...` |
| **Delete all rows** | `DELETE FROM T;` (rollback-able) vs `TRUNCATE TABLE T;` (faster, DDL) |

## 3.15 SQL mistakes to avoid

- `WHERE` with aggregate → use `HAVING`.
- `COUNT(col)` ignores NULL; `COUNT(*)` doesn't.
- String dates: use `'YYYY-MM-DD'`.
- `NULL` comparisons need `IS NULL`, not `= NULL`.
- `NOT IN` with NULLs can return no rows; prefer `NOT EXISTS`.
- Join condition missing → Cartesian product.
- `GROUP BY` missing non-aggregated columns.
- Trigger `:OLD` on INSERT / `:NEW` on DELETE is invalid.

---

# 4. Normalization — formulas + algorithms

**Anomalies:** insertion, update, deletion, redundancy. **Pitfalls:** insufficient normalization, over-normalization (too many joins), bad/missing primary key, missing foreign keys, inadequate indexing, excessive NULLs, wrong data types, no scalability plan.

## 4.1 Functional dependency types

| Type | Definition | Example |
|---|---|---|
| **Trivial** | `Y ⊆ X` | `{A,B} → A` |
| **Non-trivial** | `Y ⊄ X` | `A → B` |
| **Completely non-trivial** | `X ∩ Y = ∅` | `StudentID → Name` |
| **Full** | No proper subset of X determines Y | `{StudentID, CourseID} → Grade` |
| **Partial** | A non-prime attribute depends on part of a candidate key | `{StudentID, CourseID} → StudentName` via `StudentID → StudentName` |
| **Transitive** | `X → Y`, `Y → Z`, so `X → Z`; Y not a candidate key | `EmpID → DeptID → DeptName` |
| **Multivalued** | One X is associated with a set of Y values | `Student →→ Course` (4NF) |

## 4.2 Closure `X+` — algorithm

```text
result = X
repeat:
    for each FD A → B:
        if A ⊆ result and B ⊄ result:
            result = result ∪ B
until no change
```

`X` is a **superkey** if `X+` = all attributes. It is a **candidate key** if it is a superkey and minimal.

## 4.3 Candidate key — exam method

1. Put every attribute that **never appears on the RHS** of any FD into the key (it must be there).
2. Compute the closure of that set.
3. If closure ≠ all attributes, add attributes one by one until closure = all.
4. Remove redundant attributes → minimal candidate keys.
5. **Prime** = part of any candidate key; **non-prime** = not part of any.

## 4.4 Normal forms — conditions

| NF | Condition | Violation to look for |
|---|---|---|
| **1NF** | Atomic values, no repeating groups, unique rows | Multivalued/composite column |
| **2NF** | 1NF + no **partial dependency** | Non-prime attribute depends on part of a candidate key |
| **3NF** | 2NF + no **transitive dependency**; for every `X → A`, X is superkey **or** A is prime | Non-prime attribute depends on another non-prime |
| **BCNF** | For every non-trivial `X → Y`, X is a **superkey** | Determinant is not a superkey |

**Memory line:** 2NF kills *partial*, 3NF kills *transitive*, BCNF kills *all non-superkey determinants*.

## 4.5 Decomposition — 3NF synthesis algorithm

1. Compute a **minimal cover** (remove redundant FDs/extraneous attributes).
2. Combine FDs with the same LHS: `X → A1, A2` → relation `(X, A1, A2)`.
3. If no relation contains a candidate key, add a relation for a candidate key.
4. Remove redundant relations (a relation contained in another).
5. Result is lossless and dependency-preserving 3NF.

## 4.6 BCNF decomposition algorithm

```text
while some relation R has a non-trivial FD X → Y with X not a superkey:
    decompose R into R1(X ∪ Y) and R2(R − Y)
    continue on R1, R2
```

- Each step is lossless because `R1 ∩ R2 = X` and `X → Y`.
- BCNF may **not** preserve all FDs; mention this trade-off.

## 4.7 Lossless join + dependency preservation

- **Lossless:** `R1 ∩ R2` is a key of at least one of `R1`, `R2`; or chase test yields a row of all distinguished variables.
- **Dependency-preserving:** every FD can be checked within one decomposed relation. If not, say “not dependency-preserving; BCNF trade-off.”
- **3NF** can always be both lossless and dependency-preserving; **BCNF** may lose dependencies.

## 4.8 Past FD sets — answer table

| Given | Candidate key(s) | Highest NF | Decomposition |
|---|---|---|---|
| Big Patient: `PatNo→PatAge,PatCity`; `ProvNo→ProvSpecialty`; `VisitNo→PatNo,VisitDate,PatAge,PatCity`; `VisitNo,ProvNo→Diagnosis` | `(VisitNo, ProvNo)` | 1NF (partials) | `Patient(PatNo,PatAge,PatCity)`, `Visit(VisitNo,VisitDate,PatNo)`, `Provider(ProvNo,ProvSpecialty)`, `VisitDiagnosis(VisitNo,ProvNo,Diagnosis)` |
| `R(ABCDEF)`: `AB→C`, `C→DE`, `E→F`, `F→A` | `AB`, `BC` | 1NF (`C→D,E` partial on BC) | 3NF: `ABC`, `CDE`, `EF`, `FA`; BCNF: `AC`, `BC`, `CDE`, `EF`, `FA` |
| `R(ABCDEF)`: `AB→C`, `C→D`, `C→E`, `E→F`, `F→A` | `AB`, `BC` | 1NF | BCNF: `AC`, `BC`, `CDE`, `EF`, `FA` |
| `R(ABCDEFG)`: `AB→CD`, `DE→P`, `C→E`, `P→C`, `B→G` | `AB` | 1NF (`B→G` partial) | `BG`, `ABCD`, `DEP`, `CE`, `PC` (all BCNF) |
| Book: `Title,Author→Catalog_no`; `Catalog_no→Title,Author,Publisher,Year`; `Publisher,Title,Year→Price` | `{Title,Author}`, `{Catalog_no}` | 2NF | `(Title,Author,Catalog_no)`, `(Catalog_no,Publisher,Year)`, `(Publisher,Title,Year,Price)` |
| Collection: `Title,Author→Catalog_no`; `Catalog_no→Title,Author` | `{Title,Author}`, `{Catalog_no}` | BCNF (official: 3NF) | already BCNF |
| Student: `Student_ID,Course_ID→Instructor_ID,Course_Name`; `Instructor_ID→Instructor_Name` | `(Student_ID,Course_ID)` | 2NF | `(Student_ID,Course_ID,Instructor_ID,Course_Name)`, `(Instructor_ID,Instructor_Name)` |
| Book: `Book_ID→Title,Publisher`; `Author→Author_Age` | `(Book_ID,Author)` | 1NF | `Book(Book_ID,Title,Publisher)`, `Author(Author,Author_Age)`, `Book_Author(Book_ID,Author)` |
| Employee: `Employee_ID→Department_ID,Manager_ID`; `Department_ID→Department_Name,Manager_ID`; `Manager_ID→Manager_Name` | `Employee_ID` | 2NF | `Employee(Employee_ID,Department_ID)`, `Department(Department_ID,Department_Name,Manager_ID)`, `Manager(Manager_ID,Manager_Name)` |
| Student: `Student_ID,Course→Instructor,Instructor_Office,Course_Hours`; `Course→Instructor,Instructor_Office,Course_Hours` | `(Student_ID,Course)` | 1NF | `Student_Course(Student_ID,Course)`, `Course_Info(Course,Instructor,Instructor_Office,Course_Hours)` |
| `R(X,Y,Z,W,P)`: `X→Y`, `Y→Z`, `XP→W` | `XP` | 1NF | `XY`, `YZ`, `XPW` |
| `R(A,B,C,D,E)`: `CE→D`, `D→B`, `C→A` | `CE` | 1NF | `CED`, `CA`, `DB` |

**Fast check:** if a non-prime attribute depends on part of any candidate key → 1NF. If a non-prime determines a non-prime → 2NF. If a non-superkey determines a prime → 3NF but not BCNF.

---

# 5. Transactions — formulas + methods

**Transaction:** a logical unit of work that reads/updates data and must execute fully or not at all.

## 5.1 ACID

| Property | Meaning | Mechanism |
|---|---|---|
| **Atomicity** | All or nothing | Undo log / rollback |
| **Consistency** | Valid state → valid state | Constraints, triggers |
| **Isolation** | Concurrent = some serial order | 2PL, timestamp, validation |
| **Durability** | Committed survives crash | WAL, forced commit record |

**States:** Active → Partially committed → Committed; Active/Partially committed → Failed → Aborted (→ restart).

## 5.2 Conflict serializability — 4-step method

1. List operations in order.
2. Find conflicts: different transactions, same item, at least one write.
3. Add edge `Ti → Tj` for each conflict where Ti's operation comes first.
4. If graph has a **cycle** → not conflict serializable; else topological sort = equivalent serial schedule.

**Conflict types:** `R–W`, `W–R`, `W–W`.

## 5.3 View serializability — 3 conditions

S and S' are view equivalent if:
1. **Initial reads** are the same.
2. **Final writes** are the same.
3. Each read sees the same write (same read-from relation).

View serializable = view equivalent to some serial schedule. Conflict serializable ⇒ view serializable; converse fails with **blind writes**.

**Blind write:** a write without a preceding read of that item. Blind writes often make a cyclic schedule view serializable.

## 5.4 Past schedules — quick results

| Paper | Schedule | Conflict? | View? | Equivalent serial |
|---|---|---|---|---|
| IT-F24 | `w1(A), r2(A), w1(B), w3(C), r2(C), r4(B), w2(D), w4(E), r5(D), w5(E)` | Yes | Yes | `T1→T3→T4→T2→T5` |
| IT-R25 | `r1(x), r2(z), r1(z), r3(x), r3(y), w1(x), w3(y), r2(y), w2(z), w2(y)` | Yes | Yes | `T3→T1→T2` |
| IT-F25 S1 | `r1(X); r1(Y); r2(X); r2(Y); w2(Y); w1(X)` | No (T1↔T2) | No | — |
| IT-F25 S2 | `r1(X); r2(X); r2(Y); w2(Y); r1(Y); w1(X)` | No (T1↔T2) | Yes (blind write Y) | `T2→T1` |
| IT-R26 | `R1(x), R2(y), R3(x), R1(y), R1(z), W3(y), W2(z), R3(z), W1(x), W3(z)` | No (T1↔T3) | No | — |
| CO-R25 | `R1(a); W2(a); W1(a); W3(a)` | No (T1↔T2) | Yes (blind writes) | `T1→T2→T3` |
| CO-B26 | `R1(A); R2(A); W3(A); W1(A)` | No (T1↔T3) | No | — |

## 5.5 2PL — syntax + rules

**Lock compatibility**

| Requested ↓ / Held → | S | X |
|---|---|---|
| **S** | ✔ | wait |
| **X** | wait | wait |

- **S lock:** read; multiple S allowed.
- **X lock:** write; exclusive.
- Upgrade S→X allowed if no other S holder.

**2PL phases**

1. **Growing:** acquire locks, no releases.
2. **Shrinking:** release locks, no new acquisitions.

**Variants**

| Variant | Rule | Benefit |
|---|---|---|
| **Strict 2PL** | Hold all X locks until commit | No cascading rollback |
| **Rigorous 2PL** | Hold all locks until commit | Strongest |
| **Conservative 2PL** | Acquire all locks before start | Deadlock-free |

**Lock sequence template for T: R(A), W(A), R(B), W(B)**

```text
S(A) → X(A) → S(B) → X(B) → [commit] → unlock B → unlock A
```

**2PL problems:** deadlock, cascading rollback. Strict 2PL solves cascading rollback, not deadlock.

## 5.6 Timestamp ordering — formulas

Let `TS(T)` = timestamp (smaller = older), `RTS(X)` = largest timestamp that read X, `WTS(X)` = largest timestamp that wrote X.

**Read `R(X)` by T**

```text
if TS(T) < WTS(X): abort T        -- would read a younger writer's value
else: RTS(X) = max(RTS(X), TS(T)); allow
```

**Write `W(X)` by T**

```text
if TS(T) < RTS(X): abort T        -- younger reader already read X
elif TS(T) < WTS(X): abort T      -- younger writer already wrote X
else: WTS(X) = TS(T); allow
```

**Thomas write rule:** if `TS(T) < WTS(X)`, **ignore** the write (do not abort), because a younger value already overwrote it.

**Past timestamp answers**

| Paper | Final RTS/WTS | Notes |
|---|---|---|
| CO-R26 T1=100, T2=200, T3=300; `R(A),R(B),W(C),R(B),R(C),W(B),W(A)` | `RTS(A)=100, WTS(A)=300; RTS(B)=300, WTS(B)=0; RTS(C)=100, WTS(C)=100` | T2 aborts at `W(B)` because `200 < RTS(B)=300` |
| IT-R26 T1=1, T2=2; `W1(A), R2(A), R1(A), W2(A)` | `RTS(A)=2, WTS(A)=2` | All allowed; order T1→T2 |
| AI-R26 T1 older, T2 younger; `R1(X), R2(Y), W2(X), W1(X)` | T1 aborts at `W1(X)` | `TS1=1 < WTS(X)=2`; Thomas rule would ignore T1's write |

## 5.7 Validation-based (optimistic)

1. **Read phase:** read data, compute in private workspace.
2. **Validation phase:** check conflicts with committed transactions; abort if conflict.
3. **Write phase:** apply updates only after validation.

No locks, no deadlock; wasted work on abort.

## 5.8 Deadlock

- **Cause:** cycle of transactions waiting for locks (different lock orders, long transactions).
- **Prevention:** conservative 2PL, ordered locking, wait-die, wound-wait, timeouts.
- **Wait-die:** older waits, younger dies.
- **Wound-wait:** older wounds (rolls back) younger, younger waits.
- **Detection:** wait-for graph; cycle → choose victim → rollback.
- **SQL causes:** concurrent access in different orders; locks held for long durations.

## 5.9 Recovery — log records + rules

**Log records**

```text
<Ti, Start>
<Ti, X, Vold, Vnew>     -- immediate modification needs old + new
<Ti, Commit>
<Ti, Abort>
<Checkpoint L>
```

**WAL:** write log record before the data page; force commit record at commit.

| Method | Log contains | Recovery |
|---|---|---|
| **Deferred modification** | New value only; DB writes after commit | **Redo** committed; **ignore** uncommitted |
| **Immediate modification** | Old + new values; DB writes before commit | **Redo** committed; **undo** uncommitted |

**Checkpoint:** flush log/dirty pages; write `<Checkpoint L>`; recovery starts from last checkpoint.

## 5.10 Shadow paging vs ARIES

| Shadow paging | ARIES |
|---|---|
| Two page tables (current + shadow) | WAL + LSN + CLRs |
| No log | Detailed log |
| Commit = switch page table | Analysis → Redo → Undo |
| Simple, high I/O/storage | Complex, high concurrency |
| Good for simple recovery | Industrial-strength recovery |

## 5.11 Failure classification

| Failure | Cause | Recovery |
|---|---|---|
| Transaction failure | Logical error, constraint, deadlock | Rollback transaction |
| System crash | Power/OS/hardware, memory lost | Log redo/undo |
| Disk failure | Head crash, bad sectors | Backup + log replay |
| Communication failure | Network partition | Retry/abort distributed transaction |

## 5.12 ACID vs BASE

| ACID | BASE |
|---|---|
| Strong consistency | Eventual consistency |
| Isolation, transactions | Basically available, soft state |
| Scale-up | Scale-out |
| Banking/OLTP | Social/catalog/DNS |

---

# 6. Indexing — formulas + insertion

## 6.1 Index types

| Type | Key | Entries | Data order |
|---|---|---|---|
| **Primary** | Ordering key of sorted file | Sparse (one per block) | File sorted by key |
| **Secondary** | Non-ordering key | Dense (one per record) | File not sorted by key |
| **Clustered** | Any key; data physically sorted | Leaf contains data | Sorted by clustering key |
| **Non-clustered** | Separate structure | Key + row pointer | Heap order |

- **Dense:** entry for every search-key value.
- **Sparse:** entry for some values (e.g., first per block); requires sorted file.
- **Single-level:** one index file; binary search; grows with file.
- **Multilevel:** index on index; top level cached; B-tree/B+ tree.

## 6.2 B-tree — order formulas

For order `m` (**m = max children**):

```text
max keys per node     = m − 1
min keys per node     = ⌈m/2⌉ − 1   (except root)
max children          = m
min children          = ⌈m/2⌉       (except root)
all leaves            = same level
```

**Insertion algorithm**

1. Search to the correct leaf.
2. Insert key in sorted order.
3. If node has `m` keys (overflow):
   - Left keeps `⌈m/2⌉ − 1` keys.
   - Median goes to parent.
   - Right keeps the rest.
4. If parent overflows, repeat; if root splits, height increases by 1.

**Past B-tree final trees**

| Order | Keys inserted | Final tree |
|---|---|---|
| 3 | 5, 15, 25, 35, 45, 55, 65 | Root `[15,35,55]`; leaves `[5] [25] [45] [65]` |
| 4 | 15, 30, 50, 5, 10, 40, 20 | Root `[15,30]`; leaves `[5,10] [20] [40,50]` |
| 3 | 78, 52, 81, 40, 33, 90, 85, 20, 38 | Root `[78]`; left `[33,40]` (children `[20],[38],[52]`); right `[85]` (children `[81],[90]`) |

## 6.3 B+ tree — order formulas

For order `m` (max `m` children):

```text
max keys per node   = m − 1
min keys per node   = ⌈m/2⌉ − 1   (except root)
max children        = m
min children        = ⌈m/2⌉       (except root)
all keys appear in leaves
internal nodes      = separators only
leaves              = linked list
search I/O          ≈ height + 1
```

**Leaf split:** overflow `m` keys → left `⌈m/2⌉`, right `⌊m/2⌋`; **copy up** the first key of the right leaf.

**Internal split:** promote the middle separator; left/right children split.

**Past B+ tree final trees**

| Order | Keys inserted | Final tree |
|---|---|---|
| 5 | 12, 25, 35, 47, 50 | Root `[47]`; leaves `[12,25,35]` and `[47,50]` |
| 4 | 3, 8, 14, 18, 22 | Root `[14]`; leaves `[3,8]` and `[14,18,22]` |

## 6.4 B+ tree CRUD

- **Search:** root → internal separators → leaf → record pointer; `h+1` block reads.
- **Insert:** find leaf; insert sorted; split if overflow; copy up; propagate; new root if needed.
- **Delete:** remove from leaf; if underflow, borrow/merge; update separator; propagate.
- **Update:** key unchanged → in place; key changed → delete + insert.

## 6.5 B-tree vs B+ tree

| B-tree | B+ tree |
|---|---|
| Data pointers in all nodes | Data pointers only in leaves |
| Search may stop early | Always reaches leaf |
| Range scan slower | Linked leaves → fast |
| Lower fan-out, taller | Higher fan-out, shorter |
| General-purpose | Standard DBMS index |

## 6.6 Hashing vs indexing

| Hashing | Indexing |
|---|---|
| O(1) equality | O(log n) |
| No order/range | Range/order supported |
| Collisions, overflow chains | Node splits |
| Good for exact match | General-purpose |

**Limitations of hashing:** collisions, no range/order, poor hash function → clustering, memory overhead, rehashing cost, bucket overflow, no prefix/partial-key search.

---

# 7. Last-minute “if you see this, do this”

| Question wording | Action |
|---|---|
| “Conflict serializable?” | Build precedence graph; cycle = no; topological order = serial schedule |
| “View serializable?” | Check initial reads, final writes, read-from; blind writes can make cyclic schedule view-serializable |
| “Normalize to 3NF” | Candidate key → 3NF synthesis; combine same LHS; add key relation if needed |
| “Normalize to BCNF” | Find non-superkey FD; split `R` into `X∪Y` and `R−Y`; repeat; note dependency loss |
| “Highest normal form” | Check partial (2NF), transitive (3NF), non-superkey determinant (BCNF) |
| “Convert ER to relational” | Strong/weak, 1:1, 1:N, M:N, multivalued, specialization, aggregation |
| “SQL queries” | Use joins for related tables; `GROUP BY`+`HAVING` for per-group; subquery for “above average”/“not exists” |
| “B-tree of order m” | State convention: m = max children, max m−1 keys; split median up |
| “B+ tree of order m” | Max m−1 keys; split leaf, copy up first right key; leaves linked |
| “Compare X and Y” | 5-row table: definition, structure, search, advantage, use |
| “Explain ACID” | Bank transfer example; one line per property |
| “Log-based recovery” | Draw log records; redo committed, undo uncommitted; immediate vs deferred |
| “2PL problem” | Deadlock + cascading rollback; strict 2PL holds X locks until commit |
| “Deadlock prevention” | Conservative 2PL, ordered locking, wait-die, wound-wait, timeout |

---

# 8. Final checklist before handing over

- [ ] Every ER diagram has PK underlined, cardinalities, participation, relationship attributes.
- [ ] Every normalization answer states candidate key and **why** the highest NF is what you claim.
- [ ] Every decomposition states **lossless** and **dependency-preserving** (or why not).
- [ ] Every schedule answer has a precedence graph and a topological order (if acyclic).
- [ ] Every 2PL answer shows growing/shrinking phases and lock releases.
- [ ] Every timestamp answer shows `RTS`/`WTS` updates and the abort condition.
- [ ] Every SQL query has `FROM`, correct join condition, and `GROUP BY` when aggregates are mixed with columns.
- [ ] Every B-tree/B+ tree answer states the order convention and shows splits.
- [ ] Diagrams are labelled; tables have headers; code is in SQL blocks.
- [ ] Attempt all sub-parts; a formula/steps can earn partial marks even if the final answer is wrong.

**Good luck.**
