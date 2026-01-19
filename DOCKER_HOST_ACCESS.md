# Docker Container Access to Host Services Guide

## Core Configuration

Use `http://host.docker.internal:PORT` inside Docker containers to access host services.

### macOS / Windows

Works directly, no additional configuration needed.

### Linux

**docker-compose** - Already configured in `docker-compose.yml`:
```yaml
extra_hosts:
  - "host.docker.internal:host-gateway"
```

**docker run** - Add parameter:
```bash
docker run --add-host=host.docker.internal:host-gateway ...
```

## Ollama Configuration Example

### 1. Start Service on Host

```bash
# Listen on all interfaces (Important)
export OLLAMA_HOST=0.0.0.0:11434
ollama serve
```

### 2. Frontend AI Service Configuration

| Field | Value |
|------|-----|
| Base URL | `http://host.docker.internal:11434/v1` |
| Provider | `openai` |
| Model | `qwen2.5:latest` |
| API Key | `ollama` or leave empty |

### 3. Other Service Ports

| Service | Default Port | Base URL |
|------|---------|----------|
| Ollama | 11434 | `http://host.docker.internal:11434/v1` |
| LM Studio | 1234 | `http://host.docker.internal:1234/v1` |
| vLLM | 8000 | `http://host.docker.internal:8000/v1` |

## Verification and Troubleshooting

### Test Connection

```bash
# Test from inside container
docker exec -it huobao-drama sh
wget -O- http://host.docker.internal:11434/api/tags

# View container logs
docker logs huobao-drama -f
```

### Common Issues

**Connection refused**

1. **Host service not running** - Check service status
   ```bash
   curl http://localhost:11434/api/tags
   ```

2. **Service not listening on 0.0.0.0** - Ollama defaults to 127.0.0.1 only
   ```bash
   export OLLAMA_HOST=0.0.0.0:11434
   ollama serve
   ```

3. **Firewall blocking** - Check firewall rules or temporarily disable for testing
