# 🖥️ AI Setup Guide: Mac Mini M4 Pro

> **Hardware:** Mac Mini Pro M4, 64GB RAM
> **Purpose:** Local LLM deployment for Sentient Factory AI
> **Last Updated:** 2024-12-27

---

## 1. Hardware Specs & Capabilities

### Your Setup
| Component         | Spec          | AI Capability                   |
| ----------------- | ------------- | ------------------------------- |
| **Chip**          | Apple M4 Pro  | 12-14 core CPU, 16-20 GPU cores |
| **RAM**           | 64GB Unified  | Can run 70B models quantized    |
| **Storage**       | 512GB-2TB SSD | Fast model loading              |
| **Neural Engine** | 16-core       | ~38 TOPS for ML                 |

### Model Sizing Guide

| Model Size   | VRAM Needed (Q4) | Your Mac    | Performance |
| ------------ | ---------------- | ----------- | ----------- |
| 7B           | ~6GB             | ✅ Excellent | 50+ tok/s   |
| 14B          | ~10GB            | ✅ Excellent | 30+ tok/s   |
| 32B          | ~20GB            | ✅ Great     | 15-20 tok/s |
| 70B (Q4_K_M) | ~40GB            | ✅ Good      | 8-12 tok/s  |
| 72B (Q4_K_S) | ~42GB            | ✅ Good      | 7-10 tok/s  |

> **Rekomendasi untuk 64GB:** Qwen2.5-72B-Instruct (Q4_K_M) atau Qwen2.5-32B-Instruct (Q4_K_M)

---

## 2. Option 1: Ollama (Recommended for Easy Setup)

### 2.1 Install Ollama

```bash
# Download dan install Ollama untuk Mac
curl -fsSL https://ollama.com/install.sh | sh

# Atau download dari website
# https://ollama.com/download/mac
```

### 2.2 Download Qwen Model

```bash
# Untuk performa optimal di 64GB RAM
# Qwen2.5-72B (quantized Q4)
ollama pull qwen2.5:72b

# Alternatif yang lebih cepat
ollama pull qwen2.5:32b

# Untuk testing cepat
ollama pull qwen2.5:14b
```

### 2.3 Configure Ollama

```bash
# Set environment variables
export OLLAMA_HOST=0.0.0.0:11434
export OLLAMA_NUM_PARALLEL=2
export OLLAMA_MAX_LOADED_MODELS=1

# Untuk startup otomatis, tambahkan ke ~/.zshrc
echo 'export OLLAMA_HOST=0.0.0.0:11434' >> ~/.zshrc
```

### 2.4 Create Custom Model (Optimized)

```bash
# Buat file Modelfile
cat > Modelfile << 'EOF'
FROM qwen2.5:72b

# System prompt untuk Sentient Factory
SYSTEM """
You are the 'Sentient Factory Brain', an AI assistant for manufacturing operations.
Rules:
1. Answer in Indonesian (Bahasa Indonesia)
2. Be data-driven, never guess
3. Focus on operational efficiency
"""

# Parameters
PARAMETER temperature 0.1
PARAMETER top_p 0.9
PARAMETER num_ctx 8192
PARAMETER num_gpu 99
PARAMETER num_thread 12
EOF

# Create custom model
ollama create sentient-brain -f Modelfile
```

### 2.5 Test Ollama

```bash
# Test query
ollama run sentient-brain "Berapa revenue bulan ini?"

# API test
curl http://localhost:11434/api/generate -d '{
  "model": "sentient-brain",
  "prompt": "Berapa revenue bulan ini?",
  "stream": false
}'
```

### 2.6 Ollama as Service

```bash
# Ollama otomatis jalan sebagai service di Mac
# Cek status
launchctl list | grep ollama

# Restart jika perlu
launchctl stop com.ollama.ollama
launchctl start com.ollama.ollama
```

---

## 3. Option 2: LM Studio (GUI-based)

### 3.1 Install LM Studio

```bash
# Download dari website
# https://lmstudio.ai/

# Atau dengan Homebrew
brew install --cask lm-studio
```

### 3.2 Download Model

1. Buka LM Studio
2. Search: `Qwen/Qwen2.5-72B-Instruct-GGUF`
3. Pilih quantization: `Q4_K_M` (recommended untuk 64GB)
4. Download (~42GB)

