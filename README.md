# OpenViking Deploy

OpenViking Helm chart deployment with Higress gateway integration.

## Architecture

```
Client ──► Higress Gateway (NodePort 31080) ──► Ingress ──► OpenViking Service (ClusterIP:1933)
```

## Quick Start

```bash
# 1. Edit values.yaml - set your API keys
vi openviking/values.yaml

# 2. Create namespace (first time only)
kubectl create namespace openviking

# 3. Copy ACR pull secret (first time only)
kubectl get secret acr-registry-secret -n open-webui -o json | \
  python3 -c "import sys,json;d=json.load(sys.stdin);del d['metadata']['namespace'];del d['metadata']['resourceVersion'];del d['metadata']['uid'];del d['metadata']['creationTimestamp'];d['metadata']['namespace']='openviking';print(json.dumps(d))" | \
  kubectl apply -f -

# 4. Install
helm install openviking ./openviking -n openviking

# 5. Health check (replace GATEWAY-IP with your Higress gateway IP)
curl http://<GATEWAY-IP>:31080/health
```

## Configuration

Edit `openviking/values.yaml`:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Docker image | `slp-acr-registry.cn-hangzhou.cr.aliyuncs.com/slp/volcengine-openviking` |
| `image.tag` | Image tag | `v20260630` |
| `imagePullSecrets` | Registry pull secrets | `acr-registry-secret` |
| `service.type` | Service type | `ClusterIP` |
| `ingress.enabled` | Enable Higress Ingress | `true` |
| `ingress.className` | Ingress class | `higress` |
| `ingress.host` | Host matching (empty = any) | `""` |
| `gateway.nodePort` | Higress gateway NodePort | `31080` |
| `openviking.config.server.root_api_key` | API key for authentication | `sk-ov-changeme` |
| `openviking.config.embedding.dense.api_key` | DashScope API key | - |
| `openviking.config.vlm.api_key` | DashScope API key | - |

## Access

| Endpoint | URL |
|----------|-----|
| Health | `http://<GATEWAY-IP>:31080/health` |
| MCP | `http://<GATEWAY-IP>:31080/mcp` |
| Web Studio | `http://<GATEWAY-IP>:31080/studio` |

## MCP Client Configuration

```json
{
  "mcpServers": {
    "openviking": {
      "url": "http://<GATEWAY-IP>:31080/mcp",
      "headers": {
        "Authorization": "Bearer sk-ov-changeme"
      }
    }
  }
}
```

## Useful Commands

```bash
# Upgrade
helm upgrade openviking ./openviking -n openviking

# Uninstall
helm uninstall openviking -n openviking

# View logs
kubectl logs -l app.kubernetes.io/name=openviking -n openviking -f

# Port forward (for local testing)
kubectl port-forward svc/openviking 1933:1933 -n openviking
```
