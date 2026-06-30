# AI Context - Snapdragon NPU Research

## Project

Mobile LLM Deployment on Android using Qualcomm Snapdragon hardware.

Primary Goal:

Run Qwen2.5-Coder-7B-Instruct locally on a Samsung Galaxy S26 Ultra using the Qualcomm Hexagon NPU.

---

# Hardware

Device

```text
Samsung Galaxy S26 Ultra

Snapdragon 8 Elite Gen 5

12 GB LPDDR5X RAM

256 GB Storage

Android
```

---

# Current CPU Pipeline (Completed)

Model

```text
Qwen2.5-Coder-7B-Instruct
```

Workflow

```text
Hugging Face

↓

GGUF (FP16)

↓

Q4_0 Quantization

↓

ADB Deployment

↓

Android Termux

↓

llama.cpp

↓

CPU Inference
```

Status

```text
Completed
```

---

# Quantization

Current Quantization

```text
Q4_0
```

Reason

```text
Small Model Size

Broad llama.cpp Support

Expected Hexagon Compatibility
```

Current Model Size

```text
≈4.3 GB
```

---

# CPU Results

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

This serves as the baseline benchmark.

---

# Repository Structure

CPU

```text
Theory.md

Mathematical_Foundations.md

Performance_Optimization.md

01_hf_to_gguf_q4_0.ipynb

02_adb_termux_setup.ipynb

03_phone_inference_server.ipynb
```

NPU

```text
README.md

00_research_hypothesis.md

EXPERIMENT_PLAN.md

(Project implementation pending)
```

---

# Knowledge Acquired

GGUF

```text
Deployment format used by llama.cpp.
```

Q4_0

```text
4-bit quantization.

Approximately 4× compression over FP16.
```

Hexagon

```text
Dedicated Qualcomm AI accelerator.
```

HTP

```text
Hexagon Tensor Processor
```

HTP0, HTP1 ...

```text
Logical execution devices exposed by the Qualcomm runtime.

They are NOT multiple phones.
```

---

# Current Research Hypothesis

Known

```text
Model Size

≈4.3 GB
```

Community reports suggest

```text
Single HTP execution context

≈3.5 GB
```

Therefore

```text
Single HTP

↓

Probably insufficient
```

Possible solutions

```text
Multiple HTP devices

or

Hybrid CPU + NPU execution
```

Current hypothesis

```text
llama.cpp may automatically split model layers
across multiple HTP execution devices.
```

---

# Research Philosophy

Do NOT assume anything.

Every stage must be experimentally verified.

Workflow

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

---

# Experiments Completed

```text
✓ CPU deployment

✓ CPU optimization

✓ Thread scaling

✓ Benchmarking

✓ Theory documentation

✓ Mathematical foundations

✓ Performance documentation

✓ Initial NPU architecture research
```

---

# Current Goal

Do NOT attempt Qwen execution yet.

First objective

```text
Build Snapdragon-enabled llama.cpp

↓

Verify Qualcomm runtime

↓

Enumerate HTP devices

↓

Verify Hexagon backend

↓

Attempt first NPU execution
```

---

# Immediate Next Research Questions

1.

```text
Can llama.cpp be successfully built with Snapdragon backend?
```

2.

```text
How many HTP devices are exposed
on Snapdragon 8 Elite Gen 5?
```

3.

```text
Can GGUF execute directly
on the Hexagon backend?
```

4.

```text
Does Q4_0 execute natively?
```

5.

```text
Can Qwen2.5-Coder-7B fit
using multiple HTP devices?
```

---

# Long-Term Goal

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

# Instructions for Future ChatGPT Sessions

Assume:

- CPU pipeline is complete.
- CPU benchmark is finalized.
- Repository structure has already been created.
- Documentation is complete.
- The project is now entering the NPU research and implementation phase.

Avoid repeating CPU setup unless explicitly requested.

Focus on:

- Qualcomm Hexagon
- Snapdragon backend
- llama.cpp NPU support
- Android deployment
- Runtime debugging
- Performance optimization
- Experimental validation