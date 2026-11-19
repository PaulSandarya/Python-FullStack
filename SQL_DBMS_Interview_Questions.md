# SQL & DBMS


## Sample Schema Used in Examples

```sql
CREATE TABLE departments (
    dept_id    INT PRIMARY KEY,
    dept_name  VARCHAR(50) NOT NULL
);

CREATE TABLE employees (
    emp_id      INT PRIMARY KEY,
    emp_name    VARCHAR(100) NOT NULL,
    salary      DECIMAL(10,2),
    dept_id     INT,
    manager_id  INT,
    hire_date   DATE,
    FOREIGN KEY (dept_id)    REFERENCES departments(dept_id),
    FOREIGN KEY (manager_id) REFERENCES employees(emp_id)
);
```

> **Note:** Syntax differs slightly across MySQL, PostgreSQL, SQL Server, and Oracle. Dialect differences are called out where relevant.

---

## 1. DBMS Fundamentals

### Q1. What is a DBMS?
A **Database Management System** is software that lets users create, store, retrieve, update, and manage data in an organized way while providing security, integrity, concurrency control, and recovery. Examples: MySQL, PostgreSQL, Oracle, SQL Server, MongoDB.

### Q2. What is the difference between DBMS and RDBMS?

| Feature | DBMS | RDBMS |
|---------|------|-------|
| Data storage | Files, hierarchical, or navigational | Tables (rows and columns) |
| Relationships | Not necessarily supported | Enforced through keys |
| Normalization | Not supported | Supported |
| ACID | Not always | Generally fully supported |
| Multi-user | Limited | Strong support |
| Examples | File systems, XML stores | MySQL, Oracle, PostgreSQL, SQL Server |

### Q3. What are the advantages of a DBMS over a file system?
- Reduced data redundancy and inconsistency
- Data sharing and concurrent access
- Integrity constraints and security controls
- Backup and recovery mechanisms
- Data independence and a standard query language

### Q4. Explain the three-schema (three-level) architecture.
1. **External level (view):** how individual users see the data.
2. **Conceptual level (logical):** the overall logical structure: entities, relationships, constraints.
3. **Internal level (physical):** how data is stored on disk: files, indexes, access paths.

This separation provides data independence.

### Q5. What is data independence?
- **Physical data independence:** changing the physical storage (e.g., adding an index) without changing the conceptual schema.
- **Logical data independence:** changing the conceptual schema (e.g., adding a column) without affecting external views or applications.

### Q6. What are DDL, DML, DCL, TCL, and DQL?

