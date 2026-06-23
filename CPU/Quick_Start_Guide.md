# Quick Start Guide

## Navigate to llama.cpp

```bash
cd ~/llama.cpp
```

---

## Verify Model Exists

```bash
ls ~/storage/downloads
```

---

## Interactive Chat

```bash
./build/bin/llama-cli \
  -m ~/storage/downloads/qwen2.5-coder-7b-q4_0.gguf
```

Exit:

```text
Ctrl + C
```

Termux:

```text
Volume Down + C
```

---

## One-Shot Prompt

```bash
./build/bin/llama-cli \
  -m ~/storage/downloads/qwen2.5-coder-7b-q4_0.gguf \
  -n 128 \
  -p "Write a Python hello world program"
```

---

## Benchmark

```bash
./build/bin/llama-bench \
  -m ~/storage/downloads/qwen2.5-coder-7b-q4_0.gguf
```

---

## Start API Server

```bash
./build/bin/llama-server \
  -m ~/storage/downloads/qwen2.5-coder-7b-q4_0.gguf \
  --host 0.0.0.0 \
  --port 8080 \
  -c 2048
```

---

## Stop API Server

```text
Ctrl + C
```

---

## Find Phone IP

```bash
ip addr show wlan0
```

---

## Verify API Locally

```bash
curl http://localhost:8080/v1/models
```

---

## Verify API From Laptop

Phone and laptop must be connected to the same Wi-Fi network.

```bash
curl http://PHONE_IP:8080/v1/models
```

Example:

```bash
curl http://192.168.1.50:8080/v1/models
```

---

## Open New Termux Session

1. Swipe from the left edge.
2. Select New Session.

Recommended:

```text
Session 1 -> llama-server
Session 2 -> shell commands
```

---

## Monitor Memory

```bash
free -h
```

---

## Monitor CPU

```bash
top
```

---

## Running Processes

```bash
ps -ef | grep llama
```

---

## Rebuild llama.cpp

```bash
cd ~/llama.cpp

rm -rf build

cmake -B build \
  -DGGML_NATIVE=OFF \
  -DGGML_CPU_ALL_VARIANTS=OFF

cmake --build build -j4
```
