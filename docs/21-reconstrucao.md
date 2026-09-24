# Reconstrução do Kubernetes Lab

## 1. Objetivo

Este documento consolida o estado atual do Kubernetes Lab e serve como referência para uma futura reconstrução do ambiente.

O laboratório atual foi construído em Hyper-V e permanece operacional.

A reconstrução futura deverá utilizar a documentação deste repositório como referência.

---

## 2. Estado atual

O ambiente possui dois nós Kubernetes:

| Hostname | Função | IP |
|---|---|---|
| VUHMT | Control Plane | 10.10.10.10 |
| VUHWK | Worker | 10.10.10.20 |

Rede:

```text
10.10.10.0/24
```

Host/gateway:

```text
10.10.10.1
```

Kubernetes:

```text
v1.35.7
```

Container runtime:

```text
containerd 2.2.6
```

---

## 3. Componentes instalados

A plataforma atual contém:

```text
Kubernetes
Calico
Helm
Metrics Server
Ingress NGINX
cert-manager
Longhorn
MetalLB
Prometheus
Grafana
Loki
Grafana Alloy
Argo CD
```

---

## 4. Arquitetura

```text
                    ┌─────────────────────┐
                    │      GitHub         │
                    │  kubernets-lab      │
                    └──────────┬──────────┘
                               │
                               │ GitOps
                               ▼
                    ┌─────────────────────┐
                    │      Argo CD        │
                    └──────────┬──────────┘
                               │
                               ▼
             ┌────────────────────────────────┐
             │        Kubernetes Cluster      │
             │                                │
             │  VUHMT          VUHWK         │
             │ Control Plane    Worker        │
             │                                │
             │  ┌──────────┐  ┌───────────┐  │
             │  │ Calico   │  │ Longhorn  │  │
             │  └──────────┘  └───────────┘  │
             │                                │
             │  ┌──────────┐  ┌───────────┐  │
             │  │ MetalLB  │  │ Ingress   │  │
             │  └──────────┘  └───────────┘  │
             │                                │
             │  ┌──────────────────────────┐  │
             │  │      Observabilidade      │  │
             │  │                          │  │
             │  │ Prometheus               │  │
             │  │ Grafana                  │  │
             │  │ Loki                     │  │
             │  │ Alloy                    │  │
             │  └──────────────────────────┘  │
             └────────────────────────────────┘
```

---

## 5. Storage

Longhorn:

```text
Version: 1.12.0
```

Storage node:

```text
VUHWK
```

Replicas:

```text
1
```

O `VUHMT` não participa do storage.

Essa configuração foi necessária devido à quantidade de workers disponíveis no laboratório.

### Limitação

O ambiente não possui alta disponibilidade de storage.

Uma falha do `VUHWK` representa indisponibilidade do armazenamento Longhorn.

---

## 6. MetalLB

MetalLB:

```text
Version: 0.16.1
```

Pool:

```text
10.10.10.100-10.10.10.120
```

Modo:

```text
Layer 2
```

O funcionamento foi validado através de um Service `LoadBalancer` utilizando NGINX.

---

## 7. Observabilidade

Stack implementada:

```text
Prometheus
Grafana
Loki
Grafana Alloy
```

Fluxo de métricas:

```text
Kubernetes
    ↓
Prometheus
    ↓
Grafana
```

Fluxo de logs:

```text
Kubernetes Pods
    ↓
Alloy
    ↓
Loki
    ↓
Grafana
```

As duas cadeias foram validadas.

---

## 8. GitOps

Argo CD:

```text
v3.4.5
```

Repository:

```text
https://github.com/fabaonoob/kubernets-lab.git
```

Branch:

```text
main
```

Application de teste:

```text
gitops-test
```

Estado validado:

```text
Synced
Healthy
```

O fluxo GitHub → Argo CD → Kubernetes foi validado.

---

## 9. Ordem de reconstrução

Uma reconstrução completa deve seguir aproximadamente a seguinte ordem:

```text
1. Infraestrutura
        ↓
2. Rede
        ↓
3. Ubuntu Server
        ↓
4. containerd
        ↓
5. Kubernetes / kubeadm
        ↓
6. Calico
        ↓
7. Helm
        ↓
8. Metrics Server
        ↓
9. Ingress NGINX
        ↓
10. cert-manager
        ↓
11. Longhorn
        ↓
12. MetalLB
        ↓
13. Prometheus
        ↓
14. Grafana
        ↓
15. Loki
        ↓
16. Alloy
        ↓
17. Argo CD
        ↓
18. Applications
```

A ordem representa as dependências lógicas entre os componentes.

---

## 10. Componentes ainda não implantados

As aplicações de negócio ainda não fazem parte do ambiente validado:

```text
PostgreSQL
Redis
n8n
API Python
Chatbot
```

Esses componentes serão implementados posteriormente.

---

## 11. Situação da infraestrutura

O laboratório atual continua sendo executado em Hyper-V.

A infraestrutura **não deve ser destruída neste momento**.

O ambiente será mantido como referência para:

- testes;
- aprendizado;
- comparação com a infraestrutura cloud;
- validação de futuras automações;
- comparação entre configuração manual e Infrastructure as Code.

---

## 12. Próxima fase — Cloud

A próxima evolução planejada é levar a arquitetura para uma infraestrutura cloud.

Modelo pretendido:

```text
                         GitHub
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         ▼
         Terraform                  Argo CD
              │                         │
              ▼                         │
      Cloud Infrastructure              │
              │                         │
              ▼                         │
         Kubernetes ◄───────────────────┘
              │
              ▼
         Applications
```

### Responsabilidades

**GitHub**

- versionamento;
- documentação;
- manifests;
- código;
- configuração.

**Terraform**

- infraestrutura cloud;
- rede;
- máquinas;
- discos;
- regras de acesso;
- recursos necessários para o cluster.

**Kubernetes**

- orquestração dos workloads.

**Argo CD**

- GitOps;
- sincronização;
- reconciliação;
- entrega das aplicações.

---

## 13. Princípio de evolução

O projeto evolui de:

```text
Infraestrutura manual
```

para:

```text
Infrastructure as Code
```

e posteriormente:

```text
Infrastructure as Code
        +
Kubernetes
        +
GitOps
        +
Cloud
```

O laboratório Hyper-V continuará disponível durante essa evolução.

---

## 14. Critério para desativação do laboratório

O laboratório local somente deverá ser desativado após:

- infraestrutura cloud criada;
- Kubernetes funcional;
- componentes essenciais reproduzidos;
- GitOps validado;
- documentação atualizada;
- testes executados;
- procedimento de reconstrução registrado no Git.

Até que esses critérios sejam atendidos, o ambiente Hyper-V permanece como referência.

---

## 15. Estado da Fase 1

```text
Plataforma Kubernetes ........ OK
Storage ...................... OK
Networking ................... OK
Load Balancer ................ OK
Observabilidade .............. OK
GitOps ....................... OK
Aplicações de negócio ........ PENDENTE
Cloud / Terraform ............ PENDENTE
```

A Fase 1 do Kubernetes Lab está concluída.