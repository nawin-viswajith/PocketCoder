# Runtime Dependency Analysis

## Purpose

This document serves as the engineering investigation log for **Experiment 03 – Runtime Dependency Analysis**.

Unlike the experiment notebook, which presents the validated investigation in a structured format, this document records the chronological debugging process, intermediate findings, discarded hypotheses, command references, and future work.

The finalized investigation is documented in:

```
notebooks/03_runtime_dependency_analysis.ipynb
```

---

# Platform

## Device

- Samsung Galaxy S26 Ultra
- Snapdragon 8 Elite Gen 5
- Android 16
- 12 GB RAM
- 256 GB Storage

## Build

- Official `llama.cpp` Snapdragon backend
- Android ARM64
- OpenCL Enabled
- Hexagon Enabled

---

# Investigation Timeline

## Stage 1 — Executable Permission

### Error

```text
Permission denied
```

### Root Cause

Executable permissions were not preserved after deployment.

### Resolution

```bash
chmod +x bin/*
```

### Status

✅ Resolved

---

## Stage 2 — Missing Runtime Library

### Error

```text
library "libllama-cli-impl.so" not found
```

### Root Cause

Runtime library search path was not configured.

### Resolution

```bash
export LD_LIBRARY_PATH=/data/local/tmp/llama/lib
```

### Status

✅ Resolved

---

## Stage 3 — Android Linker Failure

### Error

```text
cannot locate symbol

_ZNSt3__113__hash_memoryEPKvm

referenced by

/system/lib64/liblog.so
```

### Initial Assessment

The executable terminated during Android's dynamic linking stage before application initialization.

This became the focus of Experiment 03.

### Status

✅ Root cause identified

---

# Investigation Summary

The following investigations were performed.

| Investigation | Result |
|---------------|--------|
| Executable dependency analysis | Completed |
| Snapdragon backend dependency analysis | Completed |
| Android program interpreter inspection | Completed |
| Deployment package symbol inspection | Completed |
| Android `liblog.so` inspection | Completed |
| Android `libc++.so` inspection | Completed |
| Runtime environment isolation | Completed |

---

# Major Findings

## 1. Executable Dependencies

Inspection of `llama-cli` confirmed that the executable depends only on the expected `llama.cpp` runtime libraries together with standard Android system libraries.

No unexpected dependencies were identified.

---

## 2. Snapdragon Backend

Inspection of `libggml-hexagon.so` showed dependencies on:

- liblog.so
- libdl.so
- libggml-base.so
- libc.so
- libm.so

No dependency on `libc++.so` was declared.

---

## 3. Android Program Interpreter

The executable specifies:

```text
/system/bin/linker64
```

Therefore, execution relies on Android's standard dynamic linker.

---

## 4. Deployment Package Inspection

Every shared library included in the deployment package was inspected for references to

```text
_ZNSt3__113__hash_memoryEPKvm
```

Result:

No deployed library referenced the symbol.

---

## 5. Android System Libraries

Inspection of Android's `liblog.so` showed:

- undefined reference to

```
_ZNSt3__113__hash_memoryEPKvm
```

- dependency on

```
libc++.so
```

Inspection of Android's `libc++.so` confirmed that the required symbol is exported by the system runtime.

Therefore, the symbol exists on the device.

---

## 6. Runtime Environment Isolation

Multiple runtime configurations were evaluated.

Configuration A:

```bash
export LD_LIBRARY_PATH=/data/local/tmp/llama/lib:/vendor/lib64:/system/lib64
```

Result:

Even standard Android executables (`ls`, `find`, `readlink`) failed with the same linker error.

---

Configuration B:

```bash
export LD_LIBRARY_PATH=/data/local/tmp/llama/lib
```

Result:

- `ls` executed successfully.
- `llama-cli` initialized successfully.
- Android linker completed without errors.

---

# Root Cause

The original linker failure was **not** caused by:

- missing deployment libraries
- incomplete package generation
- missing Android C++ runtime
- corrupted deployment
- unresolved application dependencies

The investigation determined that the failure originated from the runtime environment configuration.

Explicitly appending

```text
/vendor/lib64
/system/lib64
```

to `LD_LIBRARY_PATH` altered Android's dynamic linker behaviour and resulted in the unresolved symbol error.

Restricting `LD_LIBRARY_PATH` to the deployment library directory restored correct linker behaviour.

---

# Rejected Hypotheses

| Hypothesis | Result |
|------------|--------|
| Deployment package incomplete | ❌ Rejected |
| Missing `libc++.so` | ❌ Rejected |
| Missing runtime symbol | ❌ Rejected |
| Corrupted deployment | ❌ Rejected |
| Missing Snapdragon libraries | ❌ Rejected |

---

# Current Status

Current runtime output:

```text
ggml-hex: failed to open session 0 : error 0x80000406
ggml-hex: failed to create device/session 0
```

The executable now:

- loads successfully
- passes Android dynamic linking
- initializes the Snapdragon backend
- reaches Qualcomm Hexagon runtime initialization

The remaining failure is unrelated to Android's dynamic linker.

---

# Remaining Work

The next investigation focuses on Qualcomm Hexagon runtime initialization.

Planned topics:

- FastRPC communication
- CDSP availability
- HTP session creation
- Qualcomm runtime libraries
- Interpretation of error `0x80000406`

This work is documented in:

```
Experiment 04 – Hexagon Runtime Initialization
```