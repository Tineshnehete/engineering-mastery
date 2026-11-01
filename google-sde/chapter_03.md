# Chapter 03: Databases 101 — Tables, Indexing & Transactions (Deep Dive)

> **Welcome to Chapter 03!**
> Every application in the world—from Instagram to Uber to your local grocery app—is essentially a user interface sitting on top of a database.
> 
> In technical interviews, interviewers don't just ask *"how to write a SQL query"*. They ask: **"Why is this query slow?", "What happens to the disk when an index is created?", "What are the 4 Transaction Isolation Levels?", and "How does the database resolve deadlocks?"**
> 
> In this chapter, we will demystify databases from the physical disk blocks up to high-performance SQL indexing and ACID transactions.

---

## 1. Why Do Databases Exist? (RAM vs. Disk Persistence)

Why can't we just store all our app data in a Python dictionary or JavaScript object in memory?
```javascript
// Why not just do this?
const database = {};
database["user_101"] = { name: "Raj", balance: 5000 };
```

Two physical reasons:
1. **RAM is Volatile**: If someone trips over the power cable in the AWS datacenter, all RAM memory is instantly wiped to zero. Your users' bank balances are gone!
2. **RAM is Expensive & Small**: 1 Terabyte of RAM costs thousands of dollars; 1 Terabyte of NVMe SSD storage costs less than $80.

```mermaid
flowchart TD
    App["Your Backend App"] -->|"Fast queries in RAM"| RAM["RAM (Volatile / Fast)<br/>Power loss = Instant Data Loss!"]
    App -->|"Writes to persistent disk"| Disk["Database Engine on SSD (Non-Volatile)<br/>Data survives reboots & crashes"]
```

A **Database Management System (DBMS)** (like PostgreSQL or MySQL) is an engine that gives you the best of both worlds:
* It keeps hot data in RAM caches for speed.
* It guarantees that every committed change is safely written to permanent SSD storage.

---

## 2. The Write-Ahead Log (WAL): How Databases Never Lose Data

Writing data randomly across a 500GB database table on disk is very slow. 

If a user makes a payment, the database can't afford to spend 20 milliseconds searching the disk for where to write the new row before sending a confirmation to the user.

How do modern databases write in **under 1 millisecond** without risking data loss?
They use an append-only journal called the **WAL (Write-Ahead Log)**.

```mermaid
sequenceDiagram
    autonumber
    actor User as User
    participant App as Backend App
    participant WAL as Write-Ahead Log (Sequential SSD Append)
    participant Buffer as RAM Buffer Pool
    participant Table as Main Data Table on Disk

    User->>App: Pay ₹500
    App->>WAL: 1. Append transaction log: "Account 101 -= 500"
    Note over WAL: Sequential write is instant! (~0.1 ms)
    App->>Buffer: 2. Update cached row in RAM
    App-->>User: 3. Return HTTP 200 "Success!"
    
    Note over Table: In background (every few seconds),<br/>Database flushes dirty RAM pages to Disk.
```

### The Power Outage Scenario
What happens if the power cuts at Step 2.5?
* The RAM was wiped clean.
* But the **WAL on disk is safe**!
* When the database boots back up, it reads the WAL journal and replays the transaction. **Zero money is lost!**

---

## 3. SQL Joins Under the Hood: How DB Engines Actually Join

When you write `SELECT * FROM users JOIN orders ON users.id = orders.user_id`, how does the database physically combine millions of rows?

```mermaid
flowchart TD
    subgraph JoinAlgos["3 Core Join Algorithms Inside the DB Engine"]
        A1["1. Nested Loop Join<br/>(For small tables: For each row in A, loop through B)"]
        A2["2. Hash Join<br/>(Builds in-memory Hash Table of smaller table in RAM)"]
        A3["3. Merge Join<br/>(Both tables sorted on join key; marches pointers together)"]
    end
```

### The 3 Physical Join Algorithms:
1. **Nested Loop Join**:
   * Like a nested `for` loop in code.
   * Best when one table is tiny (e.g., 10 rows) and the other table has an index on the join key.
2. **Hash Join**:
   * The database takes the smaller table, hashes its join keys into RAM, and scans the larger table once.
   * Extremely fast for large, unsorted datasets.
3. **Merge Join**:
   * If both tables are already sorted on the join key (e.g., by a B-Tree index), the database advances two pointers simultaneously in **O(N + M)** linear time.

---

