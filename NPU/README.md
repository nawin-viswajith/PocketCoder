# Qualcomm Hexagon NPU Deployment

This directory contains research, experiments, and documentation related to running Large Language Models on Qualcomm Snapdragon Neural Processing Units (NPUs).

The objective is to move beyond CPU-based inference and investigate hardware acceleration using the Qualcomm AI stack.

## Current Status

Status: In Progress

The CPU workflow documented in the `CPU` directory is currently the recommended and fully tested deployment path.

NPU deployment has not yet been validated end-to-end.

## Goals

The NPU workflow aims to:

* Reduce inference latency
* Increase tokens-per-second throughput
* Improve power efficiency
* Reduce thermal throttling
* Enable longer inference sessions on mobile devices

## Planned Workflow

```text
Hugging Face Model
        │
        ▼
ONNX Export
        │
        ▼
QNN Conversion
        │
        ▼
Qualcomm AI Engine Runtime
        │
        ▼
Hexagon NPU Deployment
        │
        ▼
Benchmarking & Validation
```

## Planned Topics

### 1. ONNX Export

Export Hugging Face models into ONNX format suitable for Qualcomm tooling.

Planned Investigations:

* Qwen 2.5 Coder 7B
* Qwen 2.5 3B
* Qwen 2.5 1.5B

---

### 2. Qualcomm QNN Conversion

Convert ONNX models into formats compatible with Qualcomm AI Engine Runtime.

Areas of interest:

* QNN SDK
* Quantization strategies
* Compatibility constraints
* Memory requirements

---

### 3. Snapdragon AI Engine Runtime

Evaluate deployment options for:

* Android
* Termux
* Native applications

---

### 4. Hexagon NPU Deployment

Investigate:

* DSP offloading
* Hexagon acceleration
* HTP execution
* Runtime configuration

---

### 5. Benchmarking

Compare:

* CPU Inference
* GPU Inference
* NPU Inference

Metrics:

* Tokens per second
* Memory consumption
* Power consumption
* Device temperature
* Sustained performance

## Target Hardware

Primary Device:

```text
Samsung Galaxy S26 Ultra
```

Target Platform:

```text
Snapdragon 8 Elite
```

## Planned Repository Structure

```text
NPU/
├── README.md
├── 01_onnx_export.ipynb
├── 02_qnn_conversion.ipynb
├── 03_npu_deployment.ipynb
├── 04_benchmarking.ipynb
└── QUICK_START.md
```

## Notes

The NPU workflow is experimental.

Model support, tooling compatibility, deployment procedures, and performance characteristics may change as Qualcomm tooling and open-source ecosystems evolve.

Until NPU deployment is validated, the CPU workflow remains the primary and supported deployment path.