| Category | Purpose | Commands |
|----------|---------|----------|
| DDL | Define structure | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` |
| DML | Modify data | `INSERT`, `UPDATE`, `DELETE`, `MERGE` |
| DQL | Query data | `SELECT` |
| DCL | Access control | `GRANT`, `REVOKE` |
| TCL | Transaction control | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

### Q7. What is the difference between OLTP and OLAP?

| Aspect | OLTP | OLAP |
|--------|------|------|
| Purpose | Day-to-day transactions | Analysis and reporting |
| Queries | Short, simple, frequent | Complex, aggregated, long-running |
| Schema | Highly normalized | Denormalized (star/snowflake) |
| Data volume | Current data | Historical data |
| Example | Banking, e-commerce orders | Data warehouse, BI dashboards |

### Q8. What is an ER (Entity-Relationship) model?
A conceptual design technique that represents data as **entities** (objects), **attributes** (properties), and **relationships** (associations). ER diagrams are later mapped into relational tables.

### Q9. What types of relationships exist between entities?
- **One-to-One (1:1):** one person has one passport.
- **One-to-Many (1:N):** one department has many employees.
- **Many-to-Many (M:N):** students and courses. Implemented via a junction (bridge) table.

### Q10. Define tuple, attribute, domain, degree, and cardinality.
- **Tuple:** a row.
- **Attribute:** a column.
- **Domain:** the set of permissible values for an attribute.
- **Degree:** the number of attributes (columns) in a relation.
- **Cardinality:** the number of tuples (rows) in a relation.

### Q11. What is the difference between SQL and NoSQL databases?

| Aspect | SQL (Relational) | NoSQL |
|--------|------------------|-------|
| Model | Tables with fixed schema | Document, key-value, column-family, graph |
| Scaling | Primarily vertical (also horizontal with effort) | Designed for horizontal scaling |
| Consistency | Strong (ACID) | Often eventual (BASE), tunable |
| Query language | SQL | Database-specific APIs |
| Best for | Structured data, complex joins, transactions | Flexible schema, massive scale, high write throughput |

### Q12. Explain the CAP theorem.
A distributed data store can guarantee at most **two** of the following three simultaneously:
- **Consistency:** every read returns the latest write.
- **Availability:** every request receives a response.
- **Partition tolerance:** the system keeps working despite network failures.

Since partitions are unavoidable in distributed systems, the real trade-off is between **CP** and **AP** systems.

### Q13. What is a data dictionary (system catalog)?
A repository of **metadata**: table definitions, column types, constraints, indexes, users, and privileges. Examples: `INFORMATION_SCHEMA` (ANSI), `pg_catalog` (PostgreSQL), `ALL_TABLES` (Oracle).

### Q14. What is the difference between a database schema and an instance?
- **Schema:** the logical design or blueprint of the database (structure). It changes rarely.
- **Instance:** the actual data stored at a particular moment. It changes continuously.

### Q15. What is the difference between a database and a data warehouse?
A **database** (OLTP) supports operational transactions with current data. A **data warehouse** consolidates historical data from multiple sources, is optimized for analytical queries, and is typically loaded through ETL/ELT pipelines.

---

## 2. SQL Basics

### Q16. What is SQL?
**Structured Query Language** is the standard language for defining, manipulating, querying, and controlling relational databases. It is declarative: you describe *what* you want, and the optimizer decides *how* to get it.

### Q17. What is the difference between CHAR and VARCHAR?
- `CHAR(n)`: fixed length, padded with spaces. Faster for uniformly sized data (e.g., country codes).
- `VARCHAR(n)`: variable length, stores only the actual characters plus a small length overhead. Better for variable-length text.

### Q18. What is the difference between DELETE, TRUNCATE, and DROP?

| Feature | DELETE | TRUNCATE | DROP |
|---------|--------|----------|------|
| Type | DML | DDL | DDL |
| Removes | Selected or all rows | All rows | Entire table (structure and data) |
| `WHERE` clause | Yes | No | No |
| Rollback | Yes | Depends on DBMS (yes in PostgreSQL/SQL Server within a transaction) | Depends on DBMS |
| Triggers fire | Yes | No | No |
| Identity reset | No | Yes | N/A |
| Speed | Slower (row-by-row logging) | Faster (minimal logging) | Fast |

### Q19. What is the difference between WHERE and HAVING?
- `WHERE` filters **rows before** grouping and cannot use aggregate functions.
- `HAVING` filters **groups after** aggregation and can use aggregate functions.

```sql
SELECT dept_id, AVG(salary) AS avg_sal
FROM employees
WHERE hire_date >= '2020-01-01'
GROUP BY dept_id
HAVING AVG(salary) > 50000;
```

### Q20. What is the logical order of execution of a SQL query?

```
1. FROM / JOIN
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. DISTINCT
7. ORDER BY
8. LIMIT / TOP / OFFSET
```

This is why a column alias defined in `SELECT` cannot be used in `WHERE` (but can be used in `ORDER BY`).

### Q21. How does NULL behave in SQL?
`NULL` represents an unknown or missing value. It is not equal to anything, including another `NULL`.
- Use `IS NULL` / `IS NOT NULL`. `= NULL` always evaluates to UNKNOWN.
- Any arithmetic with `NULL` yields `NULL`.
- Aggregate functions (except `COUNT(*)`) ignore `NULL`.
- SQL uses **three-valued logic**: TRUE, FALSE, UNKNOWN.

### Q22. How do you handle NULL values in output?
```sql
SELECT COALESCE(manager_id, 0) FROM employees;     -- ANSI, all major DBs
SELECT IFNULL(manager_id, 0)   FROM employees;     -- MySQL
SELECT ISNULL(manager_id, 0)   FROM employees;     -- SQL Server
SELECT NVL(manager_id, 0)      FROM employees;     -- Oracle
```
`COALESCE` returns the first non-NULL argument and is the portable choice.

### Q23. Explain the LIKE operator and wildcards.
- `%` matches zero or more characters.
- `_` matches exactly one character.

```sql
SELECT * FROM employees WHERE emp_name LIKE 'A%';     -- starts with A
SELECT * FROM employees WHERE emp_name LIKE '_a%';    -- second letter is a
SELECT * FROM employees WHERE emp_name LIKE '%son';   -- ends with son
```
A leading wildcard (`'%abc'`) prevents normal index usage.

### Q24. What does DISTINCT do?
It removes duplicate rows from the result set based on all selected columns.
```sql
SELECT DISTINCT dept_id FROM employees;
```

### Q25. What is the difference between UNION and UNION ALL?
- `UNION` combines results and **removes duplicates** (requires a sort or hash step).
- `UNION ALL` combines results and **keeps duplicates** (faster).

Both require the same number of columns with compatible data types.

### Q26. What are INTERSECT and EXCEPT (MINUS)?
- `INTERSECT`: rows present in both result sets.
- `EXCEPT` (SQL Server/PostgreSQL) or `MINUS` (Oracle): rows in the first set not present in the second.

MySQL added `INTERSECT` and `EXCEPT` in version 8.0.31.

### Q27. How do you limit the number of rows returned?
```sql
SELECT * FROM employees ORDER BY salary DESC LIMIT 5;                 -- MySQL, PostgreSQL
SELECT TOP 5 * FROM employees ORDER BY salary DESC;                   -- SQL Server
SELECT * FROM employees ORDER BY salary DESC FETCH FIRST 5 ROWS ONLY; -- Oracle 12c+, ANSI
```

### Q28. Explain BETWEEN, IN, and EXISTS.
- `BETWEEN a AND b`: inclusive range check.
- `IN (list or subquery)`: matches any value in a set.
- `EXISTS (subquery)`: returns TRUE if the subquery returns at least one row.

```sql
SELECT * FROM employees WHERE salary BETWEEN 40000 AND 60000;
SELECT * FROM employees WHERE dept_id IN (10, 20);
```

### Q29. What are aliases in SQL?
Temporary names given to columns or tables for readability or to resolve ambiguity (essential in self joins).
```sql
SELECT e.emp_name AS employee, d.dept_name AS department
FROM employees AS e
JOIN departments AS d ON e.dept_id = d.dept_id;
```

### Q30. What is the CASE expression?
SQL's conditional logic construct.
```sql
SELECT emp_name,
       CASE
           WHEN salary >= 100000 THEN 'High'
           WHEN salary >= 50000  THEN 'Medium'
           ELSE 'Low'
       END AS salary_band
