# Parallel Python & JavaScript Mastery Curriculum

A structured, side-by-side study guide designed for developers transitioning from copy-pasting/AI generation to deep, first-principles mastery of both Python and JavaScript.

---

## Progress Tracker & Timeline Overview

- [ ] **Month 1**: Part 0 *(Prerequisites Zero: Mental Models, Package Managers, File Systems, Serialization, Debugging)* + Chapters 05 – 11 *(Runtimes, Scopes, Types, IEEE 754 Floats, Unicode, Collections, Timsort, Buffers)*
- [ ] **Month 2**: Chapters 12 – 22 *(Closures, Functional Patterns, OOP vs Prototypes, `__new__` vs `__init__`, Metaprogramming, Iterators, Generators, Asyncio vs Event Loop, Microtask Starvation, Streams, Networking)*
- [ ] **Month 3**: Chapters 23 – 37 *(DOM/Web APIs, CORS, WSGI/ASGI vs Middleware, Concurrency, Multiprocessing, GC, Security, Containers, Testing, Profiling)*

---

## Part 0: Ground Zero Primitives (The Self-Taught Bridge)

### Chapter 00: Core Mental Models & Memory Semantics
- [ ] **Pass-by-Value vs. Pass-by-Sharing**:
  - *Primitives* (numbers, booleans, small strings): Stored directly as immutable values. Passed by value (copy).
  - *Objects/Collections*: Stored on the Heap. Variables hold a reference pointer. Passed by sharing (the pointer is copied, pointing to the same Heap block).
  - *Mutation vs. Reassignment*: `list.append(1)` or `arr.push(1)` mutates the shared Heap memory; `list = [1]` or `arr = [1]` overwrites the local pointer.
- [ ] **The 4 Concurrency Buzzwords**:
  1. **Synchronous**: Line 2 waits for Line 1.
  2. **Asynchronous**: Initiate an operation, continue other work, handle callback/promise when ready.
  3. **Concurrent**: Interleaving multiple tasks on 1 CPU core.
  4. **Parallel**: Physically executing multiple tasks on multiple CPU cores simultaneously.
  - *Key Rule*: `async/await` in Python and JS is single-threaded concurrency, NOT parallel multi-threading!
- [ ] **Blocking vs. Non-Blocking I/O**:
  - What happens when a program waits for a file or HTTP response: CPU idle waiting vs OS event notification.

---

### Chapter 01: Tooling, Package Managers & Environment Isolation
- [ ] **Python**:
  - Why global `pip install` breaks systems: Dependency conflicts.
  - Virtual Environments: How `python -m venv .venv` works (manipulating `sys.prefix` and `PATH`).
  - Modern package management: `pip` vs `poetry` vs `uv` (lockfiles: `poetry.lock` for reproducible builds).
- [ ] **JavaScript**:
  - `npm`, `yarn`, `pnpm` mechanics: The `package.json` file vs `package-lock.json`.
  - The `node_modules` structure: Flat vs nested dependency resolution, symlinking in `pnpm`.
  - Executable binaries: `npx` vs globally installed CLI tools.

---

### Chapter 02: File Systems, Paths & OS Interfaces
- [ ] **Python**:
  - Paths across operating systems: `os.path` vs modern object-oriented `pathlib.Path`.
  - File access modes: `'r'`, `'w'`, `'a'`, `'rb'`, `'wb'`.
  - Safely opening files: Why context managers (`with open(...) as f:`) prevent dangling file descriptor leaks.
- [ ] **JavaScript / Node.js**:
  - The `node:path` module: `path.join()` vs `path.resolve()`, relative (`./`) vs absolute paths.
  - File system operations: `node:fs` synchronous methods (`readFileSync`) vs asynchronous callback (`fs.readFile`) vs Promise-based (`fs.promises.readFile`).
  - Stream-based file writing for large datasets without out-of-memory crashes.

---

### Chapter 03: Data Formats & Serialization 101
- [ ] **Python**:
  - JSON handling: `json.dumps()` (object to string) vs `json.loads()` (string to object), `json.dump()` / `json.load()` for file streams.
  - Parsing edge cases: `datetime` serialization, non-string dictionary keys, custom `JSONEncoder`.
  - Working with CSV, Query parameters (`urllib.parse`), and environment variables (`os.environ`).
- [ ] **JavaScript**:
  - JSON handling: `JSON.stringify()` vs `JSON.parse()`.
  - Parsing pitfalls: Malformed JSON throwing uncaught exceptions, losing `Date` objects (converted to ISO strings), losing `undefined` and functions.
  - URL handling: `URL` and `URLSearchParams` standard APIs.

