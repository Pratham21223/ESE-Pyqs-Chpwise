# Unit 3 — Structured Query Language (SQL)

> **Syllabus (DJS23IPC202, 11 hrs):** Overview of SQL; DDL commands; DML commands; integrity constraints — key constraints, domain constraints, referential integrity, check constraints; DCL commands; TCL commands; set and string operations; aggregate functions — GROUP BY, HAVING; views in SQL; joins; nested and complex queries; triggers; security and authorization in SQL.
>
> **Past-paper coverage:** SQL basics/DDL vs DML, constraints, 16 query sets, joins, views, triggers, GRANT/REVOKE, DCL vs TCL, aggregate functions, set/string operations, nested queries.

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

## 3.1 What is SQL? Differentiate between DDL and DML. **[5]**

**Asked in:** CO-R25 Q2(B); IC-R26 Q2(b)(i) (list/explain DML commands).

**Questions (verbatim):**
> what is SQL? Write a difference between DDL & DML Command.
>
> List and explain Data Manipulation Commands (DML) commands in SQL.

**Answer**

**SQL (Structured Query Language)** is the standard declarative language for defining, manipulating and controlling data in a relational database. It is used by MySQL, Oracle, SQL Server, PostgreSQL, etc.

**SQL command categories**

| Category | Purpose | Commands |
|---|---|---|
| **DDL — Data Definition Language** | Define/modify database structure | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` |
| **DML — Data Manipulation Language** | Manipulate data | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `MERGE` |
| **DCL — Data Control Language** | Control access | `GRANT`, `REVOKE` |
| **TCL — Transaction Control Language** | Manage transactions | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

**DDL vs DML**

| Point | DDL | DML |
|---|---|---|
| Works on | Structure (schema, tables, indexes) | Data (rows) |
| Auto-commit | Usually auto-commits | Requires `COMMIT` |
| Rollback | Cannot normally roll back DDL | Can roll back DML |
| Examples | `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE` | `INSERT`, `UPDATE`, `DELETE`, `SELECT` |

**DML commands**

1. `INSERT` — add rows.
2. `UPDATE` — modify existing rows.
3. `DELETE` — remove rows.
4. `SELECT` — retrieve rows.
5. `MERGE` — insert/update conditionally (Oracle/SQL Server).

---

## 3.2 What are integrity constraints? Explain any five with examples. **[5–10]**

**Asked in:** AI-R26 Q2(A) **[5]**; IT-F25 Q3(B) **[10]**; IO-F24 Q3(a) **[8]**.

**Questions (verbatim):**
> Explain any 5 integrity constraints with example.
>
> What are integrity constraints in DBMS? Explain its different types.
>
> What are constraints in SQL? Explain any three types of constraints.

**Answer**

**Integrity constraints** are rules that ensure data entered into a database is accurate, valid and consistent. They are declared with the table and enforced automatically by the DBMS.

| Constraint | Purpose | Example |
|---|---|---|
| **PRIMARY KEY** | Uniquely identifies each row; no NULLs | `StudentID INT PRIMARY KEY` |
| **FOREIGN KEY** | Enforces referential integrity between tables | `DeptID INT REFERENCES Department(DeptID)` |
| **UNIQUE** | All values in a column are distinct (NULL allowed) | `Email VARCHAR(50) UNIQUE` |
| **NOT NULL** | Column cannot contain NULL | `Name VARCHAR(50) NOT NULL` |
| **CHECK** | Restricts values to a condition | `CHECK (Salary > 0)` |
| **DEFAULT** | Supplies a value when none is given | `Status VARCHAR(10) DEFAULT 'Active'` |
| **DOMAIN** | Restricts a column to a valid data type/range | `Age INT CHECK (Age BETWEEN 18 AND 60)` |

**SQL example**

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
);
```

- `EmpID` — primary key.
- `DeptID` — foreign key / referential integrity.
- `Email` — unique.
- `Salary > 0` — check/domain constraint.
- `Name NOT NULL` — entity integrity.

**Referential integrity actions:** `ON DELETE CASCADE`, `ON DELETE SET NULL`, `ON UPDATE CASCADE`.

---

## 3.3 SQL Query Bank (past-paper sets)

> Each set below is a complete exam question. Assumptions about column names are stated where the paper's table image was not fully readable.

## Set A — Employees / Departments **[10]**

**Asked in:** IT-F24 Q4(B).

**Question (verbatim):**
> Consider the following two tables and write SQL queries for following questions.
> a) Write a SQL query to create a table Employees with EmployeeID as primary key and DepartmentID as foreign key.
> b) Write a SQL query to retrieve the names of employees who work in the "IT" department and have a salary greater than ₹50,000. Display their names and salaries.
> c) Write a query to find the total salary of employees in each department. Display the department name and the total salary.
> d) Write a SQL query to retrieve the names of employees who joined after 1st January 2022. Display their names, department names, and joining dates in descending order of the joining date.
> e) Write a SQL query to display count of employees whose Department location is Mumbai.

Schema assumed: `Employees(EmployeeID, Name, DepartmentID, Salary, JoinDate)`, `Departments(DepartmentID, DepartmentName, Location)`.

**(a) Create `Employees` with `EmployeeID` as primary key and `DepartmentID` as foreign key.**

```sql
CREATE TABLE Departments (
    DepartmentID INT PRIMARY KEY,
    DepartmentName VARCHAR(50) NOT NULL,
    Location VARCHAR(50)
);

CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    DepartmentID INT,
    Salary DECIMAL(10,2),
    JoinDate DATE,
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
);
```

**(b) Names of employees who work in the IT department and earn more than ₹50,000; display name and salary.**

```sql
SELECT e.Name, e.Salary
FROM Employees e
JOIN Departments d ON e.DepartmentID = d.DepartmentID
WHERE d.DepartmentName = 'IT'
  AND e.Salary > 50000;
```

**(c) Total salary of employees in each department; display department name and total salary.**

```sql
SELECT d.DepartmentName, SUM(e.Salary) AS TotalSalary
FROM Employees e
JOIN Departments d ON e.DepartmentID = d.DepartmentID
GROUP BY d.DepartmentName;
```

**(d) Employees who joined after 1 January 2022; display name, department name and joining date, descending by joining date.**

```sql
SELECT e.Name, d.DepartmentName, e.JoinDate
FROM Employees e
JOIN Departments d ON e.DepartmentID = d.DepartmentID
WHERE e.JoinDate > '2022-01-01'
ORDER BY e.JoinDate DESC;
```

**(e) Count of employees whose department location is Mumbai.**

```sql
SELECT COUNT(*) AS EmployeeCount
FROM Employees e
JOIN Departments d ON e.DepartmentID = d.DepartmentID
WHERE d.Location = 'Mumbai';
```

---

## Set B — Books / Authors **[10]**

**Asked in:** IT-R25 Q4(B).

