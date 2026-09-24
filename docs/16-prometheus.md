# Prometheus

Última atualização: 24/09/2026

## Objetivo

Implementar monitoramento de métricas do cluster Kubernetes e dos nós utilizando Prometheus Operator através do chart `kube-prometheus-stack`.

## Arquitetura

```text
Kubernetes
    │
    ├── kube-state-metrics
    │
    ├── node-exporter
    │     ├── VUHMT
    │     └── VUHWK
    │
    ├── kubelet
    │
    └── Kubernetes Control Plane
             │
             ▼
         Prometheus
```

## Versão

Instalação realizada através do chart:

```text
kube-prometheus-stack: 89.2.1
```

Application version:

```text
v0.93.1
```

## Namespace

```bash
kubectl create namespace monitoring
```

## Repositório Helm

```bash
helm repo add prometheus-community \
  https://prometheus-community.github.io/helm-charts

helm repo update
```

## Configuração

Arquivo:

```text
prometheus-values.yaml
```

Configuração base:

```yaml
grafana:
  enabled: false

alertmanager:
  enabled: false

prometheus:
  prometheusSpec:
    retention: 7d
    replicas: 1

    resources:
      requests:
        cpu: 100m
        memory: 256Mi
      limits:
        cpu: 500m
        memory: 512Mi

nodeExporter:
  enabled: true

kubeStateMetrics:
  enabled: true
```

Posteriormente o Grafana foi habilitado no mesmo stack.

## Instalação

```bash
helm install prometheus \
  prometheus-community/kube-prometheus-stack \
  -n monitoring \
  -f prometheus-values.yaml
```

## Componentes

Pods validados:

```text
prometheus-kube-prometheus-operator
prometheus-kube-state-metrics
prometheus-prometheus-kube-prometheus-prometheus-0
prometheus-prometheus-node-exporter
```

O Node Exporter foi executado nos dois nós:

```text
VUHMT
10.10.10.10

VUHWK
10.10.10.20
```

## Prometheus CR

Recurso:

```text
prometheus-kube-prometheus-prometheus
```

Estado validado:

```text
DESIRED: 1
READY: 1
RECONCILED: True
AVAILABLE: True
```

## ServiceMonitors

Foram identificados ServiceMonitors para componentes como:

```text
kube-apiserver
coredns
controller-manager
etcd
kube-proxy
scheduler
kubelet
prometheus operator
kube-state-metrics
node-exporter
prometheus
```

## Validação da API

Port-forward:

```bash
kubectl port-forward \
  -n monitoring \
  svc/prometheus-kube-prometheus-prometheus \
  9090:9090
```

Teste:

```bash
curl http://127.0.0.1:9090/-/ready
```

Resultado:

```text
Prometheus Server is Ready.
```

Consulta:

```bash
curl -s \
  'http://127.0.0.1:9090/api/v1/query?query=up'
```

A consulta retornou séries com valor:

```text
1
```

incluindo targets dos dois nós e do próprio Prometheus.

## Métricas utilizadas pelo Grafana

As métricas do Prometheus foram utilizadas para criar o dashboard:

```text
Kubernetes Lab
```

Entre as consultas utilizadas:

### CPU por nó

```promql
100 - (
  avg by (instance) (
    rate(node_cpu_seconds_total{mode="idle"}[5m])
  ) * 100
)
```

### Memória por nó

```promql
100 * (
  1 -
  (
    node_memory_MemAvailable_bytes
    /
    node_memory_MemTotal_bytes
  )
)
```

### Número de nós

```promql
count(kube_node_info)
```

### Total de Pods

```promql
count(kube_pod_info)
```

### Pods por namespace

```promql
count by (namespace) (
  kube_pod_info
)
```

## Retenção

Retenção configurada:

```text
7 dias
```

## Limitações

O ambiente possui apenas:

```text
1 réplica do Prometheus
```

Portanto, não existe alta disponibilidade do servidor Prometheus.

A configuração é adequada ao laboratório e ao objetivo de aprendizado, mas não representa uma implantação HA de produção.

## Resultado

```text
Prometheus
    │
    ├── Métricas Kubernetes
    ├── Métricas dos nós
    ├── kube-state-metrics
    ├── node-exporter
    └── ServiceMonitors
```

A coleta foi validada através da API e os dados foram utilizados pelo Grafana.

**Status: ✅ Concluído**