---

### Chapter 04: Debugging Mastery & Reading Stack Traces
- [ ] **Python**:
  - Anatomy of a Traceback: Reading from bottom (actual error line) to top (call chain).
  - Interactive debugging: Setting breakpoints with `breakpoint()` or `import pdb; pdb.set_trace()`.
  - Debugger commands: `n` (next line), `s` (step into function), `c` (continue), `p variable` (print value).
- [ ] **JavaScript**:
  - Anatomy of a V8 Stack Trace: Reading from top (error line) to bottom (entry caller).
  - Debugging in Node.js: `node --inspect index.js` and Chrome DevTools debugger.
  - Browser DevTools: Breakpoints, conditional breakpoints, DOM mutation breakpoints, and the Network waterfall tab.

---

## Part 1: Runtimes & Core Mechanics

### Chapter 05: Runtimes, Compilation Pipelines & Execution Engines
- [ ] **Python**: CPython AST $\to$ Bytecode $\to$ PVM loop (`ceval.c`), the GIL.
- [ ] **JavaScript**: V8 Parser $\to$ Ignition (bytecode) $\to$ TurboFan (JIT compilation), hidden classes.

### Chapter 06: Scoping, Variable Lifetimes & Environment Records
- [ ] **Python**: LEGB scope rule, `global` vs `nonlocal`, UnboundLocalError.
- [ ] **JavaScript**: Execution contexts, `var` vs `let`/`const`, Hoisting, Temporal Dead Zone (TDZ).

### Chapter 07: Numeric Representations & IEEE 754 Floating-Point Quirks
- [ ] **Python**:
  - Small integer caching: Pre-allocated pool from `-5` to `256`.
  - Arbitrary precision integers (Python automatically promotes large ints to bignums, no 64-bit integer overflow).
  - Floating-point precision: `float` (IEEE 754 double precision) and `decimal.Decimal` for exact currency math.
- [ ] **JavaScript**:
  - **Why `0.1 + 0.2 !== 0.3`**: IEEE 754 binary representation of decimal fractions.
  - Number boundaries: `Number.MAX_SAFE_INTEGER` ($2^{53} - 1$) vs `BigInt` ($100n$).
  - Type-tag trivia: Why `typeof null === 'object'` (1995 V8/Netscape type-tag `000` pointer bug).

### Chapter 08: Text Encodings, Unicode & String Representation Internals
- [ ] **Python**: PEP 393 flexible string representation, `str` vs `bytes`, ReDoS risks.
- [ ] **JavaScript**: UTF-16 surrogate pairs (`"😀".length === 2`), `codePointAt()`, RegExp stateful `/g` `lastIndex` bug.

### Chapter 09: Sequential Collections & Array/Sorting Internals
- [ ] **Python**:
  - `list` over-allocated dynamic array, `tuple`, `deque`, slicing copies.
  - Reference repetition pitfall: Why `a = [[]] * 3; a[0].append(1)` mutates all rows.
  - **Timsort**: How Python sorts in O(N) on partially sorted data by detecting natural ascending/descending runs.
- [ ] **JavaScript**:
  - V8 Array representation: Fast elements (PACKED) vs Slow Dictionary elements (HOLEY).
  - `ArrayBuffer` and TypedArrays (`Uint8Array`).
  - V8 TimSort engine implementation for `Array.prototype.sort()`.

### Chapter 10: Hash Tables & Key-Value Mappings
- [ ] **Python**: `dict` compact hash table, open addressing collisions, `set`, `defaultdict`, `Counter`.
- [ ] **JavaScript**: `Object` vs `Map` (arbitrary keys, ordered), `Set`, `WeakMap`, `WeakSet`.

### Chapter 11: Binary Buffers, Serialization & Marshalling
- [ ] **Python**: `bytearray`, `memoryview`, `struct` C-packing, `pickle` RCE vulnerability.
- [ ] **JavaScript**: `Buffer` (Node.js) vs `Blob` (Browser), `structuredClone()` deep copying.

---

## Part 2: Functions, OOP & Metaprogramming

### Chapter 12: Functions, Closures & Execution Contexts
- [ ] **Python**: First-class functions, closures (`__closure__`), `*args`/`**kwargs`, mutable default trap (`def f(x=[])`).
- [ ] **JavaScript**: Closures, rest parameters (`...`), complete `this` binding rules (`call`/`apply`/`bind`/arrow).

### Chapter 13: Functional Patterns, Decorators & Currying
- [ ] **Python**: Decorators (`@wraps`), `functools.partial`, `lru_cache`, comprehensions.
- [ ] **JavaScript**: Higher-order functions (`map`, `filter`, `reduce`), currying, Stage-3 Decorators.