**Question (verbatim):**
> Consider the following two tables and write SQL queries for following questions.
> 1. Create the two tables applying appropriate constraints.
> 2. Write an SQL query to retrieve the titles of books authored by "Dr. John Smith" and published after 1st January 2022.
> 3. Write a query to calculate the average price of books authored by each author. Display the author's name and the average price.
> 4. Write an SQL query to retrieve the details of books with cost higher than 500. Display the book title, author's name, and publish date in descending order of publish date.
> 5. Find all books where the price of the book is greater than the price of the book "Data Structures". Exclude the book on Data Structures from the output.

Schema assumed: `Authors(AuthorID, Name, Nationality)`, `Books(BookID, Title, AuthorID, PublishDate, Price)`.

**(a) Create both tables with appropriate constraints.**

```sql
CREATE TABLE Authors (
    AuthorID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Nationality VARCHAR(30)
);

CREATE TABLE Books (
    BookID INT PRIMARY KEY,
    Title VARCHAR(100) NOT NULL,
    AuthorID INT,
    PublishDate DATE,
    Price DECIMAL(10,2) CHECK (Price > 0),
    FOREIGN KEY (AuthorID) REFERENCES Authors(AuthorID)
);
```

**(b) Titles of books authored by “Dr. John Smith” and published after 1 January 2022.**

```sql
SELECT b.Title
FROM Books b
JOIN Authors a ON b.AuthorID = a.AuthorID
WHERE a.Name = 'Dr. John Smith'
  AND b.PublishDate > '2022-01-01';
```

**(c) Average price of books authored by each author; display author name and average price.**

```sql
SELECT a.Name AS AuthorName, AVG(b.Price) AS AveragePrice
FROM Books b
JOIN Authors a ON b.AuthorID = a.AuthorID
GROUP BY a.AuthorID, a.Name;
```

**(d) Books with cost higher than 500; display title, author name and publish date, descending by publish date.**

```sql
SELECT b.Title, a.Name AS AuthorName, b.PublishDate
FROM Books b
JOIN Authors a ON b.AuthorID = a.AuthorID
WHERE b.Price > 500
ORDER BY b.PublishDate DESC;
```

**(e) Books whose price is greater than the price of “Data Structures”; exclude “Data Structures”.**

```sql
SELECT *
FROM Books
WHERE Price > (SELECT Price FROM Books WHERE Title = 'Data Structures')
  AND Title <> 'Data Structures';
```

> If multiple books have the title “Data Structures”, use `> ALL (SELECT Price ...)`.

---

## Set C — Employee / Works / Company **[10]**

**Asked in:** CO-F24 Q2(A)(i).

**Question (verbatim):**
> Q.2 Employee (eid, ename, Address, city)
> Works (eid, cid, salary)
> Company (cid, cname, city)
>
> Construct the SQL queries for the following:
> 1. Modify database so that John now lives in Mumbai.
> 2. Display the information of all employees whose name ends with ‘A’.
> 3. Display the information of all the employees whose first name starts with ‘R’ in descending order of their salary.
> 4. Find the total number of employees who live in ‘Nagpur’.
> 5. Display the information of all employees whose name contains at least 3 characters.

Schema: `Employee(eid, ename, Address, city)`, `Works(eid, cid, salary)`, `Company(cid, cname, city)`.

**1. Modify the database so that John now lives in Mumbai.**

```sql
UPDATE Employee
SET city = 'Mumbai'
WHERE ename = 'John';
```

**2. Employees whose name ends with ‘A’.**

```sql
SELECT *
FROM Employee
WHERE ename LIKE '%A';
```

**3. Employees whose first name starts with ‘R’, descending by salary.**

```sql
SELECT e.*
FROM Employee e
JOIN Works w ON e.eid = w.eid
WHERE e.ename LIKE 'R%'
ORDER BY w.salary DESC;
```

**4. Total number of employees who live in Nagpur.**

```sql
SELECT COUNT(*) AS TotalEmployees
FROM Employee
WHERE city = 'Nagpur';
```

**5. Employees whose name contains at least 3 characters.**

```sql
SELECT *
FROM Employee
WHERE ename LIKE '___%';
```

`_` matches exactly one character; three underscores + `%` means at least three characters.

---

## Set D — Employee / Departments / Location (average salary) **[10]**

**Asked in:** CO-F24 Q2(A)(ii).

**Question (verbatim):**
> Q.2 Employee ( EID, Fname, Lname, Email, Phone, Salary, DeptID )
> Departments ( DeptID, DeptName, LocationID )
> Location ( LocationID, LocationName, City, Pincode )
>
> Construct the SQL queries for the following:
> 1. Find Employees with Salary Greater Than the Average Salary
> 2. Find Employees Who Work in Specific Departments (e.g., 'HR', 'Finance')
> 3. Get a List of Employees and Their Department Names
> 4. Find Employees Whose Salary is Above the Average Salary in Their Department

Schema: `Employee(EID, Fname, Lname, Email, Phone, Salary, DeptID)`, `Departments(DeptID, DeptName, LocationID)`, `Location(LocationID, LocationName, City, Pincode)`.

**1. Employees with salary greater than the average salary.**

```sql
SELECT *
FROM Employee
WHERE Salary > (SELECT AVG(Salary) FROM Employee);
```

**2. Employees who work in specific departments (e.g., HR, Finance).**

```sql
SELECT e.*
FROM Employee e
JOIN Departments d ON e.DeptID = d.DeptID
WHERE d.DeptName IN ('HR', 'Finance');
```

**3. List of employees and their department names.**

```sql
SELECT e.Fname, e.Lname, d.DeptName
FROM Employee e
JOIN Departments d ON e.DeptID = d.DeptID;
```

**4. Employees whose salary is above the average salary of their department.**

```sql
SELECT e.*
FROM Employee e
WHERE e.Salary > (
    SELECT AVG(e2.Salary)
    FROM Employee e2
    WHERE e2.DeptID = e.DeptID
);
```

---

## Set E — Employee / Departments / Location (updates) **[10]**

**Asked in:** CO-R25 Q2(A)(i).

**Question (verbatim):**
> Q.1 Employee ( EID, Fname, Lname, Email, Phone, Salary, DeptID )
> Departments ( DeptID, DeptName, LocationID )
> Location ( LocationID, LocationName, City, Pincode )
>
> Construct the SQL queries for the following:
> 1. Display the information of all the employees whose first name starts with ‘A’.
> 2. Update the Phone number of ‘John’ to 123456.
> 3. Display all the information of the employees whose salary is within the range 1000 and 3000.
> 4. Give 10% hike in salary to all the employees working in DeptID = 6.
> 5. Add a new location to the database.

Same schema as Set D.

**1. Employees whose first name starts with ‘A’.**

```sql
SELECT * FROM Employee WHERE Fname LIKE 'A%';
```

**2. Update the phone number of ‘John’ to 123456.**