## 4. The Magic of Indexing: Full Table Scan vs. B-Tree

Imagine a phonebook with **10,000,000 users**. You want to find `"Vikram Sharma"`.

### Case A: No Index (Full Table Scan — O(N))
If the phonebook is completely unsorted, you must open page 1, page 2, page 3... and inspect every single name until you find Vikram.
* On a disk with 10M rows, this takes **15 seconds** of continuous disk grinding!

### Case B: With a B-Tree Index (O(log N))
The database maintains an ordered, multi-level tree index on the `name` column.

```mermaid
flowchart TD
    Root["Root Node<br/>Names: [H, P]"]
    
    Root -->|"Name < H"| L1["Branch 1: [A - G]"]
    Root -->|"H <= Name < P"| L2["Branch 2: [H - O]"]
    Root -->|"Name >= P"| L3["Branch 3: [P - Z]"]
    
    L3 -->|"V is between T and Z"| Leaf["Leaf Page: 'Vikram Sharma'<br/>Points to: Disk Block #9482"]
    Leaf --> Record["Physical Table Row: Disk Block #9482<br/>{ id: 4120, name: 'Vikram Sharma', email: '...' }"]
```

### Why Can't We Index Every Column?
1. **Writes become very slow**: Every `INSERT`, `UPDATE`, or `DELETE` must rebalance all B-Trees on disk.
2. **Storage explodes**: Indexes can consume more disk space than the actual table itself!

---

## 5. The "Leftmost Prefix Rule" for Composite Indexes

Imagine you create a **Composite Index** on two columns:
```sql
CREATE INDEX idx_user ON users (country, city);
```

Think of an English dictionary:
* Words are sorted by first letter, then second letter (`a` $\to$ `b` $\to$ `c`).
* Can you easily find all words where the **second letter is 'z'**?
* No! You would have to read every single word in the dictionary!
* **The Rule**: A composite index `(A, B)` only works if your query includes the **leftmost column (`A`)**.

---

## 6. The 4 Transaction Isolation Levels (Deep Dive)

In ACID, the **`I` (Isolation)** prevents concurrent transactions from interfering with each other.

SQL defines **4 Isolation Levels**:

```mermaid
flowchart TD
    L1["1. Read Uncommitted (Fastest / Least Safe)<br/>Allows Dirty Reads!"]
    L2["2. Read Committed (Postgres Default)<br/>Prevents Dirty Reads."]
    L3["3. Repeatable Read<br/>Guarantees same row reads across transaction."]
    L4["4. Serializable (Slowest / 100% Strict)<br/>Transactions run as if in single-file line!"]
    
    L1 --> L2 --> L3 --> L4
```

### The 3 Database Phenomena (Bugs):
1. **Dirty Read**: Transaction 2 reads changes made by Transaction 1 *before* Transaction 1 commits. If Transaction 1 rolls back, Transaction 2 acted on fake ghost data!
2. **Non-Repeatable Read**: Transaction 1 reads a row (balance = ₹1000). Transaction 2 commits an update (balance = ₹500). Transaction 1 reads the exact same row again and sees a different number!
3. **Phantom Read**: Transaction 1 queries `WHERE salary > 50000` (finds 5 rows). Transaction 2 inserts a 6th employee. Transaction 1 queries again and finds a "phantom" 6th row!

### Isolation Levels Comparison Table:

| Isolation Level | Prevents Dirty Read? | Prevents Non-Repeatable Read? | Prevents Phantom Read? |
|---|---|---|---|
| **Read Uncommitted** | ❌ No | ❌ No | ❌ No |
| **Read Committed** *(Default)* | ✅ **Yes** | ❌ No | ❌ No |
| **Repeatable Read** | ✅ **Yes** | ✅ **Yes** | ❌ No *(in standard SQL)* |
| **Serializable** | ✅ **Yes** | ✅ **Yes** | ✅ **Yes** |

---

## 7. Database Deadlocks: The Lock Standoff

What happens when two concurrent transactions wait for each other in a circle?

```mermaid
sequenceDiagram
    participant T1 as Transaction 1
    participant T2 as Transaction 2
    
    T1->>T1: Locks Account A (UPDATE A)
    T2->>T2: Locks Account B (UPDATE B)
    T1->>T2: T1 wants to update B... WAITS FOR T2!
    T2->>T1: T2 wants to update A... WAITS FOR T1!
    Note over T1,T2: DEADLOCK! Both transactions freeze forever!
```

