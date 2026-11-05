# Applied AI & Deep LLM Systems Engineering Master Curriculum

A rigorous, ground-up curriculum designed to transition from high-level wrapper usage to true first-principles mastery: from linear algebra and neural network mechanics from scratch, to Transformer internals, GPU memory math, production RAG, MCP, and multi-agent systems.

---

## Progress Tracker & Timeline Overview

- [ ] **🎯 Immediate Priority Chapters (Job Screen Fast-Track)**:
  - **Part 3 & 4 (RAG & pgvector)**: **Chapter 12** *(pgvector & HNSW tuning)*, **Chapter 13** *(Hybrid Search & Reranking)*, **Chapter 14** *(Chunking & Ingestion)*
  - **Part 5 & 6 (Agents & LangGraph)**: **Chapter 18** *(Tool Calling)*, **Chapter 19** *(ReAct from scratch)*, **Chapter 20** *(LangGraph state machines)*
- [ ] **Month 1**: Part 0 *(Math, Tensors & Neural Nets from Scratch)* + Part 1 *(Transformer Internals & Training Stages)*
- [ ] **Month 2**: Part 2 *(GPU VRAM Math & Quantization)* + Part 3 *(Embeddings, HNSW & Vector DB Internals)*
- [ ] **Month 3**: Part 4 *(Production RAG Architecture)* + Part 5 *(Constrained Decoding & Tool Calling Protocols)*
- [ ] **Month 4**: Part 6 *(Autonomous Agents, MCP & Swarms)* + Part 7 *(LoRA/QLoRA Fine-Tuning)* + Part 8 & 9 *(Evals, Security & Capstones)*

---

## Part 0: Ground Zero — Math, Tensors & Neural Nets from First Principles

### Chapter 00: Linear Algebra & Matrix Mathematics for AI
- [ ] **Vectors & Dimensionality**:
  - Scalars (0D), Vectors (1D), Matrices (2D), Tensors ($N$-dimensional arrays).
  - Vector operations: Vector addition, scalar multiplication, vector magnitude / L2-norm ($\|v\| = \sqrt{\sum v_i^2}$).
  - **The Dot Product**: Geometric intuition ($A \cdot B = \|A\| \|B\| \cos \theta$) and algebraic intuition ($\sum A_i B_i$). Why the dot product measures similarity between two concepts.
- [ ] **Matrix Multiplication ($A \times B$)**:
  - Dimensionality rules: $(M \times K) \times (K \times N) = (M \times N)$.
  - Why deep learning is essentially billions of parallel matrix multiplications.
- [ ] **The Softmax Function**:
  - Formula: $\sigma(z)_i = \frac{e^{z_i}}{\sum e^{z_j}}$.
  - Converting arbitrary raw real numbers (logits) into a probability distribution that sums strictly to $1.0$.

---

### Chapter 01: Calculus, Loss Functions & Optimization Intuition
- [ ] **Gradients & Derivatives Demystified**:
  - What is a derivative? The rate of change of an output relative to an input.
  - What is a Gradient? A vector of partial derivatives pointing in the direction of steepest ascent.
- [ ] **Loss Functions**:
  - How an AI measures how "wrong" it is:
    - Mean Squared Error (MSE) for regression.
    - **Cross-Entropy Loss**: Measuring the divergence between the model's predicted probability distribution and the true next token.
- [ ] **Gradient Descent & Backpropagation**:
  - **Gradient Descent**: Taking small steps (governed by Learning Rate $\alpha$) in the opposite direction of the gradient to minimize loss: $W_{new} = W_{old} - \alpha \nabla L$.
  - **Backpropagation**: Applying the Calculus Chain Rule backwards through layers to calculate how much each weight contributed to the final error.

---

### Chapter 02: Building a Neural Network from Scratch in Pure Python / NumPy
- [ ] **The Artificial Neuron (Perceptron)**:
  - Linear combination: $z = W \cdot X + b$ (Weights $\times$ Inputs + Bias).
  - Activation: $a = f(z)$.
- [ ] **Activation Functions (Why Non-Linearity is Mandatory)**:
  - Why linear layers stacked without activations collapse into a single linear regression.
  - **ReLU** ($\max(0, x)$), **GELU** (Gaussian Error Linear Unit — standard in modern LLMs), and **Sigmoid**.
- [ ] **Hands-On Coding Milestone**:
  - Build a 2-layer Multi-Layer Perceptron (MLP) in pure Python using only NumPy (forward pass, cross-entropy loss, manual backward pass gradient computation, and weight update loop). No PyTorch or TensorFlow allowed.

