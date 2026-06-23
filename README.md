# Mobile LLM Deployment on Android

This repository documents workflows for running local Large Language Models (LLMs) on Android devices.

The project focuses on deploying coding-oriented open-source models using quantization, GGUF conversion, llama.cpp, Android Termux, and Snapdragon-based smartphones.

## Objective

The goal of this project is to create a portable, offline AI environment capable of running local coding models directly on a smartphone without relying on cloud APIs.

Key objectives include:

* Downloading and preparing open-source LLMs
* Converting models to GGUF format
* Applying quantization for mobile deployment
* Deploying models to Android devices
* Running local inference using llama.cpp
* Hosting an OpenAI-compatible API endpoint
* Exploring Qualcomm Hexagon NPU acceleration

---

## Workflows

### CPU Workflow

The CPU workflow is fully documented and tested.

Features:

* Hugging Face model download
* GGUF conversion
* Q4_0 quantization
* Android deployment using ADB
* Termux environment setup
* llama.cpp compilation
* Local inference
* Performance benchmarking
* OpenAI-compatible API server

Documentation:

```text
CPU/README.md
```

---

### NPU Workflow

The NPU workflow is currently under development.

Planned areas of exploration:

* ONNX export
* Qualcomm QNN conversion
* Snapdragon AI Engine Runtime
* Hexagon NPU deployment
* Performance benchmarking
* CPU vs NPU comparison

Documentation:

```text
NPU/README.md
```

---

## Repository Structure

```text
.
├── CPU/
│   ├── README.md
│   ├── 01_hf_to_gguf_q4_0.ipynb
│   ├── 02_adb_termux_setup.ipynb
│   ├── 03_phone_inference_server.ipynb
│   └── Quick_Start_Guide.md
│
├── NPU/
│   └── README.md
│
├── .gitignore
└── README.md
```

---

## CPU Workflow Execution Order

Execute the notebooks sequentially:

1. 01_hf_to_gguf_q4_0.ipynb
2. 02_adb_termux_setup.ipynb
3. 03_phone_inference_server.ipynb

After deployment, refer to:

```text
CPU/Quick_Start_Guide.md
```

for daily usage commands.

---

## Tested Configuration

Device:

```text
Samsung Galaxy S26 Ultra
```

Model:

```text
Qwen2.5-Coder-7B Q4_0
```

Backend:

```text
CPU
```

Observed Performance:

```text
Prompt Processing: ~9 tokens/sec
Generation: ~6.4 tokens/sec
```

---

## Status

| Workflow             | Status      |
| -------------------- | ----------- |
| HF → GGUF Conversion | Complete    |
| Q4_0 Quantization    | Complete    |
| Android Deployment   | Complete    |
| CPU Inference        | Complete    |
| API Server           | Complete    |
| NPU Deployment       | In Progress |

---

## License

Refer to the licenses of the respective upstream projects and model providers, including:

* Qwen
* llama.cpp
* Hugging Face
* Qualcomm AI tooling (where applicable)

