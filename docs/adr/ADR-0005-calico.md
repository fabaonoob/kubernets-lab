# ADR-0005 - Escolha do Calico como CNI

## Status

**Accepted**

## Data

24/07/2026

## Autor

Fábio Alves da Silva Santos

---

## Contexto

O Kubernetes necessita de uma implementação da Container Network Interface (CNI) para permitir a comunicação entre Pods e Serviços.

---

## Problema

Selecionar a solução de rede para o cluster.

---

## Alternativas Consideradas

- Calico
- Flannel
- Cilium

---

## Decisão

Utilizar o **Calico**.

---

## Justificativa

- Ampla adoção em ambientes corporativos.
- Suporte completo a Network Policies.
- Excelente documentação.
- Facilidade de implantação com kubeadm.
- Escalabilidade para clusters maiores.

---

## Consequências

### Positivas

- Implementação de políticas de segurança entre Pods.
- Excelente integração com Kubernetes.
- Plataforma preparada para crescimento do ambiente.

### Negativas

- Configuração mais complexa do que soluções como Flannel.
- Maior número de componentes em execução.

---

## Referências

- Kubernetes Documentation
- Project Calico Documentation