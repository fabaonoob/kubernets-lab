# Kubernetes

## Objetivo

Implementar a plataforma de orquestração de containers responsável pelo gerenciamento dos workloads executados no Kubernetes Lab.

---

## Visão Geral

O Kubernetes foi escolhido como plataforma de gerenciamento do cluster devido à sua ampla utilização em ambientes Cloud Native, permitindo automatização, escalabilidade e gerenciamento de aplicações baseadas em containers.

A implantação inicial do laboratório foi realizada utilizando kubeadm, preparando a base da infraestrutura para evolução futura com múltiplos nós.

---

## Arquitetura Inicial

A primeira etapa do cluster foi construída utilizando um nó responsável pelo Control Plane.

| Host | Função | Endereço IP |
|---|---|---|
| vuhmt | Control Plane | 10.10.10.10 |

---

## Componentes Instalados

Foram instalados os componentes principais do Kubernetes:

- kubeadm
- kubelet
- kubectl

---

## Implantação

O cluster Kubernetes foi inicializado utilizando o kubeadm.

A preparação contemplou:

- Instalação dos componentes Kubernetes.
- Inicialização do Control Plane.
- Configuração do acesso administrativo através do kubectl.
- Validação dos componentes principais do cluster.

---

## Configuração Kubernetes

Após a inicialização do cluster, foram configurados os recursos necessários para administração do ambiente.

O kubeconfig foi configurado para permitir o gerenciamento do cluster através do kubectl.

---

## Validação

A versão do kubeadm foi validada utilizando:

```bash
kubeadm version
```

O acesso ao cluster foi validado utilizando:

```bash
kubectl version --client
```

O estado inicial do nó foi verificado utilizando:

```bash
kubectl get nodes
```

---

## Estado Inicial

Após a inicialização do cluster, o nó Control Plane apresentou o estado:

```text
NotReady
```

O motivo identificado foi a ausência de uma solução de rede CNI (Container Network Interface), necessária para comunicação entre os Pods do cluster.

---

## Considerações

A implantação do Kubernetes estabeleceu a base de gerenciamento do cluster, permitindo a evolução da infraestrutura com novos nós, componentes de rede, armazenamento, observabilidade e aplicações.

A próxima etapa consiste na implantação da camada de rede utilizando o Calico.