# Local Docker vLLM Deployment (RTX 5090)

This repository provides a structured way to deploy Large Language Models (LLMs) using **vLLM** within **Docker** containers, optimized for deployment on **NVIDIA RTX 5090** hardware. It is organized by model family and includes pre-configured `docker-compose.yml` files for easy deployment.

## 💻 Tested Hardware

All models in this repository have been tested on the following hardware configuration:

| Component | Specification |
|-----------|---------------|
| **GPU** | NVIDIA GeForce RTX 5090 (32GB VRAM) |
| **CPU** | AMD Ryzen 9 9950X3D (16 cores) |
| **RAM** | 96GB |
| **OS** | Windows (Linux via WSL2) |

The deployment runs inside **WSL2**, which passes the NVIDIA GPU through from the Windows driver. Note that WSL caps the RAM visible to the Linux VM (default 50% of host, ~45GB here) — see `.wslconfig` if you need the model cache to fit in the WSL memory segment.

## 🚀 Project Structure

The project is organized into directories based on the model provider:

- `qwen/`: Contains deployment configurations for Alibaba's Qwen models.
- `docker-compose.common.yml`: Shared configuration for all vLLM deployments.
- `.env.example`: Template for environment variables (copy to `.env`).
- `opencode.json`: Configuration for Opencode, mapping the local models to a custom provider.

## 🛠️ Quick Start

1. **Setup environment variables:**
   ```bash
   cp .env.example .env
   # Edit .env with your HuggingFace token
   ```

2. **Deploy a model:**
    ```bash
    cd qwen/qwen3.8-27b-nvfp4
    docker compose up -d
    ```

3. **Access the API:**
    ```bash
    curl http://localhost:8000/v1/chat/completions \
      -H "Content-Type: application/json" \
      -d '{"model":"unsloth/Qwen3.8-27B-NVFP4","messages":[{"role":"user","content":"Hello"}]}'
    ```

## 📋 Configuration

All models use a shared configuration (`docker-compose.common.yml`) with:
- `pull_policy: always` so the `vllm/vllm-openai:nightly` image is fetched fresh on every start
- `ipc: host` for large shared memory (long context)
- Host-mounted caches for HuggingFace and vLLM (keeps weights across restarts)
- `restart: unless-stopped` and a health check with a 10-minute start period
- `CUDA_VISIBLE_DEVICES=0` (single GPU)

### 🌟 Recommended: Qwen 3.8 27B NVFP4 (unsloth)

The **Qwen 3.8 27B NVFP4** (`qwen/qwen3.8-27b-nvfp4`) is the recommended model in this setup:

- **128K context** (max-model-len: 131072)
- **NVFP4 quantization** + **FP8 KV cache**
- **Full tool calling** (`--enable-auto-tool-choice` with `qwen3_coder` parser) and `qwen3` reasoning parser
- **MTP speculative decoding** (num_speculative_tokens: 2) for extra speed
- Prefix caching + chunked prefill enabled
- Uses the language model only (`--language-model-only`, skips the VLM vision tower)
- Thinking enabled by default (`enable_thinking=true`, `preserve_thinking=true`)
- Based on [`unsloth/Qwen3.8-27B-NVFP4`](https://huggingface.co/unsloth/Qwen3.8-27B-NVFP4)

### Available Models

#### Qwen Models
| Model Path | Notes |
| :--- | :--- |
| `qwen/qwen3.8-27b-nvfp4/` | ⭐ **Qwen 3.8 27B NVFP4 (unsloth)** — **Recommended** |

## 🔌 TUI Integration

This codebase is pre-configured for [Opencode](https://opencode.ai). The `opencode.json` file defines a custom provider named `local-vllm` that uses the `@ai-sdk/openai-compatible` package and maps the local model to the vLLM endpoint: `http://localhost:8000/v1`.
