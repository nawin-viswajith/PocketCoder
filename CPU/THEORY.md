# Theory of Mobile LLM Deployment

## Introduction

Modern Large Language Models (LLMs) such as Qwen, Llama, Mistral, Gemma, and DeepSeek are typically trained on large clusters containing multiple GPUs and hundreds of gigabytes of memory.

Although these models can demonstrate impressive reasoning and coding abilities, they are generally designed for server-class hardware rather than mobile devices.

This project demonstrates how a model originally intended for data-center deployment can be transformed into a format suitable for execution on a smartphone.

The complete deployment pipeline consists of four major stages:

```text
Transformer Model
        ↓
Hugging Face Checkpoint
        ↓
GGUF Conversion
        ↓
Quantization
        ↓
Mobile Inference
```

Understanding each stage explains why a 7-billion parameter model can run locally on a device that fits inside a pocket.

---

# Large Language Models as Mathematical Functions

At their core, neural networks are mathematical functions.

Given an input:

```text
x
```

the network produces an output:

```text
y = f(x)
```

The behavior of this function is controlled by a large collection of learned numerical values called parameters or weights.

For a model advertised as:

```text
7 Billion Parameters
```

the total number of learned values is approximately:

```text
7 × 10^9
```

Each parameter contributes to the model's ability to recognize patterns in language, code, mathematics, and reasoning tasks.

During training, these parameters are adjusted repeatedly until the model learns useful statistical relationships from enormous datasets.

---

# Tokens and Tokenization

Language models do not process words directly.

Instead, text is converted into tokens.

Example:

```text
Hello world
```

may become:

```text
[9707, 1879]
```

depending on the tokenizer used by the model.

Similarly:

```text
def hello_world():
```

may be split into multiple code-related tokens.

Each token represents a numerical identifier rather than a word itself.

The model only understands numbers.

---

# Embeddings

Tokens are transformed into dense vectors called embeddings.

For example:

```text
Token: "cat"
```

may become:

```text
[0.12, -0.41, 0.87, ...]
```

containing thousands of dimensions.

These vectors are learned during training.

Embeddings capture semantic relationships between concepts.

Examples:

```text
King - Man + Woman ≈ Queen
```

or

```text
Python ≈ Programming
```

in vector space.

Embeddings serve as the input representation for transformer models.

---

# Transformers

Most modern LLMs use the Transformer architecture introduced in the paper:

```text
Attention Is All You Need
```

published by Google in 2017.

Transformers replaced earlier architectures because they scale effectively to extremely large datasets and parameter counts.

The fundamental innovation of transformers is self-attention.

---

# Self-Attention

Self-attention allows every token to examine every other token in the sequence.

Consider:

```text
The animal didn't cross the street because it was tired.
```

The model must determine that:

```text
it → animal
```

rather than:

```text
it → street
```

Self-attention enables this relationship to be learned dynamically.

Each token generates three vectors:

```text
Query
Key
Value
```

Attention scores are computed using:

```text
Attention(Q, K, V)
=
softmax(QKᵀ / √d) V
```

This operation determines which tokens should influence the current prediction.

Self-attention is one of the most computationally expensive parts of transformer inference.

---

# Matrix Multiplication

Most transformer computation ultimately reduces to matrix multiplication.

A simplified operation looks like:

```text
Y = XW
```

where:

```text
X = Input Matrix
W = Weight Matrix
Y = Output Matrix
```

Large language models perform billions of such operations during inference.

This is why specialized hardware such as GPUs and NPUs can dramatically accelerate LLM execution.

---

# Predicting the Next Token

A language model does not "know" the answer.

Instead it predicts probabilities.

Example:

```text
The capital of France is
```

Possible output:

```text
Paris     94%
London     2%
Berlin     1%
Other      3%
```

The model selects one token and then repeats the process again.

Generation therefore occurs one token at a time.

---

# Why Models Are So Large

Most modern checkpoints are stored using FP16 precision.

FP16 means:

```text
16 bits per parameter
```

A 7-billion parameter model therefore requires approximately:

```text
7,000,000,000 × 16 bits
```

Converting bits to bytes:

```text
(7,000,000,000 × 16) / 8
```

gives:

```text
14 GB
```

approximately.

A smartphone cannot efficiently dedicate 14 GB of memory to a single model.

Compression is therefore required.

---

# Quantization

Quantization reduces memory requirements by representing weights using fewer bits.

Instead of storing:

```text
0.24873
-0.91374
0.14829
```

the model stores compressed approximations.

The objective is:

```text
Smaller Model
Lower Memory Usage
Faster Inference
Minimal Accuracy Loss
```

---

# Q4_0 Quantization

In this project, the model was quantized using:

```text
Q4_0
```

Q4 means:

```text
4 bits per weight
```

rather than:

```text
16 bits per weight
```

Each weight is represented using one of:

```text
2^4 = 16
```

possible values.

Instead of storing the original floating-point number directly, the quantizer stores:

```text
Quantized Integer
Scale Factor
```

