# Nomad Kubernetes Deployment

This Helm chart deploys the Nomad application and its components to a Kubernetes cluster.

## Components

- **app**: Main application service
- **worker**: Background worker service
- **north**: JupyterHub service
- **fs-pvc**: Shared persistent volume for all components

## Installation

1. Add required Helm repositories:
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

2. Install the chart:
```bash
helm install nomad ./ops/kubernetes \
  --namespace nomad \
  --create-namespace \
  --set global.storageClass="standard" \
  --set global.volumeSize="10Gi"
```

## Ingress Configuration

The ingress configuration allows exposing the Nomad services externally. Here's an example configuration:

```yaml
ingress:
  enabled: true
  host: nomad.example.com
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
  tls:
    - hosts:
        - nomad.example.com
      secretName: nomad-tls
```

Key parameters:
- `enabled`: Enable/disable ingress
- `host`: Domain name for the ingress
- `annotations`: Custom annotations for ingress controller
- `tls`: TLS configuration with automatic certificate management

## Configuration Reference

The main configuration options are available in `values.yaml`:

### Global Configuration
```yaml
global:
  storageClass: "standard"  # Storage class for persistent volumes
  volumeSize: "10Gi"        # Default volume size for all components
```

### Application Configuration
```yaml
app:
  replicaCount: 2           # Number of app replicas
  resources:                # Resource requests/limits
    requests:
      cpu: "500m"
      memory: "512Mi"
    limits:
      cpu: "1000m"
      memory: "1Gi"
```

### Worker Configuration
```yaml
worker:
  replicaCount: 3           # Number of worker replicas
  resources:
    requests:
      cpu: "500m"
      memory: "512Mi"
    limits:
      cpu: "1000m"
      memory: "1Gi"
```

### JupyterHub Configuration
```yaml
north:
  replicaCount: 1           # Number of JupyterHub replicas
  resources:
    requests:
      cpu: "1000m"
      memory: "1Gi"
    limits:
      cpu: "2000m"
      memory: "2Gi"
```

### Storage Configuration
```yaml
fs:
  accessModes: ["ReadWriteOnce"]
  storageClassName: "standard"
  size: "10Gi"
```

### Network Policies
```yaml
networkPolicies:
  enabled: true             # Enable network policies
  allowNamespaceTraffic: true # Allow traffic within namespace

- **Storage**: Configure storage class and volume size
```yaml
global:
  storageClass: "standard"
  volumeSize: "10Gi"
```

- **Replica counts**: Configure number of replicas for each component
```yaml
app:
  replicaCount: 2
worker:
  replicaCount: 3
north:
  replicaCount: 1
```

- **Network Policies**: By default, all traffic within the namespace is allowed

## Dependencies

- RabbitMQ (installed via Bitnami chart)
- Elasticsearch (installed via Elastic chart)
- MongoDB (installed via Bitnami chart)
- JupyterHub (installed via JupyterHub chart)
