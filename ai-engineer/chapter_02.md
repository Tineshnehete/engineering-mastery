# Chapter 02: Neural Networks & Backpropagation from Scratch (Deep Dive)

> **Welcome to Chapter 02 of the AI Engineer Track!**
> Most machine learning courses tell you to type `model = torch.nn.Sequential(...)` or `model.fit()`. 
> 
> But if you only use high-level libraries, you will never understand what the GPU is actually computing, why weights blow up, or how attention layers in Transformers work.
> 
> In this chapter, we will build a complete Multi-Layer Neural Network from scratch in pure Python (NumPy) and pure JavaScript. We will derive **Backpropagation** step-by-step and master weight initialization (He/Xavier) and Layer Normalization.

---

## 1. Anatomy of a Multi-Layer Perceptron (MLP)

A **Neural Network** is simply a series of matrix multiplications separated by non-linear activation functions.

```mermaid
flowchart LR
    subgraph InputLayer["Input Layer (X)"]
        x1["Feature 1"]
        x2["Feature 2"]
    end

    subgraph HiddenLayer["Hidden Layer (H)"]
        h1["Neuron 1"]
        h2["Neuron 2"]
        h3["Neuron 3"]
        h4["Neuron 4"]
    end

    subgraph OutputLayer["Output Layer (Y)"]
        y1["Final Output"]
    end

    x1 & x2 -->|"Weights W1 + Bias b1"| h1 & h2 & h3 & h4
    h1 & h2 & h3 & h4 -->|"Weights W2 + Bias b2"| y1
```

### The Matrix Dimensions:
For a batch of $B$ items:
1. **Input ($X$)**: Shape `(B x 2)` (2 features per item).
2. **First Weight Matrix ($W_1$)**: Shape `(2 x 4)` (connects 2 inputs to 4 hidden neurons).
3. **Hidden Layer ($Z_1 = X \times W_1 + b_1$)**: Shape `(B x 4)`.
4. **Activation ($A_1 = \text{Activation}(Z_1)$)**: Shape `(B x 4)`.
5. **Second Weight Matrix ($W_2$)**: Shape `(4 x 1)`.
6. **Final Output ($Z_2 = A_1 \times W_2 + b_2$)**: Shape `(B x 1)`.

---

## 2. Weight Initialization: The Xavier / He Breakthrough

What happens if you initialize weights by multiplying random numbers by `0.0001`?
* Across 50 layers, multiplying activations by tiny numbers causes signal values to **vanish to 0.000000**!
* If you initialize with large numbers like `10.0`, activations explode to **`Infinity` / `NaN`**!

```mermaid
flowchart LR
    W_Small["Small Weights (0.0001):<br/>Signal vanishes to 0 across layers!"]
    W_Big["Large Weights (10.0):<br/>Signal explodes to Infinity / NaN!"]
    He_Init["He / Xavier Initialization:<br/>Std = sqrt(2 / Input_Dimensions)<br/>Variance stays 1.0 through 100 layers!"]
```

### The He Initialization Formula (Kaiming He):
$$\text{Weight Standard Deviation} = \sqrt{\frac{2}{\text{Number of Input Neurons}}}$$
* In Python: `W = np.random.randn(d_in, d_out) * np.sqrt(2.0 / d_in)`.
* This single mathematical scaling guarantees that signal variance remains constant whether your network has 2 layers or 100 layers!

---

## 3. Batch Normalization vs. Layer Normalization (Why Transformers Use LayerNorm)

In deep learning, you will hear about two kinds of normalization:

```mermaid
flowchart TD
    subgraph BatchNorm["1. Batch Normalization (Computer Vision)"]
        BN["Normalizes across the BATCH for one feature.<br/>(Fails on NLP because sentences have different lengths!)"]
    end
    subgraph LayerNorm["2. Layer Normalization (Transformers / LLMs)"]
        LN["Normalizes across the FEATURES of a single token independently.<br/>(Works perfectly for variable sequence lengths!)"]
    end
```

* **Batch Normalization**: Used in CNNs for images. It calculates mean and variance across the batch. It fails in NLP because sentence 1 might be 5 words and sentence 2 might be 50 words!
* **Layer Normalization (LayerNorm / RMSNorm)**: Used in all modern Transformers (Llama, GPT, Claude). It normalizes the embedding dimensions of each token independently, making it immune to variable batch sizes and sequence lengths!

---

## 4. Backpropagation: The Chain Rule in Reverse

```mermaid
flowchart RL
    Loss["1. Calculate Loss (Mistake)"] -->|"Step 1"| dW2["Calculate gradients for W2 and b2"]
    dW2 -->|"Step 2: Chain Rule through Activation"| dH["Calculate gradients for Hidden Layer"]
    dH -->|"Step 3"| dW1["Calculate gradients for W1 and b1"]
    dW1 --> Update["Update all weights simultaneously!"]
```

1. Error at the output: $d_{\text{output}} = \text{Predictions} - \text{Targets}$.
2. Gradient for $W_2$: $\nabla W_2 = A_1^T \times d_{\text{output}}$.
3. Error at the hidden layer: $d_{\text{hidden}} = (d_{\text{output}} \times W_2^T) \times f'(Z_1)$.
4. Gradient for $W_1$: $\nabla W_1 = X^T \times d_{\text{hidden}}$.

---

