# Mathematical Foundations of Mobile LLM Deployment

## 1. Language Modeling as Probability Estimation

The objective of a language model is to estimate the probability of the next token given all previous tokens.

```text
P(x_t | x_1, x_2, ..., x_(t-1))
```

where:

```text
x_1 ... x_(t-1) = Previous Tokens
x_t             = Next Token
```

During training, the model maximizes the likelihood of observing the correct next token.

Training Objective:

```text
L = Σ log P(x_t | x_<t)
```

where:

```text
L      = Log-Likelihood
Σ      = Summation Across Training Tokens
x_<t   = All Tokens Before Position t
```

Inference consists of repeatedly sampling from this probability distribution.

---

## 2. Embedding Layer

Language models do not process words directly.

Each token is converted into a dense vector using an embedding matrix.

Definitions:

```text
V = Vocabulary Size
d = Embedding Dimension
```

Embedding Matrix:

```text
E ∈ R^(|V| × d)
```

For a token index:

```text
i
```

the embedding vector becomes:

```text
e_i = E[i]
```

Example:

```text
Vocabulary Size  = 151,936
Embedding Size   = 3,584
Token ID         = 9,707
```

Embedding Matrix Shape:

```text
E ∈ R^(151936 × 3584)
```

Parameter Count:

```text
151,936 × 3,584

= 544,538,624 Parameters
```

Each token is therefore transformed into a vector containing 3,584 learned numerical values.

---

## 3. Transformer Layer

Given an input matrix:

```text
X ∈ R^(n × d)
```

where:

```text
n = Sequence Length
d = Hidden Dimension
```

the model computes:

```text
Q = XW_Q
K = XW_K
V = XW_V
```

where:

```text
W_Q ∈ R^(d × d)
W_K ∈ R^(d × d)
W_V ∈ R^(d × d)
```

These matrices are learned during training.

---

## 4. Self-Attention

Self-attention allows every token to examine every other token in the sequence.

Attention Equation:

```text
Attention(Q,K,V)

= softmax(QK^T / √d_k)V
```

where:

```text
Q = Query Matrix
K = Key Matrix
V = Value Matrix
d_k = Key Dimension
```

Dimensions:

```text
Q ∈ R^(n × d)
K ∈ R^(n × d)
V ∈ R^(n × d)
```

Matrix Multiplication:

```text
QK^T

(n × d) × (d × n)

= (n × n)
```

This produces the attention score matrix.

Example:

```text
Sequence Length = 4096

Attention Matrix

4096 × 4096

= 16,777,216 Scores
```

Computational Complexity:

```text
O(n²)
```

This is one of the primary bottlenecks in long-context inference.

---

## 5. Feed Forward Network (FFN)

Each transformer block contains a Feed Forward Network.

Equation:

```text
FFN(x)

= W₂σ(W₁x + b₁) + b₂
```

where:

```text
σ = Activation Function
W₁ = First Weight Matrix
W₂ = Second Weight Matrix
b₁ = Bias Vector
b₂ = Bias Vector
```

Typical Expansion:

```text
d → 4d → d
```

Example:

```text
3584 → 14336 → 3584
```

Parameter Count:

```text
3584 × 14336

= 51,380,224
```

for a single FFN matrix.

This contributes significantly to overall model size.

---

## 6. Parameter Count

For a dense matrix:

```text
W ∈ R^(m × n)
```

Parameter Count:

```text
Parameters = m × n
```

Example:

```text
3584 × 3584

= 12,845,056 Parameters
```

A 7B model contains approximately:

```text
7 × 10^9

= 7,000,000,000 Parameters
```

---

## 7. Memory Requirement

FP16 stores each parameter using:

```text
16 Bits
= 2 Bytes
```

Memory Formula:

```text
Memory

= Parameters × Bytes Per Parameter
```

For a 7B model:

```text
Parameters

= 7 × 10^9

Bytes Per Parameter

= 2
```

Total Memory:

```text
7 × 10^9 × 2

= 14 × 10^9 Bytes

≈ 14 GB
```

