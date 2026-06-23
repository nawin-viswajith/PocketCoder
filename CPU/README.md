# Qwen 2.5 Coder 7B Android Deployment Pipeline

This repository documents the complete workflow for deploying Qwen 2.5 Coder 7B on an Android device using llama.cpp and Termux.

## Notebooks

### 01_hf_to_gguf_q4_0.ipynb

Downloads the model from Hugging Face and converts it to a quantized GGUF format.

Steps:

1. Download Qwen 2.5 Coder 7B Instruct
2. Convert Hugging Face model to GGUF
3. Quantize using Q4_0

Outputs:

* qwen2.5-coder-7b-f16.gguf
* qwen2.5-coder-7b-q4_0.gguf

---

### 02_adb_termux_setup.ipynb

Prepares the Android device.

Steps:

1. Verify ADB connectivity
2. Transfer the quantized model
3. Install and configure Termux
4. Prepare the device for local inference

Outputs:

* Model deployed to Android storage
* Termux environment configured

---

### 03_phone_inference_server.ipynb

Runs local inference on the Android device.

Steps:

1. Build llama.cpp
2. Resolve common build issues
3. Run inference
4. Benchmark performance
5. Start an OpenAI-compatible API server

Outputs:

* Local inference endpoint
* Benchmark results
* OpenAI-compatible API

---

## Repository Structure

```text
.
├── 01_hf_to_gguf_q4_0.ipynb
├── 02_adb_termux_setup.ipynb
├── 03_phone_inference_server.ipynb
├── .gitignore
└── README.md
```

## Execution Order

Run the notebooks sequentially:

1. 01_hf_to_gguf_q4_0.ipynb
2. 02_adb_termux_setup.ipynb
3. 03_phone_inference_server.ipynb

## Benchmark Reference

Device:

Samsung Galaxy S26 Ultra

Model:

Qwen2.5-Coder-7B Q4_0

Backend:

CPU

Observed Performance:

* Prompt Processing: ~9 tokens/sec
* Generation: ~6.4 tokens/sec
