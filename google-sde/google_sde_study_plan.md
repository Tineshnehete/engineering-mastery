# Google SDE Preparation Curriculum (2 YOE Target: L3 / L4)

A complete, ground-up curriculum designed for self-taught engineers transitioning from copy-pasting/framework usage to first-principles computer science, scalable systems, and Google-level technical rounds.

---

## Progress Tracker & Timeline Overview

- [x] **⚡ Fast-Track Interview Sprint**:
  - **[Message Queues Deep Dive: Kafka vs. RabbitMQ](./fast_track_message_queues.md)** *(Mandatory for PocketFM, Medanta, 1digitalstack, Linkrunner)*
  - **[Operational DevOps Cheatsheet: Kubernetes, Terraform & GitHub Actions CI/CD](./fast_track_devops.md)** *(Mandatory for SRE & Senior Backend roles)*
- [ ] **🎯 Immediate Priority DSA Chapters**:
  - **Chapter 13: Binary Trees, BSTs & Invariant Traversal** *(Daily LeetCode Medium staple)*
  - **Chapter 17: Graph Theory I — Fundamentals & Traversal** *(DFS/BFS, cycles, topological sort)*
- [ ] **Month 1**: Part 0 *(Prerequisites Zero: Hardware, Linux, Web Flow, DB 101, Git)* + Chapters 05 – 11 *(Math, Two Pointers, Intervals, Stacks, Binary Search, Heaps)*
- [ ] **Month 2**: Chapters 12 – 20 *(Linked Lists, Hybrid DS, Trees, Probabilistic DS, Graphs, DSU, Range Queries)*
- [ ] **Month 3**: Chapters 21 – 28 *(Backtracking, 1D/2D/Bitmask DP, LLD, Concurrency, Estimation Math, High-Scale Bottlenecks)*
- [ ] **Month 4**: Chapters 29 – 37 *(API Design, Distributed Systems, SRE Observability, Security, Sagas, Googleyness & Mocks)*

---

## Part 0: Foundational Prerequisites (Ground Zero for the Self-Taught Developer)

### Chapter 00: Hardware Reality, Memory Anatomy & Core Jargon
- [ ] **The Physical Latency Hierarchy**:
  - CPU Registers & L1 Cache: $\sim 1\text{ ns}$
  - RAM (Main Memory): $\sim 100\text{ ns}$ ($100\times$ slower)
  - NVMe SSD / Disk: $\sim 100,000\text{ ns}$ ($1,000\times$ slower than RAM)
  - Cross-Datacenter Network: $\sim 150,000,000\text{ ns}$ ($1,500,000\times$ slower than RAM)
  - *Why Big-O$ matters*: An O(N^2) algorithm forces millions of redundant RAM/CPU cycles; bad architecture forces redundant network round-trips.
- [ ] **Stack vs. Heap Memory**:
  - **Stack**: Fast scratchpad memory allocated per thread. Stores function frames and local variables. Infinite recursion causes a **Stack Overflow**.
  - **Heap**: Dynamic memory pool where objects, arrays, and hash tables live. Requires Garbage Collection.
  - **Pointers & References**: Variables holding an object store a 64-bit memory address pointing to the Heap.
- [ ] **Core Vocabulary**:
  - **State**: The snapshot of all values currently held in memory.
  - **Side Effect**: When a function modifies state outside its own scope (e.g., mutating an input argument, writing to disk).
  - **Pure Function**: Given identical inputs, always returns identical output with zero side effects.
  - **Invariant**: A condition that must *never* break throughout an algorithm (e.g., "Left child < Root < Right child").
  - **Idempotent**: Performing an operation 1 time or 100 times results in the exact same system state.

---

### Chapter 01: Linux OS & Command-Line Primitives
- [ ] **Processes vs. Threads**:
  - What is a Process? An isolated execution unit with its own private virtual memory space and PID (Process ID).
  - What is a Thread? A lightweight path of execution *inside* a process that shares the process's Heap memory.
- [ ] **Standard Streams & File Descriptors**:
  - Everything in Linux is a file: File Descriptors (FDs) are integer handles (`0`: stdin, `1`: stdout, `2`: stderr).
  - Pipes (`|`): Redirecting the stdout of process A to the stdin of process B in memory.