FROM employees;
```

---

## 3. Joins, Subqueries and Classic Queries

### Q31. What are the different types of joins?

| Join | Returns |
|------|---------|
| `INNER JOIN` | Only matching rows from both tables |
| `LEFT (OUTER) JOIN` | All rows from the left table, matched rows from the right (NULL if none) |
| `RIGHT (OUTER) JOIN` | All rows from the right table, matched rows from the left |
| `FULL (OUTER) JOIN` | All rows from both tables, NULL where no match |
| `CROSS JOIN` | Cartesian product |
| `SELF JOIN` | A table joined with itself |

MySQL does not support `FULL OUTER JOIN` natively; emulate it with `LEFT JOIN UNION RIGHT JOIN`.

### Q32. What is the difference between INNER JOIN and OUTER JOIN?
`INNER JOIN` discards non-matching rows. `OUTER JOIN` preserves rows from one or both sides even when there is no match, filling the missing side with `NULL`.

### Q33. What is a self join? Give an example.
A join of a table to itself, commonly used for hierarchical data.
```sql
SELECT e.emp_name AS employee, m.emp_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### Q34. What is a CROSS JOIN?
It produces the Cartesian product: every row of table A paired with every row of table B (m × n rows). Used for generating combinations, calendars, or test data.

### Q35. What is a NATURAL JOIN? Why is it discouraged?
It automatically joins on all columns with the same name in both tables. It is risky because adding or renaming a column silently changes the join condition. Prefer explicit `ON` or `USING`.

### Q36. What are the types of subqueries?
- **Scalar subquery:** returns a single value.
- **Row subquery:** returns a single row.
- **Table (derived) subquery:** returns a result set, used in `FROM`.
- **Correlated subquery:** references columns of the outer query.

### Q37. What is a correlated subquery?
A subquery that depends on the outer query and is evaluated (logically) once per outer row.
```sql
-- Employees earning more than their department's average
SELECT e.emp_name, e.salary
FROM employees e
WHERE e.salary > (SELECT AVG(salary)
                  FROM employees
                  WHERE dept_id = e.dept_id);
```

### Q38. What is the difference between IN and EXISTS?
- `IN` compares against a set of values; it is usually fine for small, non-NULL lists.
- `EXISTS` short-circuits at the first match and is often better for large correlated checks.
- `NOT IN` returns no rows if the subquery contains a `NULL`. Prefer `NOT EXISTS` for safety.

### Q39. How do you find duplicate records?
```sql
SELECT emp_name, COUNT(*) AS cnt
FROM employees
GROUP BY emp_name
HAVING COUNT(*) > 1;
```

### Q40. How do you delete duplicate rows while keeping one?
```sql
-- Using ROW_NUMBER (PostgreSQL / SQL Server / MySQL 8+ via CTE)
WITH ranked AS (
    SELECT emp_id,
           ROW_NUMBER() OVER (PARTITION BY emp_name ORDER BY emp_id) AS rn
    FROM employees
)
DELETE FROM employees
WHERE emp_id IN (SELECT emp_id FROM ranked WHERE rn > 1);
```
Syntax for deleting through a CTE varies by DBMS (SQL Server allows `DELETE FROM ranked WHERE rn > 1` directly).

