# ADR-0007 — Padronização do Ingress Controller com Ingress NGINX

## Status

Aceito

---

## Contexto

O cluster Kubernetes necessita de um controlador de Ingress para expor aplicações HTTP e HTTPS.

Foram consideradas as seguintes alternativas:

- Ingress NGINX
- Traefik
- HAProxy Ingress
- Kong

---

## Decisão

Adotar o **Ingress NGINX** como controlador padrão do cluster.

---

## Justificativa

### Ampla adoção

É o controlador de Ingress mais utilizado na comunidade Kubernetes.

### Estabilidade

Projeto maduro, amplamente documentado e utilizado em ambientes corporativos.

### Compatibilidade

Compatível com:

- Cert-Manager
- Prometheus
- Grafana
- ArgoCD
- MetalLB

### Facilidade operacional

- Instalação simples via Helm
- Grande quantidade de exemplos e documentação
- Facilidade de troubleshooting

### Recursos disponíveis

- TLS
- Path Based Routing
- Host Based Routing
- Rewrites
- Rate Limit
- Autenticação
- Métricas Prometheus

---

## Consequências

### Positivas

- Grande comunidade
- Excelente documentação
- Facilidade de manutenção
- Integração com o ecossistema Kubernetes
- Compatível com futuras evoluções do laboratório

### Negativas

- Consumo de recursos superior a soluções mais leves.
- Necessidade de um LoadBalancer (MetalLB em ambientes on-premises) para exposição simplificada.

---

## Alternativas avaliadas

### Traefik

Excelente integração e configuração simplificada.

Não foi escolhido por priorizarmos maior aderência ao ecossistema corporativo Kubernetes.

### HAProxy Ingress

Alto desempenho.

Menor adoção e comunidade.

### Kong

Muito poderoso para API Gateway.

Introduziria complexidade desnecessária para os objetivos atuais do laboratório.

---

## Resultado esperado

Disponibilizar um controlador de Ingress robusto, amplamente suportado e preparado para integração com Cert-Manager, MetalLB e GitOps.