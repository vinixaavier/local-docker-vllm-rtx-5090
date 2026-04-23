# Local Docker vLLM Deployment (RTX 5090)

This repository provides a structured way to deploy various Large Language Models (LLMs) using **vLLM** within **Docker** containers, optimized for deployment on **NVIDIA RTX 5090** hardware. It is organized by model family (e.g., Gemma, Qwen) and includes pre-configured `docker-compose.yml` files for easy deployment.

## 💻 Tested Hardware

All models in this repository have been tested on the following hardware configuration:

| Component | Specification |
|-----------|---------------|
| **GPU** | NVIDIA GeForce RTX 5090 (32GB VRAM) |
| **CPU** | AMD Ryzen 9 9950X3D |
| **RAM** | 96GB DDR5 |

## ⚠️ Critical Notes

> [!IMPORTANT]
> **Gemma 4 31B NVFP4 Deployment**: This model requires a specific patch for the vLLM tool parser to function correctly. Please ensure you use the provided `Dockerfile` in `gemma/gemma4-31b-it-nvfp4/` instead of the standard vLLM image.

## 🚀 Project Structure

The project is organized into directories based on the model provider:

- `gemma/`: Contains deployment configurations for Google's Gemma models.
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
   cd qwen/qwen3.5-27b-nvfp4
   docker compose up -d
   ```

3. **Access the API:**
   ```bash
   curl http://localhost:8000/v1/chat/completions \
     -H "Content-Type: application/json" \
     -d '{"model":"apolo13x/Qwen3.5-27B-NVFP4","messages":[{"role":"user","content":"Hello"}]}'
   ```

## 📋 Configuration

All models use a shared configuration (`docker-compose.common.yml`) with:
- `shm_size: 32GB` for large context support
- GPU caching for HuggingFace and vLLM
- Health check with 10-minute start period

Model-specific parameters (such as `--max-model-len`, `--gpu-memory-utilization`, and `--kv-cache-dtype`) are defined in each `docker-compose.yml` file to optimize performance for the specific model architecture and quantization method used.

### Available Models

The following models are currently configured in this codebase:

#### Gemma Models
| Model Path | Model Name |
| :--- | :--- |
| `gemma/gemma4-26b-a4b-it/` | Gemma 4 26B A4B IT |
| `gemma/gemma4-31b-it-nvfp4/` | Gemma 4 31B IT NVFP4 |
| `gemma/gemma4-e4b-it/` | Gemma 4 E4B IT |

#### Qwen Models
| Model Path | Model Name |
| :--- | :--- |
| `qwen/qwen3.5-27b-awq/` | Qwen 3.5 27B AWQ |
| `qwen/qwen3.5-27b-int4-autoround/` | Qwen 3.5 27B AutoRound |
| `qwen/qwen3.5-27b-nvfp4/` | Qwen 3.5 27B NVFP4 |
| `qwen/qwen3.5-35b-a3b-nvfp4/` | Qwen 3.5 35B A3B NVFP4 |
| `qwen/qwen3.6-27b-nvfp4/` | Qwen 3.6 27B NVFP4 |
| `qwen/qwen3.6-35b-a3b-nvfp4/` | Qwen 3.6 35B A3B NVFP4 |

## 🔌 IDE Integration

This codebase is pre-configured for both [Opencode](https://opencode.ai) and **Roo Code**. The `opencode.json` file defines a custom provider named `local-vllm` that uses the `@ai-sdk/openai-compatible` package.

The models are mapped to the local vLLM endpoint: `http://localhost:8000/v1`.