### 3.3 Enable API Server

1. Klik tab "Local Server"
2. Load model: `qwen2.5-72b-instruct-q4_k_m.gguf`
3. Set context length: 8192
4. Klik "Start Server"
5. API available at: `http://localhost:1234/v1`

### 3.4 LM Studio Config

```yaml
# Recommended settings untuk M4 Pro 64GB
model: qwen2.5-72b-instruct-q4_k_m.gguf
context_length: 8192
gpu_layers: 99  # Use all GPU layers
threads: 12     # Match CPU cores
batch_size: 512
```

---

## 4. Option 3: MLX (Apple Silicon Optimized)

### 4.1 Install MLX

```bash
# Pastikan Python 3.11+
python3 --version

# Install MLX
pip install mlx mlx-lm

# Install dependencies
pip install transformers huggingface_hub
```

### 4.2 Download Model (MLX Format)

```bash
# Download Qwen2.5-72B dalam format MLX
# (Optimized untuk Apple Silicon)

python -c "
from huggingface_hub import snapshot_download

snapshot_download(
    repo_id='mlx-community/Qwen2.5-72B-Instruct-4bit',
    local_dir='./models/qwen2.5-72b-mlx',
    local_dir_use_symlinks=False
)
"
```

### 4.3 Run MLX Server

```python
# mlx_server.py

from mlx_lm import load, generate
import uvicorn
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Load model
model, tokenizer = load("./models/qwen2.5-72b-mlx")

class ChatRequest(BaseModel):
    prompt: str
    max_tokens: int = 1024
    temperature: float = 0.1

@app.post("/v1/completions")
async def chat(request: ChatRequest):
    response = generate(
        model, 
        tokenizer,
        prompt=request.prompt,
        max_tokens=request.max_tokens,
        temp=request.temperature,
    )
    return {"text": response}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8080)
```

```bash
# Run server
python mlx_server.py
```

---

## 5. Performance Optimization

### 5.1 macOS Settings

```bash
# Disable sleep untuk server
sudo pmset -a disablesleep 1
sudo pmset -a sleep 0

# Increase file descriptors
ulimit -n 65536

# Disable Spotlight indexing untuk model folder
sudo mdutil -i off /path/to/models
```

### 5.2 Memory Management

```bash
# Check memory usage
top -l 1 | grep PhysMem

# Clear memory cache (if needed)
sudo purge

# Monitor during inference
while true; do
    memory_pressure | head -5
    sleep 5
done
```

### 5.3 Ollama Tuning

```bash
# Optimal settings untuk 64GB + M4 Pro
export OLLAMA_NUM_GPU=99        # Use all GPU layers
export OLLAMA_KEEP_ALIVE=24h    # Keep model loaded
export OLLAMA_MAX_QUEUE=10      # Max pending requests
```

---

## 6. Integration with Python Service

### 6.1 Update Python AI Config

```python
# app/config.py

from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    # LLM Settings for Mac Mini M4
    LLM_PROVIDER: str = "ollama"  # ollama, lm_studio, mlx
    LLM_BASE_URL: str = "http://localhost:11434"
    LLM_MODEL: str = "sentient-brain"  # Custom Ollama model
    
    # Performance settings for 64GB
    LLM_NUM_CTX: int = 8192
    LLM_NUM_GPU: int = 99
    LLM_TEMPERATURE: float = 0.1
    LLM_MAX_TOKENS: int = 4096
```

### 6.2 LangChain Integration

```python
# app/agent/llm_client.py

from langchain_community.llms import Ollama
from langchain_community.chat_models import ChatOllama
from app.config import settings

def get_llm():
    """Get Ollama LLM for Mac Mini M4."""
    return ChatOllama(
        model=settings.LLM_MODEL,
        base_url=settings.LLM_BASE_URL,
        temperature=settings.LLM_TEMPERATURE,
        num_ctx=settings.LLM_NUM_CTX,
        num_gpu=settings.LLM_NUM_GPU,
    )
```

### 6.3 Test Connection

```python
# test_llm.py

from langchain_community.llms import Ollama

llm = Ollama(
    model="sentient-brain",
    base_url="http://localhost:11434",
    temperature=0.1,
)

response = llm.invoke("Berapa revenue bulan ini?")
print(response)
```

