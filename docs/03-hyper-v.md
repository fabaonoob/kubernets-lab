# Hyper-V

## Objetivo

Preparar a camada de virtualização utilizada pelo Kubernetes Lab, fornecendo o ambiente necessário para criação e gerenciamento das máquinas virtuais que compõem a infraestrutura Kubernetes.

---

## Visão Geral

O laboratório utiliza o Hyper-V como plataforma de virtualização, aproveitando o recurso nativo disponível no Windows 10 Pro.

O Hyper-V será responsável por hospedar os nós do cluster Kubernetes, permitindo a criação de um ambiente controlado para testes, validações e desenvolvimento de soluções Cloud Native.

---

## Ambiente de Virtualização

A infraestrutura inicial é composta por:

- Host físico Windows 10 Pro.
- Hyper-V habilitado.
- Máquinas virtuais Ubuntu Server 24.04 LTS.
- Rede virtual dedicada para comunicação do cluster.

---

## Switches Virtuais

Foram criados switches virtuais para separar os diferentes tipos de comunicação do ambiente.

### WAN

Tipo:

External

Objetivo:

Permitir comunicação das máquinas virtuais com redes externas utilizando o adaptador físico disponível no host.

---

### k8s

Tipo:

Internal

Objetivo:

Criar uma rede privada dedicada para comunicação entre os nós Kubernetes.

Essa rede será utilizada para o tráfego interno do cluster.

---

## Máquinas Virtuais

O laboratório será composto inicialmente por dois nós:

### vuhmt

Função:

Control Plane Kubernetes

Responsabilidades:

- Gerenciamento do cluster.
- Execução dos componentes principais do Kubernetes.
- Controle do estado desejado da infraestrutura.

---

### vuhwk

Função:

Worker Node Kubernetes

Responsabilidades:

- Execução das aplicações.
- Processamento dos workloads.
- Expansão da capacidade computacional do cluster.

---

## Considerações

A utilização do Hyper-V permite simular uma arquitetura semelhante a ambientes corporativos, possibilitando validar conceitos de alta disponibilidade, escalabilidade, redes, containers e automação.