---

## Part 1: The Transformer Architecture Deep-Dive

### Chapter 03: Tokenization & Byte-Pair Encoding (BPE) from Scratch
- [ ] **Why Characters and Words Fail**:
  - Character-level: Sequences become too long; loses semantic chunking.
  - Word-level: Infinite vocabulary size; fails completely on typos and out-of-vocabulary words.
- [ ] **Byte-Pair Encoding (BPE) Algorithm**:
  - Starting with base byte vocabulary (256 bytes).
  - Iteratively finding the most frequent adjacent pair of tokens and merging them into a new token.
  - Building a miniature BPE tokenizer in Python that trains on raw text and encodes/decodes strings.
- [ ] **Special Tokens**: `<|endoftext|>`, `<|im_start|>`, `<|im_end|>`, padding tokens, BOS/EOS markers.

---

### Chapter 04: Positional Encodings (Teaching Order to Transformers)
- [ ] **The Permutation Invariance Problem**:
  - Self-attention processes all words in parallel; without positional data, `"dog bites man"` and `"man bites dog"` produce identical representations.
- [ ] **Sinusoidal Positional Embeddings (Original Transformer)**:
  - Using interleaved Sine and Cosine waves of varying frequencies.
- [ ] **RoPE (Rotary Position Embeddings — Modern Standard in Llama/Mistral/Gemini)**:
  - Rotating the Query and Key vectors in 2D complex planes by an angle proportional to their sequence position.
  - Preserving relative distance ($m - n$) naturally through dot products.

---

### Chapter 05: Attention Mechanics from Scratch
- [ ] **Scaled Dot-Product Attention**:
  - Step 1: Input embeddings $\times$ Projection Weights $\to$ Query ($Q$), Key ($K$), Value ($V$).
    - $Q$: "What am I looking for?"
    - $K$: "What do I contain?"
    - $V$: "What information do I pass along if selected?"
  - Step 2: Attention Scores = $\frac{Q \cdot K^T}{\sqrt{d_k}}$ (scaling by $\sqrt{d_k}$ prevents gradients from vanishing in Softmax).
  - Step 3: Attention Weights = $\text{Softmax}(\text{Scores})$.
  - Step 4: Output = $\text{Weights} \cdot V$.
- [ ] **Causal (Masked) Self-Attention**:
  - Masking upper-triangular matrix with $-\infty$ so tokens cannot look into the future during autoregressive generation.
- [ ] **Multi-Head Attention (MHA) vs. Multi-Query Attention (MQA) vs. Grouped-Query Attention (GQA)**:
  - Why GQA (Llama 3 standard) shares Key/Value heads across multiple Query heads to drastically reduce KV-cache memory.

---

### Chapter 06: Transformer Blocks & Normalization
- [ ] **Layer Normalization**:
  - Why Batch Normalization fails on variable-length text sequences.
  - **RMSNorm** (Root Mean Square Normalization): Normalizing inputs by root mean square without subtracting mean, saving 10-20% compute.
- [ ] **Residual (Skip) Connections**:
  - Passing raw input around layers ($x + \text{SubLayer}(x)$) to prevent vanishing gradients in 100-layer models.
- [ ] **Feed-Forward Blocks & SwiGLU**:
  - The MLP block inside each Transformer layer.
  - SwiGLU activation (Swish-Gated Linear Unit) for enhanced non-linear expressiveness.

---

### Chapter 07: The 3 Training Stages of Modern LLMs
- [ ] **Stage 1: Pre-Training (Base Models)**:
  - Self-supervised learning on 5–15 Trillion tokens of web crawl, books, code.
  - Predict the next token ($L_{next}$). Output: An "autocomplete" engine (not an assistant).
- [ ] **Stage 2: Supervised Fine-Tuning (SFT / Instruction Tuning)**:
  - Training on high-quality curated dialogues (User prompt $\to$ Assistant response).
  - Teaches the model conversational format, system prompt adherence, and tool calling syntax.
- [ ] **Stage 3: Alignment (RLHF & DPO)**:
  - **RLHF (Reinforcement Learning from Human Feedback)**: Training a Reward Model on human preferences, then optimizing the LLM via PPO (Proximal Policy Optimization).
  - **DPO (Direct Preference Optimization)**: Bypassing the separate reward model; optimizing the LLM directly on pairs of (Chosen, Rejected) responses using an implicit reward mathematical formulation.

---