### How Databases Resolve Deadlocks:
The database maintains a background thread checking the **Wait-For Graph** for cycles.
* When it detects a circular lock, the database picks one transaction as the **"Deadlock Victim"**.
* It aborts the victim with error `ERROR: deadlock detected`, releasing its locks so the other transaction can complete!
* **Best Practice**: In backend code, always acquire locks in the **exact same alphabetical order** across all transactions to prevent cycles!

---

## 8. Code Examples: Transactions in Python & JavaScript

### Python (psycopg2 / SQLAlchemy):
```python
import psycopg2

conn = psycopg2.connect("dbname=bank user=postgres password=secret")
cursor = conn.cursor()

try:
    cursor.execute("UPDATE accounts SET balance = balance - 1000 WHERE id = 101;")
    cursor.execute("UPDATE accounts SET balance = balance + 1000 WHERE id = 202;")
    conn.commit()
    print("Transfer committed atomically!")
except Exception as e:
    conn.rollback()
    print(f"Transfer aborted! Reverted all changes: {e}")
finally:
    cursor.close()
    conn.close()
```

### Node.js (pg):
```javascript
const { Client } = require('pg');
const client = new Client({ connectionString: "postgresql://postgres:secret@localhost:5432/bank" });

async function transferMoney(fromId, toId, amount) {
    await client.connect();
    try {
        await client.query('BEGIN'); // Start transaction
        await client.query('UPDATE accounts SET balance = balance - $1 WHERE id = $2', [amount, fromId]);
        await client.query('UPDATE accounts SET balance = balance + $1 WHERE id = $2', [amount, toId]);
        await client.query('COMMIT');
        console.log("Transfer successful!");
    } catch (err) {
        await client.query('ROLLBACK');
        console.error("Transfer failed, rolled back:", err.message);
    } finally {
        await client.end();
    }
}
```

---

## 9. Production Case Studies, System Drifts & Interview Traps

### Production Case Study 1: The Missing Composite Index Database Meltdown
* **The Incident**: A social media platform deployed a new feature displaying user orders: `SELECT * FROM orders WHERE user_id = 142 AND status = 'PENDING' ORDER BY created_at DESC LIMIT 10`. The table had 250,000,000 rows. Within 4 minutes of deployment, database CPU utilization surged to 100%, disk read IOPS hit maximum capacity, and database connection pools across all 40 backend microservices were exhausted. All customer checkouts ground to a halt.
* **Root Cause**: The table only had an index on `status`. Because 20% of orders were `'PENDING'`, the query engine performed a **Bitmap Index Scan filtering 50,000,000 rows into memory and sorting on disk**!
* **The Fix**:
  ```sql
  -- The Optimal Composite Index (Equality first, then Range/Sort):
  CREATE INDEX CONCURRENTLY idx_orders_user_status_created 
  ON orders (user_id, status, created_at DESC);
  ```
  Execution time dropped from **14,200 ms** to **0.4 ms** ($35,000\times$ speedup). Notice the use of `CONCURRENTLY`—running `CREATE INDEX` without it locks writes to the table!

### Production Case Study 2: The Postgres 2-Billion Transaction ID (TXID) Wraparound Shutdown
* **The Incident**: At a fintech startup handling millions of ledger writes, PostgreSQL suddenly refused to accept any `INSERT` or `UPDATE` queries, logging: `PANIC: database is not accepting commands to avoid wraparound data loss in database "postgres"`. The primary database was completely dead for 14 hours.
* **Root Cause**: PostgreSQL uses a 32-bit transaction identifier (`txid`). Because $2^{31} \approx 2.14$ billion transactions, PostgreSQL uses a circular comparison logic. If old dead rows are not frozen by the background **Autovacuum daemon**, past transactions wrap around and appear to be in the *future*, threatening silent data loss. To prevent corruption, Postgres forcefully halts all write traffic.
* **The Fix**:
  1. Never disable or throttle `autovacuum` on high-write systems.
  2. Actively monitor the metric `SELECT max(age(datfrozenxid)) FROM pg_database;` and alert when it crosses 1.2 billion.

### Production System Drifts to Watch:
1. **Replication Lag Drift (Read-After-Write Inconsistency)**:
   In high-scale architectures, writes go to the Primary DB while reads go to Read Replicas. If a replica is 800ms behind due to network or write load:
   * A user updates their shipping address on the checkout page (writes to Primary).
   * User is redirected to confirmation page (reads from Replica).
   * User screams: *"Why does it still show my old address?! Did my update fail?!"*
   * **Fix**: Route reads to the Primary DB immediately following a mutation (Session Sticky Routing), or enforce read-after-write consistency tokens.