- [ ] **Signals & Process Lifecycles**:
  - `SIGINT` (Ctrl+C: graceful interrupt), `SIGTERM` (polite shutdown request), `SIGKILL` (`kill -9`: immediate kernel termination).
  - Environment variables: How `PATH`, `PORT`, and secrets are passed to child processes via the OS environment block.

---

### Chapter 02: Web Architecture 101: "What Happens When You Type a URL?"
- [ ] **Step-by-Step Request Lifecycle**:
  1. **DNS Resolution**: Browser cache $\to$ OS hosts file $\to$ Recursive DNS resolver $\to$ Root/TLD/Authoritative nameservers $\to$ IP address.
  2. **TCP 3-Way Handshake**: `SYN` $\to$ `SYN-ACK` $\to$ `ACK` establishing reliable byte stream.
  3. **TLS/SSL Handshake**: Certificate exchange, asymmetric encryption to negotiate symmetric session keys.
  4. **HTTP Request**: Method (GET/POST), Path, Headers, Body transmitted over TCP.
  5. **Reverse Proxy / Load Balancer**: NGINX / Cloudflare terminates TLS, distributes traffic to application servers.
  6. **App Server Execution**: Routing, controller logic, database querying, serialization to JSON/HTML.
  7. **HTTP Response**: Status code (200, 404, 500), response headers, response payload.

---

### Chapter 03: Databases 101: Tables, Indexing & Transactions
- [ ] **Why Databases Exist (Disk vs. RAM Persistence)**:
  - RAM is volatile (power loss = data lost); Disk/SSD is non-volatile.
  - Write-Ahead Logging (WAL): Appending changes to disk sequentially before updating complex data structures.
- [ ] **The Magic of Indexing (Why Searches Are Fast or Slow)**:
  - **Full Table Scan (O(N))**: The database reads every single row on disk from start to finish.
  - **B-Tree Index (O(\log N))**: A balanced search tree stored on disk that allows pinpointing a row in 3-4 disk I/O reads.
  - Primary Keys vs. Foreign Keys vs. Composite Indexes.
- [ ] **Transactions & ACID Basics**:
  - **Atomicity**: All operations succeed, or all roll back (no half-saved state).
  - **Consistency**: Database invariants and constraints are preserved.
  - **Isolation**: Concurrent transactions do not read each other's half-finished work.
  - **Durability**: Once committed, data survives power outages.

---

### Chapter 04: Git Under the Hood
- [ ] **Git as a Content-Addressable Graph**:
  - **Blob**: The raw file contents hashed with SHA-1/SHA-256.
  - **Tree**: A directory listing containing file names, permissions, and pointers to blobs.
  - **Commit**: A snapshot pointing to a top-level Tree, author metadata, and parent commit(s).
- [ ] **Core Git Mechanics**:
  - **Branches**: Merely a 41-byte text file containing a pointer (commit hash). Creating a branch is instant (O(1)).
  - **HEAD**: A reference pointing to the current active branch or commit.
  - **Merge vs. Rebase**: Merge creates a new commit joining two histories; Rebase replays commits linearly on top of another base.

---

## Part 1: Core Data Structures & Algorithmic Patterns

### Chapter 05: Complexity Analysis & Theoretical Foundations
- [ ] Big-O$, $\Omega$, $\Theta$, auxiliary vs call-stack memory, amortized analysis.

### Chapter 06: Bit Manipulation, Number Theory & Discrete Math
- [ ] Bitwise operators, bitmasks, submask iteration, Euclidean GCD, modular exponentiation, Reservoir Sampling.

### Chapter 07: Two Pointers & Sliding Window
- [ ] Opposing pointers, fast/slow pointers, fixed vs dynamic windows, invariant maintenance.

### Chapter 08: Intervals & Sweep-Line Algorithms
- [ ] Interval overlapping math, event transformations (`+1`/`-1`), Meeting Rooms II, Employee Free Time.

### Chapter 09: Binary Search on Values & Answer Spaces
- [ ] Invariants (`low <= high`), rotated search, monotonic predicate functions $P(x) \to \{\text{True}, \text{False}\}$.

### Chapter 10: Monotonic Structures, Stacks & String Evaluators
- [ ] Monotonic Stack, Monotonic Deque, Basic Calculator I, II, III, string parsers.

### Chapter 11: Linked Lists & Multi-Pointer Traversal
- [ ] Floyd’s cycle detection proof, in-place reversal, dummy nodes, LRU node linking.