```sql
UPDATE Employee
SET Phone = '123456'
WHERE Fname = 'John';
```

**3. Employees whose salary is between 1000 and 3000.**

```sql
SELECT * FROM Employee WHERE Salary BETWEEN 1000 AND 3000;
```

**4. Give a 10% hike to employees working in DeptID = 6.**

```sql
UPDATE Employee
SET Salary = Salary * 1.10
WHERE DeptID = 6;
```

**5. Add a new location to the database.**

```sql
INSERT INTO Location (LocationID, LocationName, City, Pincode)
VALUES (101, 'M.G. Road', 'Delhi', '110001');
```

---

## Set F — emp / proj / dept / workson **[8]**

**Asked in:** IO-F24 Q1(a).

**Question (verbatim):**
> Consider following tables
> emp (eno, ename, bdate, title, salary, dno)
> proj (pno, pname, budget, dno)
> dept (dno, dname, mgreno)
> workson (eno, pno, resp, hours)
> Note: In table dept mgreno is a foreign key from table emp which is eno. resp is the responsbility.
>
> 1. Write query to display name of employee with decreasing order of salary (1 Mark)
> 2. Display the names of employees and their salaries, but only for employees who earn more than 50,000, and it orders the result in descending order of salary (1 Mark)
> 3. SQL query that applies an 8% salary hike to each employee and displays their new salary (2 Marks)
> 4. Retrieve the names of employees along with the names of the departments they work in. (2 Marks)
> 5. Retrieves the names of employees who work in the same department as an employee named ‘Nilam Shah’. (2 Marks)

Schema: `emp(eno, ename, bdate, title, salary, dno)`, `proj(pno, pname, budget, dno)`, `dept(dno, dname, mgreno)`, `workson(eno, pno, resp, hours)`.

**1. Names of employees in decreasing order of salary.**

```sql
SELECT ename FROM emp ORDER BY salary DESC;
```

**2. Names and salaries of employees earning more than 50,000, descending by salary.**

```sql
SELECT ename, salary
FROM emp
WHERE salary > 50000
ORDER BY salary DESC;
```

**3. Apply an 8% salary hike and display the new salary.**

```sql
SELECT ename, salary AS OldSalary, salary * 1.08 AS NewSalary
FROM emp;
```

To persist the hike:
```sql
UPDATE emp SET salary = salary * 1.08;
```

**4. Employee names with their department names.**

```sql
SELECT e.ename, d.dname
FROM emp e
JOIN dept d ON e.dno = d.dno;
```

**5. Employees who work in the same department as ‘Nilam Shah’.**

```sql
SELECT ename
FROM emp
WHERE dno = (SELECT dno FROM emp WHERE ename = 'Nilam Shah')
  AND ename <> 'Nilam Shah';
```

---

## Set G — Products / Categories / Orders / Suppliers (joins) **[10]**

**Asked in:** DS-F24 Q2(B)(1).

**Question (verbatim):**
> Given the following database schema:
> products (prod_id, prod_name, category_id)
> categories (category_id, category_name)
> orders (order_id, prod_id, order_amt)
> suppliers (supp_id, supp_name, prod_id)
>
> Write SQL queries to:
> 1. Find the names of all products along with their category names using a join between products and categories.
> 2. Find the names of products that have been ordered, including the total order amount for each product, by joining products and orders.
> 3. List the names of all products along with their supplier names using a join between products and suppliers. Display all products, even those that do not have a supplier.
> 4. Retrieve the names of products in the 'Electronics' category that have total orders greater than $5000 by using joins between products, categories, and orders.

Schema: `products(prod_id, prod_name, category_id)`, `categories(category_id, category_name)`, `orders(order_id, prod_id, order_amt)`, `suppliers(supp_id, supp_name, prod_id)`.

**1. Product names with category names.**

```sql
SELECT p.prod_name, c.category_name
FROM products p
JOIN categories c ON p.category_id = c.category_id;
```

**2. Products that have been ordered, with total order amount.**

```sql
SELECT p.prod_name, SUM(o.order_amt) AS TotalAmount
FROM products p
JOIN orders o ON p.prod_id = o.prod_id
GROUP BY p.prod_id, p.prod_name;
```

**3. All products with supplier names, including products without a supplier.**

```sql
SELECT p.prod_name, s.supp_name
FROM products p
LEFT JOIN suppliers s ON p.prod_id = s.prod_id;
```

**4. Electronics products whose total orders exceed $5000.**

```sql
SELECT p.prod_name
FROM products p
JOIN categories c ON p.category_id = c.category_id
JOIN orders o ON p.prod_id = o.prod_id
WHERE c.category_name = 'Electronics'
GROUP BY p.prod_id, p.prod_name
HAVING SUM(o.order_amt) > 5000;
```

---

## Set H — Products / Suppliers / Customers / Warehouses (nested queries) **[10]**

**Asked in:** DS-F24 Q2(B)(2).

**Question (verbatim):**
> Given the following database schema:
> products (product_id, product_name, supplier_id)
> suppliers (supplier_id, supplier_name)
> customers (customer_id, product_id, purchase_date)
> warehouses (warehouse_id, warehouse_name, product_id)
>
> Write SQL queries to:
> 1. List the names of products supplied by 'Premium Suppliers' using a nested query that first selects the supplier_id of 'Premium Suppliers' from the suppliers table and then uses this result to filter products in the products table.
> 2. Find the names of warehouses that store at least one product by first selecting unique product_id values from the products table and then using this result to filter the warehouses table.
> 3. Retrieve the names of products purchased by customers using a nested query to select unique product_id values from the customers table and use these values to filter products in the products table.
> 4. List the names of suppliers who have supplied at least one product by first selecting unique supplier_id values from the products table and then using this result to filter the suppliers.

Schema: `products(product_id, product_name, supplier_id)`, `suppliers(supplier_id, supplier_name)`, `customers(customer_id, product_id, purchase_date)`, `warehouses(warehouse_id, warehouse_name, product_id)`.

**1. Products supplied by ‘Premium Suppliers’.**

```sql
SELECT product_name
FROM products
WHERE supplier_id IN (
    SELECT supplier_id
    FROM suppliers
    WHERE supplier_name = 'Premium Suppliers'
);
```

**2. Warehouses that store at least one product.**

```sql
SELECT warehouse_name
FROM warehouses
WHERE product_id IN (SELECT DISTINCT product_id FROM products);
```

**3. Products purchased by customers.**

```sql
SELECT product_name
FROM products
WHERE product_id IN (SELECT DISTINCT product_id FROM customers);
```

**4. Suppliers who have supplied at least one product.**

```sql
SELECT supplier_name
FROM suppliers
WHERE supplier_id IN (SELECT DISTINCT supplier_id FROM products);
```

---

## Set I — Students / Courses / Grades / Instructors (joins) **[10]**