2. **Schema Drift & DDL Lock Queues**:
   Running `ALTER TABLE users ADD COLUMN phone VARCHAR(20) DEFAULT 'N/A';` in older databases acquires an **`ACCESS EXCLUSIVE` lock**. Even if the query only takes 500ms, all incoming reads and writes queue behind it. Once the connection pool reaches maximum capacity (`max_connections = 500`), the entire backend crashes.
   * **Fix**: Always set lock timeouts (`SET lock_timeout = '2s';`), add nullable columns without defaults first, backfill in batches, and avoid holding exclusive locks.

### FAANG Interview Scenarios & Traps:

```mermaid
flowchart LR
    subgraph DeadlockCycle["Wait-For Graph (Deadlock Cycle)"]
        T1["Transaction 1 (Holds Lock on Row A)"] -->|"Waiting for"| LockB["Row B"]
        T2["Transaction 2 (Holds Lock on Row B)"] -->|"Waiting for"| LockA["Row A"]
        LockB --> T2
        LockA --> T1
    end
```

#### Scenario 1: "Why are B+ Trees preferred over Binary Search Trees (BST) or B Trees for database indexes?"
* **Answer**:
  1. **High Fanout (Block Size)**: A binary tree has 2 children per node (tree depth = $\log_2 N$). For 100M rows, depth is 27, requiring up to 27 disk reads! A B+ Tree node matches the OS page size (4 KB or 8 KB), holding hundreds of keys per node. Tree depth is only **3 or 4**, requiring just 3 disk seeks!
  2. **Linked Leaf Range Queries**: In a B+ Tree, data records reside exclusively in the leaf nodes, which are connected by a bidirectional linked list. Range scans (`WHERE age BETWEEN 20 AND 30`) simply locate the starting leaf in $O(\log N)$ and sequentially scan the linked list in RAM without jumping up and down the tree!

#### Scenario 2: "Under Repeatable Read isolation, can a Phantom Read still happen?"
* **Answer**:
  * **According to ANSI SQL Standard**: YES! Repeatable Read guarantees existing rows won't change, but a concurrent transaction inserting *new* matching rows can cause a "phantom" row to appear in a subsequent query.
  * **In PostgreSQL MVCC Implementation**: **NO!** PostgreSQL's snapshot isolation at `REPEATABLE READ` takes a transaction-level snapshot at the start of the first non-transaction statement. It does not see newly inserted committed rows from other transactions.
  * *The Trap*: If the interviewer asks about standard ANSI SQL vs. PostgreSQL, citing this difference demonstrates deep senior-level mastery.

#### Scenario 3: "What is the difference between Optimistic Concurrency Control (OCC) and Pessimistic Concurrency Control?"
* **Answer**:
  * **Pessimistic Locking (`SELECT FOR UPDATE`)**: Acquires an exclusive row lock before reading. Prevents conflicts by making other transactions wait. Ideal for high-conflict financial transactions (e.g., ticket booking with 1 seat remaining).
  * **Optimistic Locking (OCC)**: Reads without locks, using a `version` column (`UPDATE products SET stock = stock - 1, version = version + 1 WHERE id = 1 AND version = 5`). If 0 rows updated, someone else modified it; the application aborts and retries. Ideal for low-conflict high-throughput web apps.

#### Scenario 4: "How does the Write-Ahead Log (WAL) guarantee Durability during sudden power loss?"
* **Answer**:
  * Writing modified 8 KB data pages to random disk locations is slow ($O(N)$ random I/O).
  * Instead, the database appends a compact record of the change to the **sequential Write-Ahead Log file (WAL)** and calls `fsync()` to force it to physical disk platter before acknowledging `COMMIT` to the client!
  * If power is cut 1 millisecond later, the dirty pages in RAM are lost, but on boot, the database reads the WAL sequentially from the last **Checkpoint** and **replays the operations (Redo Log)** to reconstruct the exact committed state.