The original weight can be approximated as:

```text
w ≈ s × q
```

where:

```text
w = Original Weight
s = Scale Factor
q = Quantized Integer
```

This dramatically reduces storage requirements.

---

# Compression Ratio

FP16 storage:

```text
16 bits per weight
```

Q4_0 storage:

```text
4 bits per weight
```

Compression ratio:

```text
16 ÷ 4 = 4×
```

A model requiring:

```text
~14 GB
```

can therefore be reduced to approximately:

```text
~4.3 GB
```

making smartphone deployment feasible.

---

# Quantization Error

Compression introduces approximation error.

Mathematically:

```text
error = original_weight - quantized_weight
```

The challenge of quantization is balancing:

```text
Model Size
Inference Speed
Output Quality
```

A smaller model is faster but less accurate.

A larger model is more accurate but slower and more memory intensive.

---

# Why Q4_0 Was Selected

This project uses Q4_0 because it provides:

* Significant compression
* Good inference speed
* Broad llama.cpp compatibility
* Mobile-friendly memory requirements

Alternative formats include:

```text
Q4_K_M
Q5_K_M
Q6_K
Q8_0
```

which generally improve quality at the cost of larger file sizes.

---

# Hugging Face Checkpoints

Models downloaded from Hugging Face are typically stored as:

```text
safetensors
```

files.

These files contain:

* Neural network weights
* Model configuration
* Tokenizer data
* Training metadata

They are designed for interoperability with machine learning frameworks such as PyTorch.

---

# Why GGUF Exists

llama.cpp does not directly execute Hugging Face checkpoints.

Instead it expects models in:

```text
GGUF
```

format.

GGUF is a deployment-oriented format optimized for inference.

Benefits include:

* Faster loading
* Quantization support
* Embedded metadata
* Single-file deployment
* Efficient memory layout

GGUF acts as a bridge between training frameworks and lightweight inference engines.

---

# KV Cache

During generation, the model stores intermediate attention information.

This storage is called the Key-Value Cache.

Without KV caching, the model would repeatedly recompute previous tokens.

As conversations become longer, computational cost grows rapidly.

The KV cache allows previous computations to be reused efficiently.

This is one of the reasons long-context conversations remain practical.

---

# Running on a Smartphone

The deployment target for this project is:

```text
Samsung Galaxy S26 Ultra
12 GB RAM
Snapdragon 8 Elite
```

The quantized model occupies roughly:

```text
4.3 GB
```

Additional memory is required for:

* Android operating system
* Runtime buffers
* KV cache
* Context window
* llama.cpp overhead

This is why quantization is essential.

Without quantization, deployment would not be practical.

---

# CPU Inference

When running on the CPU, the processor repeatedly performs matrix multiplication and attention calculations.

Performance depends on:

* Core count
* SIMD instructions
* Cache hierarchy
* Memory bandwidth
* Thread configuration

Observed benchmark results for this project:

```text
Model: Qwen2.5-Coder-7B Q4_0
Threads: 8

Prompt Processing: ~13.6 tokens/sec
Generation: ~10 tokens/sec
```

These results demonstrate that modern flagship smartphones are capable of running coding-focused LLMs locally.

---

# Why Training and Inference Differ

Training performs:

```text
Forward Pass
Backward Pass
Gradient Updates
```

Inference performs only:

```text
Forward Pass
```

Inference is therefore significantly cheaper than training.

However, processing billions of parameters still requires substantial computational resources.

---

# Future Direction: NPU Acceleration

Modern Snapdragon processors contain dedicated AI accelerators known as NPUs.

These processors are optimized for:

* Matrix multiplication
* Tensor operations
* Quantized arithmetic

Potential benefits include:

* Higher throughput
* Lower power consumption
* Reduced heat generation
* Improved sustained performance

The planned NPU workflow for this project is:

```text
Hugging Face Model
        ↓
ONNX Export
        ↓
QNN Conversion
        ↓
Qualcomm AI Engine Runtime
        ↓
Hexagon NPU
        ↓
Mobile Inference
```

---

# End-to-End Deployment Pipeline

The complete workflow implemented in this repository is:

```text
Qwen 2.5 Coder 7B
        ↓
Hugging Face Download
        ↓
Safetensors Checkpoint
        ↓
GGUF Conversion
        ↓
Q4_0 Quantization
        ↓
4.3 GB Mobile Model
        ↓
ADB Deployment
        ↓
Android Termux
        ↓
llama.cpp
        ↓
Local Inference
        ↓
OpenAI-Compatible API
```

---

# Conclusion

Large language models are fundamentally collections of matrices and numerical parameters.

By converting Hugging Face checkpoints into GGUF format and applying quantization techniques such as Q4_0, models originally designed for servers can be compressed sufficiently to run on modern smartphones.

This project demonstrates that local AI deployment is no longer limited to high-end GPUs. With careful optimization, quantization, and efficient inference engines such as llama.cpp, powerful coding models can operate entirely offline on consumer mobile hardware.