## 5. Complete Implementation in Pure Python (NumPy)

Let's train a 2-layer Neural Network to solve the non-linear **XOR Problem** from scratch using **He Initialization**:

```python
import numpy as np

# 1. Dataset: XOR truth table
X = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
y = np.array([[0], [1], [1], [0]])

# 2. He Weight Initialization
np.random.seed(42)
d_in, d_hidden, d_out = 2, 4, 1
W1 = np.random.randn(d_in, d_hidden) * np.sqrt(2.0 / d_in)
b1 = np.zeros((1, d_hidden))
W2 = np.random.randn(d_hidden, d_out) * np.sqrt(2.0 / d_hidden)
b2 = np.zeros((1, d_out))

# Activation: Sigmoid and its derivative
def sigmoid(z):
    return 1 / (1 + np.exp(-z))

def sigmoid_derivative(a):
    return a * (1 - a)

learning_rate = 0.5

# 3. Training Loop (Gradient Descent)
for epoch in range(10000):
    # --- FORWARD PASS ---
    Z1 = np.dot(X, W1) + b1
    A1 = sigmoid(Z1)
    
    Z2 = np.dot(A1, W2) + b2
    predictions = sigmoid(Z2)
    
    # --- BACKWARD PASS (Backpropagation) ---
    dZ2 = (predictions - y) * sigmoid_derivative(predictions)
    dW2 = np.dot(A1.T, dZ2)
    db2 = np.sum(dZ2, axis=0, keepdims=True)
    
    dA1 = np.dot(dZ2, W2.T)
    dZ1 = dA1 * sigmoid_derivative(A1)
    dW1 = np.dot(X.T, dZ1)
    b1_grad = np.sum(dZ1, axis=0, keepdims=True)
    
    # --- WEIGHT UPDATE ---
    W2 -= learning_rate * dW2
    b2 -= learning_rate * db2
    W1 -= learning_rate * dW1
    b1 -= learning_rate * b1_grad

# 4. Evaluation
print("--- Final Predictions ---")
for i in range(len(X)):
    pred = predictions[i][0]
    print(f"Input: {X[i]} | Target: {y[i][0]} | AI Prediction: {pred:.3f} (Rounded: {round(pred)})")
```

---

## 6. Complete Implementation in Pure JavaScript

```javascript
const X = [[0, 0], [0, 1], [1, 0], [1, 1]];
const y = [[0], [1], [1], [0]];

const sigmoid = (z) => 1 / (1 + Math.exp(-z));
const sigmoidDeriv = (a) => a * (1 - a);

// He initialization in JS
let W1 = Array.from({ length: 2 }, () => Array.from({ length: 4 }, () => (Math.random() - 0.5) * Math.sqrt(2 / 2)));
let b1 = [0, 0, 0, 0];
let W2 = Array.from({ length: 4 }, () => [(Math.random() - 0.5) * Math.sqrt(2 / 4)]);
let b2 = [0];

const learningRate = 0.5;

for (let epoch = 0; epoch < 10000; epoch++) {
    for (let i = 0; i < X.length; i++) {
        let A1 = [];
        for (let j = 0; j < 4; j++) {
            let z1 = X[i][0] * W1[0][j] + X[i][1] * W1[1][j] + b1[j];
            A1.push(sigmoid(z1));
        }

        let z2 = b2[0];
        for (let j = 0; j < 4; j++) z2 += A1[j] * W2[j][0];
        let pred = sigmoid(z2);

        let error = pred - y[i][0];
        let dZ2 = error * sigmoidDeriv(pred);

        for (let j = 0; j < 4; j++) {
            W2[j][0] -= learningRate * (A1[j] * dZ2);
        }
        b2[0] -= learningRate * dZ2;

        for (let j = 0; j < 4; j++) {
            let dZ1 = (dZ2 * W2[j][0]) * sigmoidDeriv(A1[j]);
            W1[0][j] -= learningRate * (X[i][0] * dZ1);
            W1[1][j] -= learningRate * (X[i][1] * dZ1);
            b1[j] -= learningRate * dZ1;
        }
    }
}

console.log("--- JavaScript Training Complete ---");
X.forEach((input, idx) => {
    let A1 = [0, 1, 2, 3].map(j => sigmoid(input[0] * W1[0][j] + input[1] * W1[1][j] + b1[j]));
    let z2 = b2[0] + A1.reduce((sum, val, j) => sum + val * W2[j][0], 0);
    let pred = sigmoid(z2);
    console.log(`Input: [${input}] -> Target: ${y[idx]} | Pred: ${pred.toFixed(3)} (Rounded: ${Math.round(pred)})`);
});
```

---

## Chapter 02 Checklist (Summary)

Before moving to Chapter 03, verify that you can answer these:
- [x] How do matrix dimensions change from input through hidden layers to the output?
- [x] Why does He / Xavier initialization use $\sqrt{2 / d_{in}}$ to prevent vanishing/exploding signals?
- [x] Why do Transformers use Layer Normalization instead of Batch Normalization?
- [x] How does backpropagation use the Calculus Chain Rule to calculate gradients in reverse?
- [x] What is the difference between an Epoch and a Batch?

*Chapter 02 is complete! Proceed to [Chapter 03: Tokenization & Byte-Pair Encoding (BPE) from Scratch](./chapter_03.md).*
