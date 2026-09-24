# Status do Projeto

Última atualização: 24/07/2026

## Infraestrutura

| Componente | Status |
|------------|:------:|
| Hyper-V | ✅ |
| Ubuntu Server | ✅ |
| Rede | ✅ |
| SSH | ✅ |
| Containerd | ✅ |
| Kubernetes | ✅ |
| Calico | ✅ |
| Worker Node | ✅ |

## Plataforma

| Componente | Status |
|------------|:------:|
| Helm | ✅ |
| Metrics Server | ✅ |
| NGINX Ingress | ✅ |
| Cert-Manager | ✅ |
| Longhorn | ✅ |
| MetalLB | ✅ |

## Observabilidade

| Componente | Status |
|------------|:------:|
| Prometheus | ✅ |
| Grafana | ✅ |
| Loki | ✅ |

## GitOps

| Componente | Status |
|------------|:------:|
| Argo CD | ✅ |

## Aplicações

| Aplicação | Status |
|-----------|:------:|
| PostgreSQL | 🔄 |
| Redis | ⏳ |
| n8n | ⏳ |
| API Python | ⏳ |

Arquitetura atual

                         Windows Host
                              │
                              │ Hyper-V
                              ▼
                    ┌─────────────────────┐
                    │ Kubernetes Cluster  │
                    │    10.10.10.0/24   │
                    └──────────┬──────────┘
                               │
              ┌────────────────┴────────────────┐
              │                                 │
              ▼                                 ▼
      ┌───────────────┐                 ┌───────────────┐
      │     VUHMT     │                 │     VUHWK     │
      │ 10.10.10.10   │                 │ 10.10.10.20   │
      │ Control Plane │                 │    Worker     │
      └───────────────┘                 │    Storage    │
                                        └───────────────┘
              │                                 │
              └──────────── Kubernetes ─────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
     Calico                MetalLB              Longhorn
                              │                     │
                     10.10.10.100-120         Storage
                                                    │
        ┌───────────────────────────────────────────┤
        │                                           │
        ▼                                           ▼
   Prometheus                                   Grafana
        │                                           │
        │ metrics                                   │
        └───────────────────────────────────────────┘

        Logs:

   Kubernetes Pods
          │
          ▼
        Alloy
          │
          ▼
         Loki
          │
          ▼
       Grafana

Componentes concluídos

Até o momento foram concluídos e validados:

Kubernetes
Calico
Metrics Server
NGINX Ingress
Cert-Manager
Longhorn
MetalLB
Prometheus
Grafana
Loki
Alloy

Observabilidade

O laboratório possui atualmente dois fluxos de observabilidade.

Métricas

Kubernetes
    │
    ▼
Prometheus
    │
    ▼
Grafana

O dashboard Kubernetes Lab apresenta:

CPU por nó
Memória por nó
Quantidade de nós
Total de Pods
Pods por namespace

Logs

Kubernetes Pods
    │
    ▼
Alloy
    │
    ▼
Loki
    │
    ▼
Grafana

A coleta de logs foi validada através da API do Loki.

Próximas etapas

GitOps

Argo CD

Aplicações

PostgreSQL
Redis
n8n
API Python

Integração

Após a implantação das aplicações:

Ingress
TLS
DNS/hosts
Persistência
Secrets
Observabilidade
GitOps

deverão ser validados de ponta a ponta.

Critério para encerramento do laboratório

Antes de destruir o ambiente local, o projeto deverá possuir:

infraestrutura documentada;

componentes Kubernetes documentados;

manifests/Helm values versionados;

observabilidade funcional;

GitOps funcional;

aplicações funcionais;

persistência validada;

ingress e TLS validados;

ausência de secrets reais no repositório;

procedimento de reconstrução documentado;

repositório GitHub atualizado.

Somente após essa validação será considerada a reconstrução completa do ambiente a partir do GitHub.

Status geral

Infraestrutura       ████████████████████ 100%
Plataforma            ████████████████████ 100%
Observabilidade       ████████████████████ 100%
GitOps                ████░░░░░░░░░░░░░░░░  20%
Aplicações            ░░░░░░░░░░░░░░░░░░░░   0%

Estado atual: Plataforma e Observabilidade concluídas. GitOps e aplicações pendentes.