### Chapter 12: Hybrid Data Structures & System Simulation (Google Staple)
- [ ] **LRU Cache**, **LFU Cache**, **Snapshot Array**, **Insert Delete GetRandom O(1)**, **Time-Based Key-Value Store**.

### Chapter 13: Binary Trees, BSTs & Invariant Traversal
- [ ] DFS traversals, BFS level-order, LCA, BST properties, tree serialization/deserialization.

### Chapter 14: Heaps, Priority Queues & Streaming Data
- [ ] Binary heap internals, O(N) build heap vs O(N \log N) insert, dual-heap streaming median.

### Chapter 15: Tries, Radix Trees & Compressed Dictionaries
- [ ] Standard Trie node layout vs. **Radix Tree** (compressed single-child edges to save memory).
- [ ] Prefix searches, Trie + Backtracking (Word Search II), Rolling hash (Rabin-Karp).

### Chapter 16: Probabilistic Data Structures & Massive-Scale Datasets
- [ ] **Bloom Filters**: Bit-array hashing, zero false negatives guarantee, membership testing for billions of items in RAM.
- [ ] **HyperLogLog (HLL)**: Logarithmic cardinality estimation for unique visitor counts with minimal memory footprint.
- [ ] **External Merge Sort**: Sorting Terabytes of data that exceed physical RAM using sorted disk runs and Min-Heap streaming.

### Chapter 17: Graph Theory I — Fundamentals & Traversal
- [ ] Adjacency lists, BFS shortest path, DFS exhaustive search, 3-color directed cycle detection.

### Chapter 18: Graph Theory II — Dependency Resolution & Shortest Paths
- [ ] Kahn’s algorithm, topological sort, Dijkstra’s with Priority Queue, Bellman-Ford intuition (handling negative cycles).

### Chapter 19: Graph Theory III — Disjoint Set Union (DSU) & MST
- [ ] Path compression, union by rank, O(\alpha(N))$, Kruskal’s Minimum Spanning Tree.

### Chapter 20: Graph Theory IV — Advanced Graphs & Range Queries
- [ ] Tarjan’s algorithm (Bridges/Articulation points), Hierholzer’s Eulerian path, Segment Trees / Fenwick Trees.

### Chapter 21: Recursion & Backtracking
- [ ] State space trees, pruning branches, combinations, permutations, N-Queens, Sudoku.

### Chapter 22: Dynamic Programming I — 1D & Decision Problems
- [ ] Overlapping subproblems, Memoization $\to$ Tabulation $\to$ Space optimization, LIS, Coin Change.

### Chapter 23: Dynamic Programming II — 2D, Grids, Strings & Bitmask DP
- [ ] Grid paths, LCS, Edit Distance, 0/1 Knapsack, Bitmask DP state representations.

---

## Part 2: Low-Level Design & Concurrency

### Chapter 24: Object-Oriented Principles & Clean Code
- [ ] SOLID principles, decoupling interfaces, code smell detection.

### Chapter 25: Design Patterns, Concurrency & Message Queues
- [ ] Factory, Strategy, Observer, Decorator.
- [ ] Mutex, Semaphore, Read-Write Locks, Producer-Consumer queue, Deadlock prevention.
- [ ] *Sprint Chapter*: ⚡ **[Read Message Queues Deep Dive: Kafka vs. RabbitMQ](./fast_track_message_queues.md)**

---

## Part 3: Distributed Systems & Scalability (L4 Insurance)

### Chapter 26: Back-of-the-Envelope Estimation (Google System Design Essential)
- [ ] **The Numbers Every Engineer Must Know**:
  - Powers of Two Table: $2^{10} \approx 1\text{ KB}$, $2^{20} \approx 1\text{ MB}$, $2^{30} \approx 1\text{ GB}$, $2^{40} \approx 1\text{ TB}$, $2^{50} \approx 1\text{ PB}$.
  - Seconds per day: $24 \times 60 \times 60 \approx 86,400 \approx 100,000\text{ seconds/day}$ (for quick interview math).