## Part 2: GPU Hardware, Precision Economics & Serving

### Chapter 08: Floating-Point Formats & Quantization Mathematics
- [ ] **Numeric Precision Breakdown**:
  - **FP32** (Single Precision): 1 sign bit, 8 exponent bits, 23 mantissa bits (4 bytes per parameter).
  - **FP16 / BF16** (Half Precision / Brain Float): 2 bytes per parameter. BF16 preserves FP32 dynamic range, preventing underflow during training.
  - **INT8 & INT4**: Quantizing 16-bit floats to 8-bit or 4-bit integers.
- [ ] **Quantization Techniques**:
  - Symmetric vs. Asymmetric Quantization.
  - Scale factor and Zero-point mapping formulas.
  - Post-Training Quantization (PTQ) formats: **GGUF** (CPU/Apple Silicon inference via `llama.cpp`), **AWQ** (Activation-aware Weight Quantization), and **GPTQ**.

---

### Chapter 09: VRAM Sizing Calculations & Serving Mechanics
- [ ] **The Exact VRAM Formula Every AI Engineer Must Know**:
  $$\text{Memory for Weights} = \text{Parameters (in Billions)} \times \text{Bytes per Parameter}$$
  - A 70B parameter model in FP16 (2 bytes/param) = $70 \times 2 = 140\text{ GB}$ VRAM (requires two 80GB A100 GPUs).
  - The same 70B model in 4-bit (0.5 bytes/param) = $70 \times 0.5 = 35\text{ GB}$ VRAM (runs on a single consumer RTX 3090/4090 or Mac M-series).
- [ ] **The KV Cache Memory Equation**:
  - Calculating KV Cache size per token: $2 \times 2 \times n_{\text{layers}} \times n_{\text{heads}} \times d_{\text{head}} \times \text{bytes}$.
  - Why context window expansion causes GPU Out-Of-Memory (OOM) errors during generation.
- [ ] **High-Throughput Serving Engines**:
  - **vLLM & PagedAttention**: Managing KV-cache memory like virtual memory pages in an OS, eliminating memory fragmentation and boosting throughput $5\text{--}10\times$.
  - Continuous Batching (iteration-level scheduling) vs. Static Request Batching.

---

## Part 3: Embeddings, Vector Search & Database Internals

### Chapter 10: Vector Embeddings & Metric Spaces
- [ ] **Embedding Topologies**:
  - How neural networks map semantic meaning into dense continuous geometric spaces.
  - High-dimensional space properties: Why orthogonal vectors are common in 1536 dimensions.
- [ ] **Distance Metrics**:
  - Cosine Similarity vs. Dot Product vs. Euclidean (L2) distance.
  - Mathematical normalization: Why normalized vectors allow dot product to equal cosine similarity, reducing compute costs.

---

### Chapter 11: Approximate Nearest Neighbor (ANN) Algorithms Internals
- [ ] **The B-Tree Failure**: Why traditional 1D database indexes cannot divide $N$-dimensional space.
- [ ] **HNSW (Hierarchical Navigable Small World)**:
  - Multi-layer graph inspired by Skip Lists.
  - Top layers: Long-range links for coarse navigation.
  - Bottom layers: Dense local clustering for fine-grained nearest neighbor discovery.
  - Search complexity: O(\log N).
- [ ] **IVF (Inverted File Index) & Product Quantization (PQ)**:
  - Voronoi cell space partitioning.
  - Breaking 1536D vectors into sub-vectors and quantizing each sub-vector to centroids.

---

### Chapter 12: Production Vector Storage & `pgvector`
- [ ] **PostgreSQL `pgvector` Deep-Dive**:
  - Vector column data types (`vector(1536)`).
  - Creating and tuning HNSW indexes: `m` (max connections per node) and `ef_construction` (search quality trade-off).
  - Writing SQL queries combining relational filters (`WHERE org_id = '123'`) with vector distance operators (`<=>` cosine distance, `<#>` negative inner product).

---

### Chapter 13: Hybrid Search & Cross-Encoder Reranking
- [ ] **Sparse Search (BM25)**:
  - Term Frequency (TF) and Inverse Document Frequency (IDF) mathematics.
  - When vector search fails (exact SKUs, acronyms, code symbols).
- [ ] **Reciprocal Rank Fusion (RRF)**:
  - Merging ranked lists mathematically: $RRF(d) = \sum_{m \in M} \frac{1}{60 + r_m(d)}$.
