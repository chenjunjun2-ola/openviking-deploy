# OpenViking Deploy

OpenViking Helm chart deployment configuration for K8s.

## Quick Start

```bash
# 1. Clone this repo
git clone git@github.com:chenjunjun2-ola/openviking-deploy.git
cd openviking-deploy

# 2. Create your values file with actual API keys
cat > my-values.yaml <<EOF
openviking:
  config:
    server:
      root_api_key: "sk-ov-your-own-key"
    embedding:
      dense:
        api_key: "your-dashscope-api-key"
    vlm:
      api_key: "your-dashscope-api-key"
EOF

# 3. Install
helm install openviking ./openviking -f my-values.yaml

# 4. Check status
kubectl get pods -l app.kubernetes.io/name=openviking
kubectl get svc openviking

# 5. Health check
curl http://<EXTERNAL-IP>:1933/health
```

## Configuration

Edit `my-values.yaml` to override default values:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Docker image | `slp-acr-registry.cn-hangzhou.cr.aliyuncs.com/slp/volcengine-openviking` |
| `image.tag` | Image tag | `v20260630` |
| `service.type` | Service type | `LoadBalancer` |
| `openviking.config.server.root_api_key` | API key for authentication | `CHANGE_ME` |
| `openviking.config.embedding.dense.api_key` | DashScope API key | `CHANGE_ME` |
| `openviking.config.vlm.api_key` | DashScope API key | `CHANGE_ME` |

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
helm upgrade openviking ./openviking -f my-values.yaml

# Uninstall
helm uninstall openviking

# View logs
kubectl logs -l app.kubernetes.io/name=openviking -f

# Port forward (for local testing)
kubectl port-forward svc/openviking 1933:1933
```
