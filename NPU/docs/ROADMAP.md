# Snapdragon Hexagon NPU Research and Experiment Plan

## Objective

The CPU deployment pipeline has been successfully completed.

Current benchmark:

```text
Device      : Samsung Galaxy S26 Ultra
Chipset     : Snapdragon 8 Elite Gen 5
Memory      : 12 GB
Backend     : CPU

Prompt Processing : ~13.6 Tokens/sec
Generation        : ~10 Tokens/sec
```

The objective of the NPU branch is to investigate whether the Qualcomm Hexagon Tensor Processor (HTP) can accelerate inference for Qwen2.5-Coder-7B.

Unlike the CPU workflow, the NPU workflow is research-driven. The hardware, runtime, and software stack must be understood before deployment.

This document records every planned experiment.

---

# Research Methodology

Each experiment follows the same workflow.

```text
Research Question

↓

Hypothesis

↓

Experiment

↓

Observation

↓

Conclusion

↓

Next Experiment
```

Each experiment must answer a single engineering question before moving to the next.

---

# Phase 1 — Snapdragon Backend Investigation

## Experiment 1

### Research Question

```text
Does llama.cpp currently support the Snapdragon backend?
```

### Hypothesis

```text
The latest Snapdragon backend supports
CPU,
GPU,
and Hexagon NPU execution.
```

### Deliverables

- Verify Snapdragon backend documentation
- Verify supported Android versions
- Verify supported Snapdragon chipsets

Status:

```text
Not Started
```

---

## Experiment 2

### Research Question

```text
Can the Snapdragon backend be compiled successfully?
```

### Hypothesis

```text
llama.cpp can be cross-compiled
using the Snapdragon toolchain.
```

Deliverables

- Working Snapdragon build
- Successful compilation
- Executable generated

Status

```text
Not Started
```

---

## Experiment 3

### Research Question

```text
Can the Snapdragon runtime enumerate
available execution devices?
```

Expected devices:

```text
CPU

GPU

HTP0

HTP1

HTP2

...
```

Deliverables

- List available devices
- Record runtime output

Status

```text
Not Started
```

---

# Phase 2 — Hexagon Hardware Investigation

## Experiment 4

### Research Question

```text
How many HTP devices are exposed
by the Snapdragon 8 Elite Gen 5?
```

Hypothesis

```text
Multiple logical HTP execution devices
are available.
```

Possible Results

```text
HTP0

or

HTP0
HTP1

or

HTP0
HTP1
HTP2
HTP3
```

Status

```text
Not Started
```

---

## Experiment 5

### Research Question

```text
What is the practical memory limit
of one HTP execution context?
```

Hypothesis

```text
≈3.5 GB
```

Deliverables

- Verify documentation
- Verify experimentally

Status

```text
Not Started
```

---

## Experiment 6

### Research Question

```text
Where is the KV Cache stored?
```

Possibilities

```text
CPU RAM

Shared Memory

Hexagon Memory
```

Deliverables

- Runtime investigation
- Memory profiling

Status

```text
Not Started
```

---

# Phase 3 — GGUF Compatibility

## Experiment 7

### Research Question

```text
Does the Hexagon backend
accept GGUF directly?
```

Hypothesis

```text
GGUF can be executed
without ONNX conversion.
```

Deliverables

- Load GGUF
- Verify successful initialization

Status

```text
Not Started
```

---

## Experiment 8

### Research Question

```text
Does Q4_0 execute
on the Hexagon backend?
```

Hypothesis

```text
Q4_0 is supported.
```

Deliverables

- Successful model load
- Successful inference

Status

```text
Not Started
```

---

# Phase 4 — Large Model Investigation

## Experiment 9

### Research Question

```text
Can Qwen2.5-Coder-7B
fit inside one HTP?
```

Known Information

```text
Model

≈4.3 GB

Estimated HTP Memory

≈3.5 GB
```

Hypothesis

```text
Entire model

↓

Single HTP

Not Possible
```

Status

```text
Not Started
```

---

## Experiment 10

### Research Question

```text
Can llama.cpp automatically split
the model across multiple HTP devices?
```

Hypothesis

```text
Layer splitting

↓

Multiple HTP Devices

↓

Successful Execution
```

Status

```text
Not Started
```

---

## Experiment 11

### Research Question

```text
Is hybrid CPU + NPU execution required?
```

Possible Pipeline

```text
Embedding

↓

CPU

↓

Attention

↓

Hexagon

↓

Sampling

↓

CPU
```

Status

```text
Not Started
```

---

# Phase 5 — Benchmarking

## Experiment 12

Compare CPU and NPU performance.

Metrics

```text
Prompt Processing

Generation

Memory Usage

Temperature

Battery Consumption

CPU Utilization

NPU Utilization
```

Benchmark Table

| Backend | Prompt TPS | Generation TPS | Temperature | Power |
|----------|-----------:|---------------:|------------:|------:|
| CPU | 13.6 | 10.0 | TBD | TBD |
| NPU | TBD | TBD | TBD | TBD |

Status

```text
Not Started
```

---

# Stretch Goals

The following experiments will be attempted if the basic NPU workflow succeeds.

## Alternative Quantizations

```text
Q4_K_M

Q5_K_M

Q6_K
```

---

## Alternative Models

```text
Qwen2.5-3B

Qwen2.5-1.5B

Gemma

Llama
```

---

## Long Context Benchmark

Investigate

```text
2048

4096

8192

16384
```

context lengths.

---

## Sustained Performance

Measure

```text
30 Minutes

↓

Temperature

↓

TPS

↓

Battery
```

---

# Success Criteria

The NPU investigation will be considered successful if the following objectives are achieved.

```text
✓ Snapdragon backend compiled

✓ Hexagon devices detected

✓ GGUF successfully loaded

✓ Q4_0 successfully executed

✓ Qwen2.5-Coder-7B inference completed

✓ CPU vs NPU benchmark completed

✓ Power and thermal comparison documented
```

---

# End Goal

The final objective of this research is to determine whether a modern flagship Android smartphone can execute a 7-billion parameter coding model primarily on the Qualcomm Hexagon NPU while improving efficiency over CPU execution.

If successful, the outcome will be a fully documented deployment pipeline that can be reproduced by other developers using Snapdragon-powered Android devices.