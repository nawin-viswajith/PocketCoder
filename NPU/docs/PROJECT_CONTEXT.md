# Project Context

## Project Objective

The long-term objective of this project is to execute a 7-billion parameter coding Large Language Model entirely on a flagship Android smartphone using the Qualcomm Hexagon NPU.

Unlike conventional mobile deployments that rely solely on the CPU, this project investigates hardware acceleration through the Snapdragon AI Engine while maintaining an entirely offline inference pipeline.

The final goal is to expose the model as an OpenAI-compatible API server running directly on the phone.

---

# Current Project Status

## CPU Deployment

Status

```text
Completed
```

Achievements

```text
✓ Downloaded Qwen2.5-Coder-7B-Instruct
✓ Converted Hugging Face model to GGUF
✓ Quantized model to Q4_0
✓ Deployed model to Android
✓ Configured Termux
✓ Built llama.cpp
✓ Successfully executed inference
✓ Successfully benchmarked CPU inference
✓ Started OpenAI-compatible API server
```

---

# Current CPU Benchmark

Device

```text
Samsung Galaxy S26 Ultra

Snapdragon 8 Elite Gen 5

12 GB RAM

256 GB Storage
```

Model

```text
Qwen2.5-Coder-7B

Q4_0
```

Backend

```text
CPU
```

Optimized Configuration

```text
Threads

8
```

Observed Performance

```text
Prompt Processing

≈13.6 Tokens/sec

Generation

≈10 Tokens/sec
```

This benchmark serves as the baseline for all future NPU comparisons.

---

# Why Move to the NPU?

Although CPU inference is functional, it has several limitations.

```text
Higher Power Consumption

Higher CPU Utilization

Thermal Throttling

Competition with Android Processes
```

The Snapdragon platform contains a dedicated AI accelerator called the Hexagon Tensor Processor (HTP).

The objective is to determine whether the model can execute on the NPU more efficiently than on the CPU.

---

# Knowledge Acquired During Research

The following concepts have already been investigated.

## Hexagon Tensor Processor

HTP stands for:

```text
Hexagon Tensor Processor
```

The HTP is Qualcomm's dedicated AI accelerator for tensor operations such as:

- Matrix Multiplication
- Attention
- Feed Forward Networks
- Neural Network Inference

---

## HTP Devices

Identifiers such as

```text
HTP0

HTP1

HTP2

HTP3
```

do **not** represent multiple phones.

Instead, they are logical execution devices exposed by the Qualcomm AI Runtime.

The exact number available depends on the Snapdragon platform and runtime implementation.

---

## Model Size

Current model:

```text
Qwen2.5-Coder-7B

Q4_0

≈4.3 GB
```

---

## Known Memory Constraint

Existing documentation suggests that a single HTP execution context may have a practical memory limit of approximately:

```text
≈3.5 GB
```

This immediately raises the primary engineering question.

```text
Can a 4.3 GB model execute entirely on the Hexagon NPU?
```

---

# Current Hypothesis

Three possible execution strategies exist.

## Scenario 1

Entire model executes on one HTP.

```text
Model

↓

HTP0
```

Current Assessment

```text
Unlikely
```

---

## Scenario 2

Model is partitioned across multiple HTP devices.

```text
Layer Split

↓

HTP0

↓

HTP1

↓

HTP2

↓

HTP3
```

Current Assessment

```text
Most Likely
```

---

## Scenario 3

Hybrid execution.

```text
CPU

+

Hexagon
```

Current Assessment

```text
Possible
```

---

# Questions Already Answered

```text
✓ CPU deployment completed

✓ GGUF pipeline completed

✓ Q4_0 quantization completed

✓ Android deployment completed

✓ Termux deployment completed

✓ CPU benchmarking completed

✓ Hexagon architecture understood

✓ HTP devices understood

✓ Memory constraint identified
```

---

# Questions Remaining

```text
How many HTP devices are exposed?

Can llama.cpp detect them?

Can GGUF execute directly on Hexagon?

Does Q4_0 execute natively?

Where is the KV Cache stored?

Can the model be automatically partitioned?

Can the full model execute?

How does NPU performance compare to CPU?
```

---

# Immediate Next Step

The next milestone is not running the model.

The next milestone is understanding the Snapdragon backend.

Objectives

```text
Build Snapdragon-enabled llama.cpp

↓

Verify Hexagon backend

↓

Enumerate HTP devices

↓

Verify runtime libraries

↓

Attempt first NPU execution
```

Only after the backend is verified will experiments be performed with Qwen2.5-Coder-7B.

---

# Long-Term Goal

The desired deployment pipeline is:

```text
Qwen2.5-Coder-7B

↓

GGUF

↓

Q4_0

↓

Snapdragon Backend

↓

Hexagon NPU

↓

Android

↓

OpenAI-Compatible API

↓

Offline Coding Assistant
```

---

# Repository Progress

```text
CPU
│
├── Completed
│
│   ✓ Theory
│   ✓ Mathematical Foundations
│   ✓ Performance Optimization
│   ✓ GGUF Conversion
│   ✓ Android Deployment
│   ✓ CPU Inference
│
└── Benchmarked

NPU
│
├── Research Hypothesis
├── Project Context
├── Experiment Plan
│
└── Implementation (Pending)
```

---

# Notes for Future Development

This project follows a research-first methodology.

Every experiment should answer one engineering question before moving to the next stage.

No assumptions should be made regarding hardware capabilities or runtime behavior without experimental verification.

The CPU workflow serves as the reference implementation, while the NPU workflow is an investigation into whether modern Snapdragon hardware can execute a 7B coding model more efficiently using the Qualcomm Hexagon AI Engine.