**Asked in:** DS-R25 Q2(B)(1).

**Question (verbatim):**
> Given the following database schema:
> students (student_id, student_name, course_id)
> courses (course_id, course_name)
> grades (grade_id, student_id, grade_value)
> instructors (instructor_id, instructor_name, course_id)
>
> Write SQL queries to:
> 1. Find the names of all students along with their course names using a join between students and courses.
> 2. Retrieve the names of students who have received grades, including their total grade value, by joining students and grades.
> 3. List the names of all courses along with their instructors using a join between courses and instructors. Display all courses, even those that do not have an assigned instructor.
> 4. Find the names of students enrolled in the 'Mathematics' course who have received grades greater than 80 using joins between students, courses, and grades.

Schema: `students(student_id, student_name, course_id)`, `courses(course_id, course_name)`, `grades(grade_id, student_id, grade_value)`, `instructors(instructor_id, instructor_name, course_id)`.

**1. Student names with their course names.**

```sql
SELECT s.student_name, c.course_name
FROM students s
JOIN courses c ON s.course_id = c.course_id;
```

**2. Students who received grades, with total grade value.**

```sql
SELECT s.student_name, SUM(g.grade_value) AS TotalGrade
FROM students s
JOIN grades g ON s.student_id = g.student_id
GROUP BY s.student_id, s.student_name;
```

**3. All courses with their instructors, including courses without an instructor.**

```sql
SELECT c.course_name, i.instructor_name
FROM courses c
LEFT JOIN instructors i ON c.course_id = i.course_id;
```

**4. Students enrolled in ‘Mathematics’ who received a grade greater than 80.**

```sql
SELECT s.student_name
FROM students s
JOIN courses c ON s.course_id = c.course_id
JOIN grades g ON s.student_id = g.student_id
WHERE c.course_name = 'Mathematics'
  AND g.grade_value > 80;
```

---

## Set J — Events / Organizers / Attendees / Venues (nested) **[10]**

**Asked in:** DS-R25 Q2(B)(2).

**Question (verbatim):**
> Given the following database schema:
> events (event_id, event_name, organizer_id)
> organizers (organizer_id, organizer_name)
> attendees (attendee_id, event_id, attendance_date)
> venues (venue_id, venue_name, event_id)
>
> Write SQL queries to:
> 1. List the names of events organized by 'Sarah Lee' using a nested query that first selects the organizer_id of 'Sarah Lee' from the organizers table and then uses this result to filter events in the events table.
> 2. Find the names of venues that are hosting at least one event by first selecting unique event_id values from the events table and then using this result to filter the venues table.
> 3. Retrieve the names of events that have attendees using a nested query to select unique event_id values from the attendees table and use these values to filter events in the events table.
> 4. List the names of organizers who have organized at least one event by first selecting unique organizer_id values from the events table and then using this result to filter the organizers table.

Schema: `events(event_id, event_name, organizer_id)`, `organizers(organizer_id, organizer_name)`, `attendees(attendee_id, event_id, attendance_date)`, `venues(venue_id, venue_name, event_id)`.

**1. Events organized by ‘Sarah Lee’.**

```sql
SELECT event_name
FROM events
WHERE organizer_id IN (
    SELECT organizer_id FROM organizers WHERE organizer_name = 'Sarah Lee'
);
```

**2. Venues hosting at least one event.**

```sql
SELECT venue_name
FROM venues
WHERE event_id IN (SELECT DISTINCT event_id FROM events);
```

**3. Events that have attendees.**

```sql
SELECT event_name
FROM events
WHERE event_id IN (SELECT DISTINCT event_id FROM attendees);
```

**4. Organizers who organized at least one event.**

```sql
SELECT organizer_name
FROM organizers
WHERE organizer_id IN (SELECT DISTINCT organizer_id FROM events);
```

---

## Set K — Accounts / Transactions **[10]**

**Asked in:** IT-F25 Q4(B).

**Question (verbatim):**
> Consider the tables shown below and write SQL queries for below mentioned questions.
> a. Consider the Accounts table is already created. Write an SQL query to modify Accounts table to add PRIMARY KEY constraint. [2M]
> b. Write an SQL query to display the total transaction amount per account. [2M]
> c. Write an SQL query to display all accounts sorted by their balance in descending order. [1M]
> d. Write an SQL query to display the account holder name, transaction date, and amount. [2M]
> e. Write an SQL query to display details of transactions for accounts with IDs other than 1 and 3. [1M]
> f. Create a view named TransactionView that displays transaction id and date for transaction amounts above 5000. [2M]

Schema:
- `Accounts(account_id, account_holder, account_type, balance)`
- `Transactions(transaction_id, account_id, transaction_date, amount, transaction_type)`

**(a) Add a PRIMARY KEY constraint to the existing Accounts table.**

```sql
ALTER TABLE Accounts
ADD CONSTRAINT pk_accounts PRIMARY KEY (account_id);
```

**(b) Total transaction amount per account.**

```sql
SELECT account_id, SUM(amount) AS TotalAmount
FROM Transactions
GROUP BY account_id;
```

**(c) All accounts sorted by balance descending.**

```sql
SELECT * FROM Accounts ORDER BY balance DESC;
```

**(d) Account holder name, transaction date and amount (JOIN).**

```sql
SELECT a.account_holder, t.transaction_date, t.amount
FROM Accounts a
JOIN Transactions t ON a.account_id = t.account_id;
```

**(e) Transactions for accounts with IDs other than 1 and 3.**

```sql
SELECT *
FROM Transactions
WHERE account_id NOT IN (1, 3);
```

**(f) View `TransactionView` with transaction id and date for amounts above 5000.**

```sql
CREATE VIEW TransactionView AS
SELECT transaction_id, transaction_date
FROM Transactions
WHERE amount > 5000;
```

---

## Set L — Students / Major / Department / Faculty **[10]**

**Asked in:** CO-R26 Q2.

**Question (verbatim):**
> Q.3 Student (StudentID, FirstName, LastName, Email, Phone, GPA, MajorID)
> Major (MajorID, MajorName, DepartmentID)
> Department (DepartmentID, DepartmentName, FacultyID)
> Faculty (FacultyID, FacultyName, OfficeLocation)
>
> Construct the SQL queries for the following:
> 1. Update the GPA of a student with StudentID = 103 to 3.9.
> 2. Delete a student record with StudentID = 104 from the database.
> 3. Display the details of students whose first name starts with the letter 'A'.
> 4. Insert a new major record into the Major table.
> 5. Count the number of students in each major.

Schema: `Student(StudentID, FirstName, LastName, Email, Phone, GPA, MajorID)`, `Major(MajorID, MajorName, DepartmentID)`, `Department(DepartmentID, DepartmentName, FacultyID)`, `Faculty(FacultyID, FacultyName, OfficeLocation)`.

**1. Update GPA of StudentID 103 to 3.9.**