### Chapter 14: Object Creation Internals (`__new__` vs `__init__`)
- [ ] **Python**:
  - `__new__(cls)`: The static allocator that creates the instance in memory and returns it.
  - `__init__(self)`: The initializer that assigns attributes to the already-allocated instance.
  - `__slots__`: Bypassing instance `__dict__` to save memory.
- [ ] **JavaScript**:
  - Constructor functions with `new` keyword: Allocating memory, binding `this` to the new instance, setting prototype link.
  - `Object.create(proto)`: Creating an object with direct prototype delegation without running a constructor.

### Chapter 15: Inheritance, Delegation & Polymorphism
- [ ] **Python**: Multiple inheritance, Method Resolution Order (MRO / C3 Linearization), `super()`.
- [ ] **JavaScript**: Single prototypal inheritance, `super`, mixins (`Object.assign()`).

### Chapter 16: Special Magic Hooks & Well-Known Symbols
- [ ] **Python**: Dunder methods (`__repr__`, `__getitem__`, `__call__`, `__eq__`, `__hash__`).
- [ ] **JavaScript**: Well-Known Symbols (`Symbol.iterator`, `Symbol.toPrimitive`, `Symbol.toStringTag`).

### Chapter 17: Dynamic Interception & Descriptors
- [ ] **Python**: `__getattr__` vs `__getattribute__`, Descriptor protocol (`__get__`/`__set__`), Metaclasses.
- [ ] **JavaScript**: `Proxy` traps (`get`, `set`, `has`, `apply`), `Reflect` API, `Object.defineProperty`.

---

## Part 3: Iterators, Async Systems & Networking

### Chapter 18: Iterables & Iterator Protocols
- [ ] **Python**: `__iter__()`, `__next__()`, `StopIteration`, `itertools`.
- [ ] **JavaScript**: `[Symbol.iterator]()`, `next() -> {value, done}`, `for...of` vs `for...in`.

### Chapter 19: Generators, `yield` & Coroutine Pipelines
- [ ] **Python**: `yield`, `yield from`, bidirectional `send()` and `throw()`.
- [ ] **JavaScript**: `function*`, `yield`, `yield*`, generator stepping.

### Chapter 20: Asynchronous Fundamentals & Primitives
- [ ] **Python**: `async def`, `await`, `asyncio.Future`, `asyncio.Task`, `asyncio.gather()`.
- [ ] **JavaScript**: Callbacks, Promises (lifecycle, states), `Promise.all()`, `async`/`await`.

### Chapter 21: Event Loop Internals & Microtask Starvation
- [ ] **Python**:
  - `asyncio` event loop, OS `selectors` (epoll/kqueue), `loop.run_in_executor`.
- [ ] **JavaScript**:
  - V8/Node.js Event Loop, Call stack, Microtasks (`process.nextTick`, Promises) vs Macrotasks.
  - **Microtask Queue Starvation**: What happens when a microtask recursively spawns microtasks (complete freeze of timers, I/O, and UI rendering).

### Chapter 22: Streaming Pipelines, Backpressure & File I/O
- [ ] **Python**: Chunked file streams, `io.BytesIO`, `asyncio.StreamReader`/`StreamWriter`.
- [ ] **JavaScript**: Node.js Streams (`Readable`, `Writable`, `Transform`), Backpressure (`highWaterMark`, `drain`).

### Chapter 23: Networking, Sockets & HTTP Connection Lifecycles
- [ ] **Python**: Raw `socket` module, connection pool reuse in `urllib3`/`requests`/`httpx`.
- [ ] **JavaScript**: Node `net`/`http` modules, `http.Agent` keep-alive, `fetch()` cancellation via `AbortController`.

---

## Part 4: Web Servers, DOM & Web Security

### Chapter 24: Web Server Architectures (WSGI/ASGI vs. Middleware Pipelines)
- [ ] **Python**:
  - The Synchronous Standard: **WSGI** (`gunicorn`, `uWSGI`, Django, Flask) — 1 request per worker thread/process.
  - The Asynchronous Standard: **ASGI** (`uvicorn`, `FastAPI`, `Starlette`) — Coroutine-based request handling for high concurrency.
- [ ] **JavaScript / Node.js**:
  - The Middleware Onion Model: Express `(req, res, next) => {}`.
  - Asynchronous middleware pipelines: Fastify & Koa async execution stacks.

---

