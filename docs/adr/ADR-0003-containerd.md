# ADR-0003 - Escolha do containerd como Container Runtime

## Status

**Accepted**

## Data

24/07/2026

## Autor

Fábio Alves da Silva Santos

---

## Contexto

O Kubernetes necessita de um Container Runtime compatível com a Container Runtime Interface (CRI) para executar containers.

Após a descontinuação do Dockershim, tornou-se necessário selecionar um runtime moderno, estável e compatível com as versões atuais do Kubernetes.

---

## Problema

Definir qual Container Runtime será utilizado pelo cluster Kubernetes.

---

## Alternativas Consideradas

- containerd
- Docker Engine
- CRI-O

---

## Decisão

Utilizar o **containerd** como Container Runtime do cluster.

---

## Justificativa

A decisão foi baseada nos seguintes fatores:

- Runtime recomendado pela comunidade Kubernetes.
- Compatibilidade nativa com a Container Runtime Interface (CRI).
- Menor consumo de recursos quando comparado ao Docker Engine.
- Arquitetura simples e focada exclusivamente na execução de containers.
- Excelente estabilidade para ambientes de produção.
- Ampla adoção pela comunidade Cloud Native.

---

## Consequências

### Positivas

- Melhor desempenho.
- Menor consumo de memória.
- Compatibilidade com versões futuras do Kubernetes.
- Instalação simplificada.

### Negativas

- Ausência da interface de gerenciamento fornecida pelo Docker CLI.
- Necessidade de utilizar ferramentas específicas como `ctr`, `crictl` ou `nerdctl` para algumas operações administrativas.

---

## Referências

- Kubernetes Documentation
- containerd Documentation