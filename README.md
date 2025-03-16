# OpenAI-Forward: Universal API Proxy & Compatibility Layer

⚠️ **GPL-3.0 Licensed - Copyleft Enforcement**  

**This software is licensed under the GNU General Public License v3.0 (GPL-3.0).** Any derivative work must be distributed under the same license terms. You **MUST** open-source your modified version if you deploy this software in production.


![GitHub](https://img.shields.io/github/license/zwpride/openai-forward)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)
[![FastAPI](https://img.shields.io/badge/Framework-FastAPI-green)](https://fastapi.tiangolo.com/)

A flexible proxy server that enables seamless integration between OpenAI-compatible clients and various LLM API providers (e.g., `Alibaba Cloud DashScope`, `VLLM`, `Ollama`...) with enhanced logging, protocol translation, and compatibility features.

## Key Features

✨ **Smart Stream Conversion**  
Auto-convert between stream/non-stream formats for client and server  
🌉 **Protocol Translation**  
Seamless API spec conversion between OpenAI and other providers  
📊 **Full Observability**  
JSONL logging for both client interactions and upstream calls  
⚡ **Dual-Mode Operation**  
`stream`/`non_stream` modes with automatic fallback handling  
🔀 **Response Normalization**  
Standardized error codes and JSON formats across providers  
🛡️ **Compatibility Shield**  
200-status wrapper for legacy client support  

## 🚀 Quick Start

### Prerequisites

- Python 3.8+

```bash
pip install -r requirements.txt
```

## Minimum usage

```bash
python app.py \
  --output_path ./logs \
  --expose_host 0.0.0.0 \
  --expose_port 58080 \
  --url https://dashscope.aliyuncs.com/compatible-mode/v1/chat/completions \
  --timeout 60000
```

## Configuration Options

| Parameter        | Description                                  | Default                              |
|------------------|----------------------------------------------|--------------------------------------|
| `--output_path`  | Log storage directory                        | `.`                                  |
| `--expose_host`  | Binding address                              | `0.0.0.0`                            |
| `--expose_port`  | Listening port                               | `58080`                              |
| `--mode`         | Upstream comm mode [stream\|non_stream\|off] | `off`                                |
| `--url`          | Target API endpoint                          | OpenAI compatibility endpoint (e.g., `Alibaba Cloud DashScope`, `VLLM`, `Ollama`...)     |
| `--timeout`      | API timeout (ms)                             | `60000`                              |
| `--compatible`   | Force 200 status for errors                  | `False`                              |


* Attention: We follow [OpenAI API Reference](https://platform.openai.com/docs/api-reference) for best practices, but the actual parameters need to be supported by the URL provider, as we are not responsible for parameter parsing, only forwarding.

| URL Endpoint Doc | URL |
| --- | --- |
| `Dashscope` / `Qwen` | `https://help.aliyun.com/zh/model-studio/developer-reference/use-qwen-by-calling-api`   |
| `OpenAI`             | `https://platform.openai.com/docs/api-reference`                                        |
| `VLLM`               | `https://docs.vllm.ai/en/latest/api/inference_params.html`                              |


## 📚 Usage Examples

### 1. Basic Chat Completion
```bash
curl http://localhost:58080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_DASHSCOPE_KEY" \
  -d '{
    "model": "qwen-plus",
    "messages": [{"role": "user", "content": "Explain quantum computing"}]
  }'
```

### 2. Stream Conversion Scenarios

**a. Client Stream → Server Non-stream (Auto-convert)**  
```bash
curl http://localhost:58080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_KEY" \
  -d '{
    "model": "qwen-turbo",
    "messages": [{"role": "user", "content": "Hello!"}],
    "stream": true
  }' \
  --NON_STREAM_SERVER_MODE
```

**b. Client Non-stream → Server Stream (Auto-packaging)**  
```bash
curl http://localhost:58080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_KEY" \
  -d '{
    "model": "qwen-max",
    "messages": [{"role": "user", "content": "Write a poem"}]
  }' \
  --STREAM_SERVER_MODE
```

### 3. Compatibility Mode
**Normal Mode (True HTTP Codes)**  
```bash
# Returns actual status codes (401/429/500 etc.)
curl http://localhost:58080/v1/chat/completions \
  -H "Authorization: invalid_key" \
  -d '{"model": "qwen-plus", "messages": [...]}'
```

**Compatibility Mode (Always 200)**  
```bash
# Returns 200 with error details in JSON body
curl http://localhost:58080/v1/chat/completions \
  -H "Authorization: invalid_key" \
  -d '{"model": "qwen-plus", "messages": [...]}' \
  --compatible
```

### 4. Advanced Logging
```bash
# View client interactions
tail -f ./logs/client_interaction.jsonl

# Monitor server communications
tail -f ./logs/server_interaction.jsonl
```

## 🔍 Protocol Conversion

### OpenAI → DashScope
```python
# Original OpenAI request
{
  "model": "gpt-4",
  "messages": [{"role": "user", "content": "Hello"}],
  "temperature": 0.7
}

# Converted DashScope request
{
  "model": "qwen-max",
  "input": {
    "messages": [{"role": "user", "content": "Hello"}]
  },
  "parameters": {
    "temperature": 0.7
  }
}
```

### Response Normalization
```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "qwen-plus",
  "usage": {
    "prompt_tokens": 15,
    "completion_tokens": 32,
    "total_tokens": 47
  },
  "choices": [{
    "message": {
      "role": "assistant",
      "content": "Hello! How can I help you today?"
    }
  }]
}
```

## 📊 Logging Architecture

```
/logs
├── client_interaction.jsonl  # Client-side requests/responses
├── server_interaction.jsonl  # Upstream API communications
└── log.log                   # System operation logs
```

Sample log entry:
```json
{
  "timestamp": "2024-02-15T14:22:35.123456",
  "logging_id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "input": {"model": "qwen-plus", "messages": [...]},
  "output": {"choices": [...]},
  "send_mode": "stream",
  "return_mode": "non_stream"
}
```


## License

This project is licensed under the GNU General Public License v3.0 - see [LICENSE](LICENSE) for details.

## 🤝 Contributing


Contributions are welcome! Please open an issue or submit a PR for any improvements.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add some amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request


**Disclaimer**: This project is not affiliated with OpenAI or any API providers. Ensure proper authorization when accessing commercial APIs.
