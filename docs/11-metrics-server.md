# Metrics Server

## Objetivo

O Metrics Server fornece métricas de utilização de CPU e memória dos nós e Pods do cluster Kubernetes.

Essas métricas são utilizadas por diversos componentes do ecossistema Kubernetes, como:

- Horizontal Pod Autoscaler (HPA)
- Vertical Pod Autoscaler (VPA)
- kubectl top
- Dashboards (Grafana, Lens, OpenLens)
- Monitoramento operacional

Sem o Metrics Server o cluster continua funcionando normalmente, porém recursos de autoscaling e monitoramento ficam indisponíveis.

---

## Arquitetura

```
                kubectl top
                     │
                     ▼
             Metrics Server
                     │
             HTTPS (10250)
                     │
         ┌───────────┴───────────┐
         │                       │
      kubelet                kubelet
      vuhmt                  vuhwk
```

---

## Instalação

Adicionar o repositório oficial:

```bash
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm repo update
```

Instalação:

```bash
helm install metrics-server metrics-server/metrics-server \
  -n metrics-server \
  --create-namespace
```

---

## Problema encontrado

Após a instalação o Pod permaneceu:

```
READY 0/1
```

Logs:

```
tls: failed to verify certificate:
x509: cannot validate certificate because it doesn't contain any IP SANs
```

O kubelet foi configurado com certificados sem IP SAN, impedindo que o Metrics Server validasse o certificado TLS.

---

## Solução aplicada

Foi adicionada a opção:

```text
--kubelet-insecure-tls
```

no Deployment do Metrics Server.

Exemplo:

```yaml
args:
  - --cert-dir=/tmp
  - --secure-port=10250
  - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
  - --kubelet-insecure-tls
```

Após reiniciar o Deployment:

```bash
kubectl rollout restart deployment metrics-server -n metrics-server
```

o serviço passou a coletar métricas corretamente.

---

## Validação

Verificar Pod:

```bash
kubectl get pods -n metrics-server
```

Verificar APIService:

```bash
kubectl get apiservice | grep metrics
```

Resultado esperado:

```
True
```

Verificar métricas:

```bash
kubectl top nodes
```

```bash
kubectl top pods -A
```

---

## Considerações

A opção `--kubelet-insecure-tls` foi adotada devido às características do laboratório.

Em ambientes de produção recomenda-se utilizar certificados kubelet contendo Subject Alternative Names (SANs) adequados, permitindo validação TLS completa.