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

## Configuration

The main configuration options are available in `values.yaml`:

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
- Nginx (installed via Bitnami chart)
- JupyterHub (installed via JupyterHub chart)
