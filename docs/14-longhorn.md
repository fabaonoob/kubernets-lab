# 12 - Longhorn

## Objetivo

Instalar e validar o Longhorn como solução de armazenamento persistente do cluster Kubernetes.

Nesta primeira etapa do laboratório, o Longhorn será utilizado com **apenas o worker `vuhwk` como nó de armazenamento**, mantendo o control-plane `vuhmt` fora do storage.

A configuração atual utiliza **1 réplica por volume**, pois existe somente um nó de storage disponível.

> Esta configuração é adequada para validação funcional, mas não fornece alta disponibilidade de storage.

---

## Motivo da escolha

O Longhorn foi escolhido por apresentar:

- Open Source
- Desenvolvido pela SUSE
- Instalação via Helm
- Interface Web integrada
- Snapshots
- Backups para S3
- Replicação entre nós
- Expansão online de volumes
- Recuperação de volumes
- Integração nativa com Kubernetes
- Provisionamento dinâmico através de StorageClass

---

## Pré-requisitos

Todos os nós que utilizarão volumes Longhorn devem possuir os componentes necessários para o funcionamento do storage.

No Ubuntu:

```bash
sudo apt update
sudo apt install open-iscsi -y
```

Habilitar o serviço:

```bash
sudo systemctl enable --now iscsid
```

Validar:

```bash
systemctl status iscsid
```

O serviço deve estar ativo.

---

# Arquitetura do laboratório

Atualmente o cluster possui:

| Nó | Função | Longhorn |
|---|---|---|
| `vuhmt` | Control Plane | Não |
| `vuhwk` | Worker | Sim |

Arquitetura:

```text
                    Kubernetes Cluster
                           |
              +------------+------------+
              |                         |
          vuhmt                       vuhwk
      Control Plane                  Worker
              |                         |
              |                    Longhorn
              |                         |
              |                 /var/lib/longhorn
              |                         |
              +------------+------------+
                           |
                       PVC / PV
```

O `vuhmt` não participa do armazenamento Longhorn.

---

# Instalação via Helm

Adicionar o repositório:

```bash
helm repo add longhorn https://charts.longhorn.io
helm repo update
```

Verificar o chart:

```bash
helm search repo longhorn/longhorn
```

Versão utilizada no laboratório:

```text
1.12.0
```

---

# Configuração

Arquivo utilizado:

```text
longhorn-values.yaml
```

Conteúdo:

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

### Observação

O `systemManagedComponentsNodeSelector` utiliza o formato:

```text
kubernetes.io/hostname:vuhwk
```

e não:

```text
kubernetes.io/hostname=vuhwk
```

O Longhorn 1.12 valida esse parâmetro utilizando `:` como separador.

---

# Instalação

```bash
helm install longhorn longhorn/longhorn \
  --namespace longhorn-system \
  --create-namespace \
  --version 1.12.0 \
  -f longhorn-values.yaml
```

Validar:

```bash
helm list -n longhorn-system
```

---

# StorageClass

O Longhorn cria automaticamente a StorageClass:

```text
longhorn
```

Por padrão, o Longhorn utiliza três réplicas. Como o laboratório possui somente um nó de storage, a StorageClass foi configurada para utilizar uma única réplica.

Foi aplicado:

```bash
helm upgrade longhorn longhorn/longhorn \
  -n longhorn-system \
  --version 1.12.0 \
  --reuse-values \
  --set persistence.defaultClassReplicaCount=1
```

Validar:

```bash
kubectl get storageclass longhorn -o yaml
```

O parâmetro esperado é:

```yaml
numberOfReplicas: "1"
```

### Importante

Existem duas configurações diferentes:

```text
defaultSettings.defaultReplicaCount
```

e:

```text
persistence.defaultClassReplicaCount
```

Para volumes criados através de PVC Kubernetes, a configuração da StorageClass é a relevante.

---

# Longhorn Nodes

Validar os nós registrados pelo Longhorn:

```bash
kubectl get nodes.longhorn.io -n longhorn-system -o wide
```

Resultado esperado:

```text
vuhwk
```

O `vuhmt` não deve possuir disk Longhorn.

---

# Disk Longhorn

O storage utilizado pelo `vuhwk` está localizado em:

```text
/var/lib/longhorn/
```

Esse diretório representa o armazenamento local utilizado pelo Longhorn neste laboratório.

---

# Engine Image

O Longhorn utiliza uma Engine Image para executar as operações dos volumes.

Versão utilizada:

```text
docker.io/longhornio/longhorn-engine:v1.12.0
```

Validar:

```bash
kubectl get engineimages.longhorn.io \
  -n longhorn-system \
  -o wide
```

Resultado validado:

```text
NAME          INCOMPATIBLE   STATE      IMAGE
ei-a4d05f02   false          deployed   docker.io/longhornio/longhorn-engine:v1.12.0
```

A Engine Image foi executada exclusivamente no:

```text
vuhwk
```

---

# Componentes

Validar os DaemonSets:

```bash
kubectl get daemonset -n longhorn-system
```

Estado validado:

```text
engine-image-ei-a4d05f02   1/1
longhorn-csi-plugin        1/1
longhorn-manager           1/1
```