#### Scenario 5: "What is the difference between a Clustered Index and a Secondary Index?"
* **Answer**:
  * **Clustered Index (Primary Key in MySQL InnoDB)**: The leaf nodes of the B+ Tree contain the **actual physical row data**. There can only be ONE clustered index per table because data can only be physically sorted on disk in one order.
  * **Secondary Index**: Leaf nodes do NOT store the row data; they store the indexed column value plus a **pointer (or Primary Key)** back to the clustered index. Looking up by a secondary index requires two steps: traverse secondary B+ Tree $\to$ take PK $\to$ traverse clustered B+ Tree (**Index Lookup / Bookmark Lookup**).

#### Scenario 6: "Explain the Leftmost Prefix Rule for composite index `(status, country, created_at)`."
* **Answer**: The B+ Tree sorts keys hierarchically: first by `status`, ties broken by `country`, further ties broken by `created_at`.
  * `WHERE status = 'ACTIVE' AND country = 'IN'` $\to$ Uses index fully!
  * `WHERE status = 'ACTIVE'` $\to$ Uses index!
  * `WHERE country = 'IN'` $\to$ **CANNOT use index!** (Without `status`, countries are scattered across the entire tree, forcing an $O(N)$ full table scan).
  * *Rule*: A composite index can only be used if the query filters include the leftmost column in the index definition.

#### Scenario 7: "When does the database query planner choose Hash Join vs Nested Loop vs Sort-Merge Join?"
* **Answer**:
  * **Nested Loop Join**: For every row in Table A, lookup Table B using an index. Optimal when Table A is tiny (< 100 rows) and Table B has an indexed foreign key ($O(M \log N)$).
  * **Hash Join**: Hashes the smaller table into an in-memory hash table, then streams the larger table through it. Optimal for large unindexed equality joins (`A.id = B.id`) ($O(M + N)$).
  * **Sort-Merge Join**: Both tables are already sorted (e.g. via B+ Tree index). The engine walks two pointers down both lists simultaneously. Optimal for large sorted tables or range joins ($O(M + N)$).

#### Scenario 8: "What is MVCC (Multi-Version Concurrency Control), and why does reading never block writing in Postgres?"
* **Answer**:
  * In older databases, reading a row required acquiring a Shared Read Lock, which blocked concurrent writers.
  * In **MVCC (Postgres/MySQL)**: Updating a row **does not overwrite the old row in-place**! It writes a brand-new row version with transaction timestamps (`xmin`, `xmax`).
  * When a `SELECT` runs, it simply reads the snapshot version of the row that was committed before its transaction started. Readers never wait for writers, and writers never wait for readers!

#### Scenario 9: "How does a database detect and resolve Deadlocks?"
* **Answer**:
  * The database maintains an internal **Wait-For Graph** where nodes are active transactions and directed edges indicate which transaction is waiting for a lock held by another.
  * A background daemon checks for **directed cycles** in the graph (e.g. using Tarjan's or DFS cycle detection every 1 second).
  * When a cycle is detected, the engine selects the **cheapest victim transaction** (least amount of changes written) and forcefully aborts it (`ROLLBACK`), returning error `40P01: deadlock detected` so the remaining transaction can finish.

#### Scenario 10: "Why is Database Connection Pooling mandatory, and why does opening 5,000 direct connections crash PostgreSQL?"
* **Answer**:
  * In PostgreSQL, **each connection spawns a separate OS process** (consuming ~10 MB of RAM, private caches, and kernel tracking).
  * If 5,000 client HTTP requests open 5,000 raw DB connections, 5,000 processes compete for 16 CPU cores. The CPU spends 95% of its time thrashing in OS process context switching and lock contention on shared memory.
  * A **Connection Pooler (e.g. PgBouncer / HikariCP)** maintains a small, fixed pool of 50 active connections, queuing incoming queries. Counter-intuitively, throughput is **$10\times$ higher with 50 pooled connections than with 5,000 unpooled connections**!

---

## Chapter 03 Checklist (Summary)

Before moving to Chapter 04, verify that you can answer these:
- [x] Why does a database write to a WAL (Write-Ahead Log) before updating the main table on disk?
- [x] What are the 3 physical join algorithms (Nested Loop, Hash Join, Merge Join)?
- [x] How does a B-Tree index turn an O(N) full table scan into an O(log N) lookup?
- [x] What is the Leftmost Prefix Rule for composite indexes `(A, B)`?
- [x] What are the 4 Transaction Isolation Levels, and what is a Dirty Read vs. a Phantom Read?
- [x] How does a database detect and break a Deadlock?

*Chapter 03 is complete! Proceed to [Chapter 04: Git Under the Hood](./chapter_04.md).*