- [ ] **Bi-Encoder vs. Cross-Encoder Architecture**:
  - Bi-Encoders: Embed Query and Document separately (fast vector lookup, weak interaction).
  - Cross-Encoders: Concatenate Query + Document into a single Transformer pass (slow, but captures deep semantic interaction for reranking).

---

## Part 4: Production RAG (Retrieval-Augmented Generation) Architecture

### Chapter 14: Ingestion Pipelines & Advanced Chunking
- [ ] **Document Parsing**:
  - Extracting tables, headers, and reading order from complex multi-column PDFs.
- [ ] **Chunking Strategies Compared**:
  - Fixed-Size Chunking (with overlap).
  - Recursive Character Splitting.
  - **Semantic Chunking**: Splitting text dynamically where the cosine distance between consecutive sentence embeddings spikes.
  - **Parent-Child Chunking**: Indexing small 100-token sentences for vector precision, but injecting the surrounding 1,000-token parent chunk into the prompt.

---

### Chapter 15: Query Transformations & Context Optimization
- [ ] **Query Transformations**:
  - Multi-Query Expansion: Generating 3 diverse search queries from 1 user input.
  - **HyDE (Hypothetical Document Embeddings)**: Generating a hypothetical response first, then embedding the response.
  - Step-Back Prompting: Abstracting the user query into high-level concepts.
- [ ] **Context Optimization**:
  - Solving the **"Lost in the Middle"** problem: Ordering retrieved documents so the most critical evidence is at the very beginning and end of the context window.
  - Contextual Retrieval (Anthropic technique): Generating an automated document-level summary prefix for every chunk before embedding.

---

### Chapter 16: Corrective RAG (CRAG) & Self-RAG
- [ ] **CRAG (Corrective RAG)**:
  - Evaluating retrieved document confidence:
    - *High Confidence*: Proceed directly to generation.
    - *Low Confidence*: Discard chunks, automatically fall back to Web Search API.
    - *Ambiguous*: Blend web search and vector chunks.
- [ ] **Self-RAG (Self-Reflective RAG)**:
  - Model outputs reflection tokens: Is this retrieved? Is this supported? Is this useful?
  - Automatic hallucination rejection and citation grounding.

---

## Part 5: Structured Outputs & Function Calling Protocols

### Chapter 17: Constrained Decoding & Schema Enforcement
- [ ] **Why Naive Prompting Fails**: Prompting `"Output valid JSON"` still yields syntax errors in 1 out of 500 requests at production scale.
- [ ] **Grammar-Based Constrained Decoding**:
  - Converting a Pydantic schema / JSON Schema into a formal Context-Free Grammar (CFG / BNF).
  - At every generation step, masking the model's output logits so that tokens violating the grammar have probability $-\infty$.
  - Libraries: `Outlines`, `Instructor`, OpenAI Structured Outputs.

---

### Chapter 18: Tool & Function Calling Under the Hood
- [ ] **The Protocol Lifecycle**:
  1. Client sends JSON Schema tool definitions.
  2. Model outputs structured `tool_calls` arguments.
  3. Client executes the function locally.
  4. Client feeds the tool response back as a `tool` role message.
  5. Model synthesizes the final response.
- [ ] **Self-Healing Tool Execution**:
  - Catching local Python/SQL runtime exceptions and injecting the error message back into the conversation so the model self-corrects its arguments.

---

## Part 6: Autonomous Agents & Multi-Agent Swarms

### Chapter 19: Building a ReAct Agent from Scratch (No Frameworks)
- [ ] **The Core ReAct Loop**:
  - Constructing a pure Python agent using standard loops without LangChain or LlamaIndex:
    - `Thought`: Model reasoning.
    - `Action`: Function name + arguments.
    - `Observation`: Result of the executed Python function.
    - Loop termination: When model outputs `Final Answer`.
- [ ] **Memory Management**: Sliding window conversation history, summary memory, and token limit budgeting.

---

### Chapter 20: Stateful Agent State Machines with LangGraph
- [ ] **Graph-Based Agent Architecture**:
  - State schemas, Graph Nodes (action execution), Conditional Edges (branching logic).
  - Cyclic execution: Allowing agents to retry steps, run critique loops, and self-correct.
  - **Human-in-the-Loop (HITL)**: Setting breakpoints before critical nodes (e.g., executing code, modifying database records) to await explicit user confirmation.
  - Checkpointing & Time-Travel: Restoring agent state from any point in the history.

---

### Chapter 21: The Model Context Protocol (MCP) Architecture
- [ ] **Standardizing the Agent Ecosystem**:
  - Solving the $M$ agents $\times$ $N$ tools integration problem.