```sql
UPDATE Student SET GPA = 3.9 WHERE StudentID = 103;
```

**2. Delete student 104.**

```sql
DELETE FROM Student WHERE StudentID = 104;
```

**3. Students whose first name starts with ‘A’.**

```sql
SELECT * FROM Student WHERE FirstName LIKE 'A%';
```

**4. Insert a new major.**

```sql
INSERT INTO Major (MajorID, MajorName, DepartmentID)
VALUES (10, 'Data Science', 3);
```

**5. Count students in each major.**

```sql
SELECT m.MajorName, COUNT(s.StudentID) AS StudentCount
FROM Major m
LEFT JOIN Student s ON m.MajorID = s.MajorID
GROUP BY m.MajorID, m.MajorName;
```

`LEFT JOIN` includes majors with zero students.

---

## Set M — Students / Departments / Courses / Enrollments **[10]**

**Asked in:** CO-B26 Q3(A).

**Question (verbatim):**
> Consider the following database schema, write an sql Queries for the following
> Students (StudentID, FirstName, LastName, DepartmentID, AdmissionDate)
> Departments (DepartmentID, DepartmentName, Location)
> Courses (CourseID, CourseName, DepartmentID, Credits)
> Enrollments (EnrollmentID, StudentID, CourseID, Grade)
>
> 1. Retrieve the average grade for each course (1M)
> 2. Find the total number of students enrolled in each department (2M)
> 3. Identify the students who have completed more than 40 credits (2M)
> 4. Retrieve the names of students who are enrolled in the 'Computer Science' (2M)
> 5. Find the total number of courses in the database (1M)
> 6. Find the highest grade achieved in any course (1M)
> 7. Insert 3 rows in Department table

Schema: `Students(StudentID, FirstName, LastName, DepartmentID, AdmissionDate)`, `Departments(DepartmentID, DepartmentName, Location)`, `Courses(CourseID, CourseName, DepartmentID, Credits)`, `Enrollments(EnrollmentID, StudentID, CourseID, Grade)`.

**1. Average grade for each course.**

```sql
SELECT c.CourseID, c.CourseName, AVG(e.Grade) AS AverageGrade
FROM Courses c
JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY c.CourseID, c.CourseName;
```

**2. Total number of students enrolled in each department.**

```sql
SELECT d.DepartmentName, COUNT(DISTINCT s.StudentID) AS StudentCount
FROM Departments d
JOIN Students s ON d.DepartmentID = s.DepartmentID
GROUP BY d.DepartmentID, d.DepartmentName;
```

**3. Students who have completed more than 40 credits.**

```sql
SELECT s.StudentID, s.FirstName, s.LastName, SUM(c.Credits) AS TotalCredits
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
JOIN Courses c ON e.CourseID = c.CourseID
GROUP BY s.StudentID, s.FirstName, s.LastName
HAVING SUM(c.Credits) > 40;
```

**4. Names of students enrolled in ‘Computer Science’.**

```sql
SELECT DISTINCT s.FirstName, s.LastName
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
JOIN Courses c ON e.CourseID = c.CourseID
JOIN Departments d ON c.DepartmentID = d.DepartmentID
WHERE d.DepartmentName = 'Computer Science';
```

If ‘Computer Science’ is a course name, replace the last join with `WHERE c.CourseName = 'Computer Science'`.

**5. Total number of courses.**

```sql
SELECT COUNT(*) AS TotalCourses FROM Courses;
```

**6. Highest grade achieved in any course.**

```sql
SELECT MAX(Grade) AS HighestGrade FROM Enrollments;
```

**7. Insert 3 rows into Department.**

```sql
INSERT INTO Departments (DepartmentID, DepartmentName, Location)
VALUES
    (1, 'Computer Science', 'Mumbai'),
    (2, 'Information Technology', 'Mumbai'),
    (3, 'Electronics', 'Mumbai');
```

---

## Set N — EMP table **[10]**

**Asked in:** AI-R26 Q4(A)(i).

**Question (verbatim):**
> Write SQL queries for following
> i) Create table EMP with following attributes using suitable data types (Eno, Ename, Deptname, Salary, designation, Joining Date, DOB, city)
> ii) Write a query to display ename and empno ordered by salary in ascending order
> iii) Display names of employee whose name start with alphabet ‘B’
> iv) Increase the salary of employees by 10% who joined after 2020.
> v) Create a view to display employees who belong to the 'Finance' department
> vi) Display the department name and number of employees in each department, but only those departments having more than 2 employees.

Schema: `EMP(Eno, Ename, Deptname, Salary, designation, JoiningDate, DOB, city)`.

**1. Create the EMP table.**

```sql
CREATE TABLE EMP (
    Eno INT PRIMARY KEY,
    Ename VARCHAR(50) NOT NULL,
    Deptname VARCHAR(50),
    Salary DECIMAL(10,2) CHECK (Salary >= 0),
    designation VARCHAR(50),
    JoiningDate DATE,
    DOB DATE,
    city VARCHAR(50)
);
```

**2. Display Ename and Eno ordered by salary ascending.**

```sql
SELECT Ename, Eno FROM EMP ORDER BY Salary ASC;
```

**3. Names of employees starting with ‘B’.**

```sql
SELECT Ename FROM EMP WHERE Ename LIKE 'B%';
```

**4. Increase salary by 10% for employees who joined after 2020.**

```sql
UPDATE EMP
SET Salary = Salary * 1.10
WHERE JoiningDate > '2020-12-31';
```

**5. View of employees in the Finance department.**

```sql
CREATE VIEW FinanceEmployees AS
SELECT * FROM EMP WHERE Deptname = 'Finance';
```

**6. Department name and number of employees, only departments with more than 2 employees.**

```sql
SELECT Deptname, COUNT(*) AS EmployeeCount
FROM EMP
GROUP BY Deptname
HAVING COUNT(*) > 2;
```

---

## Set O — Customers / Orders **[10]**

**Asked in:** IT-R26 Q4(B).

**Question (verbatim):**
> Consider the tables shown below and write SQL queries for below mentioned questions.
> a. Write an SQL query to add a foreign key constraint to the Orders table using the alter command. [2M]
> b. Write an SQL query to delete the order with order_id as 104. [1M]
> c. Write an SQL query to remove all the rows from the Orders table. [1M]
> d. Write an SQL query to display customers and the amount spent, who spent more than 400 in total. [2M]
> e. Write an SQL query using a JOIN to display the customer name, city, and order amount for each order. [2M]
> f. Write an SQL query to display details of orders placed by customers with IDs 3, 4 and 7. [2M]

Schema:
- `Customers(customer_id, customer_name, city, phone)`
- `Orders(order_id, order_date, amount, customer_id)`

**(a) Add a foreign key constraint to `Orders` using `ALTER`.**

