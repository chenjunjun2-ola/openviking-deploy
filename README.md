# OpenViking Deploy

OpenViking Helm chart deployment configuration for K8s.

## Quick Start

```bash
# 1. Edit values.yaml - set your API keys
vi openviking/values.yaml

# 2. Install
helm install openviking ./openviking

# 3. Check status
kubectl get pods -l app.kubernetes.io/name=openviking
kubectl get svc openviking

# 4. Health check
curl http://<EXTERNAL-IP>:1933/health
```

## Configuration

Edit `openviking/values.yaml`:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Docker image | `slp-acr-registry.cn-hangzhou.cr.aliyuncs.com/slp/volcengine-openviking` |
| `image.tag` | Image tag | `v20260630` |
| `service.type` | Service type | `LoadBalancer` |
| `openviking.config.server.root_api_key` | API key for authentication | `sk-ov-changeme` |
| `openviking.config.embedding.dense.api_key` | DashScope API key | - |
| `openviking.config.vlm.api_key` | DashScope API key | - |

## MCP Integration

After deployment, configure your local MCP client:

```json
{
  "mcpServers": {
    "openviking": {
      "url": "http://<EXTERNAL-IP>:1933/mcp",
      "headers": {
        "Authorization": "Bearer <your-api-key>"
      }
    }
  }
}
```

## Useful Commands

```bash
# Upgrade
helm upgrade openviking ./openviking

# Uninstall
helm uninstall openviking

# View logs
kubectl logs -l app.kubernetes.io/name=openviking -f

# Port forward (for local testing)
kubectl port-forward svc/openviking 1933:1933
```
