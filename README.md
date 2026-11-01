# Master Engineering & Applied AI Curriculum

A structured, first-principles study curriculum organized into three focused tracks to transition from copy-pasting/framework usage to top-tier technical depth.

---

## 📚 Complete Chapter Guides (Chapters 00 – 04)

Every chapter is written in simple, crystal-clear English with real-world analogies, Mermaid diagrams, and dual Python & JavaScript code snippets:

### 1. Google SDE & Core CS Track ([`google-sde/`](./google-sde/))
* **[Chapter 00: Computer Foundations, Memory & Core Jargon](./google-sde/chapter_00.md)** — Latency hierarchy, Stack vs. Heap, Bits/Bytes, Pointers, Linux OS, and core CS vocabulary.
* **[Chapter 01: Linux OS & Command-Line Primitives](./google-sde/chapter_01.md)** — Processes vs. Threads, Mutex locks, File Descriptors (0, 1, 2), Pipes, and Signals (`SIGTERM`/`SIGKILL`).
* **[Chapter 02: Web Architecture 101](./google-sde/chapter_02.md)** — DNS hierarchy, TCP 3-way handshake, TLS 1.3 encryption, HTTP anatomy, NGINX reverse proxies, and status codes.
* **[Chapter 03: Databases 101](./google-sde/chapter_03.md)** — Write-Ahead Log (WAL), B-Tree indexing, the Leftmost Prefix Rule, ACID transactions, and `EXPLAIN ANALYZE`.
* **[Chapter 04: Git Under the Hood](./google-sde/chapter_04.md)** — Blobs, Trees, Commits DAG, branches as 41-byte text pointers, Merge vs. Rebase, and `git reflog`.
* 📄 **[Complete 37-Chapter Roadmap](./google-sde/google_sde_study_plan.md)**

---

### 2. Python & JavaScript Mechanics Track ([`python-javascript/`](./python-javascript/))
* **[Chapter 00: Core Mental Models & Runtimes](./python-javascript/chapter_00.md)** — Mutation vs. Reassignment, Pass-by-Sharing, Truthiness traps, Garbage Collection, and Sync vs. Async.
* **[Chapter 01: Tooling, Package Managers & Environment Isolation](./python-javascript/chapter_01.md)** — Virtual environments (`.venv` `$PATH`), `package.json` vs. `package-lock.json`, `pnpm` hard links, and `npx`.
* **[Chapter 02: File Systems, Paths & OS Interfaces](./python-javascript/chapter_02.md)** — Windows vs. Linux paths, the Current Working Directory (CWD) trap, `with open()` context managers, and Streams.
* **[Chapter 03: Data Formats & Serialization 101](./python-javascript/chapter_03.md)** — Serialization vs. Deserialization, JSON gotchas (Dates, `undefined`, circular loops), URL encoding, CSV parsing, and `pickle` RCE.
* **[Chapter 04: Debugging Mastery & Reading Stack Traces](./python-javascript/chapter_04.md)** — Python tracebacks (bottom-to-top) vs. JS stack traces (top-to-bottom), `breakpoint()` PDB, Chrome DevTools, and Heisenbugs.
* 📄 **[Complete 37-Chapter Roadmap](./python-javascript/python_javascript_study_plan.md)**

---

### 3. Applied AI & Deep LLM Systems Track ([`ai-engineer/`](./ai-engineer/))
* **[Chapter 00: AI Math & Neural Networks from Scratch](./ai-engineer/chapter_00.md)** — Vectors, Dot Product similarity, Matrix multiplication, Softmax, ReLU proof, and building a 2-layer Neural Net.
* **[Chapter 01: Calculus, Loss Functions & Optimization Intuition](./ai-engineer/chapter_01.md)** — Derivatives, Gradients ($\nabla L$), Cross-Entropy Loss, Gradient Descent, Learning rates, Adam, and Vanishing Gradients.
* **[Chapter 02: Neural Networks & Backpropagation from Scratch](./ai-engineer/chapter_02.md)** — Multi-Layer Perceptrons (MLP), matrix dimensions, GELU vs. ReLU, Chain Rule backprop, and pure Python/JS XOR models.
* **[Chapter 03: Tokenization & Byte-Pair Encoding (BPE) from Scratch](./ai-engineer/chapter_03.md)** — Subword tokenization evolution, BPE merge algorithm, Special Tokens/ChatML, and the multilingual token tax.
* **[Chapter 04: Positional Encodings & Attention Foundations](./ai-engineer/chapter_04.md)** — Permutation invariance, Sinusoidal encodings vs. RoPE, Query/Key/Value Attention, Multi-Head Attention dimensionality math, Causal Attention code in Python/JS, KV-Cache, and FlashAttention SRAM tiling.
* 📄 **[Complete 29-Chapter Roadmap](./ai-engineer/ai_engineer_study_plan.md)**

---

## 🎯 How to Study for 100% Mastery

1. **Follow the 20-minute struggle rule**: When stuck on any concept or exercise, never immediately copy-paste or query AI. Sketch the problem on paper first.
2. **Practice on a blank editor**: Implement algorithms and code without IDE autocompletion or red squiggly lines.
3. **Talk out loud**: Explain your reasoning to an imaginary interviewer as you code.