```sql
ALTER TABLE Orders
ADD CONSTRAINT fk_orders_customer
FOREIGN KEY (customer_id) REFERENCES Customers(customer_id);
```

**(b) Delete the order with `order_id = 104`.**

```sql
DELETE FROM Orders WHERE order_id = 104;
```

**(c) Remove all rows from `Orders`.**

```sql
DELETE FROM Orders;
```

`TRUNCATE TABLE Orders;` is faster but cannot be rolled back in some DBMSs and resets identity values.

**(d) Customers and the amount spent, only customers who spent more than 400 in total.**

```sql
SELECT c.customer_id, c.customer_name, SUM(o.amount) AS TotalSpent
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
HAVING SUM(o.amount) > 400;
```

**(e) Customer name, city and order amount for each order (JOIN).**

```sql
SELECT c.customer_name, c.city, o.amount
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id;
```

**(f) Details of orders placed by customers with IDs 3, 4 and 7.**

```sql
SELECT *
FROM Orders
WHERE customer_id IN (3, 4, 7);
```

---

## Set P — Employee / Works / Company / Manages **[10]**

**Asked in:** IC-R26 Q3(a).

**Question (verbatim):**
> Consider following employee database:
> Employee (empname, street, city, date_of_joining)
> Works (empname, company_name, salary)
> Company (company_name, city)
> Manages (empname, manager_name)
>
> Write SQL queries for following statements:
> i. List employees who live in ‘Pune’ or ‘Mumbai’.
> ii. Display the names of employees whose street name contains the word ‘Road’.
> iii. Retrieve employees who joined between 2018 and 2022.
> iv. Display employee name, company city, and employee city.

Schema:
- `Employee(empname, street, city, date_of_joining)`
- `Works(empname, company_name, salary)`
- `Company(company_name, city)`
- `Manages(empname, manager_name)`

**(i) Employees who live in Pune or Mumbai.**

```sql
SELECT *
FROM Employee
WHERE city IN ('Pune', 'Mumbai');
```

**(ii) Employees whose street name contains the word ‘Road’.**

```sql
SELECT *
FROM Employee
WHERE street LIKE '%Road%';
```

**(iii) Employees who joined between 2018 and 2022.**

```sql
SELECT *
FROM Employee
WHERE date_of_joining BETWEEN '2018-01-01' AND '2022-12-31';
```

**(iv) Employee name, company city and employee city.**

```sql
SELECT e.empname,
       c.city AS CompanyCity,
       e.city AS EmployeeCity
FROM Employee e
JOIN Works w ON e.empname = w.empname
JOIN Company c ON w.company_name = c.company_name;
```

---

## 3.4 Joins — INNER, LEFT, RIGHT, NATURAL **[10]**

**Asked in:** CO-B26 Q3(B)(i); used throughout Sets G–K.

**Question (verbatim):**
> Demonstrate INNER JOIN, LEFT OUTER JOIN, RIGHT OUTER JOIN and NATURAL JOIN using suitable example.

**Answer**

A **join** combines rows from two or more tables using a related column.

Sample tables:

`Student(StudentID, Name, DeptID)`

| StudentID | Name | DeptID |
|---|---|---|
| 1 | Aarav | 10 |
| 2 | Diya | 20 |
| 3 | Rohan | NULL |

`Department(DeptID, DeptName)`

| DeptID | DeptName |
|---|---|
| 10 | IT |
| 20 | HR |
| 30 | Finance |

**1. INNER JOIN** — returns only rows with matching values in both tables.

```sql
SELECT s.Name, d.DeptName
FROM Student s
INNER JOIN Department d ON s.DeptID = d.DeptID;
```

Result: Aarav–IT, Diya–HR. Rohan (NULL) and Finance (no student) are excluded.

**2. LEFT (OUTER) JOIN** — returns all rows from the left table and matching rows from the right; unmatched right columns are NULL.

```sql
SELECT s.Name, d.DeptName
FROM Student s
LEFT JOIN Department d ON s.DeptID = d.DeptID;
```

Result: Aarav–IT, Diya–HR, Rohan–NULL.

**3. RIGHT (OUTER) JOIN** — returns all rows from the right table and matching rows from the left; unmatched left columns are NULL.

```sql
SELECT s.Name, d.DeptName
FROM Student s
RIGHT JOIN Department d ON s.DeptID = d.DeptID;
```

Result: Aarav–IT, Diya–HR, NULL–Finance.

**4. NATURAL JOIN** — automatically joins on all columns with the same name and removes duplicate columns.

```sql
SELECT Name, DeptName
FROM Student NATURAL JOIN Department;
```

Equivalent to an inner join on `DeptID`; use only when the common column names are unambiguous.

**5. FULL OUTER JOIN** (bonus) — all rows from both sides, NULLs where there is no match.

```sql
SELECT s.Name, d.DeptName
FROM Student s
FULL OUTER JOIN Department d ON s.DeptID = d.DeptID;
```

**Comparison**

| Join | Rows returned |
|---|---|
| INNER | Only matching rows |
| LEFT | All left + matching right |
| RIGHT | All right + matching left |
| FULL | All rows from both |
| NATURAL | Inner join on all same-named columns |
| CROSS | Cartesian product (every combination) |

---

## 3.5 Views in SQL **[5–10]**

**Asked in:** CO-R26 Q3 **[10]**; IT-F25 Q4(B)(f) (create `TransactionView`).

**Question (verbatim):**
> What is a view? Describe the syntax to create the views. Suppose you are managing an employee database and need to create a view to simplify querying high-earning employees. The condition for a high salary is earning above Rs.100,000. Consider the Employee schema as Employee(EID,Fname,Lname,Salary,DOB,position,hiredate,DeptId). Frame a query to:
> 1. Create a view named HighEarningEmployees that includes EID, Fname, Lname, Salary, and DeptID.
> 2. Query the view to find all high-earning employees in department DeptID = 5.

**Answer**

A **view** is a virtual table defined by a stored SQL query. It contains no data of its own; the data is fetched from the underlying base tables when the view is queried.

**Syntax**

```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;

CREATE OR REPLACE VIEW view_name AS
SELECT ...;

DROP VIEW view_name;
```

**Example — high-earning employees**

Schema: `Employee(EID, Fname, Lname, Salary, DOB, position, hiredate, DeptID)`.

```sql
CREATE VIEW HighEarningEmployees AS
SELECT EID, Fname, Lname, Salary, DeptID
FROM Employee
WHERE Salary > 100000;
```

Query the view for department 5:

```sql
SELECT *
FROM HighEarningEmployees
WHERE DeptID = 5;
```

**Advantages of views**

1. **Security** — users see only permitted columns/rows; base tables remain hidden.
2. **Simplicity** — complex joins/aggregations are encapsulated in one view.
3. **Consistency** — everyone uses the same derived data definition.
4. **Logical data independence** — schema changes can be absorbed by redefining the view.
5. **Restricted updates** — views can be read-only or restricted to certain columns.

