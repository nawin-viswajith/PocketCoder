# Qwen 2.5 Coder 7B → GGUF → Q4_0 Quantization

This repository contains a reproducible notebook for downloading, converting, and quantizing the Qwen 2.5 Coder 7B Instruct model for local inference workflows.

## Overview

The notebook performs the following steps:

1. Download the Qwen 2.5 Coder 7B Instruct model from Hugging Face.
2. Clone and configure llama.cpp.
3. Convert the Hugging Face model into GGUF format.
4. Quantize the GGUF model using Q4_0 quantization.
5. Verify the generated artifacts.

## Repository Structure

```text
.
├── 01_hf_to_gguf_q4_0.ipynb
├── .gitignore
└── README.md
```

## Files Generated During Execution

Running the notebook will create additional local files that are not tracked by Git:

```text
qwen-2.5-7b/
├── *.safetensors
├── tokenizer.json
└── config.json

llama.cpp/

qwen2.5-coder-7b-f16.gguf

qwen2.5-coder-7b-q4_0.gguf
```

## Requirements

* Python 3.10+
* Git
* Hugging Face CLI
* llama.cpp
* Jupyter Notebook

## Quantization Format

This notebook generates:

```text
Q4_0
```

Q4_0 was selected because it is compatible with planned Android deployment and Snapdragon experimentation workflows.

## Generated Files

The notebook produces:

```text
qwen2.5-coder-7b-f16.gguf
qwen2.5-coder-7b-q4_0.gguf
```

These files are intentionally excluded from version control.

## llama.cpp Release

The notebook was validated using:

```text
b9761
```

Release:

https://github.com/ggml-org/llama.cpp/releases/tag/b9761

## Usage

Open and execute:

```text
notebooks/01_hf_to_gguf_q4_0.ipynb
```

Run all cells sequentially.

## Notes

* The notebook does not upload models.
* Generated GGUF files are stored locally.
* Large model files are excluded through `.gitignore`.
* Quantization binaries must be placed inside the `llama.cpp` directory before running the quantization step.