- [ ] **MCP Protocol Internals (JSON-RPC 2.0)**:
  - **MCP Host**: The application orchestrating the conversation (e.g., Claude Desktop, custom agent).
  - **MCP Client**: Maintains 1:1 connections with servers.
  - **MCP Server**: Lightweight process exposing Prompts, Resources (read-only data), and Tools (executable functions).
- [ ] **Hands-on Implementation**:
  - Building an MCP server in Python/TypeScript exposing a local SQLite database and file search tools.

---

### Chapter 22: Multi-Agent Coordination & Swarm Patterns
- [ ] **Orchestrator-Worker Architecture**:
  - Central planner breaks complex tasks into sub-tasks and delegates to specialized agents (e.g., Researcher Agent, Coder Agent, Validator Agent).
- [ ] **Supervisor & Swarm Patterns**:
  - Peer-to-peer handoffs vs. centralized routing.
  - Guarding against infinite agent ping-pong loops: Hard iteration limits and execution timeouts.

---

## Part 7: Model Customization & Fine-Tuning

### Chapter 23: The Prompt vs. RAG vs. Fine-Tune Decision Framework
- [ ] **When to Fine-Tune (and when NOT to)**:
  - Fine-tuning changes *form, tone, format, and behavior*. It is ineffective for injecting factual knowledge (RAG is required).
  - Cost, maintenance, and dataset preparation trade-offs.

---

### Chapter 24: Parameter-Efficient Fine-Tuning (PEFT: LoRA & QLoRA)
- [ ] **The Mathematics of LoRA (Low-Rank Adaptation)**:
  - Decomposing weight updates $\Delta W$ ($d \times k$) into two smaller low-rank matrices $B$ ($d \times r$) and $A$ ($r \times k$) where rank $r \ll d$.
  - Number of trainable parameters drops by $>99\%$.
- [ ] **QLoRA (Quantized LoRA)**:
  - Quantizing the base model weights to 4-bit NormalFloat (NF4).
  - Double Quantization and Paged Optimizers to eliminate memory spikes.
- [ ] **Instruction Dataset Preparation & Training**:
  - Formatting datasets (Alpaca vs. ShareGPT formats).
  - Fine-tuning an open-source model (e.g., Llama 3 8B) using Unsloth / HuggingFace `peft` and `trl`.

---

## Part 8: Evals, Observability & Security

### Chapter 25: Non-Deterministic Testing & LLM-as-a-Judge Evals
- [ ] **The Failure of Unit Tests**: Non-deterministic outputs cannot be evaluated with string matching.
- [ ] **RAGAS Evaluation Framework**:
  - **Faithfulness**: Are all claims grounded strictly in the retrieved context?
  - **Answer Relevance**: Does the answer address the question?
  - **Context Precision & Recall**: Did retrieval find all the signal and minimize noise?
- [ ] **Synthetic Test Generation**: Generating 100 test questions and ground-truth answers from source documents using an LLM.

---

### Chapter 26: Observability, Tracing & AI Safety
- [ ] **Production Tracing**:
  - Tracing nested execution trees with **Langfuse / LangSmith**: Latency per node, token counts, cost tracking.
- [ ] **Threat Mitigation**:
  - Direct Prompt Injection (jailbreaking).
  - **Indirect Prompt Injection**: Malicious instructions embedded inside retrieved documents, websites, or emails hijacking the agent.
  - Defense: PII sanitization, dual-LLM input/output validation, NeMo Guardrails.

---

## Part 9: Production Capstone Projects

### Chapter 27: Capstone 1 — Enterprise Hybrid Knowledge RAG with Self-Correction
- [ ] Ingests enterprise documents (PDFs, Markdown) into `pgvector` with HNSW indexes.
- [ ] Implements hybrid search (BM25 + Dense vector embeddings) with Cohere Rerank.
- [ ] Features Self-Reflective evaluation (CRAG) and automated citation footnotes.
- [ ] Complete CI/CD eval suite measuring Faithfulness and Context Precision using Langfuse.

### Chapter 28: Capstone 2 — Autonomous Research & Data Agent with MCP
- [ ] Built with LangGraph state machines and Human-in-the-Loop checkpoints.
- [ ] Connects via Model Context Protocol (MCP) to SQLite databases and sandboxed Python execution environments.
- [ ] Autonomously plans tasks, writes and runs SQL queries, generates charts, critiques its own output, and generates structured executive reports.
