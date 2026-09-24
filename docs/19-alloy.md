# Grafana Alloy

## 1. Objetivo

O Grafana Alloy foi instalado para atuar como agente de coleta de logs dos Pods Kubernetes e encaminhá-los para o Loki.

Fluxo implementado:

```text
Kubernetes Pods
      ↓
Grafana Alloy
      ↓
Loki Gateway
      ↓
Loki
      ↓
Grafana
```

O Alloy complementa a stack de observabilidade do laboratório, sendo responsável pela coleta dos logs.

---

## 2. Componente

Namespace:

```text
alloy
```

Chart:

```text
grafana/alloy
```

Versão do chart:

```text
1.12.1
```

Versão da aplicação:

```text
v1.19.2
```

---

## 3. Instalação

O repositório oficial do Grafana foi utilizado:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

Instalação:

```bash
helm install alloy grafana/alloy \
  --namespace alloy \
  --create-namespace \
  --version 1.12.1
```

---

## 4. Configuração

O Alloy foi configurado para descobrir os Pods Kubernetes:

```hcl
discovery.kubernetes "pods" {
  role = "pod"
}
```

Foi utilizada descoberta e relabeling para disponibilizar informações dos Pods como labels:

```hcl
discovery.relabel "pods" {
  targets = discovery.kubernetes.pods.targets

  rule {
    source_labels = ["__meta_kubernetes_namespace"]
    target_label  = "namespace"
  }

  rule {
    source_labels = ["__meta_kubernetes_pod_name"]
    target_label  = "pod"
  }

  rule {
    source_labels = ["__meta_kubernetes_pod_container_name"]
    target_label  = "container"
  }

  rule {
    source_labels = ["__meta_kubernetes_pod_node_name"]
    target_label  = "node"
  }
}
```

A coleta utiliza:

```hcl
loki.source.kubernetes "pods" {
  targets    = discovery.relabel.pods.output
  forward_to = [loki.write.default.receiver]
}
```

O destino configurado é o Loki Gateway:

```hcl
loki.write "default" {
  endpoint {
    url = "http://loki-gateway.loki.svc.cluster.local/loki/api/v1/push"
  }
}
```

---

## 5. Funcionamento

O Alloy utiliza a API do Kubernetes para descobrir os Pods e seus containers.

Os logs são coletados e enviados para o endpoint do Loki dentro do cluster.

O fluxo é:

```text
Pod
 │
 │ stdout/stderr
 ▼
Alloy
 │
 │ Loki Push API
 ▼
loki-gateway
 │
 ▼
Loki
```

---

## 6. Validação

Verificar a instalação:

```bash
helm list -n alloy
```

Verificar os Pods:

```bash
kubectl get pods -n alloy -o wide
```

Verificar o DaemonSet:

```bash
kubectl get daemonset -n alloy
```

Os Pods do Alloy foram validados como `Running`.

Também foram analisados os logs do Alloy.

Foram observados streams provenientes de diferentes namespaces do cluster, incluindo:

```text
argocd
calico-system
monitoring
longhorn-system
```

Isso confirmou que o Alloy estava descobrindo workloads Kubernetes e realizando a coleta dos logs.

---

## 7. Integração com Loki

A integração foi validada de ponta a ponta:

```text
Kubernetes
    ↓
Alloy
    ↓
Loki
    ↓
Grafana
```

Foi possível consultar no Loki logs originados dos workloads Kubernetes.

Resultado:

```text
PASS
```

---

## 8. Observação

Durante a validação foi observado um registro relacionado a:

```text
client-side throttling, not priority and fairness
```

O evento não impediu a coleta dos logs.

Os streams continuaram sendo processados normalmente e a integração Alloy → Loki permaneceu funcional.

---

## 9. Resultado

O Grafana Alloy está instalado e funcional.

Responsabilidades no laboratório:

- descoberta de Pods;
- coleta de logs;
- enriquecimento dos logs com labels;
- envio dos logs para Loki.

Estado:

```text
Grafana Alloy ............ OK
Coleta de logs .......... OK
Integração com Loki ..... OK
```