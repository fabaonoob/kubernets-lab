# Arquitetura da Infraestrutura

## Visão Geral

O Kubernetes Lab será executado sobre um ambiente virtualizado utilizando Hyper-V em um computador físico Windows.

A infraestrutura inicial será composta por máquinas virtuais Linux responsáveis por executar o cluster Kubernetes.

---

## Arquitetura Inicial

```mermaid
flowchart TB

HOST["Windows 10 Pro<br/>Host físico"]

HYPERV["Hyper-V"]

MASTER["vuhmt<br/>Ubuntu Server 24.04<br/>Control Plane<br/>10.10.10.10"]

WORKER["vuhwk<br/>Ubuntu Server 24.04<br/>Worker Node<br/>10.10.10.20"]

HOST --> HYPERV

HYPERV --> MASTER
HYPERV --> WORKER

Componentes
Camada de Virtualização
Hyper-V
Sistema Operacional
Ubuntu Server 24.04 LTS
Container Runtime
containerd
Orquestração
Kubernetes
Rede
Calico
Evolução planejada
A arquitetura será expandida com:
Helm
NGINX Ingress
MetalLB
Storage
Prometheus
Grafana
Argo CD
PostgreSQL
Redis
Aplicações Python
Serviços de Inteligência Artificial