### Q41. How do you find the Nth highest salary?
```sql
-- Using DENSE_RANK (handles ties)
SELECT salary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) t
WHERE rnk = 3;

-- Using LIMIT/OFFSET (MySQL / PostgreSQL)
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 2;
```

### Q42. Find employees who earn more than their manager.
```sql
SELECT e.emp_name
FROM employees e
JOIN employees m ON e.manager_id = m.emp_id
WHERE e.salary > m.salary;
```

### Q43. Find departments that have no employees.
```sql
SELECT d.dept_name
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id
WHERE e.emp_id IS NULL;

-- Alternative
SELECT dept_name FROM departments d
WHERE NOT EXISTS (SELECT 1 FROM employees e WHERE e.dept_id = d.dept_id);
```

---

## 4. Aggregation, Window Functions and CTEs

### Q44. What are aggregate functions?
Functions that compute a single result from a set of rows: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`. They ignore `NULL` values (except `COUNT(*)`).

### Q45. How does GROUP BY work?
It partitions rows into groups based on one or more columns and applies aggregate functions per group. Every non-aggregated column in `SELECT` must appear in `GROUP BY` (strict ANSI behavior).

```sql
SELECT dept_id, COUNT(*) AS headcount, SUM(salary) AS payroll
FROM employees
GROUP BY dept_id;
```

### Q46. What is the difference between COUNT(*), COUNT(column), and COUNT(DISTINCT column)?
- `COUNT(*)`: counts all rows, including those with NULLs.
- `COUNT(column)`: counts non-NULL values in that column.
- `COUNT(DISTINCT column)`: counts unique non-NULL values.

### Q47. What are window functions?
Functions that perform calculations across a set of rows related to the current row **without collapsing rows**, using `OVER (PARTITION BY ... ORDER BY ... frame)`.

```sql
SELECT emp_name, dept_id, salary,
       AVG(salary) OVER (PARTITION BY dept_id) AS dept_avg
FROM employees;
```

### Q48. What is the difference between ROW_NUMBER, RANK, and DENSE_RANK?

| Salary | ROW_NUMBER | RANK | DENSE_RANK |
|--------|-----------|------|------------|
| 90000 | 1 | 1 | 1 |
| 90000 | 2 | 1 | 1 |
| 80000 | 3 | 3 | 2 |
| 70000 | 4 | 4 | 3 |

- `ROW_NUMBER`: unique sequential number, ties broken arbitrarily.
- `RANK`: ties share a rank and the next rank is skipped.
- `DENSE_RANK`: ties share a rank and no gap follows.

### Q49. What are LAG and LEAD?
They access a value from a previous (`LAG`) or following (`LEAD`) row within the partition.
```sql
SELECT emp_id, salary,
       LAG(salary)  OVER (ORDER BY hire_date) AS prev_salary,
       LEAD(salary) OVER (ORDER BY hire_date) AS next_salary
FROM employees;
```

### Q50. How do you calculate a running total?
```sql
SELECT hire_date, salary,
       SUM(salary) OVER (ORDER BY hire_date
                         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM employees;
```

### Q51. What is the difference between PARTITION BY and GROUP BY?
`GROUP BY` collapses rows into one row per group. `PARTITION BY` (inside a window function) keeps every row and computes the aggregate over the partition alongside it.

### Q52. How do you get the top N rows per group?
```sql
-- Top 2 earners in each department
SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
    FROM employees e
) t
WHERE rn <= 2;
```

### Q53. What is a CTE (Common Table Expression)?
A named temporary result set defined with `WITH` that exists for the duration of a single statement. It improves readability and enables recursion.
```sql
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
)
SELECT e.emp_name, e.salary, d.avg_sal
FROM employees e
JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```

### Q54. What is a recursive CTE? Give an example.
A CTE that references itself, used for hierarchies such as org charts and category trees.
```sql
WITH RECURSIVE org AS (
    SELECT emp_id, emp_name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    SELECT e.emp_id, e.emp_name, e.manager_id, o.level + 1
    FROM employees e
    JOIN org o ON e.manager_id = o.emp_id
)
SELECT * FROM org;
```
SQL Server and Oracle omit the `RECURSIVE` keyword.

### Q55. How do you pivot rows into columns?
Using conditional aggregation (portable across databases):
```sql
SELECT dept_id,
       SUM(CASE WHEN EXTRACT(YEAR FROM hire_date) = 2023 THEN 1 ELSE 0 END) AS hired_2023,
       SUM(CASE WHEN EXTRACT(YEAR FROM hire_date) = 2024 THEN 1 ELSE 0 END) AS hired_2024
FROM employees
GROUP BY dept_id;
```
SQL Server and Oracle also provide native `PIVOT` / `UNPIVOT` operators.

### Q56. How do you calculate the median salary?
```sql
-- PostgreSQL / Oracle
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary
FROM employees;

