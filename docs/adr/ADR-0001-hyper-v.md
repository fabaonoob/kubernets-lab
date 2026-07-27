# ADR-0001 - Escolha do Hyper-V

## Status

Aceito

## Contexto

O Kubernetes Lab será executado em um computador com Windows 10 Pro, exigindo uma solução de virtualização estável para hospedar as máquinas virtuais do cluster.

## Problema

Definir a plataforma de virtualização que servirá como base para o ambiente.

## Alternativas Consideradas

- Hyper-V
- VMware Workstation
- Oracle VirtualBox
- Proxmox VE

## Decisão

Utilizar o Hyper-V como plataforma de virtualização.

## Justificativa

- Integrado ao Windows 10 Pro.
- Sem necessidade de licenciamento adicional.
- Boa integração com o sistema operacional hospedeiro.
- Desempenho adequado para ambientes de laboratório.
- Ampla documentação e suporte da Microsoft.

## Consequências

### Positivas

- Administração simplificada.
- Excelente compatibilidade com Ubuntu Server.
- Fácil criação de switches virtuais.

### Negativas

- Recursos avançados inferiores aos de plataformas dedicadas, como Proxmox VE.
- Dependência do Windows como sistema operacional hospedeiro.

## Referências

- Documentação oficial do Hyper-V.