### Chapter 25: Browser DOM Mechanics & Web Storage
- [ ] **JavaScript Frontend / Full-Stack Core**:
  - **The Critical Rendering Path**: HTML parsing $\to$ DOM Tree + CSSOM $\to$ Render Tree $\to$ Layout (Reflow) $\to$ Paint $\to$ Composite.
  - **Event Propagation**: Capturing phase $\to$ Target $\to$ Bubbling phase.
  - **Event Delegation**: Handling 10,000 items with 1 listener on the parent element (`event.target`).
  - **Client-Side Storage**: Cookies (`HttpOnly`, `Secure`, `SameSite=Lax/Strict`) vs `localStorage` (synchronous 5MB limit) vs `sessionStorage` vs `IndexedDB`.

---

### Chapter 26: Web Security & Cross-Origin Policies (CORS & CSP)
- [ ] **CORS (Cross-Origin Resource Sharing)**:
  - Why CORS is a *browser* security feature, not server protection.
  - Simple requests vs. Preflight requests (`OPTIONS` with `Origin`, `Access-Control-Request-Method`).
  - Required headers: `Access-Control-Allow-Origin`, `Access-Control-Allow-Credentials`.
- [ ] **Content Security Policy (CSP)** & Cross-Site Scripting (XSS) Mitigation.

---

## Part 5: Concurrency, Memory & Security

### Chapter 27: Multi-Threading & Thread Synchronization
- [ ] **Python**: `threading`, GIL impact on CPU vs I/O, `Lock`, `RLock`, `Semaphore`, `ThreadPoolExecutor`.
- [ ] **JavaScript**: Web Workers / Node.js `worker_threads`, message passing with `postMessage`.

### Chapter 28: Multi-Processing & Shared Memory
- [ ] **Python**: `multiprocessing` module (bypassing GIL), IPC Queues/Pipes, `shared_memory`.
- [ ] **JavaScript**: `SharedArrayBuffer`, `Atomics` lock-free operations.

### Chapter 29: Memory Models & Garbage Collection Algorithms
- [ ] **Python**: Reference Counting + Generational Cyclic GC (Gen 0, 1, 2), `weakref`.
- [ ] **JavaScript**: V8 Scavenger (Young Gen) + Mark-Sweep-Compact (Old Gen), `WeakRef`.

### Chapter 30: Memory Leak Patterns & Diagnostics
- [ ] **Python**: Cyclic references with custom `__del__`, unbounded caches, `tracemalloc`.
- [ ] **JavaScript**: Accidental globals, forgotten event listeners, detached DOM, Chrome Heap Snapshots.

### Chapter 31: Language-Specific Security Vulnerabilities
- [ ] **Python**: Code injection via `eval`/`exec`, `pickle` RCE, path traversal.
- [ ] **JavaScript**: **Prototype Pollution**, Cross-Site Scripting (XSS), ReDoS.

---

## Part 6: Production Engineering, Containers & Optimization

### Chapter 32: Exception Handling & Resource Management
- [ ] **Python**: `try...except...else...finally`, `BaseException` vs `Exception`, Context Managers (`with`, `__enter__`/`__exit__`).
- [ ] **JavaScript**: `try...catch...finally`, Error causality, Explicit Resource Management (`using`, `Symbol.dispose`).

### Chapter 33: Module Systems & Modern Type Systems
- [ ] **Python**: `import` algorithm, `sys.modules`, `sys.path`, type hints (`typing.Protocol`, `mypy`, `pydantic`).
- [ ] **JavaScript**: CommonJS vs ES Modules, TypeScript structural type system comparison, JSDoc annotations.

### Chapter 34: Containerization & Docker for Python & Node.js
- [ ] **Python**:
  - Writing clean multi-stage `Dockerfile`s (caching `requirements.txt`/wheels, non-root user).
  - Unbuffered output (`ENV PYTHONUNBUFFERED=1`).
- [ ] **JavaScript / Node.js**:
  - Node.js production containerization (`NODE_ENV=production`, non-root `node` user).
  - **The PID 1 Problem**: Why Node.js doesn't handle OS signals (`SIGTERM`) properly as PID 1; using `tini` / `dumb-init`.

### Chapter 35: Production Testing, Mocking & Fixture Lifecycles
- [ ] **Python**: `pytest` fixtures, parameterization, `unittest.mock` (`patch.object`, autospec).
- [ ] **JavaScript**: Vitest / Jest test suites, spies, module mocking, fake timers.

### Chapter 36: Performance Profiling & Optimization Techniques
- [ ] **Python**: `cProfile`, `timeit`, `memory_profiler`.
- [ ] **JavaScript**: Chrome DevTools CPU Profiler, Node.js `--prof` and `--inspect`.