-- Portable approach using window functions
SELECT AVG(salary) AS median_salary
FROM (
    SELECT salary,
           ROW_NUMBER() OVER (ORDER BY salary) AS rn,
           COUNT(*) OVER () AS cnt
    FROM employees
) t
WHERE rn IN (FLOOR((cnt + 1) / 2.0), CEIL((cnt + 1) / 2.0));
```

---

## 5. Keys, Constraints and Normalization

### Q57. What is a primary key?
A column (or set of columns) that uniquely identifies each row. It cannot contain `NULL` and a table can have only one primary key. It typically creates a unique index automatically.

### Q58. What is a foreign key? What are referential actions?
A column that references the primary (or unique) key of another table to enforce referential integrity.

Referential actions on `DELETE`/`UPDATE` of the parent row:
- `CASCADE`: propagate the change to child rows.
- `SET NULL`: set the child's foreign key to `NULL`.
- `SET DEFAULT`: set to the default value.
- `RESTRICT` / `NO ACTION`: block the operation.

```sql
ALTER TABLE employees
ADD CONSTRAINT fk_dept FOREIGN KEY (dept_id)
REFERENCES departments(dept_id) ON DELETE SET NULL;
```

### Q59. Explain super key, candidate key, alternate key, composite key, and surrogate key.

| Key | Meaning |
|-----|---------|
| Super key | Any set of attributes that uniquely identifies a row |
| Candidate key | A minimal super key (no redundant attribute) |
| Primary key | The candidate key chosen as the main identifier |
| Alternate key | Candidate keys not chosen as primary |
| Composite key | A key made of two or more columns |
| Surrogate key | An artificial key with no business meaning (e.g., auto-increment ID) |
| Natural key | A key from real-world data (e.g., email, SSN) |

### Q60. What is the difference between a primary key and a unique key?

| Feature | Primary Key | Unique Key |
|---------|-------------|-----------|
| NULL allowed | No | Yes (number of NULLs varies by DBMS) |
| Per table | One | Many |
| Purpose | Row identity | Uniqueness of a column |

### Q61. What constraints are available in SQL?
`NOT NULL`, `UNIQUE`, `PRIMARY KEY`, `FOREIGN KEY`, `CHECK`, `DEFAULT`.
```sql
CREATE TABLE accounts (
    acc_id   INT PRIMARY KEY,
    balance  DECIMAL(12,2) NOT NULL CHECK (balance >= 0),
    status   VARCHAR(10) DEFAULT 'ACTIVE'
);
```

### Q62. What is normalization and why is it needed?
The process of organizing tables to reduce redundancy and avoid update, insert, and delete anomalies by decomposing relations according to normal forms.

### Q63. Explain 1NF, 2NF, and 3NF.
- **1NF:** all attributes are atomic (no repeating groups or multi-valued cells) and each row is unique.
- **2NF:** in 1NF and no **partial dependency**: every non-key attribute depends on the *whole* primary key (relevant for composite keys).
- **3NF:** in 2NF and no **transitive dependency**: non-key attributes depend only on the key, not on other non-key attributes.

*Mnemonic:* every non-key attribute depends on "the key (1NF), the whole key (2NF), and nothing but the key (3NF)."

### Q64. What is BCNF (Boyce-Codd Normal Form)?
A stricter version of 3NF: for every non-trivial functional dependency `X → Y`, `X` must be a super key. BCNF removes anomalies that 3NF can still permit when a table has overlapping candidate keys.

### Q65. What are 4NF and 5NF?
- **4NF:** no non-trivial multi-valued dependencies other than those on a candidate key.
- **5NF:** no join dependencies that are not implied by candidate keys; the relation cannot be further decomposed without loss of information.

### Q66. What is denormalization and when is it used?
Deliberately introducing redundancy (merging tables, duplicating columns, precomputing aggregates) to improve read performance. Common in reporting, data warehouses, and read-heavy systems. The trade-off is more storage, and the added complexity of keeping redundant data consistent.

### Q67. What is a functional dependency?
`X → Y` means the value of attribute set `X` uniquely determines the value of `Y`. For example, `emp_id → emp_name, salary`.

### Q68. What are insertion, update, and deletion anomalies?
In an unnormalized table (e.g., employee and department details together):
- **Insertion anomaly:** cannot add a department without an employee.
- **Update anomaly:** changing a department name requires updating many rows.
- **Deletion anomaly:** deleting the last employee of a department also loses the department's information.

### Q69. What are Armstrong's axioms?
Inference rules for functional dependencies:
1. **Reflexivity:** if `Y ⊆ X`, then `X → Y`.
2. **Augmentation:** if `X → Y`, then `XZ → YZ`.
3. **Transitivity:** if `X → Y` and `Y → Z`, then `X → Z`.

Derived rules: union, decomposition, and pseudo-transitivity. They are used to compute attribute closure and find candidate keys.

### Q70. How do you generate auto-incrementing identifiers?
```sql
-- MySQL
id INT AUTO_INCREMENT PRIMARY KEY
-- PostgreSQL
id SERIAL PRIMARY KEY            -- or GENERATED ALWAYS AS IDENTITY
-- SQL Server
id INT IDENTITY(1,1) PRIMARY KEY
-- Oracle 12c+
id NUMBER GENERATED ALWAYS AS IDENTITY
-- Sequence-based (Oracle, PostgreSQL, SQL Server 2012+)
CREATE SEQUENCE emp_seq START WITH 1 INCREMENT BY 1;
```
UUIDs are an alternative for globally unique IDs in distributed systems, at the cost of larger index size and poorer locality for random UUIDs.

---

## 6. Indexing and Performance Tuning

### Q71. What is an index and why is it used?
A separate data structure (commonly a B-tree or B+ tree) that allows the database to find rows without scanning the entire table. Indexes speed up reads (`SELECT`, `JOIN`, `ORDER BY`, `WHERE`) but add overhead to writes and consume storage.

### Q72. What is the difference between clustered and non-clustered indexes?

| Feature | Clustered | Non-clustered |
|---------|-----------|---------------|
| Data order | Defines the physical (or logical) order of table rows | Separate structure with pointers to rows |
| Per table | One | Many |
| Leaf level | Contains the actual data rows | Contains key values and row locators |
| Typical default | Primary key (SQL Server, InnoDB) | Secondary indexes |

PostgreSQL tables are heap-organized; its `CLUSTER` command orders data once but does not maintain it.

### Q73. What are the types of indexes?
- **B-tree / B+ tree:** default, good for equality and range queries.
- **Hash:** equality lookups only.
- **Bitmap:** low-cardinality columns, common in data warehouses (Oracle).
- **Full-text:** text search.
- **Spatial (R-tree / GiST):** geographic data.
- **Unique, composite, partial/filtered, covering, function-based.**

### Q74. What is a composite index and the leftmost prefix rule?
An index on multiple columns, e.g., `(dept_id, salary, hire_date)`. It can serve queries filtering on `dept_id`, or `dept_id + salary`, or all three, but generally **not** on `salary` alone. Column order matters; place the most selective or most frequently filtered column first, considering query patterns.

### Q75. What is a covering index?
An index that contains all columns needed by a query, so the database can answer it from the index alone without touching the table (an "index-only scan").
```sql
CREATE INDEX idx_cover ON employees (dept_id) INCLUDE (emp_name, salary);  -- SQL Server / PostgreSQL 11+
```

### Q76. When should you avoid indexes?
- Very small tables.
- Columns with very low selectivity (e.g., boolean) in OLTP workloads.
- Tables with heavy writes and few reads.
- Columns rarely used in filters, joins, or sorts.
- Too many indexes on one table slow down `INSERT`, `UPDATE`, and `DELETE`.

### Q77. How do you analyze a slow query?
Use the execution plan:
```sql
EXPLAIN SELECT * FROM employees WHERE dept_id = 10;            -- MySQL
EXPLAIN (ANALYZE, BUFFERS) SELECT ...;                         -- PostgreSQL
SET SHOWPLAN_ALL ON; / "Display Estimated Execution Plan"      -- SQL Server
EXPLAIN PLAN FOR SELECT ...;                                   -- Oracle
```
Look for full table scans, missing indexes, large row estimates vs. actuals, expensive sorts, nested-loop joins on large inputs, and key lookups.

### Q78. What are best practices for query optimization?
- Select only required columns; avoid `SELECT *`.
- Filter early and use indexed columns in `WHERE` and `JOIN`.
- Avoid functions on indexed columns in predicates.
- Prefer `EXISTS` over `IN` for large correlated checks.
- Use `UNION ALL` instead of `UNION` when duplicates are not a concern.
- Avoid unnecessary `DISTINCT` and `ORDER BY`.
- Paginate with keyset (seek) pagination for large offsets.
- Keep statistics up to date.
- Use batch operations rather than row-by-row processing.

### Q79. What is a SARGable query?
A predicate is **SARGable** (Search ARGument-able) if the optimizer can use an index seek for it. Wrapping a column in a function breaks this.

```sql
-- Non-SARGable
WHERE YEAR(hire_date) = 2024
-- SARGable
WHERE hire_date >= '2024-01-01' AND hire_date < '2025-01-01'
```

### Q80. What are database statistics and the query optimizer?
Statistics describe data distribution (row counts, histograms, distinct values). A **cost-based optimizer** uses them to estimate costs and choose the best execution plan (join order, join type, index choice). Stale statistics lead to poor plans; refresh with `ANALYZE` (PostgreSQL), `UPDATE STATISTICS` (SQL Server), or `ANALYZE TABLE` (MySQL).

### Q81. What is partitioning?
Splitting a large table into smaller physical pieces (partitions) within the same database while presenting a single logical table.
- **Range** (e.g., by date), **List**, **Hash**, **Composite**.
- Benefits: partition pruning for faster queries, easier archival, and better maintenance.

### Q82. What is the difference between sharding and replication?
- **Replication:** copies the *same* data to multiple nodes for high availability and read scaling (primary-replica, multi-primary).
- **Sharding:** splits *different* subsets of data across nodes (horizontal partitioning) to scale writes and storage.
- Large systems often combine both: each shard is replicated.

---

## 7. Transactions and Concurrency

### Q83. What is a transaction?
A logical unit of work consisting of one or more SQL operations that must execute as a whole: either all succeed (`COMMIT`) or all are undone (`ROLLBACK`).

```sql
BEGIN;
UPDATE accounts SET balance = balance - 500 WHERE acc_id = 1;
UPDATE accounts SET balance = balance + 500 WHERE acc_id = 2;
COMMIT;
```

### Q84. Explain ACID properties.
- **Atomicity:** all or nothing.
- **Consistency:** a transaction moves the database from one valid state to another, respecting all constraints.
- **Isolation:** concurrent transactions do not interfere with each other.
- **Durability:** once committed, changes survive crashes (via logging).

### Q85. What are the transaction isolation levels?

| Level | Dirty Read | Non-repeatable Read | Phantom Read |
|-------|:---------:|:-------------------:|:------------:|
| READ UNCOMMITTED | Possible | Possible | Possible |
| READ COMMITTED | No | Possible | Possible |
| REPEATABLE READ | No | No | Possible (prevented in MySQL InnoDB and PostgreSQL) |
| SERIALIZABLE | No | No | No |

Defaults: READ COMMITTED (PostgreSQL, SQL Server, Oracle) and REPEATABLE READ (MySQL InnoDB).

### Q86. Define dirty read, non-repeatable read, and phantom read.
- **Dirty read:** reading uncommitted data from another transaction.
- **Non-repeatable read:** the same row read twice returns different values because another transaction committed an update in between.
- **Phantom read:** the same query run twice returns a different *set of rows* because another transaction inserted or deleted matching rows.

### Q87. What types of locks exist?
- **Shared (S) lock:** for reads; multiple holders allowed.
- **Exclusive (X) lock:** for writes; no other locks allowed.
- **Update (U) lock:** (SQL Server) prevents conversion deadlocks.
- **Intent locks:** signal locking at a finer granularity (IS, IX).
- **Granularity:** row, page, table, database. Finer granularity gives higher concurrency but more overhead.

### Q88. What is a deadlock and how is it handled?
A deadlock occurs when two or more transactions each hold a lock the other needs, so none can proceed.

**Handling:**
- The DBMS detects cycles (wait-for graph) and aborts a *victim* transaction.
- **Prevention:** access resources in a consistent order, keep transactions short, use lower isolation levels where acceptable, and set lock timeouts.
- Applications should retry after a deadlock error.

### Q89. What is MVCC?
**Multi-Version Concurrency Control** keeps multiple versions of a row so readers see a consistent snapshot without blocking writers, and writers do not block readers. Used by PostgreSQL, Oracle, MySQL InnoDB, and SQL Server (snapshot isolation). Old versions are cleaned up by vacuum/purge processes.

### Q90. What is the difference between optimistic and pessimistic locking?
- **Pessimistic:** lock the data up front (`SELECT ... FOR UPDATE`) assuming conflicts are likely.
- **Optimistic:** do not lock; check at commit time (e.g., a `version` column) and retry on conflict. Suited for low-contention workloads.

```sql
UPDATE products SET stock = stock - 1, version = version + 1
WHERE product_id = 7 AND version = 12;  -- 0 rows affected means a conflict
```

### Q91. What are COMMIT, ROLLBACK, and SAVEPOINT?
- `COMMIT`: makes changes permanent.
- `ROLLBACK`: undoes changes since the last commit (or to a savepoint).
- `SAVEPOINT name`: marks a point to which a partial rollback can occur (`ROLLBACK TO name`).

### Q92. How does a database ensure durability and crash recovery?
Through **Write-Ahead Logging (WAL)**: log records are flushed to disk before the data pages are modified. On restart, the recovery process uses **checkpoints** and the ARIES approach (analysis, redo, undo) to reapply committed work and roll back uncommitted work.

---

## 8. Advanced Topics

### Q93. What is the difference between a view and a materialized view?

| Feature | View | Materialized View |
|---------|------|-------------------|
| Storage | Stores only the query | Stores the result set physically |
| Freshness | Always current | Must be refreshed (manual, scheduled, or incremental) |
| Performance | Runs query each time | Fast reads |
| Use case | Simplification, security, abstraction | Heavy aggregations and reporting |

```sql
CREATE VIEW high_earners AS
SELECT emp_id, emp_name, salary FROM employees WHERE salary > 100000;
```

### Q94. What is the difference between a stored procedure and a function?

| Feature | Stored Procedure | Function |
|---------|------------------|----------|
| Return value | Optional (via OUT parameters or result sets) | Must return a value |
| Usage in `SELECT` | Cannot be called inline | Can be used inside queries |
| DML operations | Allowed | Restricted in many DBMS |
| Transaction control | Allowed | Generally not |

Stored procedures reduce network round trips, centralize logic, and can improve security by limiting direct table access.

### Q95. What is a trigger?
A database object that automatically executes in response to an event (`INSERT`, `UPDATE`, `DELETE`) on a table, either `BEFORE`/`AFTER` (or `INSTEAD OF`). Used for auditing, validation, and derived data. Overuse makes logic hard to trace and can hurt performance.

```sql
CREATE TRIGGER trg_salary_audit
AFTER UPDATE ON employees
FOR EACH ROW
INSERT INTO salary_audit(emp_id, old_salary, new_salary, changed_at)
VALUES (OLD.emp_id, OLD.salary, NEW.salary, NOW());   -- MySQL syntax
```

### Q96. What is a cursor? Why is it discouraged?
A cursor processes a result set row by row. It is slow compared with set-based operations because of per-row overhead and locking. Prefer set-based SQL (joins, window functions, bulk updates) and use cursors only when row-wise logic is unavoidable.

### Q97. What is the difference between temporary tables, table variables, and CTEs?
- **Temp table (`#temp`, `CREATE TEMP TABLE`):** physically stored in tempdb or temp space, supports indexes and statistics, and persists for the session.
- **Table variable (SQL Server):** scoped to the batch, with limited statistics.
- **CTE:** a query-scoped named result set; not materialized in all engines and not reusable across statements.

