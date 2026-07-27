# Ubuntu Server

## Objetivo

Preparar o sistema operacional base utilizado pelos nós Kubernetes, garantindo que o ambiente esteja configurado para execução dos componentes necessários da plataforma.

---

## Sistema Operacional

O Kubernetes Lab utiliza:

- Ubuntu Server 24.04 LTS

A escolha do Ubuntu foi baseada na ampla utilização da distribuição em ambientes corporativos, Cloud Native e Kubernetes.

---

## Nós do Cluster

O ambiente será composto inicialmente por:

| Hostname | Função |
|---|---|
| vuhmt | Control Plane |
| vuhwk | Worker Node |

---

## Preparação do Sistema

Antes da instalação do Kubernetes foram realizadas configurações de preparação do sistema operacional:

- Atualização dos pacotes.
- Configuração de hostname.
- Configuração de acesso remoto.
- Preparação dos módulos necessários do kernel.
- Ajustes de parâmetros de rede.
- Desabilitação do uso de swap.

---

## Requisitos Kubernetes

Foram preparados os componentes necessários para execução do cluster:

- Configuração de encaminhamento de pacotes IPv4.
- Carregamento do módulo `br_netfilter`.
- Configuração dos parâmetros necessários para comunicação entre containers.

---

## Validação

Durante a preparação foram realizadas validações do ambiente:

- Identificação do hostname.
- Verificação do estado do swap.
- Validação dos módulos do kernel.
- Validação dos parâmetros de rede.

---

## Considerações

A preparação correta do sistema operacional é fundamental para garantir estabilidade dos componentes Kubernetes e evitar problemas durante a implantação do cluster.