**Updatable views:** A view is generally updatable if it is based on one table, contains no `DISTINCT`, `GROUP BY`, `HAVING`, aggregate functions or set operations, and includes the primary key. Otherwise use `INSTEAD OF` triggers.

---

## 3.6 Triggers in SQL **[10]**

**Asked in:** IT-F24 Q3(B) (BEFORE vs AFTER) **[10]**; CO-R25 Q2(A)(ii) (backup trigger) **[10]**; IC-R26 Q2(b)(ii); AI-R26 Q4(A)(ii).

**Questions (verbatim):**
> What is the main difference between a BEFORE trigger and an AFTER trigger in a DBMS? Provide an example scenario where each type of trigger would be appropriate.
>
> What is trigger? when we use trigger? Write the SQL code to create a trigger t1 that will copy a row from the main table to the backup table before the row is deleted. Consider the database Schema main(id,salary) and backup(id,salary).
>
> What are triggers in SQL? Explain with syntax and example.
>
> Compare BEFORE and AFTER triggers in SQL by explaining how they behave during INSERT/UPDATE/DELETE operations with real world scenario and write the general syntax of a trigger.

**Answer**

A **trigger** is a stored PL/SQL block that fires automatically when a specified DML event (`INSERT`, `UPDATE`, `DELETE`) occurs on a table. Triggers enforce business rules, audit changes, maintain derived data and prevent invalid operations.

**General syntax (Oracle style)**

```sql
CREATE OR REPLACE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON table_name
[FOR EACH ROW]
[WHEN (condition)]
BEGIN
    -- trigger body
END;
/
```

- `FOR EACH ROW` — row-level trigger (fires once per affected row); omit for statement-level.
- `:NEW.column` — new value (INSERT/UPDATE).
- `:OLD.column` — old value (UPDATE/DELETE).

**BEFORE vs AFTER trigger**

| Point | BEFORE trigger | AFTER trigger |
|---|---|---|
| Fires | Before the DML operation is applied | After the DML operation is applied |
| Use | Validate/transform data, reject invalid rows | Audit/log changes, cascade actions |
| `:NEW` modification | Can modify `:NEW` values | Cannot modify `:NEW` |
| Typical example | Reject salary < 1000 | Insert a row into an audit table |
| Constraint timing | Can prevent the operation | Operation has already happened |

**Example 1 — BEFORE trigger (salary validation)**

```sql
CREATE OR REPLACE TRIGGER check_salary_before_update
BEFORE UPDATE OF Salary ON Employee
FOR EACH ROW
BEGIN
    IF :NEW.Salary < 1000 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary must be at least 1000');
    END IF;
END;
/
```

Appropriate scenario: reject an update that would pay an employee below the minimum wage.

**Example 2 — AFTER trigger (audit log)**

```sql
CREATE TABLE SalaryAudit (
    EmpID INT,
    OldSalary DECIMAL(10,2),
    NewSalary DECIMAL(10,2),
    ChangedOn DATE
);

CREATE OR REPLACE TRIGGER log_salary_update_after
AFTER UPDATE OF Salary ON Employee
FOR EACH ROW
BEGIN
    INSERT INTO SalaryAudit (EmpID, OldSalary, NewSalary, ChangedOn)
    VALUES (:OLD.EID, :OLD.Salary, :NEW.Salary, SYSDATE);
END;
/
```

Appropriate scenario: keep a permanent history of salary changes for auditing.

**Example 3 — BEFORE DELETE trigger that copies a row to a backup table**

Schema: `main(id, salary)`, `backup(id, salary)`.

```sql
CREATE OR REPLACE TRIGGER t1
BEFORE DELETE ON main
FOR EACH ROW
BEGIN
    INSERT INTO backup (id, salary)
    VALUES (:OLD.id, :OLD.salary);
END;
/
```

Test:

```sql
INSERT INTO main VALUES (1, 1000);
INSERT INTO main VALUES (2, 2000);
DELETE FROM main WHERE id = 1;
SELECT * FROM backup;   -- (1, 1000)
```

**MySQL note:** use `NEW.column` / `OLD.column` (no colon) and `SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = '...'` instead of `RAISE_APPLICATION_ERROR`.

**Statement-level vs row-level**

| Row-level trigger | Statement-level trigger |
|---|---|
| `FOR EACH ROW` | No `FOR EACH ROW` |
| Fires once per affected row | Fires once per SQL statement |
| Can use `:OLD`/`:NEW` | Cannot use `:OLD`/`:NEW` |

---

## 3.7 Security, Authorization, DCL and TCL **[5–10]**

**Asked in:** CO-F24 Q2(B) **[5]**; IT-R25 Q3(B) **[10]**; IT-R26 Q3(B) **[10]**; AI-R26 Q4(A)(ii); DS-R25 Q2(A) **[5]**; CO-B26 Q3(B)(ii) **[10]**.

**Questions (verbatim):**
> Explain authorization in SQL. Write appropriate SQL queries to show authorization.
>
> Discuss Security and Authorization in DBMS. Provide examples.
>
> Define authorization. What are the different types of authorization? Discuss how privileges are granted and revoked. Support your answer with examples.
>
> Analyze the roles of Data Control Commands (DCL), such as GRANT and REVOKE, compared to Transaction Control Commands (TCL) like COMMIT and ROLLBACK.
>
> Elaborate Transaction Control Commands. Explain their purpose and provide scenarios where these commands would would be used with the help of an example.

**Answer**

**Database security** protects data from unauthorized access, modification or destruction. **Authorization** is the process of granting specific privileges to users so they can perform only permitted operations.

**Security mechanisms**

1. **Authentication** — verify identity (password, biometric, token).
2. **Authorization** — grant/revoke privileges (`GRANT`, `REVOKE`).
3. **Views** — expose only permitted rows/columns.
4. **Roles** — group privileges and assign them to users.
5. **Encryption** — protect data at rest/in transit.
6. **Auditing** — log access and changes.

**Privileges in SQL:** `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `REFERENCES`, `EXECUTE`, `ALL PRIVILEGES`.

**GRANT examples**

```sql
-- Give users U1, U2, U3 SELECT on Emp_Salary
GRANT SELECT ON Emp_Salary TO U1, U2, U3;

-- Give U4 UPDATE on the amount column of loan
GRANT UPDATE (amount) ON loan TO U4;

-- Give U5 UPDATE on Branch and allow U5 to pass it on
GRANT UPDATE ON Branch TO U5 WITH GRANT OPTION;

