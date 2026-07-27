# Ingress NGINX

## Objetivo

Instalar e configurar o Ingress NGINX como controlador de entrada HTTP/HTTPS do cluster Kubernetes.

O Ingress Controller é responsável por receber as requisições externas e encaminhá-las para os Services internos do cluster.

---

## Motivação

O Kubernetes não publica aplicações HTTP nativamente.

O Ingress NGINX fornece:

- Balanceamento de carga HTTP/HTTPS
- Virtual Hosts
- TLS
- Path Based Routing
- Reescrita de URLs
- Integração com Cert-Manager
- Compatibilidade com praticamente todo o ecossistema Kubernetes

---

## Pré-requisitos

- Kubernetes operacional
- Calico instalado
- Helm instalado
- Metrics Server funcional

---

## Repositório Helm

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

helm repo update
```

---

## values.yaml

Localização no repositório:

```
infra/helm-values/ingress-nginx/values.yaml
```

Conteúdo:

```yaml
controller:
  replicaCount: 2

  ingressClassResource:
    enabled: true
    default: true
    name: nginx

  service:
    type: NodePort

  metrics:
    enabled: true

  admissionWebhooks:
    enabled: true

defaultBackend:
  enabled: true
```

---

## Instalação

```bash
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --create-namespace \
  -f values.yaml
```

---

## Validação

Pods

```bash
kubectl get pods -n ingress-nginx
```

Services

```bash
kubectl get svc -n ingress-nginx
```

IngressClass

```bash
kubectl get ingressclass
```

---

## Teste funcional

Deployment de teste:

- Deployment
- Service
- Ingress

Aplicação utilizada:

```
registry.k8s.io/echoserver:1.10
```

Ingress:

```
echo.lab.local
```

Teste realizado:

```bash
curl -H "Host: echo.lab.local" http://10.10.10.20:31619
```

Resultado:

- Ingress respondeu corretamente
- Service acessível
- Pod respondeu
- Cabeçalhos X-Forwarded-* adicionados
- Roteamento validado

---

## Arquitetura

```
Cliente
   │
   ▼
NodePort
   │
   ▼
Ingress NGINX
   │
   ▼
Service
   │
   ▼
Pod
```

Após a instalação do MetalLB:

```
Cliente
   │
   ▼
LoadBalancer
   │
   ▼
Ingress NGINX
   │
   ▼
Service
   │
   ▼
Pod
```

---

## Troubleshooting

Verificar Pods

```bash
kubectl get pods -n ingress-nginx
```

Logs

```bash
kubectl logs -n ingress-nginx <pod>
```

Ingress

```bash
kubectl describe ingress
```

Eventos

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

## Resultado

O Ingress NGINX foi instalado com sucesso.

Foram validados:

- Publicação HTTP
- Roteamento
- Service Discovery
- Balanceamento
- Comunicação entre componentes