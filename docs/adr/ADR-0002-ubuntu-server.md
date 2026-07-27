# ADR-0002 - Escolha do Ubuntu Server

## Status

Aceito

## Contexto

O cluster Kubernetes necessita de um sistema operacional Linux amplamente utilizado e compatível com os principais componentes do ecossistema Cloud Native.

## Problema

Selecionar a distribuição Linux para os nós do cluster.

## Alternativas Consideradas

- Ubuntu Server
- Debian
- Rocky Linux
- AlmaLinux

## Decisão

Utilizar Ubuntu Server 24.04 LTS.

## Justificativa

- Versão LTS com suporte estendido.
- Grande adoção pela comunidade Kubernetes.
- Documentação abundante.
- Compatibilidade com ferramentas do ecossistema Cloud Native.
- Facilidade de manutenção.

## Consequências

### Positivas

- Atualizações de longo prazo.
- Excelente compatibilidade com Kubernetes e containerd.
- Grande quantidade de material de apoio.

### Negativas

- Ciclo de atualizações próprio da Canonical.

## Referências

- Documentação oficial do Ubuntu Server.