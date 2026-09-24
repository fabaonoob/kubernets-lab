# Grafana

Última atualização: 24/09/2026

## Objetivo

Disponibilizar visualização das métricas coletadas pelo Prometheus e construir um dashboard básico para acompanhamento do cluster Kubernetes.

## Arquitetura

```text
Prometheus
    │
    │ métricas
    ▼
Grafana
    │
    └── Dashboard Kubernetes Lab
```

## Instalação

O Grafana foi instalado como parte do:

```text
kube-prometheus-stack
```

Chart:

```text
kube-prometheus-stack: 89.2.1
```

## Configuração

O Grafana foi habilitado com:

```yaml
grafana:
  enabled: true

  adminUser: admin
  adminPassword: admin

  persistence:
    enabled: true
    size: 5Gi
    storageClassName: longhorn

  service:
    type: ClusterIP
```

> A senha apresentada acima é apropriada somente para o laboratório. Credenciais reais não devem ser armazenadas em arquivos versionados.

## Persistência

O Grafana utiliza:

```text
StorageClass: longhorn
```

com armazenamento persistente.

Isso permite manter os dados do Grafana independentemente da recriação do Pod.

## Service

Service:

```text
prometheus-grafana
```

Tipo:

```text
ClusterIP
```

## Acesso

Como o Service é `ClusterIP`, o acesso administrativo ao Grafana no laboratório é realizado através de port-forward.

No VUHMT:

```bash
kubectl port-forward \
  -n monitoring \
  svc/prometheus-grafana \
  3000:80 \
  --address=0.0.0.0
```

O acesso a partir do Windows Host é:

```text
http://10.10.10.10:3000
```

Foi necessário liberar a porta:

```text
3000/tcp
```

no firewall do VUHMT.

## Datasource

O datasource utilizado para as métricas é o Prometheus.

O acesso foi validado através do Grafana Explore.

Consulta:

```promql
up
```

A consulta retornou séries de métricas dos componentes do cluster.

## Dashboard

Foi criado o dashboard:

```text
Kubernetes Lab
```

### Painel: CPU Usage by Node

Consulta:

```promql
100 - (
  avg by (instance) (
    rate(node_cpu_seconds_total{mode="idle"}[5m])
  ) * 100
)
```

Unidade:

```text
Percent (0-100)
```

### Painel: Memory Usage by Node

Consulta:

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

Unidade:

```text
Percent
```

### Painel: Kubernetes Nodes

Consulta:

```promql
count(kube_node_info)
```

Visualização:

```text
Stat
```

Resultado esperado:

```text
2
```

### Painel: Total Pods

Consulta:

```promql
count(kube_pod_info)
```

Visualização:

```text
Stat
```

### Painel: Pods by Namespace

Consulta:

```promql
count by (namespace) (
  kube_pod_info
)
```

Visualização:

```text
Bar chart
```

## Validação

O Grafana foi validado através de:

```text
Datasource Prometheus
Explore
Dashboard Kubernetes Lab
```

Os dados foram exibidos corretamente.

Durante a implantação, houve um momento em que os painéis apresentaram `No data`. Após a validação do Prometheus e do datasource, os dados voltaram a ser exibidos.

Isso confirmou que o Prometheus permanecia funcional e que o dashboard não precisava ser recriado.

## Resultado

O Grafana permite visualizar:

```text
CPU dos nós
Memória dos nós
Quantidade de nós
Quantidade total de Pods
Distribuição de Pods por namespace
```

**Status: ✅ Concluído**