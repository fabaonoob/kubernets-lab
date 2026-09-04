# ADR-0008 — Adoção do Longhorn como solução de armazenamento distribuído

## Status

Aceito

---

## Contexto

A plataforma necessita de uma solução de armazenamento persistente para workloads stateful executados no Kubernetes.

Os principais requisitos são:

- Alta disponibilidade
- Fácil administração
- Open Source
- Integração nativa com Kubernetes
- Snapshots
- Backups
- Replicação de volumes
- Crescimento futuro do cluster

---

## Decisão

Foi adotado o Longhorn como plataforma oficial de armazenamento distribuído.

---

## Alternativas avaliadas

### Local Path Provisioner

Prós

- Simples

Contras

- Sem alta disponibilidade
- Sem replicação
- Sem snapshots

---

### NFS

Prós

- Fácil implantação

Contras

- Ponto único de falha
- Baixa escalabilidade
- Dependência de servidor externo

---

### Ceph (Rook)

Prós

- Extremamente robusto
- Excelente desempenho

Contras

- Alta complexidade operacional
- Excesso de recursos para o tamanho atual da plataforma

---

### Longhorn

Prós

- Instalação simples
- Replicação automática
- Snapshots
- Backups S3
- Interface Web
- Recuperação automática
- Excelente integração com Kubernetes

Contras

- Dependência do Open-iSCSI em todos os nós
- Consumo adicional de recursos para gerenciamento dos volumes

---

## Consequências

Positivas

- Armazenamento altamente disponível
- Maior resiliência do cluster
- Recuperação simplificada
- Melhor experiência operacional

Negativas

- Necessidade de instalar Open-iSCSI em todos os nós
- Maior consumo de CPU, memória e armazenamento para suportar a camada de storage

---

## Referências

- https://longhorn.io/
- https://github.com/longhorn/longhorn