- [ ] **Standard System Sizing Calculations**:
  - **QPS (Queries Per Second)**: $\frac{\text{DAU} \times \text{Requests per user}}{100,000}$.
  - **Peak QPS**: Typically $2\times$ to $3\times$ Average QPS.
  - **Storage Estimation**: $\text{Daily Writes} \times \text{Size per record} \times 365\text{ days} \times 5\text{ years}$.
  - **Bandwidth / Throughput**: $\text{QPS} \times \text{Payload Size}$ (Ingress vs Egress).

---

### Chapter 27: Networking, Protocols & Data Transport
- [ ] TCP vs UDP, HTTP/1 vs 2 vs 3, gRPC/Protobuf, WebSockets, DNS, CDNs.

### Chapter 28: High-Scale Bottlenecks & Real-World System Failures
- [ ] **The Celebrity / Hotspot Problem**:
  - Fan-out on Write (push model for normal users) vs. Fan-out on Read (pull model for high-follower accounts).
- [ ] **The Cache Stampede (Thundering Herd Problem)**:
  - Mutex locking around cache regeneration and probabilistic early expiration (XFetch algorithm).
- [ ] **Distributed Transactions Across Shards**:
  - The **Saga Pattern** with compensating actions vs. 2-Phase Commit (2PC).
  - The **Transactional Outbox Pattern** ensuring database changes and message queue events commit atomically.

---

### Chapter 29: API Design, Idempotency & Contract Governance
- [ ] REST vs RPC, Idempotency Keys, Cursor vs Offset pagination, API versioning.

### Chapter 30: Storage, Databases & Replication Topologies
- [ ] SQL vs NoSQL, B-Trees vs LSM-Trees, Cache-aside/Write-through, Redis eviction.
- [ ] **Replication Topologies**:
  - Single-Leader (Master-Replica) with Read Replicas (replication lag, eventual consistency).
  - Multi-Leader & Leaderless (Dynamo-style quorum: $R + W > N$).
  - Split-brain scenarios and leader election (Raft/Paxos consensus intuition).

### Chapter 31: Scalability & Distributed Primitives
- [ ] L4/L7 Load balancers, Consistent Hashing ring, DB sharding, CAP & PACELC theorems.

### Chapter 32: Distributed Observability & SRE Metrics
- [ ] Distributed Tracing (Trace/Span ID), Prometheus metrics, Google SLI, SLO, SLA, Error Budgets.

### Chapter 33: System Security, Auth & Threat Mitigation
- [ ] OAuth2, JWT verification, RBAC, TLS termination, SQLi, XSS, CSRF, DDoS mitigation.

### Chapter 34: Containers, Kubernetes, Terraform & CI/CD
- [ ] Linux Namespaces + Cgroups isolation (containers vs VMs).
- [ ] Rolling Deployment, Blue/Green Deployment, Canary Deployment (1% traffic canary).
- [ ] *Sprint Chapter*: ⚡ **[Read Operational DevOps Cheatsheet: Kubernetes, Terraform & GitHub Actions CI/CD](./fast_track_devops.md)**

### Chapter 35: Classic Distributed System Blueprints
- [ ] Rate Limiter, TinyURL, Distributed Key-Value Store, Notification System.

---

## Part 4: Behavioral & Interview Communication

### Chapter 36: Googleyness & Incident Retrospectives
- [ ] Thriving in ambiguity, intellectual humility, doing the right thing, bias for action.
- [ ] **Production Incident Retrospective**: Structured technical breakdown of a catastrophic outage, root-cause diagnosis without guessing, and systemic guardrails.

### Chapter 37: The 45-Minute Coding Interview Execution & Follow-Up Playbook
- [ ] **45-Minute Pacing Protocol**:
  - Clarify (0-5m) $\to$ Brainstorm (5-12m) $\to$ Code (12-30m) $\to$ Dry run edge cases (30-38m) $\to$ Follow-ups (38-45m).
- [ ] **The 5 Google Follow-Up Questions to Anticipate**:
  1. *"What if the input data does not fit in RAM?"* $\to$ External Merge Sort / Disk streaming.
  2. *"What if the input is an infinite real-time stream?"* $\to$ Reservoir Sampling / Count-Min Sketch.
  3. *"What if this function is called concurrently by 10,000 threads?"* $\to$ Concurrency locks / Atomic primitives.
  4. *"What if the graph contains negative weights or cycles?"* $\to$ Bellman-Ford failure handling.
  5. *"What if there are billions of read requests for items that don't exist?"* $\to$ Bloom Filter frontend shield.
