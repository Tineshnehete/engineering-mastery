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

## Chapter 03 Checklist (Summary)

Before moving to Chapter 04, verify that you can answer these:
- [x] Why does a database write to a WAL (Write-Ahead Log) before updating the main table on disk?
- [x] What are the 3 physical join algorithms (Nested Loop, Hash Join, Merge Join)?
- [x] How does a B-Tree index turn an O(N) full table scan into an O(log N) lookup?
- [x] What is the Leftmost Prefix Rule for composite indexes `(A, B)`?
- [x] What are the 4 Transaction Isolation Levels, and what is a Dirty Read vs. a Phantom Read?
- [x] How does a database detect and break a Deadlock?

*Chapter 03 is complete! Proceed to [Chapter 04: Git Under the Hood](./chapter_04.md).*
