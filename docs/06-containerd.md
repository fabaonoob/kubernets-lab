# Containerd

## Objetivo

Implementar o runtime de containers utilizado pelo Kubernetes Lab, responsável pela execução dos containers dentro do cluster Kubernetes.

---

## Visão Geral

O Kubernetes necessita de um runtime de containers para gerenciar a execução dos workloads.

Neste laboratório foi utilizado o containerd como runtime padrão, fornecendo a camada responsável pela comunicação entre o Kubernetes e os containers executados no ambiente.

---

## Decisão Arquitetural

O containerd foi escolhido devido aos seguintes fatores:

- Compatibilidade com Kubernetes.
- Utilização ampla em ambientes Cloud Native.
- Arquitetura leve e focada em execução de containers.
- Adoção em ambientes corporativos.

---

## Implantação

O containerd foi instalado como componente base dos nós Kubernetes.

A preparação do ambiente contemplou:

- Instalação do runtime.
- Configuração do serviço.
- Ajustes necessários para integração com Kubernetes.
- Validação do funcionamento do runtime.

---

## Configuração Kubernetes

O runtime foi preparado para atender aos requisitos do Kubernetes, permitindo que o kubelet utilize o containerd para criação, execução e gerenciamento dos containers dentro do cluster.

---

## Validação

A instalação foi validada utilizando o comando:

```bash
containerd --version
```

Resultado obtido:
containerd containerd v2.2.6

---

## Considerações

A utilização do containerd mantém o Kubernetes Lab alinhado às arquiteturas modernas de infraestrutura baseada em containers.

O runtime será utilizado como camada de execução para os serviços que serão implantados futuramente no cluster, incluindo aplicações, bancos de dados, automações e soluções de inteligência artificial.