### Q98. What is SQL injection and how do you prevent it?
An attack where malicious SQL is inserted through user input, for example `' OR '1'='1`.

**Prevention:**
- Use **parameterized queries / prepared statements**.
- Use ORMs correctly and avoid dynamic SQL string concatenation.
- Validate and sanitize input.
- Apply the principle of least privilege to database accounts.
- Hide detailed error messages from users.

```python
cursor.execute("SELECT * FROM users WHERE email = %s", (email,))
```

### Q99. What are the types of database backups?
- **Full:** a complete copy of the database.
- **Differential:** changes since the last full backup.
- **Incremental:** changes since the last backup of any type.
- **Transaction log backup:** enables point-in-time recovery.
- **Logical (dump) vs. physical (file-level/snapshot).**

Key metrics: **RPO** (Recovery Point Objective: acceptable data loss) and **RTO** (Recovery Time Objective: acceptable downtime). Backups must be tested by restoring them.

### Q100. What are the types of NoSQL databases and when should you choose them?

| Type | Examples | Use cases |
|------|----------|-----------|
| Document | MongoDB, Couchbase | Content management, catalogs, user profiles |
| Key-Value | Redis, DynamoDB | Caching, sessions, leaderboards |
| Column-family | Cassandra, HBase | Time series, high write throughput, IoT |
| Graph | Neo4j, Amazon Neptune | Social networks, recommendations, fraud detection |

Choose NoSQL when you need flexible schemas, horizontal scale, or very low latency at high volume. Choose a relational database when you need complex joins, strong consistency, and mature transactional guarantees. Many modern architectures use both (polyglot persistence).

---

## Quick Revision Checklist

- [ ] Write joins (all types) and self joins from memory
- [ ] Solve Nth highest salary, duplicates, and top-N-per-group problems
- [ ] Explain `ROW_NUMBER` vs `RANK` vs `DENSE_RANK`
- [ ] Normalize a table through 1NF, 2NF, 3NF, and BCNF
- [ ] Explain clustered vs non-clustered indexes and the leftmost prefix rule
- [ ] Describe ACID, isolation levels, and the anomalies each prevents
- [ ] Read an execution plan and name two optimization techniques
- [ ] Explain SQL vs NoSQL and the CAP theorem with examples

---

## Contributing

Contributions are welcome. To suggest an improvement:

1. Fork the repository.
2. Create a feature branch.
3. Submit a pull request with a clear description of the change.

## License

This content is released under the MIT License. See `LICENSE` for details.
