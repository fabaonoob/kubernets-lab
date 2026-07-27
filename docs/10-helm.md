# Helm

## Objetivo

O Helm foi implantado no Kubernetes Lab como ferramenta de gerenciamento de pacotes Kubernetes.

O objetivo é padronizar a instalação, configuração e manutenção dos componentes da plataforma, permitindo administrar aplicações através de Charts.

---

## Implantação

O Helm foi instalado utilizando o método oficial disponibilizado pelo projeto Helm.

Após a instalação, a ferramenta foi validada através da consulta de versão:

```bash
helm version
```

Resultado obtido:

```text
version.BuildInfo{Version:"v3.21.3"}
```

---

## Configuração

Inicialmente foi realizada uma tentativa de instalação utilizando repositório APT.

Durante a validação foi identificado problema relacionado à chave GPG do repositório:

```text
NO_PUBKEY 4B196BE9C4313D06
```

O repositório foi removido e a instalação foi mantida utilizando o instalador oficial do projeto Helm.

Após a instalação, foram configurados os repositórios Helm necessários para futuras implantações.

---

## Validação

A comunicação do Helm com o ambiente Kubernetes foi validada utilizando:

```bash
helm list -A
```

O comando executou corretamente, confirmando a integração da ferramenta com o ambiente.

---

## Resultado Final

O Kubernetes Lab possui o Helm instalado e operacional.

A ferramenta será utilizada para implantação dos próximos componentes da plataforma, incluindo:

- Metrics Server;
- NGINX Ingress Controller;
- Prometheus;
- Grafana;
- Loki;
- Argo CD;
- Aplicações futuras.

---

## Considerações

A adoção do Helm mantém o Kubernetes Lab alinhado às práticas modernas de gerenciamento Kubernetes.

A ferramenta será utilizada como camada de implantação da plataforma, permitindo maior organização, padronização e evolução do ambiente.