---

## 7. Benchmark & Testing

### 7.1 Quick Benchmark

```bash
# Test inference speed
time ollama run sentient-brain "Jelaskan kenapa margin turun dan apa solusinya dalam 3 paragraf."

# Expected output untuk 72B model:
# - First token: ~2-3 seconds
# - Generation: 8-12 tokens/second
# - Total: ~30-60 seconds untuk 300 token response
```

### 7.2 Stress Test

```python
# benchmark.py

import time
import requests

def benchmark(num_requests=10):
    prompt = "Jelaskan strategi untuk meningkatkan revenue pabrik dalam 200 kata."
    
    times = []
    for i in range(num_requests):
        start = time.time()
        response = requests.post(
            "http://localhost:11434/api/generate",
            json={"model": "sentient-brain", "prompt": prompt, "stream": False}
        )
        elapsed = time.time() - start
        times.append(elapsed)
        print(f"Request {i+1}: {elapsed:.2f}s")
    
    avg = sum(times) / len(times)
    print(f"\nAverage: {avg:.2f}s")
    print(f"Requests/min: {60/avg:.1f}")

if __name__ == "__main__":
    benchmark()
```

---

## 8. Troubleshooting

### 8.1 Common Issues

| Issue              | Solution                                        |
| ------------------ | ----------------------------------------------- |
| Model loading slow | Pertama kali load ~2-3 menit, setelah itu cepat |
| Out of memory      | Gunakan quantization lebih kecil (Q3_K_M)       |
| Slow generation    | Pastikan `num_gpu: 99` untuk full GPU           |
| Connection refused | Check `OLLAMA_HOST=0.0.0.0:11434`               |

### 8.2 Debug Commands

```bash
# Check Ollama status
ollama list
ollama ps

# Check loaded models
curl http://localhost:11434/api/tags

# Check memory usage
vm_stat | grep "Pages"

# Monitor GPU usage
sudo powermetrics --samplers gpu_power
```

### 8.3 Reset Ollama

```bash
# Stop Ollama
pkill ollama

# Clear cache (if needed)
rm -rf ~/.ollama/models/.cache

# Restart
ollama serve &
```

---

## 9. Recommended Architecture

```
┌──────────────────────────────────────────────────────────┐
│                   Mac Mini M4 Pro (64GB)                 │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  ┌─────────────────┐    ┌─────────────────────────────┐  │
│  │    Ollama       │◄──►│  Qwen2.5-72B-Instruct      │  │
│  │  Port: 11434    │    │  ~42GB VRAM (Q4_K_M)       │  │
│  └────────┬────────┘    └─────────────────────────────┘  │
│           │                                              │
│           ▼                                              │
│  ┌─────────────────┐    ┌─────────────────────────────┐  │
│  │ Python AI Svc   │◄──►│  PostgreSQL                │  │
│  │  NATS Client    │    │  Port: 5432                │  │
│  └────────┬────────┘    └─────────────────────────────┘  │
│           │                                              │
│           ▼                                              │
│  ┌─────────────────┐    ┌─────────────────────────────┐  │
│  │     NATS        │◄──►│  NestJS Backend            │  │
│  │  Port: 4222     │    │  Port: 8000                │  │
│  └─────────────────┘    └─────────────────────────────┘  │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

## 10. Quick Start Script

```bash
#!/bin/bash
# start-ai-services.sh

echo "🖥️ Starting Sentient Factory AI Services..."

# 1. Start Ollama (if not running)
if ! pgrep -x "ollama" > /dev/null; then
    echo "Starting Ollama..."
    ollama serve &
    sleep 5
fi

# 2. Load model
echo "Loading Qwen model..."
ollama run sentient-brain "warmup" --verbose

# 3. Start Python AI service
echo "Starting Python AI service..."
cd /path/to/sentient-ai-service
source venv/bin/activate
python -m app.main &

echo "✅ AI Services ready!"
echo "   - Ollama: http://localhost:11434"
echo "   - Python: NATS listening"
```

```bash
# Jalankan
chmod +x start-ai-services.sh
./start-ai-services.sh
```

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
