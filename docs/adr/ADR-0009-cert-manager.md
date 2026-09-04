# ADR-0008 – Adoção do Cert-Manager para Gerenciamento de Certificados

## Status

Aceito

---

## Contexto

A plataforma necessita de um mecanismo confiável para emissão, armazenamento e renovação automática de certificados TLS utilizados pelas aplicações expostas através do NGINX Ingress Controller.

O gerenciamento manual de certificados aumenta o risco operacional, principalmente devido à expiração de certificados e à necessidade de intervenção manual durante renovações.

Além disso, a arquitetura deverá ser compatível tanto com ambientes de laboratório quanto de produção.

---

## Decisão

Foi adotado o Cert-Manager como solução oficial para gerenciamento do ciclo de vida de certificados TLS da plataforma Kubernetes.

A instalação será realizada utilizando Helm, com as Custom Resource Definitions (CRDs) instaladas automaticamente.

Inicialmente o ambiente utilizará emissores apropriados ao laboratório. Em produção poderão ser utilizados emissores como:

- Let's Encrypt
- Autoridade Certificadora Interna (CA)
- HashiCorp Vault PKI

sem necessidade de alterações estruturais na plataforma.

---

## Alternativas Avaliadas

### Gerenciamento manual

**Vantagens**

- Simplicidade inicial.

**Desvantagens**

- Processo manual.
- Alto risco de expiração.
- Baixa escalabilidade.

---

### OpenSSL

**Vantagens**

- Controle total sobre os certificados.

**Desvantagens**

- Sem automação.
- Não integrado ao Kubernetes.

---

### HashiCorp Vault PKI

**Vantagens**

- Excelente integração corporativa.
- Alto nível de segurança.

**Desvantagens**

- Requer infraestrutura adicional.
- Maior complexidade operacional.

---

### Certificados fornecidos pelo provedor de nuvem

**Vantagens**

- Gerenciamento simplificado.

**Desvantagens**

- Dependência do provedor.
- Baixa portabilidade da plataforma.

---

## Consequências

### Positivas

- Renovação automática de certificados.
- Integração nativa com Kubernetes.
- Redução do risco operacional.
- Compatibilidade com diferentes autoridades certificadoras.
- Preparação para ambientes híbridos e multi-cloud.

### Negativas

- Introdução de um novo componente de infraestrutura.
- Necessidade de monitoramento dos recursos do Cert-Manager.

---

## Justificativa

O Cert-Manager tornou-se o padrão de mercado para gerenciamento automatizado de certificados em ambientes Kubernetes, oferecendo elevada integração com o ecossistema da plataforma e reduzindo significativamente o esforço operacional relacionado ao ciclo de vida de certificados TLS.