This is why a raw FP16 checkpoint cannot comfortably run on a smartphone.

---

## 8. Quantization

Quantization compresses model weights.

Original Weight:

```text
w ∈ R
```

Quantized Representation:

```text
w ≈ s × q
```

where:

```text
w = Original Weight
s = Scale Factor
q = Quantized Integer
```

Example:

```text
Original Weight

0.24873

Scale

0.03553

Quantized Value

7

Reconstructed Weight

0.03553 × 7

= 0.24871
```

---

## 9. Quantization Error

Quantization introduces approximation error.

Error:

```text
e = w - ŵ
```

where:

```text
w  = Original Weight
ŵ  = Quantized Weight
```

Example:

```text
Original

0.24873

Quantized

0.24871

Error

= 0.24873 - 0.24871

= 0.00002
```

Mean Squared Error:

```text
MSE

= (1/N) Σ(w - ŵ)²
```

The objective is to minimize MSE while maximizing compression.

---

## 10. Compression Ratio

FP16:

```text
16 Bits Per Weight
```

Q4_0:

```text
4 Bits Per Weight
```

Compression Ratio:

```text
16 / 4

= 4×
```

Memory Reduction:

```text
14 GB / 4

= 3.5 GB
```

Actual Deployment Size:

```text
≈ 4.3 GB
```

Additional storage is consumed by:

```text
Metadata
Tokenizer
Scale Factors
Quantization Blocks
GGUF Headers
```

---

## 11. KV Cache

During generation, attention results are stored in memory.

KV Cache Formula:

```text
M_KV

≈ 2 × n × d × L × B
```

where:

```text
n = Context Length
d = Hidden Dimension
L = Number of Layers
B = Bytes Per Element
```

Example:

```text
n = 4096
d = 3584
L = 28
B = 2
```

Calculation:

```text
2 × 4096 × 3584 × 28 × 2

= 1,644,167,168 Bytes

≈ 1.53 GB
```

A large context window therefore requires substantial memory.

---

## 12. Throughput

Throughput is measured as:

```text
TPS

= Tokens Generated / Time
```

Example:

```text
100 Tokens
10 Seconds
```

Calculation:

```text
TPS

= 100 / 10

= 10 Tokens Per Second
```

Observed Benchmark:

```text
Model

Qwen2.5-Coder-7B Q4_0

Threads

8

Prompt Processing

≈ 13.6 Tokens/sec

Generation

≈ 10 Tokens/sec
```

---

## 13. Computational Cost

Self-Attention Cost:

```text
O(n²d)
```

Feed Forward Cost:

```text
O(nd²)
```

Total Layer Cost:

```text
O(n²d + nd²)
```

Example:

```text
n = 4096
d = 3584

Attention Operations

≈ 4096² × 3584

≈ 60 Billion Operations
```

This explains why specialized hardware is beneficial.

---

## 14. Mobile Deployment Pipeline

Mathematical Transformation:

```text
FP16 Model

f(x;W)

W ∈ R^(7×10^9)

≈ 14 GB
```

↓

```text
GGUF Conversion

f(x;W)

Different Storage Format

Same Mathematics
```

↓

```text
Quantization

ŵ = Q(W)
```

↓

```text
Memory Reduction

M_Q4

≈ M_FP16 / 4

≈ 14 / 4

≈ 3.5 GB
```

↓

```text
Inference

ŷ = f(x;ŵ)
```

↓

```text
Token Prediction

argmax P(x_t | x_<t)
```

↓

```text
Generated Response
```

---

## Conclusion

A Large Language Model is fundamentally a sequence of matrix multiplications, attention operations, and probability estimations.

The deployment process performed in this project can be summarized mathematically as:

```text
W ∈ R^(7×10^9)

↓

GGUF(W)

↓

Q(W)

↓

ŵ

↓

f(x;ŵ)

↓

P(x_t | x_<t)

↓

Generated Tokens
```

Quantization reduces memory requirements by approximately 4× while preserving most of the model's behavior, enabling a 7-billion parameter coding model to execute locally on a modern smartphone.
