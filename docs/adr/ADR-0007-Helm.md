# ADR-0007 - Utilização do Helm como gerenciador de pacotes Kubernetes

## Status

Aceito

## Data

2026-07-27

## Contexto

O Kubernetes Lab possui como objetivo construir uma plataforma baseada em Kubernetes seguindo boas práticas utilizadas em ambientes corporativos.

Com a evolução do cluster, será necessário realizar a implantação e gerenciamento de diversos componentes de plataforma, incluindo:

- Ingress Controller;
- Métricas;
- Observabilidade;
- Banco de dados;
- Aplicações;
- Serviços auxiliares.

A instalação manual utilizando arquivos YAML individuais pode aumentar a complexidade operacional, dificultar atualizações e tornar o gerenciamento dos componentes mais trabalhoso.

Foram avaliadas alternativas para gerenciamento de aplicações Kubernetes, considerando simplicidade operacional, adoção pelo mercado e compatibilidade com o ecossistema Kubernetes.

## Decisão

Foi escolhido o Helm como gerenciador de pacotes para implantação e manutenção dos componentes Kubernetes.

O Helm será utilizado como ferramenta padrão para instalação, atualização e gerenciamento de aplicações distribuídas através de Charts.

## Justificativa

A escolha do Helm foi baseada nos seguintes critérios:

- ampla adoção pela comunidade Kubernetes;
- utilização em ambientes corporativos;
- integração nativa com o ecossistema Kubernetes;
- facilidade de instalação e atualização de componentes;
- organização padronizada através de Charts;
- possibilidade de versionamento das configurações;
- integração futura com processos GitOps.

O Helm permite que componentes complexos sejam implantados de forma padronizada, reduzindo a necessidade de manutenção manual de múltiplos manifests Kubernetes.

## Alternativas avaliadas

### Manifestos Kubernetes puros

**Não adotado.**

Embora seja uma abordagem nativa do Kubernetes, o gerenciamento manual de múltiplos arquivos YAML aumenta a complexidade conforme a plataforma cresce.

### Kustomize

**Não adotado como ferramenta principal.**

O Kustomize continua sendo uma ferramenta válida e poderá ser utilizado em cenários específicos, porém não será a estratégia principal para instalação de componentes de terceiros.

### Operadores Kubernetes

**Não adotado como substituto do Helm.**

Operadores serão utilizados quando necessários para aplicações específicas, porém não substituem um gerenciador geral de pacotes.

## Consequências positivas

- Padronização da implantação de componentes;
- Facilidade de atualização;
- Melhor organização da plataforma;
- Maior alinhamento com práticas Cloud Native;
- Preparação para integração com GitOps.

## Consequências negativas

- Adiciona uma camada adicional de conhecimento;
- Requer gerenciamento dos Charts utilizados;
- Exige cuidado com valores personalizados e versões.

## Resultado esperado

O Helm será utilizado como ferramenta padrão de implantação da plataforma Kubernetes Lab, servindo como base para instalação dos próximos componentes do ambiente.
