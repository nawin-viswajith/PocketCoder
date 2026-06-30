# Research Hypothesis: Can a 7B Large Language Model Execute Entirely on the Snapdragon Hexagon NPU?

## Objective

The CPU deployment pipeline has been successfully completed for Qwen2.5-Coder-7B using llama.cpp.

Current benchmark:

```text
Device      : Samsung Galaxy S26 Ultra
Chipset     : Snapdragon 8 Elite Gen 5
Memory      : 12 GB
Backend     : CPU

Prompt Processing : ~13.6 Tokens/sec
Generation        : ~10 Tokens/sec
```

The next objective is to investigate whether the model can be executed using the Qualcomm Hexagon NPU instead of the CPU.

Unlike the CPU workflow, the NPU workflow requires understanding the hardware architecture, runtime limitations, and memory constraints before implementation.

This document records the initial research hypothesis.

---

# Problem Statement

The current deployment executes entirely on the CPU.

```text
Qwen2.5-Coder-7B
        ↓
GGUF
        ↓
llama.cpp
        ↓
CPU
```

Although functional, CPU inference has several limitations:

- Higher power consumption
- Increased thermal output
- Limited sustained throughput
- Competition with the Android operating system for CPU resources

The Snapdragon platform includes a dedicated AI accelerator known as the Hexagon Tensor Processor (HTP).

The primary research question is therefore:

```text
Can the complete Qwen2.5-Coder-7B model execute on the Hexagon NPU?
```

---

# Initial Assumption

The initial assumption was:

```text
Phone

↓

One NPU

↓

Entire Model
```

Under this assumption, the complete model would simply be transferred from the CPU to the NPU.

Further investigation suggests that the architecture is more complex.

---

# Understanding the Snapdragon Compute Architecture

The Snapdragon SoC contains multiple compute engines.

```text
                 Snapdragon 8 Elite Gen 5
┌─────────────────────────────────────────────────────┐
│                                                     │
│  CPU                                                 │
│  General-purpose computation                         │
│                                                     │
│  Adreno GPU                                          │
│  Graphics and massively parallel computation         │
│                                                     │
│  Hexagon AI Engine                                   │
│     • Tensor Accelerator                             │
│     • Vector Accelerator (HVX)                       │
│     • Scalar Units                                   │
│     • Shared Memory                                  │
│                                                     │
│  Memory Controller                                   │
└─────────────────────────────────────────────────────┘
```

Unlike the CPU, the Hexagon processor is designed specifically for tensor operations such as:

- Matrix Multiplication
- Linear Layers
- Attention Operations
- Neural Network Inference

These operations dominate transformer execution.

---

# What is HTP?

HTP stands for:

```text
Hexagon Tensor Processor
```

The HTP is Qualcomm's dedicated neural network accelerator.

Conceptually:

```text
CPU

↓

General Computing

GPU

↓

Graphics + Parallel Computing

HTP

↓

Neural Network Acceleration
```

Rather than executing arbitrary programs, the HTP specializes in tensor operations required by modern AI workloads.

---

# Understanding HTP0, HTP1, HTP2...

During the initial investigation, a common misconception was identified.

The identifiers:

```text
HTP0
HTP1
HTP2
HTP3
```

do **not** represent multiple phones.

They also do **not** necessarily represent physically separate NPUs.

Instead, they are logical execution devices exposed by the Qualcomm AI Runtime.

Conceptually:

```text
Snapdragon 8 Elite

        ↓

Hexagon AI Engine

        ↓

HTP0
HTP1
HTP2
HTP3
```

These are software-visible execution targets that may allow workload partitioning across the accelerator.

An analogy can be drawn with CPU cores.

```text
CPU

Core0
Core1
Core2
Core3
```

Similarly:

```text
Hexagon Runtime

HTP0
HTP1
HTP2
HTP3
```

The exact number of available HTP devices depends on the hardware and runtime implementation.

---

# Memory Investigation

The deployed model is:

```text
Qwen2.5-Coder-7B

Q4_0

≈ 4.3 GB
```

Community reports and existing backend documentation suggest that a single Hexagon execution session may have practical memory limits around:

```text
≈3.5 GB
```

If this limitation applies, then:

```text
Model Size

4.3 GB

>

Single HTP Memory

≈3.5 GB
```

This immediately raises an important question.

```text
Can a model larger than a single HTP memory allocation still execute?
```

---

# Possible Execution Models

Three possibilities currently exist.

## Scenario 1

Entire model executes on one HTP.

```text
Model

↓

HTP0
```

Status:

```text
Unlikely
```

The model appears larger than the reported memory limit of a single HTP execution context.

---

## Scenario 2

Model is partitioned across multiple HTP devices.

```text
Layers 0–10

↓

HTP0

Layers 11–20

↓

HTP1

Layers 21–30

↓

HTP2

Layers 31+

↓

HTP3
```

Status:

```text
Likely
```

This approach is conceptually similar to multi-GPU layer splitting performed by llama.cpp.

---

## Scenario 3

Hybrid execution.

```text
Some Layers

↓

Hexagon

Remaining Layers

↓

CPU
```

Status:

```text
Possible
```

This would reduce CPU workload while overcoming NPU memory limitations.

---

# Current Hypothesis

The current working hypothesis is:

```text
Single HTP

↓

Insufficient Memory
```

However,

```text
Multiple HTP Devices

↓

May Enable Full Model Execution
```

provided that:

- the Snapdragon runtime exposes multiple HTP devices,
- llama.cpp supports automatic layer partitioning,
- the runtime can coordinate execution across those devices.

---

# Open Research Questions

The following questions remain unanswered.

## Question 1

```text
How many HTP devices does the Snapdragon 8 Elite Gen 5 expose?
```

Status:

```text
Unknown
```

---

## Question 2

```text
Can llama.cpp automatically split GGUF models across multiple HTP devices?
```

Status:

```text
Unknown
```

---

## Question 3

```text
Where is the KV Cache stored?

CPU Memory?

Shared Memory?

Hexagon Memory?
```

Status:

```text
Unknown
```

---

## Question 4

```text
What is the practical maximum model size
supported by a single HTP execution context?
```

Status:

```text
Unknown
```

---

## Question 5

```text
Does Q4_0 execute natively on the Hexagon backend?
```

Status:

```text
To Be Verified
```

---

# Research Plan

The NPU investigation will proceed incrementally.

```text
Step 1

Investigate Snapdragon Architecture

        ↓

Step 2

Determine HTP Device Availability

        ↓

Step 3

Investigate Memory Constraints

        ↓

Step 4

Study llama.cpp Hexagon Backend

        ↓

Step 5

Build Snapdragon-Compatible Binary

        ↓

Step 6

Attempt First NPU Inference

        ↓

Step 7

Benchmark CPU vs NPU
```

---

# Conclusion

At the beginning of this investigation, it was assumed that deploying an LLM on the Hexagon NPU simply involved replacing the CPU backend with an NPU backend.

The research performed so far indicates that the problem is fundamentally one of memory architecture and workload partitioning rather than raw computational capability.

The central hypothesis guiding the remainder of this project is therefore:

```text
The Snapdragon 8 Elite Gen 5 possesses sufficient computational capability to execute Qwen2.5-Coder-7B on its Hexagon NPU.

The determining factor is whether the model can be partitioned efficiently across the HTP execution devices exposed by the Qualcomm AI Runtime while remaining within the memory constraints of the hardware.
```

The next phase of research will focus on validating this hypothesis through architectural investigation and experimental deployment.