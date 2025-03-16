# OpenAI-Forward: Universal API Proxy & Compatibility Layer

⚠️ **GPL-3.0 Licensed - Copyleft Enforcement**  
**This software is licensed under the GNU General Public License v3.0 (GPL-3.0).** Any derivative work must be distributed under the same license terms. You **MUST** open-source your modified version if you deploy this software in production.


![GitHub](https://img.shields.io/github/license/yourusername/openai-forward)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)
[![FastAPI](https://img.shields.io/badge/Framework-FastAPI-green)](https://fastapi.tiangolo.com/)

A flexible proxy server that enables seamless integration between OpenAI-compatible clients and various LLM API providers (e.g., Alibaba Cloud DashScope) with enhanced logging, protocol translation, and compatibility features.

## Key Features

✨ **Dual-Mode Communication**  
Switch between stream and non-stream modes dynamically using config flags

🌉 **Protocol Translation**  
Convert between OpenAI API spec and other compatible services automatically

📊 **Comprehensive Logging**  
Full request/response logging in JSONL format with interaction tracking

⚙️ **Compatibility Mode**  
Optional HTTP status code normalization for legacy client support

🔄 **Bidirectional Conversion**  
Automatic stream↔non-stream response conversion based on client needs

🔒 **Middleware Support**  
Customizable middleware layer for authentication/authorization extensions

## Installation

```bash
pip install -r requirements.txt
python app.py \
  --output_path ./logs \
  --expose_host 0.0.0.0 \
  --expose_port 58080 \
  --mode stream \
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
| `--url`          | Target API endpoint                          | DashScope compatibility endpoint     |
| `--timeout`      | API timeout (ms)                             | `60000`                              |
| `--compatible`   | Force 200 status for errors                  | `False`                              |

## Usage Examples

**Basic Chat Completion**
```bash
curl http://localhost:58080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "qwen-plus",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

**Streaming Response**
```bash
curl http://localhost:58080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "qwen-plus",
    "messages": [{"role": "user", "content": "Hello!"}],
    "stream": true
  }'
```

## Logging Structure

```
/logs
├── log.log                  # Server operation logs
├── client_interaction.jsonl # Client-facing request/response records
└── server_interaction.jsonl # Upstream API communication records
```

## License

This project is licensed under the GNU General Public License v3.0 - see [LICENSE](LICENSE) for details.

## Contributing

Contributions are welcome! Please open an issue or submit a PR for any improvements.

---

**Note**: Ensure you have proper authorization before proxying requests to commercial API endpoints. This software is not affiliated with OpenAI or any API providers.
