# Qwen 2.5 Coder 7B Android Deployment Pipeline

This repository contains notebooks for preparing and deploying Qwen 2.5 Coder 7B on Android devices.

## Notebooks

### 01_hf_to_gguf_q4_0.ipynb

Performs:

1. Hugging Face model download
2. GGUF conversion
3. Q4_0 quantization

Outputs:

- qwen2.5-coder-7b-f16.gguf
- qwen2.5-coder-7b-q4_0.gguf

### 02_adb_termux_setup.ipynb

Performs:

1. Android device detection through ADB
2. GGUF model deployment
3. Termux installation and configuration
4. Device preparation for local inference

Outputs:

- Model transferred to Android device
- Termux environment initialized

## Repository Structure

.
├── 01_hf_to_gguf_q4_0.ipynb
├── 02_adb_termux_setup.ipynb
├── .gitignore
└── README.md

## Execution Order

1. Run 01_hf_to_gguf_q4_0.ipynb
2. Run 02_adb_termux_setup.ipynb