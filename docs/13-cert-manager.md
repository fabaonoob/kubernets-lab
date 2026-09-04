# 13 - Cert-Manager

## Objetivo

Instalar e configurar o Cert-Manager para automatizar o gerenciamento do ciclo de vida de certificados TLS no cluster Kubernetes.

O Cert-Manager será utilizado para emissão, renovação e gerenciamento de certificados utilizados pelo NGINX Ingress Controller e demais aplicações da plataforma.

---

## Motivação

O gerenciamento manual de certificados não é escalável e aumenta o risco operacional devido à expiração de certificados.

O Cert-Manager automatiza todo esse processo através de recursos nativos do Kubernetes.

---

## Arquitetura

```
                +----------------+
                | ClusterIssuer  |
                +-------+--------+
                        |
                        |
                +-------v--------+
                | Certificate    |
                +-------+--------+
                        |
                        |
                +-------v--------+
                | Secret TLS     |
                +-------+--------+
                        |
                        |
                +-------v--------+
                | Ingress NGINX  |
                +----------------+
```

---

## Repositório Helm

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update
```

---

## Instalação

```bash
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --set crds.enabled=true
```

---

## Validação

Pods:

```bash
kubectl get pods -n cert-manager
```

Resultado esperado:

```
cert-manager
cert-manager-cainjector
cert-manager-webhook
```

Todos em estado:

```
1/1 Running
```

---

## Verificação das CRDs

```bash
kubectl get crds | grep cert-manager
```

Exemplo:

```
certificates.cert-manager.io
clusterissuers.cert-manager.io
issuers.cert-manager.io
certificaterequests.cert-manager.io
orders.acme.cert-manager.io
challenges.acme.cert-manager.io
```

---

## Componentes Instalados

- Controller
- Webhook
- CA Injector
- Custom Resource Definitions (CRDs)

---

## Benefícios

- Renovação automática de certificados.
- Integração nativa com Kubernetes.
- Compatível com Let's Encrypt.
- Compatível com CA interna.
- Compatível com Vault PKI.
- Integração transparente com o NGINX Ingress.

---

## Próximos Passos

Nas próximas etapas serão configurados:

- ClusterIssuer para ambiente de laboratório.
- ClusterIssuer para produção.
- Integração com o NGINX Ingress.
- Emissão automática de certificados TLS para aplicações.