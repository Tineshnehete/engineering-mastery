# Master Engineering & Applied AI Curriculum

A structured, first-principles study curriculum organized into three focused tracks to transition from copy-pasting/framework usage to top-tier technical depth.

---

## 📚 Complete Chapter Guides (Chapters 00 – 08)

Every chapter is written in simple, crystal-clear English with real-world analogies, Mermaid diagrams, runnable dual Python & JavaScript code snippets, real-world production case studies, systems failure modes, and 10 to 15 in-depth FAANG / AI Lab interview questions with full answers:

### 1. Google SDE & Core CS Track ([`google-sde/`](./google-sde/))
* **[Chapter 00: Computer Foundations, Memory & Core Jargon](./google-sde/chapter_00.md)** — Latency hierarchy, Stack vs. Heap, Bits/Bytes, Pointers, Linux OS, and core CS vocabulary.
* **[Chapter 01: Linux OS & Command-Line Primitives](./google-sde/chapter_01.md)** — Processes vs. Threads, Mutex locks, File Descriptors (0, 1, 2), Pipes, and Signals (`SIGTERM`/`SIGKILL`).
* **[Chapter 02: Web Architecture 101](./google-sde/chapter_02.md)** — DNS hierarchy, TCP 3-way handshake, TLS 1.3 encryption, HTTP anatomy, NGINX reverse proxies, and status codes.
* **[Chapter 03: Databases 101](./google-sde/chapter_03.md)** — Write-Ahead Log (WAL), B-Tree indexing, the Leftmost Prefix Rule, ACID transactions, and `EXPLAIN ANALYZE`.
* **[Chapter 04: Git Under the Hood](./google-sde/chapter_04.md)** — Blobs, Trees, Commits DAG, branches as 41-byte text pointers, Merge vs. Rebase, and `git reflog`.
* **[Chapter 05: Complexity Analysis & Theoretical Foundations](./google-sde/chapter_05.md)** — Asymptotic notations (Big-O, Big-$\Omega$, Big-$\Theta$, Little-$o/\omega$), Master Theorem 3 cases, Amortized Analysis (Aggregate, Banker's, Potential methods), Cache I/O complexity, Cloudflare ReDoS case study, and 12 FAANG questions.
* **[Chapter 06: Bit Manipulation, Number Theory & Discrete Math](./google-sde/chapter_06.md)** — Two's Complement ($-x = \sim x + 1$), Bitmasks, Submask enumeration in $O(3^N)$, Brian Kernighan's bit-counting, Lowest Set Bit isolation $x \ \& \ (-x)$, Euclidean GCD, Modular Exponentiation in $O(\log B)$, Fermat's Little Theorem modular inverse, Reservoir Sampling proof on infinite streams, and 12 FAANG questions.
* **[Chapter 07: Two Pointers & Sliding Window](./google-sde/chapter_07.md)** — Opposing pointers, Floyd's Cycle Tortoise & Hare proof, Universal 4-phase Dynamic Window template, Monotonic Deque $O(N)$ sliding window max, Redis rate limiter sliding window outage, TCP window buffer bloat, and 12 FAANG questions.
* **[Chapter 08: Intervals & Sweep-Line Algorithms](./google-sde/chapter_08.md)** — Interval overlap condition $\max(A_s, B_s) \le \min(A_e, B_e)$, Start vs End sorting decision tree, Sweep-Line event transformation $+1/-1$ with tie-breaking rules, Min-Heap active tracking, Postgres `daterange` exclusion locks, Cloud GPU scheduler case study, and 12 FAANG questions.
* 📄 **[Complete 37-Chapter Roadmap](./google-sde/google_sde_study_plan.md)**

---

### 2. Python & JavaScript Mechanics Track ([`python-javascript/`](./python-javascript/))
* **[Chapter 00: Core Mental Models & Runtimes](./python-javascript/chapter_00.md)** — Mutation vs. Reassignment, Pass-by-Sharing, Truthiness traps, Garbage Collection, and Sync vs. Async.
* **[Chapter 01: Tooling, Package Managers & Environment Isolation](./python-javascript/chapter_01.md)** — Virtual environments (`.venv` `$PATH`), `package.json` vs. `package-lock.json`, `pnpm` hard links, and `npx`.
* **[Chapter 02: File Systems, Paths & OS Interfaces](./python-javascript/chapter_02.md)** — Windows vs. Linux paths, the Current Working Directory (CWD) trap, `with open()` context managers, and Streams.
* **[Chapter 03: Data Formats & Serialization 101](./python-javascript/chapter_03.md)** — Serialization vs. Deserialization, JSON gotchas (Dates, `undefined`, circular loops), URL encoding, CSV parsing, and `pickle` RCE.
* **[Chapter 04: Debugging Mastery & Reading Stack Traces](./python-javascript/chapter_04.md)** — Python tracebacks (bottom-to-top) vs. JS stack traces (top-to-bottom), `breakpoint()` PDB, Chrome DevTools, and Heisenbugs.
* **[Chapter 05: Runtimes, Compilation Pipelines & Execution Engines](./python-javascript/chapter_05.md)** — CPython AST $\to$ Bytecode $\to$ PVM loop `ceval.c`, opcode evaluation, GIL mechanics and Free-Threaded Python 3.13 PEP 703; V8 Parser $\to$ Ignition Bytecode $\to$ TurboFan JIT, Hidden Classes / Shapes, Inline Caching Monomorphic vs Megamorphic deoptimizations, and 12 FAANG questions.
* **[Chapter 06: Scoping, Variable Lifetimes & Environment Records](./python-javascript/chapter_06.md)** — Python LEGB rule, `global` vs `nonlocal`, `UnboundLocalError` compile-time lexical analysis trap, closure cell objects; JS Execution Contexts, Environment Records, `var` vs `let`/`const`, Hoisting, Temporal Dead Zone (TDZ) proof, `for (var i)` timeout closure bug, and 12 FAANG questions.
* **[Chapter 07: Numeric Representations & IEEE 754 Floating-Point Quirks](./python-javascript/chapter_07.md)** — IEEE 754 64-bit float binary fraction representation, why `0.1 + 0.2 !== 0.3`, `Number.MAX_SAFE_INTEGER` ($2^{53} - 1$), Python `PyLongObject` arbitrary-precision bignum internals, small int cache (`-5` to `256`), exact currency math with `decimal.Decimal` and minor units, `typeof null === 'object'` origin, Ariane 5 rocket disaster, and 12 FAANG questions.
* **[Chapter 08: Text Encodings, Unicode & String Representation Internals](./python-javascript/chapter_08.md)** — ASCII to 1,114,112 code point Unicode standard, UTF-8 vs UTF-16 vs UTF-32, JavaScript UTF-16 code units vs surrogate pairs (`"😀".length === 2`), grapheme clusters and ZWJ emojis (`👨‍👩‍👧‍👦`), `Intl.Segmenter`, Python PEP 393 Flexible String Representation, RegExp stateful `/g` `lastIndex` mutation trap, Cloudflare ReDoS outage, MySQL `utf8` data truncation, and 12 FAANG questions.
* 📄 **[Complete 37-Chapter Roadmap](./python-javascript/python_javascript_study_plan.md)**

---

### 3. Applied AI & Deep LLM Systems Track ([`ai-engineer/`](./ai-engineer/))
* **[Chapter 00: AI Math & Neural Networks from Scratch](./ai-engineer/chapter_00.md)** — Vectors, Dot Product similarity, Matrix multiplication, Softmax, ReLU proof, and building a 2-layer Neural Net.
* **[Chapter 01: Calculus, Loss Functions & Optimization Intuition](./ai-engineer/chapter_01.md)** — Derivatives, Gradients ($\nabla L$), Cross-Entropy Loss, Gradient Descent, Learning rates, Adam, and Vanishing Gradients.
* **[Chapter 02: Neural Networks & Backpropagation from Scratch](./ai-engineer/chapter_02.md)** — Multi-Layer Perceptrons (MLP), matrix dimensions, GELU vs. ReLU, Chain Rule backprop, and pure Python/JS XOR models.
* **[Chapter 03: Tokenization & Byte-Pair Encoding (BPE) from Scratch](./ai-engineer/chapter_03.md)** — Subword tokenization evolution, BPE merge algorithm, Special Tokens/ChatML, and the multilingual token tax.
* **[Chapter 04: Positional Encodings & Attention Foundations](./ai-engineer/chapter_04.md)** — Permutation invariance, Sinusoidal encodings vs. RoPE, Query/Key/Value Attention, Multi-Head Attention dimensionality math, Causal Attention code in Python/JS, KV-Cache, and FlashAttention SRAM tiling.
* **[Chapter 05: Attention Mechanics from Scratch & KV-Cache Deep Dive](./ai-engineer/chapter_05.md)** — Scaled Dot-Product Attention derivation, $\sqrt{d_k}$ vanishing gradient proof, Causal masking with $-\infty$, MHA vs MQA vs GQA, KV-cache tensor lifecycle step-by-step ($O(N^2) \to O(N)$), exact VRAM calculation formulas, PagedAttention (vLLM) memory paging, FlashAttention IO-awareness, and 12 AI lab questions.
* **[Chapter 06: Transformer Blocks, RMSNorm, Residuals & SwiGLU](./ai-engineer/chapter_06.md)** — Full modern Decoder block anatomy, why BatchNorm fails in NLP, LayerNorm vs RMSNorm (7-15% compute reduction), Pre-LN vs Post-LN gradient highway proof, Feed-Forward MLP knowledge storage, SwiGLU activation ($\frac{8}{3} d_{\text{model}}$ parameter parity sizing), GPU tensor core dimension alignment, and 12 AI lab questions.
* **[Chapter 07: The 3 Training Stages of Modern LLMs (Pre-training, SFT, RLHF & DPO)](./ai-engineer/chapter_07.md)** — The complete LLM lifecycle, Causal LM cross-entropy loss, Chinchilla scaling laws ($D \approx 20N$) vs overtraining for inference economics, SFT chat templates and masked loss (`-100`), PPO RLHF 4-model pipeline vs Direct Preference Optimization (DPO) mathematical derivation from Bradley-Terry preferences, sycophancy reward hacking, and 12 AI lab questions.
* **[Chapter 08: Floating-Point Formats & Quantization Mathematics (FP32/FP16/BF16/INT8/INT4, AWQ/GPTQ/GGUF)](./ai-engineer/chapter_08.md)** — Bit-level layouts of FP32/FP16/BF16/FP8, why BF16 prevents gradient overflow without loss scaling, Symmetric vs Asymmetric (Affine) scale ($S$) and zero-point ($Z$) mathematical derivations, Per-Tensor vs Group-wise granularity, systematic outlier feature collapse at 6.7B+ scale (LLM.int8()), GPTQ second-order inverse Hessian updates, AWQ salient channel protection, GGUF K-quants (`llama.cpp`), and 12 AI lab questions.
* 📄 **[Complete 29-Chapter Roadmap](./ai-engineer/ai_engineer_study_plan.md)**

---

## 🎯 How to Study for 100% Mastery

1. **Follow the 20-minute struggle rule**: When stuck on any concept or exercise, never immediately copy-paste or query AI. Sketch the problem on paper first.
2. **Practice on a blank editor**: Implement algorithms and code without IDE autocompletion or red squiggly lines.
3. **Talk out loud**: Explain your reasoning to an imaginary interviewer as you code.
4. **Solve the chapter interview questions**: Every chapter includes 10-15 deep technical interview scenarios. Attempt answering them before reading the provided solutions.