Todos os componentes de storage estão executando no `vuhwk`.

---

# Teste de PersistentVolumeClaim

Foi criado o PVC:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-test-pvc
  namespace: default
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: longhorn
  resources:
    requests:
      storage: 1Gi
```

Validar:

```bash
kubectl get pvc longhorn-test-pvc
```

Resultado validado:

```text
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS
longhorn-test-pvc   Bound    pvc-b5b65949-b00d-427e-9877-9a2406aa47c3   1Gi        RWO            longhorn
```

Resultado:

```text
PVC = Bound
```

---

# PersistentVolume

Validar:

```bash
kubectl get pv
```

Resultado validado:

```text
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS
pvc-b5b65949-b00d-427e-9877-9a2406aa47c3   1Gi        RWO            Delete           Bound
```

Resultado:

```text
PV = Bound
```

---

# Longhorn Volume

O PVC gerou o seguinte Longhorn Volume:

```text
pvc-b5b65949-b00d-427e-9877-9a2406aa47c3
```

Validar:

```bash
kubectl get volumes.longhorn.io \
  -n longhorn-system \
  -o wide
```

Durante a utilização pelo Pod:

```text
STATE       attached
ROBUSTNESS  healthy
SIZE        1Gi
NODE        vuhwk
```

Resultado:

```text
Volume = Attached
Robustness = Healthy
Node = vuhwk
```

---

# Réplica

Validar:

```bash
kubectl get replicas.longhorn.io \
  -n longhorn-system \
  -o wide
```

Foi criada exatamente uma réplica:

```text
NODE: vuhwk
```

Não existe réplica no `vuhmt`.

Arquitetura:

```text
Longhorn Volume
      |
      +---- Replica 1
              |
            vuhwk
```

---

# Teste de montagem

Foi criado um Pod utilizando o PVC:

```text
longhorn-test-pod
```

O Pod foi executado no:

```text
vuhwk
```

O PVC foi montado em:

```text
/data
```

Foi criado o arquivo:

```text
/data/teste.txt
```

Conteúdo:

```text
Longhorn funcionando - Fri Sep  4 14:18:55 UTC 2026
```

Validação:

```bash
kubectl exec longhorn-test-pod -- cat /data/teste.txt
```

Resultado:

```text
Longhorn funcionando - Fri Sep  4 14:18:55 UTC 2026
```

Resultado:

```text
Escrita = OK
Leitura = OK
Montagem = OK
```

---

# Teste de persistência

Após a gravação do arquivo, o Pod foi removido:

```bash
kubectl delete pod longhorn-test-pod
```

O Pod foi posteriormente recriado utilizando o mesmo PVC.

O arquivo continuou disponível:

```bash
kubectl exec longhorn-test-pod -- cat /data/teste.txt
```

Resultado:

```text
Longhorn funcionando - Fri Sep  4 14:18:55 UTC 2026
```

Esse teste confirmou que o dado persistiu após a exclusão e recriação do Pod.

Fluxo validado:

```text
Pod
 |
 PVC
 |
 PV
 |
 Longhorn
 |
 Volume
 |
 Replica
 |
 vuhwk
```

---

# Estado final da validação

| Componente | Resultado |
|---|---|
| Helm | OK |
| Longhorn 1.12.0 | OK |
| Longhorn Manager | OK |
| CSI Plugin | OK |
| CSI Provisioner | OK |
| Engine Image | OK |
| StorageClass | OK |
| StorageClass com 1 réplica | OK |
| PVC | Bound |
| PV | Bound |
| Longhorn Volume | Attached |
| Volume Robustness | Healthy |
| Replica | 1 |
| Storage Node | `vuhwk` |
| `vuhmt` como storage | Não |
| Pod utilizando PVC | Running |
| Escrita | OK |
| Leitura | OK |
| Persistência após recriação do Pod | OK |

---

# Limitação atual

A configuração possui:

```text
1 nó de storage
1 réplica
```

Portanto, atualmente não existe redundância do armazenamento.

Se o `vuhwk` ficar indisponível:

```text
vuhwk
  |
  +--- única réplica
```

não existe outro nó contendo uma cópia do volume.

Consequentemente, o Longhorn não poderá reconstruir o volume em outro nó.

Essa limitação é intencional nesta fase do laboratório.

---

# Próxima etapa

A próxima etapa será testar a falha do worker `vuhwk`.

O objetivo será observar:

- comportamento do Pod;
- comportamento do PVC;
- comportamento do PV;
- estado do Longhorn Volume;
- estado da réplica;
- comportamento do Kubernetes quando o worker fica indisponível;
- impacto da existência de apenas uma réplica.

Depois disso, o laboratório poderá evoluir para múltiplos nós de storage e replicação.

Arquitetura futura:

```text
                    Longhorn
                       |
             +---------+---------+
             |                   |
           vuhwk              worker2
             |                   |
         Replica 1           Replica 2
```

Essa próxima fase permitirá validar:

- replicação;
- rebuild;
- recuperação;
- falha de nó;
- migração de workloads;
- alta disponibilidade do storage.
