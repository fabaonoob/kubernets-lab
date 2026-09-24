# GitOps com Argo CD

## 1. Objetivo

Implementar GitOps no Kubernetes Lab utilizando GitHub como fonte de configuração e Argo CD como mecanismo de sincronização com o cluster.

Arquitetura:

```text
GitHub
   ↓
Argo CD
   ↓
Kubernetes
   ↓
Applications
```

O objetivo desta etapa foi validar o fluxo básico de entrega declarativa utilizando um repositório Git.

---

## 2. Argo CD

Versão utilizada:

```text
v3.4.5
```

Namespace:

```text
argocd
```

O Argo CD foi instalado utilizando Helm.

O ambiente possui os componentes principais do Argo CD:

```text
argocd-server
argocd-repo-server
argocd-application-controller
argocd-applicationset-controller
argocd-dex-server
argocd-redis
argocd-notifications-controller
```

Os componentes foram executados no cluster Kubernetes.

---

## 3. Repositório Git

O repositório utilizado pelo laboratório é:

```text
https://github.com/fabaonoob/kubernets-lab.git
```

Branch principal:

```text
main
```

O GitHub é utilizado como fonte versionada dos manifests e da documentação do projeto.

---

## 4. Estrutura do teste

Foi criado um diretório específico para validar o funcionamento do GitOps:

```text
apps/
└── gitops-test/
    ├── namespace.yaml
    ├── deployment.yaml
    └── service.yaml
```

O teste utiliza uma aplicação simples para validar o ciclo completo entre GitHub, Argo CD e Kubernetes.

---

## 5. Application

Foi criada uma Application no Argo CD:

```text
gitops-test
```

Manifesto utilizado:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: gitops-test
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/fabaonoob/kubernets-lab.git
    targetRevision: main
    path: apps/gitops-test

  destination:
    server: https://kubernetes.default.svc
    namespace: gitops-test

  syncPolicy:
    automated:
      prune: true
      selfHeal: true

    syncOptions:
      - CreateNamespace=true
```

---

## 6. Configuração GitOps

A Application aponta para:

```text
Repository:
https://github.com/fabaonoob/kubernets-lab.git

Branch:
main

Path:
apps/gitops-test
```

Destino:

```text
Kubernetes cluster
```

Namespace:

```text
gitops-test
```

O namespace é criado automaticamente através de:

```yaml
syncOptions:
  - CreateNamespace=true
```

---

## 7. Sincronização automática

A Application foi configurada com:

```yaml
automated:
  prune: true
  selfHeal: true
```

### Prune

Permite que recursos removidos do Git sejam removidos do cluster durante a sincronização.

### Self Heal

Permite que alterações manuais no cluster sejam reconciliadas de acordo com o estado definido no Git.

---

## 8. Validação

A Application foi criada e sincronizada pelo Argo CD.

Estado validado:

```text
Application:
gitops-test

Sync:
Synced

Health:
Healthy
```

A aplicação foi criada no namespace:

```text
gitops-test
```

Resultado:

```text
GitHub → Argo CD → Kubernetes
```

```text
PASS
```

---

## 9. Resultado

O teste confirmou o funcionamento básico do modelo GitOps no laboratório.

O GitHub representa o estado desejado dos manifests.

O Argo CD monitora o repositório e realiza a reconciliação com o cluster Kubernetes.

Arquitetura validada:

```text
                 GitHub
                   │
                   │ manifests
                   ▼
                Argo CD
                   │
                   │ sync
                   ▼
              Kubernetes
                   │
                   ▼
             gitops-test
```

---

## 10. Próxima evolução

O teste `gitops-test` representa apenas a validação da plataforma GitOps.

As aplicações reais do projeto ainda não foram implantadas através do Argo CD.

Próximas aplicações planejadas:

```text
PostgreSQL
Redis
n8n
API Python
Chatbot
```

Essas aplicações serão abordadas em uma etapa posterior.

---

## 11. Resultado final

```text
Argo CD ................ OK
GitHub .................. OK
Repository sync ......... OK
Application ............. OK
Automated sync .......... OK
Self Heal ............... OK
Prune ................... OK
```

GitOps básico validado com sucesso.