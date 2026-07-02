# Session Context

## Project

PocketCoder

Current Focus:

NPU Acceleration on Snapdragon using the official llama.cpp Snapdragon backend.

Target Device:

Samsung Galaxy S26 Ultra

Hardware:

- Snapdragon 8 Elite Gen 5
- 12 GB RAM
- 256 GB Storage

---

# Current Repository Structure

```
NPU/
│
├── notebooks/
│   ├── 01_snapdragon_backend_setup.ipynb
│   ├── 02_snapdragon_runtime_validation.ipynb
│   └── 03_runtime_dependency_analysis.ipynb
│
├── research/
│   ├── 00_research_hypothesis.md
│   └── RUNTIME_DEPENDENCY_ANALYSIS.md
│
├── docs/
│   ├── AI_CONTEXT.md
│   ├── PROJECT_CONTEXT.md
│   ├── ROADMAP.md
│   └── SESSION_CONTEXT.md
│
├── llama.cpp/
│
└── README.md
```

---

# Completed Experiments

## Experiment 1

Status:

Completed

Objective:

Build the official Snapdragon backend.

Achievements:

- Successfully built llama.cpp using the Snapdragon Toolchain.
- Generated Android ARM64 binaries.
- Generated deployment package using:

```
cmake --install
```

Package contains:

- llama-cli
- llama-server
- llama-bench
- libggml-hexagon.so
- libggml-opencl.so
- libllama.so

---

## Experiment 2

Status:

Partially Completed

Objective:

Validate runtime execution on Android.

Achievements:

- Package successfully transferred to the device.
- Executable permissions fixed using:

```
chmod +x bin/*
```

- Runtime library path configured.

Observed runtime progression:

Stage 1

```
Permission denied
```

↓

Resolved

↓

Stage 2

```
library "libllama-cli-impl.so" not found
```

↓

Resolved

↓

Stage 3

```
cannot locate symbol
_ZNSt3__113__hash_memoryEPKvm
```

Current blocker:

Dynamic linker fails during C++ runtime initialization.

---

# Investigation Summary

Deployment package inspection shows:

Present:

- libggml.so
- libggml-hexagon.so
- libggml-opencl.so
- libllama.so
- libllama-cli-impl.so
- libmtmd.so

Missing:

```
libc++_shared.so
```

The Android Snapdragon preset does not explicitly configure:

- ANDROID_STL
- CMAKE_ANDROID_STL_TYPE

This may be related to the runtime failure.

---

# Research Findings

Community reports confirm successful execution of the Snapdragon backend on Android devices including:

- OnePlus 12
- Snapdragon 8 Gen 3

Therefore Android execution is known to be achievable.

Current issue is likely related to:

- Runtime packaging
- Runtime dependency resolution
- Android dynamic linker
- STL compatibility

rather than backend implementation.

---

# Current Hypothesis

The Snapdragon backend builds successfully.

Deployment also succeeds.

Runtime initialization reaches Android's dynamic linker.

The remaining issue is likely an unresolved runtime dependency or packaging issue rather than a compilation failure.

---

# Immediate Next Tasks

Priority 1

Determine why:

```
_ZNSt3__113__hash_memoryEPKvm
```

cannot be resolved.

Investigate:

- libc++ runtime
- readelf output
- Android dependency chain
- official deployment procedure
- upstream GitHub issues

Priority 2

Complete:

```
03_runtime_dependency_analysis.ipynb
```

Priority 3

Proceed to:

Backend Enumeration

only after runtime dependency resolution.

---

# Long-Term Experiment Roadmap

Experiment 1

Backend Build

Completed

Experiment 2

Runtime Validation

Partially Completed

Experiment 3

Runtime Dependency Analysis

In Progress

Experiment 4

Backend Enumeration

Pending

Experiment 5

Hexagon Runtime Validation

Pending

Experiment 6

First Model Inference

Pending

Experiment 7

Performance Benchmarking

Pending

Experiment 8

Runtime Profiling and Optimization

Pending

---

# Working Philosophy

Follow an engineering-first approach.

Each notebook must answer a single engineering question.

Research precedes implementation.

Do not apply undocumented workarounds without understanding the underlying cause.

When runtime issues are encountered:

1. Observe
2. Form a hypothesis
3. Investigate
4. Validate
5. Document

before proceeding to the next experiment.