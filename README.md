# Local Docker vLLM Deployment (RTX 5090)

This repository provides a structured way to deploy various Large Language Models (LLMs) using **vLLM** within **Docker** containers, optimized for deployment on **NVIDIA RTX 5090** hardware. It is organized by model family (e.g., Gemma, Qwen) and includes pre-configured `docker-compose.yml` files for easy deployment.

## 🚀 Project Structure

The project is organized into directories based on the model provider:

- `gemma/`: Contains deployment configurations for Google's Gemma models.
- `qwen/`: Contains deployment configurations for Alibaba's Qwen models.
- `.env`: Environment variables for the project.
- `opencode.json`: Configuration for Opencode, mapping the local models to a custom provider.

## 🛠️ Deployment

Each model has its own directory containing a `docker-compose.yml` file. To deploy a specific model, navigate to its directory and run:

```bash
docker compose up -d
```

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
| `qwen/qwen3.5-27b-nvfp4/` | Qwen 3.5 27B NVFP4 |
| `qwen/qwen3.5-35b-a3b-nvfp4/` | Qwen 3.5 35B A3B NVFP4 |

## 🔌 Opencode Integration

This codebase is pre-configured for [Opencode](https://opencode.ai). The `opencode.json` file defines a custom provider named `local-vllm` that uses the `@ai-sdk/openai-compatible` package.

The models are mapped to the local vLLM endpoint: `http://localhost:8000/v1`.

## 📝 Configuration Details

The `docker-compose.yml` files are configured to run vLLM with specific parameters such as:
- `--host=0.0.0.0`
- `--port=8000`
- `--max-model-len`
- `--gpu-memory-utilization`
- Quantization settings (e.g., `modelopt`, `AWQ`)

---
*Note: Ensure you have NVIDIA Container Toolkit installed to run these models on your GPU.*
