# Rede

## Objetivo

Definir a arquitetura de comunicação utilizada pelo Kubernetes Lab, permitindo a comunicação entre os nós do cluster e os componentes internos da plataforma.

---

## Arquitetura de Rede

O laboratório utiliza uma rede privada dedicada para comunicação entre as máquinas virtuais.

Rede definida:
10.10.10.0/24

---

## Endereçamento

| Host | Endereço IP | Função |
|-|-|-|
| vuhmt | 10.10.10.10 | Control Plane |
| vuhwk | 10.10.10.20 | Worker Node |

---

## Rede Virtual

A comunicação interna do cluster utiliza o switch virtual:
k8s

Tipo:
Internal

---

## Comunicação Kubernetes

A arquitetura de rede possui diferentes camadas:

### Rede dos Nós

Responsável pela comunicação entre as máquinas virtuais.

Exemplo:
10.10.10.0/24

---

### Rede dos Pods

Gerenciada pelo plugin CNI utilizado pelo Kubernetes.

Responsável pela comunicação entre aplicações executadas no cluster.

---

## Validação

A conectividade da infraestrutura foi validada através de:

- Configuração de IP estático.
- Comunicação entre máquinas virtuais.
- Acesso remoto via SSH.
- Validação dos endereços utilizados pelo Kubernetes.

---

## Considerações

A separação da rede do cluster permite maior controle da comunicação interna e facilita futuras expansões da infraestrutura.