-- Give A and B SELECT and UPDATE on CUSTOMER
GRANT SELECT, UPDATE ON CUSTOMER TO A, B;
```

**REVOKE examples**

```sql
REVOKE SELECT ON Emp_Salary FROM U3;
REVOKE UPDATE ON CUSTOMER FROM B;
REVOKE UPDATE ON Branch FROM U5 CASCADE;
```

- `WITH GRANT OPTION` allows the grantee to grant the same privilege to others.
- `CASCADE` on `REVOKE` also revokes privileges that were granted onward.

**Roles**

```sql
CREATE ROLE manager;
GRANT SELECT, UPDATE ON Employee TO manager;
GRANT manager TO user1, user2;
```

**Stored procedure example (AI-R26 Q4(A)(ii))**

Schema: `CUSTOMER(CustomerID, CustomerName, City)`. Procedure to update the city of customer 2:

```sql
CREATE OR REPLACE PROCEDURE update_customer_city (p_city IN VARCHAR2) AS
BEGIN
    UPDATE CUSTOMER
    SET City = p_city
    WHERE CustomerID = 2;
    COMMIT;
END;
/
```

Call:

```sql
EXEC update_customer_city('Mumbai');
```

**DCL vs TCL**

| Point | DCL | TCL |
|---|---|---|
| Purpose | Control access/permissions | Control transaction boundaries |
| Commands | `GRANT`, `REVOKE` | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |
| Affects | Users/roles and privileges | Data changes in the current transaction |
| Example | `GRANT SELECT ON Emp TO A;` | `COMMIT;` / `ROLLBACK;` |

**TCL commands**

- **COMMIT** — makes all changes of the current transaction permanent and visible to others.
- **ROLLBACK** — undoes all changes since the last `COMMIT`/`SAVEPOINT`.
- **SAVEPOINT** — sets a point to which a transaction can be partially rolled back.
- **SET TRANSACTION** — sets isolation level/read-only mode.

```sql
BEGIN TRANSACTION;
UPDATE Accounts SET balance = balance - 100 WHERE account_id = 101;
SAVEPOINT after_debit;
UPDATE Accounts SET balance = balance + 100 WHERE account_id = 102;
-- error occurs
ROLLBACK TO after_debit;
COMMIT;
```

The `ROLLBACK TO` undoes only the credit, leaving the debit pending until `COMMIT`.

---

## 3.8 Aggregate Functions **[5]**

**Asked in:** CO-R26 Q3(B).

**Question (verbatim):**
> List & Explain five commonly used aggregate functions in SQL.

**Answer**

Aggregate functions operate on a set of rows and return a single value.

| Function | Purpose | Example |
|---|---|---|
| `COUNT()` | Number of rows/non-NULL values | `SELECT COUNT(*) FROM Employee;` |
| `SUM()` | Total of numeric values | `SELECT SUM(Salary) FROM Employee;` |
| `AVG()` | Average of numeric values | `SELECT AVG(Salary) FROM Employee;` |
| `MIN()` | Smallest value | `SELECT MIN(Salary) FROM Employee;` |
| `MAX()` | Largest value | `SELECT MAX(Salary) FROM Employee;` |

**GROUP BY and HAVING**

```sql
SELECT DeptID, COUNT(*) AS Employees, AVG(Salary) AS AvgSalary
FROM Employee
GROUP BY DeptID
HAVING AVG(Salary) > 50000;
```

- `WHERE` filters rows **before** grouping.
- `HAVING` filters groups **after** grouping.
- Every non-aggregated column in the `SELECT` list must appear in `GROUP BY`.

**NULL handling:** `COUNT(*)` counts all rows; `COUNT(column)` ignores NULLs. `SUM`/`AVG` ignore NULLs.

---

## 3.9 Set and String Operations **[5]**

**Asked in:** DS-F24 Q2(A).

**Question (verbatim):**
> Examine the impact of Set and String operations on data manipulation within SQL.

**Answer**

**Set operations** combine the result sets of two queries. The queries must be **union-compatible** (same number of columns and compatible data types).

| Operator | Meaning | Example |
|---|---|---|
| `UNION` | All rows from both queries, duplicates removed | `SELECT city FROM Student UNION SELECT city FROM Faculty;` |
| `UNION ALL` | All rows, duplicates retained | `SELECT city FROM Student UNION ALL SELECT city FROM Faculty;` |
| `INTERSECT` | Rows common to both | `SELECT city FROM Student INTERSECT SELECT city FROM Faculty;` |
| `EXCEPT` / `MINUS` | Rows in the first but not the second | `SELECT city FROM Student EXCEPT SELECT city FROM Faculty;` |

**String operations**

| Operation | Purpose | Example |
|---|---|---|
| `LIKE` with `%` | Match any sequence of characters | `WHERE Name LIKE 'A%'` |
| `LIKE` with `_` | Match exactly one character | `WHERE Name LIKE '_a%'` |
| `CONCAT` / `||` | Concatenate strings | `SELECT CONCAT(Fname, ' ', Lname) FROM Employee;` |
| `SUBSTRING` / `SUBSTR` | Extract a substring | `SELECT SUBSTR(Name, 1, 3) FROM Employee;` |
| `LENGTH` / `LEN` | Length of a string | `SELECT LENGTH(Name) FROM Employee;` |
| `UPPER` / `LOWER` | Change case | `SELECT UPPER(Name) FROM Employee;` |
| `TRIM` / `LTRIM` / `RTRIM` | Remove spaces | `SELECT TRIM(Name) FROM Employee;` |
| `REPLACE` | Replace occurrences | `SELECT REPLACE(Name, 'a', 'A') FROM Employee;` |
| `INSTR` / `CHARINDEX` | Find position of a substring | `SELECT INSTR(Name, 'a') FROM Employee;` |

**Impact on data manipulation**

- Set operations allow combining data from multiple tables/queries without writing procedural code.
- String operations enable pattern-based filtering, formatting, case-insensitive search and data cleaning.
- They are often used in `WHERE`, `SELECT`, `ORDER BY` and `GROUP BY`, and can affect index usage (leading wildcard `%` usually prevents index use).

**Example**

```sql
SELECT UPPER(Name) AS Name, LENGTH(Name) AS NameLength
FROM Employee
WHERE Name LIKE 'A%' OR Name LIKE 'B%'
ORDER BY Name;
```

---

## Unit 3 — quick revision checklist

- [ ] SQL categories: DDL, DML, DCL, TCL with commands
- [ ] Five integrity constraints with SQL examples
- [ ] 16 query sets (A–P) — practise at least the joins, group-by and nested queries
- [ ] INNER/LEFT/RIGHT/FULL/NATURAL JOIN with a small example
- [ ] View syntax, advantages, updatable-view rules
- [ ] Trigger syntax, BEFORE vs AFTER, `:OLD`/`:NEW`, row vs statement
- [ ] GRANT/REVOKE, WITH GRANT OPTION, roles, views for security
- [ ] DCL vs TCL; COMMIT/ROLLBACK/SAVEPOINT
- [ ] Five aggregate functions + GROUP BY/HAVING
- [ ] UNION/UNION ALL/INTERSECT/EXCEPT and string functions
