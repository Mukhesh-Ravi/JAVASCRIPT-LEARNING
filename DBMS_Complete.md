# DBMS — Complete Notes (OA Level)

## 1. Introduction
- **Data** vs **Information** vs **Database** vs **DBMS** vs **RDBMS**.
- **Advantages of DBMS over File System**: reduced redundancy, data consistency, data isolation solved, integrity constraints, concurrent access, security, backup/recovery, data independence.

### Data Independence
- **Logical Data Independence** — change conceptual schema without changing external schema/application.
- **Physical Data Independence** — change internal (storage) schema without changing conceptual schema.

### Three-Schema Architecture
Internal (physical storage) → Conceptual (logical, whole DB structure) → External (user views).

### Database Users
DBA, Application programmers, Sophisticated users (write SQL directly), Naive users (use GUI/apps).

## 2. ER Model (Detailed)

### Components
- **Entity** — object with independent existence (Strong Entity has its own primary key; Weak Entity depends on owner entity, identified by partial key + owner's key).
- **Attribute types**: Simple, Composite (Name → First+Last), Single-valued, Multi-valued (Phone numbers), Derived (Age from DOB), Key attribute, Null attribute.
- **Relationship**: degree (unary/binary/ternary), cardinality (1:1, 1:N, M:N), participation (total = double line, partial = single line).

### Extended ER Features
- **Generalization** — combining lower-level entities into higher-level (bottom-up).
- **Specialization** — dividing higher-level entity into sub-entities (top-down).
- **Aggregation** — treating a relationship as a higher-level entity (used when relationship needs its own relationship).
- **Disjoint vs Overlapping** specialization; **Total vs Partial** specialization.

### ER to Relational Mapping Rules
1. Strong entity → table with all simple attributes; composite attribute flattened; multivalued attribute → separate table with FK.
2. Weak entity → table includes owner's primary key as part of its own primary key (with partial key).
3. 1:1 relationship → FK on either side (or merge tables).
4. 1:N relationship → FK on the "many" side referencing "one" side's PK.
5. M:N relationship → separate junction/bridge table with both PKs as composite key (+ relationship attributes if any).
6. Multivalued attribute → new table with FK to owner entity.

## 3. Keys (Detailed)
Super Key, Candidate Key, Primary Key, Alternate Key, Foreign Key, Composite Key, Surrogate Key (artificial key, e.g. auto-increment ID with no business meaning).

## 4. Relational Algebra (Important for OA!)
| Operation | Symbol | Description |
|---|---|---|
| Select | σ (sigma) | Filters rows based on condition, σ_condition(R) |
| Project | π (pi) | Selects columns, π_columns(R) |
| Union | ∪ | Combines tuples from two relations (must be union-compatible) |
| Set Difference | − | Tuples in R not in S |
| Intersection | ∩ | Tuples in both R and S |
| Cartesian Product | × | All combinations of tuples |
| Join | ⋈ | Combines related tuples from two relations |
| Rename | ρ (rho) | Renames relation/attributes |
| Divide | ÷ | Used for "for all" queries |

### Types of Joins in Relational Algebra
- **Theta Join** — join with any condition (θ).
- **Equijoin** — theta join using only equality.
- **Natural Join (⋈)** — equijoin on common attribute names, duplicate column removed automatically.
- **Outer Join** — Left, Right, Full — includes unmatched tuples with NULLs.

## 5. Functional Dependencies & Normalization (Detailed)

### FD Notation
X → Y: value of X uniquely determines value of Y. X is the determinant.

### Armstrong's Axioms (inference rules)
1. **Reflexivity**: if Y ⊆ X, then X → Y.
2. **Augmentation**: if X → Y, then XZ → YZ.
3. **Transitivity**: if X → Y and Y → Z, then X → Z.
Derived rules: Union, Decomposition, Pseudo-transitivity.

### Closure of Attribute Set (X+)
Set of all attributes functionally determined by X — used to find candidate keys and check if a FD is implied.

### Normal Forms (with example logic)
- **1NF** — atomic values, no repeating groups/multivalued attributes in a single cell.
- **2NF** — 1NF + no **partial dependency** (non-prime attribute depends on part of a composite candidate key). Only relevant when PK is composite.
- **3NF** — 2NF + no **transitive dependency** (non-prime attribute depends on another non-prime attribute, not directly on key).
- **BCNF (Boyce-Codd NF)** — for every non-trivial FD X→Y, X must be a super key. Stricter than 3NF; every BCNF relation is in 3NF but not vice versa.
- **4NF** — BCNF + no **multivalued dependency** (MVD) unless it's a trivial one. MVD: X →→ Y (X determines a SET of Y values independent of other attributes).
- **5NF (PJNF)** — no join dependency that isn't implied by candidate keys; relation cannot be decomposed further without loss.

### Decomposition Properties
- **Lossless Join Decomposition** — decomposed relations, when joined back, give exactly original relation (no spurious tuples). Test: common attribute must be a key in at least one of the decomposed relations.
- **Dependency Preservation** — all original FDs can be verified/enforced without needing to join the decomposed tables.

## 6. Transactions (Detailed)

### ACID
- Atomicity — via logging/undo.
- Consistency — application + DB constraint responsibility.
- Isolation — via concurrency control mechanisms (locking, timestamping).
- Durability — via write-ahead logging, checkpoints.

### Transaction States
Active → Partially Committed → Committed (success path)
Active → Failed → Aborted (failure path)
Aborted → (restart or kill)

### Schedules (Detailed)
- **Serial Schedule** — no interleaving.
- **Non-serial (Concurrent) Schedule** — interleaved.
- **Conflict Operations** — two operations conflict if: different transactions, same data item, at least one is a write.
- **Conflict Equivalent** — schedules with same order of conflicting operations.
- **Conflict Serializable** — can be transformed to a serial schedule by swapping non-conflicting operations. Test via **Precedence Graph**: node per transaction, edge Ti→Tj if Ti's operation precedes and conflicts with Tj's on same data item. **Acyclic graph = conflict serializable**.
- **View Serializable** — weaker; same initial read, same final write, same read-from relationships as some serial schedule. Every conflict serializable schedule is view serializable, not vice versa.
- **Recoverable Schedule** — Tj can commit only after Ti commits (if Tj read data written by Ti).
- **Cascadeless Schedule** — Tj can read data written by Ti only after Ti commits (prevents cascading rollback).
- **Strict Schedule** — Tj cannot read OR write an item written by Ti until Ti commits/aborts (strongest, easiest to recover, most used in practice).

Relationship: Serial ⊂ Strict ⊂ Cascadeless ⊂ Recoverable ⊂ All schedules.

## 7. Concurrency Control Protocols (Detailed)

### Lock-Based Protocols
- **Shared (S) Lock** — for reading, multiple transactions can hold simultaneously.
- **Exclusive (X) Lock** — for writing, only one transaction.
- Compatibility: S-S compatible, S-X and X-X not compatible.

### Two-Phase Locking (2PL)
- **Growing Phase** — transaction can acquire locks, cannot release.
- **Shrinking Phase** — transaction can release locks, cannot acquire.
- Guarantees conflict serializability but NOT deadlock-free.
- **Strict 2PL** — all exclusive locks held until commit/abort (prevents cascading rollback).
- **Rigorous 2PL** — ALL locks (S and X) held until commit/abort (easiest to implement, most restrictive).

### Deadlock Handling in DB
- **Wait-Die** (non-preemptive) — older transaction waits for younger; younger requesting a lock held by older dies (restarts).
- **Wound-Wait** (preemptive) — older transaction "wounds" (preempts) younger holding the lock; younger requesting lock held by older waits.

### Timestamp-Based Protocols
Each transaction gets a unique timestamp (TS) at start; ensures serializability equivalent to timestamp order.
- **Read/Write Timestamp rules**: reject/rollback operations that violate timestamp order (e.g., a transaction tries to write a value already read by a "younger" transaction).

### Multiversion Concurrency Control (MVCC)
Maintains multiple versions of data item, readers access an appropriate older version without blocking writers — used in PostgreSQL, MySQL (InnoDB), Oracle.

### Validation (Optimistic) Concurrency Control
3 Phases: Read phase (work on local copy) → Validation phase (check for conflicts) → Write phase (commit if valid). Good when conflicts are rare.

## 8. Database Recovery
- **Log-based recovery** — Write-Ahead Logging (WAL): log record written to disk BEFORE actual data page is modified on disk.
  - **Undo/Redo logging** — log has old & new value; on crash, redo committed transactions, undo uncommitted.
- **Checkpointing** — periodic saving of consistent state to reduce recovery time (don't need to redo from the very beginning).
- **Shadow Paging** — maintains two page tables (current & shadow); avoids need for logging but has overhead and fragmentation issues; hard with concurrent transactions.

## 9. Indexing (Detailed)

### B-Tree vs B+ Tree
- **B-Tree** — data can be stored in internal AND leaf nodes.
- **B+ Tree** — data stored ONLY in leaf nodes; leaves linked as a list (great for range queries); internal nodes only have keys for navigation. Most DBs use B+ trees.

### Index Types
- **Primary Index** — built on primary key (ordering field); can be **Dense** (entry for every record) or **Sparse** (entry for some records, e.g. one per block).
- **Secondary Index** — built on non-ordering field; always dense (since data not sorted by this field).
- **Clustering Index** — built on non-key ordering field.
- **Multilevel Index** — index on the index, reduces number of disk accesses (like B+ tree levels).
- **Hash Index** — good for equality search, bad for range queries.

## 10. SQL (Comprehensive)

### Order of Execution (important for OA tricky questions!)
`FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`

### DDL / DML / DCL / TCL
- DDL: CREATE, ALTER, DROP, TRUNCATE, RENAME (auto-commit).
- DML: SELECT, INSERT, UPDATE, DELETE.
- DCL: GRANT, REVOKE.
- TCL: COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION.

### DELETE vs TRUNCATE vs DROP
| | DELETE | TRUNCATE | DROP |
|---|---|---|---|
| Type | DML | DDL | DDL |
| WHERE clause | Yes | No | No |
| Rollback | Yes (if not committed) | Usually No (auto-commit) | No |
| Removes structure | No | No | Yes |
| Resets identity/auto-increment | No | Yes | — |

### Joins (Detailed with behavior)
- INNER JOIN — only matching rows.
- LEFT (OUTER) JOIN — all left + matched right, NULL for unmatched.
- RIGHT (OUTER) JOIN — all right + matched left.
- FULL (OUTER) JOIN — all rows, NULL where no match (not supported directly in MySQL — simulate with UNION of LEFT and RIGHT).
- CROSS JOIN — Cartesian product, no condition.
- SELF JOIN — table joined with itself using aliases.
- NATURAL JOIN — auto-joins on same-named columns.

### Subqueries
- **Single-row / Multi-row / Multi-column subqueries**.
- **Correlated subquery** — inner query references outer query's table, executes once per outer row (slower).
- **Non-correlated subquery** — executes independently once.
- Operators used with subqueries: `IN`, `ANY`, `ALL`, `EXISTS`, `NOT EXISTS`.

### Set Operations
- `UNION` — combines, removes duplicates.
- `UNION ALL` — combines, keeps duplicates (faster).
- `INTERSECT` — common rows.
- `MINUS`/`EXCEPT` — rows in first not in second.

### Views
Virtual table based on a query; doesn't store data (usually); simplifies complex queries, adds security layer (restrict columns). **Materialized View** — actually stores result physically, needs refreshing.

### Triggers
Block of code automatically executed in response to events (INSERT/UPDATE/DELETE) on a table. Types: BEFORE, AFTER, INSTEAD OF; Row-level vs Statement-level.

### Stored Procedures vs Functions
- **Procedure** — can perform actions (DML), may or may not return value, called using CALL/EXEC.
- **Function** — must return a value, can be used inside SQL expressions/SELECT statements, generally can't perform DML in standard SQL.

### Cursors
Used to process query result set row-by-row (used inside procedures for row-wise operations).

### Aggregate Functions
COUNT, SUM, AVG, MIN, MAX — ignore NULLs (except COUNT(*)).
- `GROUP BY` groups rows; `HAVING` filters groups (after aggregation); `WHERE` filters rows (before aggregation).

### Constraints
NOT NULL, UNIQUE, PRIMARY KEY, FOREIGN KEY (with ON DELETE CASCADE/SET NULL/RESTRICT), CHECK, DEFAULT.

## 11. NoSQL & Distributed DB Concepts
| Type | Examples | Use case |
|---|---|---|
| Key-Value | Redis, DynamoDB | Caching, session storage |
| Document | MongoDB, CouchDB | Flexible JSON-like data |
| Column-family | Cassandra, HBase | Large-scale, write-heavy |
| Graph | Neo4j | Relationship-heavy data |

### CAP Theorem
In presence of network **Partition**, must choose between **Consistency** and **Availability** (can't have all 3 simultaneously in distributed system).

### BASE (vs ACID)
Basically Available, Soft state, Eventual consistency — typical NoSQL model, trade strict consistency for availability/scalability.

## 12. OLTP vs OLAP
| OLTP | OLAP |
|---|---|
| Online Transaction Processing | Online Analytical Processing |
| Day-to-day operations | Data analysis/reporting |
| Normalized schema | Denormalized (star/snowflake schema) |
| Short, frequent transactions | Complex, long queries |

## 13. Common Traps / OA Gotchas
- `HAVING` without `GROUP BY` is valid (treats whole table as one group).
- `NULL` comparisons: `NULL = NULL` is `UNKNOWN`, not true — must use `IS NULL`.
- `COUNT(column)` ignores NULLs, `COUNT(*)` counts all rows.
- A candidate key can be composite; primary key is just ONE chosen candidate key.
- BCNF may not always be dependency-preserving; 3NF always is (by definition of the 3NF synthesis algorithm).
- In lossless decomposition, the common attribute between two decomposed relations must be a candidate key of at least one relation.
