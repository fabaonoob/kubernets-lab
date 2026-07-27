# Calico

## Objetivo

Implementar a camada de rede do Kubernetes Lab utilizando o Calico como solução CNI (Container Network Interface), permitindo a comunicação entre os Pods e habilitando o funcionamento completo do cluster Kubernetes.

---

## Visão Geral

O Kubernetes necessita de uma solução de rede para permitir a comunicação entre os workloads executados no cluster.

Sem uma CNI configurada, os nós Kubernetes permanecem no estado `NotReady`, pois a comunicação interna entre Pods ainda não está disponível.

---

## Decisão Arquitetural

O Calico foi escolhido como solução de rede devido aos seguintes fatores:

- Ampla utilização em ambientes Kubernetes.
- Compatibilidade com clusters implantados através do kubeadm.
- Recursos avançados de rede e políticas de segurança.
- Utilização em ambientes corporativos e Cloud Native.

---

## Implantação

A instalação do Calico foi realizada para disponibilizar a comunicação de rede entre os componentes do cluster.

Durante a primeira tentativa de implantação foram identificados problemas no processo de configuração do componente CNI.

---

## Troubleshooting

Após a primeira instalação, o componente do Calico apresentou o estado:

```text
CrashLoopBackOff
```

A análise dos logs identificou falha no componente:

```text
install-cni
```

O erro estava relacionado à permissão de escrita nos diretórios utilizados para instalação dos plugins CNI.

Foram realizadas validações dos diretórios responsáveis pela configuração da rede:

- `/opt/cni/bin`
- `/etc/cni/net.d`

Após a análise, a instalação anterior foi removida e o ambiente foi preparado para uma nova implantação.

---

## Configuração Kubernetes

O Calico foi implantado utilizando o método baseado no Tigera Operator, responsável pelo gerenciamento dos componentes necessários para operação da solução.

Após a implantação foram disponibilizados:

- tigera-operator
- calico-node
- calico-apiserver
- calico-kube-controllers
- calico-typha
- csi-node-driver

---

## Validação

A validação do funcionamento foi realizada através dos recursos Kubernetes:

```bash
kubectl get nodes
```

Resultado obtido:

```text
vuhmt   Ready
```

Os componentes do cluster foram validados utilizando:

```bash
kubectl get pods -A
```

Todos os componentes principais apresentaram estado:

```text
Running
```

---

## Considerações

A implantação do Calico finalizou a configuração básica do cluster Kubernetes, permitindo a comunicação entre os componentes internos e alterando o estado do nó Control Plane para `Ready`.

Durante esta etapa foi possível validar processos importantes de administração Kubernetes, incluindo análise de logs, troubleshooting de componentes e validação de recursos do cluster.

Com a camada de rede operacional, o próximo passo será a expansão da infraestrutura através da inclusão do Worker Node.