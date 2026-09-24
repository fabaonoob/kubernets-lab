# Longhorn

Última atualização: 24/09/2026

## Objetivo

Implementar armazenamento persistente para workloads Kubernetes utilizando Longhorn, com integração ao StorageClass padrão do cluster.

A arquitetura adotada para o laboratório utiliza apenas o worker `vuhwk` como nó de armazenamento.

> **Decisão de arquitetura:** o control-plane `vuhmt` não participa do armazenamento Longhorn e não recebe réplicas de volumes.

## Arquitetura

```text
Kubernetes Cluster

VUHMT
10.10.10.10
Control Plane
    │
    │
    └── Não utilizado pelo Longhorn

VUHWK
10.10.10.20
Worker + Storage
    │
    └── Longhorn
          │
          └── 1 réplica por volume
```

## Versão

```text
Longhorn: 1.12.0
Helm Chart: 1.12.0
```

## Namespace

```bash
kubectl create namespace longhorn-system
```

## Instalação

Repositório Helm:

```bash
helm repo add longhorn https://charts.longhorn.io
helm repo update
```

Instalação:

```bash
helm install longhorn longhorn/longhorn \
  --namespace longhorn-system \
  --create-namespace \
  --version 1.12.0 \
  -f longhorn-values.yaml
```

## Configuração

Arquivo utilizado:

```yaml
defaultSettings:
  defaultReplicaCount: 1
  taintToleration: ""
  systemManagedComponentsNodeSelector: "kubernetes.io/hostname:vuhwk"

longhornManager:
  nodeSelector:
    kubernetes.io/hostname: vuhwk

longhornDriver:
  nodeSelector:
    kubernetes.io/hostname: vuhwk

longhornUI:
  replicas: 1
  nodeSelector:
    kubernetes.io/hostname: vuhwk
```

### Observação importante

O parâmetro:

```yaml
systemManagedComponentsNodeSelector
```

utiliza `:` no valor do selector:

```text
kubernetes.io/hostname:vuhwk
```

e não:

```text
kubernetes.io/hostname=vuhwk
```

A configuração foi corrigida durante a implantação através de:

```bash
helm upgrade longhorn longhorn/longhorn \
  -n longhorn-system \
  --version 1.12.0 \
  --reuse-values \
  --set-string defaultSettings.systemManagedComponentsNodeSelector="kubernetes.io/hostname:vuhwk"
```

## Réplicas

O laboratório possui apenas um nó de armazenamento.

Por isso, o número padrão de réplicas foi configurado para `1`.

```bash
helm upgrade longhorn longhorn/longhorn \
  -n longhorn-system \
  --version 1.12.0 \
  --reuse-values \
  --set persistence.defaultClassReplicaCount=1
```

O StorageClass utiliza uma única réplica.

## StorageClass

StorageClasses existentes:

```text
longhorn
longhorn-static
```

O StorageClass `longhorn` é o padrão do cluster.

Características:

```text
Provisioner: driver.longhorn.io
ReclaimPolicy: Delete
VolumeBindingMode: Immediate
AllowVolumeExpansion: true
```

## Engine Image

Engine Image validada:

```text
ei-a4d05f02
docker.io/longhornio/longhorn-engine:v1.12.0
```

Estado:

```text
deployed
```

## Teste de persistência

Foi criado um PVC utilizando Longhorn.

O volume foi:

1. provisionado;
2. associado a um PV;
3. anexado ao worker;
4. utilizado por um Pod;
5. preenchido com dados;
6. o Pod foi removido;
7. um novo Pod foi criado;
8. os dados permaneceram disponíveis.

Isso validou o funcionamento básico de:

```text
PVC
 ↓
StorageClass
 ↓
Longhorn
 ↓
Volume
 ↓
Pod
```

## Nó de armazenamento

O nó utilizado pelo Longhorn é:

```text
vuhwk
```

Configuração do disco Longhorn:

```text
Path: /var/lib/longhorn/
Disk Type: filesystem
Schedulable: true
```

Foi utilizado disco adicional no worker para o armazenamento Longhorn.

## Validação

Comandos utilizados:

```bash
kubectl get pods -n longhorn-system -o wide
kubectl get nodes
kubectl get storageclass
kubectl get volumes.longhorn.io -n longhorn-system
```

Também foram verificadas as condições do nó Longhorn e o funcionamento do volume através de PVC/Pod.

## Limitação conhecida

Esta configuração **não fornece alta disponibilidade de armazenamento**.

Como existe somente um nó Longhorn e apenas uma réplica:

```text
Réplicas: 1
Nós de storage: 1
```

a indisponibilidade do `vuhwk` implica indisponibilidade dos volumes armazenados nele.

Essa decisão é intencional para o laboratório e reduz o consumo de recursos.

Em um ambiente produtivo seriam necessários múltiplos nós de armazenamento e múltiplas réplicas.

## Resultado

```text
Longhorn
    │
    ├── Instalado
    ├── StorageClass funcional
    ├── Storage persistente validado
    ├── Worker vuhwk configurado
    └── Persistência após recriação de Pod validada
```

**Status: ✅ Concluído**