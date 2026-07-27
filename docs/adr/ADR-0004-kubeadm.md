# ADR-0004 - Escolha do kubeadm para implantação do cluster

## Status

**Accepted**

## Data

24/07/2026

## Autor

Fábio Alves da Silva Santos

---

## Contexto

Existem diversas distribuições Kubernetes voltadas para laboratórios e ambientes simplificados, como k3s, MicroK8s e Minikube.

O objetivo deste projeto é compreender o funcionamento do Kubernetes em um ambiente próximo ao encontrado em empresas.

---

## Problema

Definir a ferramenta responsável pela implantação do cluster.

---

## Alternativas Consideradas

- kubeadm
- k3s
- MicroK8s
- Minikube

---

## Decisão

Utilizar o **kubeadm**.

---

## Justificativa

- Ferramenta oficial do Kubernetes.
- Processo de instalação semelhante ao utilizado em ambientes corporativos.
- Maior controle sobre os componentes do cluster.
- Excelente documentação.
- Facilita o aprendizado da arquitetura interna do Kubernetes.

---

## Consequências

### Positivas

- Aprendizado aprofundado.
- Ambiente semelhante ao encontrado em produção.
- Facilidade para troubleshooting.

### Negativas

- Processo de instalação mais complexo.
- Maior quantidade de configurações manuais.

---

## Referências

- Kubernetes Documentation