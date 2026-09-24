# Loki + Alloy

Última atualização: 24/09/2026

## Objetivo

Implementar centralização de logs dos Pods Kubernetes utilizando:

```text
Alloy → Loki → Grafana
```

O Alloy realiza a descoberta e coleta dos logs dos Pods, enquanto o Loki armazena e disponibiliza os logs para consulta.

## Arquitetura

```text
Kubernetes Pods
      │
      │ logs
      ▼
    Alloy
      │
      │ Loki API
      ▼
     Loki
      │
      │ consulta
      ▼
   Grafana
```

## Loki

### Versão

```text
Loki: 3.7.8
Helm Chart: 18.13.4
```

## Namespace

```bash
kubectl create namespace loki
```

## Repositórios Helm

```bash
helm repo add grafana-community \
  https://grafana-community.github.io/helm-charts

helm repo add grafana \
  https://grafana.github.io/helm-charts

helm repo update
```

## Configuração

O Loki foi implantado utilizando o modo:

```text
Monolithic
```

Com:

```text
replication_factor: 1
```

Armazenamento:

```text
filesystem
```

Schema:

```text
TSDB
schema v13
```

Retenção:

```text
168h
```

equivalente a:

```text
7 dias
```

O ambiente utiliza Longhorn para o armazenamento persistente do Loki.

## PVC

PVC validado:

```text
storage-loki-0
```

Estado:

```text
Bound
```

StorageClass:

```text
longhorn
```

Tamanho provisionado:

```text
10Gi
```

## Componentes Loki

Pods validados:

```text
loki-0
loki-canary
loki-gateway
```

Estado:

```text
Running
```

## Alloy

### Versão

```text
Alloy: v1.19.2
Helm Chart: 1.12.1
```

Namespace:

```text
alloy
```

## Instalação

Repositório:

```bash
helm repo add grafana \
  https://grafana.github.io/helm-charts

helm repo update
```

Instalação:

```bash
helm install alloy grafana/alloy \
  -n alloy \
  -f alloy-values.yaml
```

## DaemonSet

O Alloy foi executado como DaemonSet.

Isso permite que exista uma instância por nó elegível.

No ambiente atual, existe:

```text
1 worker
```

portanto o DaemonSet possui:

```text
DESIRED: 1
CURRENT: 1
READY: 1
```

A instância está executando no:

```text
vuhwk
```

## Descoberta de Pods

A configuração utiliza:

```text
discovery.kubernetes
```

para descobrir Pods.

As informações são enriquecidas com labels:

```text
namespace
pod
container
node
```

O componente:

```text
loki.source.kubernetes
```

realiza a coleta dos streams de logs dos Pods.

O envio é realizado através de:

```text
loki.write
```

para:

```text
http://loki-gateway.loki.svc.cluster.local/loki/api/v1/push
```

## Validação da coleta

Os logs do Alloy demonstraram abertura de streams para Pods de diferentes namespaces.

Exemplos validados:

```text
argocd
calico-system
monitoring
longhorn-system
```

Exemplo:

```text
component_id=loki.source.kubernetes.pods
target=monitoring/prometheus-grafana-...:grafana
```

Também foram identificados streams de:

```text
longhorn-system
calico-system
argocd
```

Isso confirmou que o Alloy estava descobrindo e abrindo streams de logs dos Pods.

## Validação do Loki

Port-forward:

```bash
kubectl port-forward \
  -n loki \
  svc/loki-gateway \
  3100:80
```

Consulta de labels:

```bash
curl -s \
  http://127.0.0.1:3100/loki/api/v1/labels
```

A API retornou:

```text
status: success
```

e labels disponíveis para consulta.

## Consulta de logs

Foi executada uma consulta para o namespace `monitoring`:

```bash
curl -s \
  http://127.0.0.1:3100/loki/api/v1/query_range \
  --data-urlencode 'query={namespace="monitoring"}' \
  --data-urlencode 'limit=10'
```

A consulta retornou dados.

Isso confirmou o fluxo completo:

```text
Pod
 ↓
Alloy
 ↓
Loki
 ↓
Query API
```

## Integração com Grafana

O Loki poderá ser utilizado como datasource do Grafana para consultas de logs.

A arquitetura de observabilidade passa a possuir dois fluxos independentes:

### Métricas

```text
Kubernetes
 ↓
Prometheus
 ↓
Grafana
```

### Logs

```text
Kubernetes
 ↓
Alloy
 ↓
Loki
 ↓
Grafana
```

## Limitações

A implantação atual utiliza:

```text
Loki replicas: 1
replication factor: 1
storage: filesystem
```

Portanto, esta configuração não representa uma arquitetura HA de produção.

O objetivo é fornecer uma plataforma funcional de observabilidade para o laboratório.

## Resultado

```text
Loki
    ├── Instalado
    ├── Persistência Longhorn
    ├── API funcional
    └── Recebendo logs

Alloy
    ├── Instalado
    ├── DaemonSet funcional
    ├── Descoberta Kubernetes
    └── Coleta de logs validada
```

**Status: ✅ Concluído**