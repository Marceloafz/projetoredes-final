<div align="center">

# 🌐 Projeto Final — Fundamentos de Redes de Computadores

**Turma:** bsi-26-1 (2026.1) &nbsp;·&nbsp; **Grupo:** 3 &nbsp;·&nbsp; **Domínio:** `grupo3.bsi-26-1.maceio.lab`

| Integrante | Usuário no sistema |
|:---|:---|
| Pedro Henrique | `pedro.henrique` |
| Wallex Kauã | `wallex.kaua` |
| Marcelo Alves | `marcelo.alves` |
| Werython Rocha | `werython.rocha` |

</div>

---

## Sumário

1. [Objetivo](#1-objetivo)
2. [Topologia de Rede](#2-topologia-de-rede)
3. [Hardware das Máquinas Virtuais](#3-hardware-das-máquinas-virtuais)
4. [Endereçamento IP](#4-endereçamento-ip)
5. [Nomenclatura e Domínio (FQDN)](#5-nomenclatura-e-domínio-fqdn)
6. [Configuração de Rede — Netplan](#6-configuração-de-rede--netplan)
7. [Arquivo `/etc/hosts`](#7-arquivo-etchosts)
8. [Usuários do Sistema](#8-usuários-do-sistema)
9. [Testes de Conectividade — Ping](#9-testes-de-conectividade--ping)
10. [Acesso Remoto — SSH via Termius](#10-acesso-remoto--ssh-via-termius)

---

## 1. Objetivo

Este repositório documenta o **Projeto Final** da disciplina de Fundamentos de Redes de Computadores (bsi-26-1, 2026.1). O objetivo foi projetar, configurar e validar um ambiente de rede virtualizada composto por **8 máquinas virtuais** rodando **Ubuntu Server**, interligadas por um switch físico de 8 portas, com endereçamento estático dentro de uma sub-rede `/28` atribuída ao Grupo 3.

O ambiente contempla:

- Criação das VMs no **VirtualBox** com adaptador de rede em modo **Bridge**;
- Configuração de **IP estático** via Netplan;
- Definição de **hostnames** e resolução local de nomes via `/etc/hosts`;
- Criação de **usuários** por integrante do grupo em cada VM;
- Validação da conectividade com testes de **ping** entre todas as VMs;
- Acesso remoto entre as máquinas via **SSH**, gerenciado pelo Termius.

---

## 2. Topologia de Rede

O ambiente segue a topologia estrela definida no enunciado do projeto. Quatro PCs físicos se conectam a um switch central de 8 portas via cabo Ethernet. Cada PC executa duas VMs no VirtualBox com o adaptador de rede em modo Bridge, fazendo com que as máquinas virtuais apareçam como nós independentes na rede local.

Cada VM possui dois adaptadores de rede configurados:

| Adaptador | Modo | Finalidade | Rede |
|:---|:---|:---|:---|
| `enp0s3` | Bridge | Comunicação entre VMs (rede do projeto) | `192.168.26.32/28` |
| `enp0s8` | NAT / DHCP | Acesso externo à internet | `10.0.3.x` (automático) |

A imagem abaixo confirma a configuração dos dois adaptadores na VM `servidor`, evidenciando o IP estático em `enp0s3` e o endereço NAT em `enp0s8`:

![Saída do ifconfig na VM servidor — adaptadores enp0s3 e enp0s8](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/Adaptador%20de%20rede%20interna.jpeg)

> **Figura 1 —** Saída do comando `ifconfig` na VM `servidor`. O adaptador `enp0s3` exibe o IP estático `192.168.26.33` com máscara `255.255.255.240` e broadcast `192.168.26.47`. O adaptador `enp0s8` exibe o IP `10.0.3.15` obtido via DHCP do adaptador NAT (rede interna de acesso à internet).

---

## 3. Hardware das Máquinas Virtuais

Configuração adotada em cada uma das 8 VMs criadas para o projeto:

| Recurso | Configuração |
|:---|:---|
| Sistema Operacional | Ubuntu Server 22.04 LTS |
| Memória RAM | 1024 MB (1 GB) |
| Processadores | 3 vCPUs |
| Disco | 10 GB — VDI, alocação dinâmica |
| Adaptador 1 (`enp0s3`) | Placa em modo Bridge |
| Adaptador 2 (`enp0s8`) | NAT (DHCP automático) |
| Hypervisor | Oracle VirtualBox 7.x |

---

## 4. Endereçamento IP

A turma bsi-26-1 utiliza a rede `192.168.26.0/24`. O **Grupo 3** recebeu a seguinte sub-rede:

| Parâmetro | Valor |
|:---|:---|
| Sub-rede | `192.168.26.32/28` |
| Endereço de rede | `192.168.26.32` |
| Primeiro host utilizável | `192.168.26.33` |
| Último host utilizável | `192.168.26.46` |
| Endereço de broadcast | `192.168.26.47` |
| Máscara de sub-rede | `255.255.255.240` |
| Total de hosts utilizáveis | 14 |
| Hosts em uso | 8 (`.33` a `.40`) |

### Tabela de endereços das VMs

| VM | Hostname | Endereço IP | Máscara |
|:---|:---|:---|:---|
| Lab01 @ PC1 | `servidor` | `192.168.26.33` | `255.255.255.240` |
| Lab02 @ PC1 | `cliente1` | `192.168.26.34` | `255.255.255.240` |
| Lab01 @ PC2 | `cliente2` | `192.168.26.35` | `255.255.255.240` |
| Lab02 @ PC2 | `cliente3` | `192.168.26.36` | `255.255.255.240` |
| Lab01 @ PC3 | `cliente4` | `192.168.26.37` | `255.255.255.240` |
| Lab02 @ PC3 | `cliente5` | `192.168.26.38` | `255.255.255.240` |
| Lab01 @ PC4 | `cliente6` | `192.168.26.39` | `255.255.255.240` |
| Lab02 @ PC4 | `cliente7` | `192.168.26.40` | `255.255.255.240` |

---

## 5. Nomenclatura e Domínio (FQDN)

O domínio do grupo segue o padrão exigido pelo projeto: `grupo3.bsi-26-1.maceio.lab`.

O FQDN (*Fully Qualified Domain Name*) de cada VM obedece ao formato `<hostname>.<domínio>`:

| Hostname | FQDN completo | Alias | IP |
|:---|:---|:---|:---|
| `servidor` | `servidor.grupo3.bsi-26-1.maceio.lab` | `servidor` | `192.168.26.33` |
| `cliente1` | `cliente1.grupo3.bsi-26-1.maceio.lab` | `cliente1` | `192.168.26.34` |
| `cliente2` | `cliente2.grupo3.bsi-26-1.maceio.lab` | `cliente2` | `192.168.26.35` |
| `cliente3` | `cliente3.grupo3.bsi-26-1.maceio.lab` | `cliente3` | `192.168.26.36` |
| `cliente4` | `cliente4.grupo3.bsi-26-1.maceio.lab` | `cliente4` | `192.168.26.37` |
| `cliente5` | `cliente5.grupo3.bsi-26-1.maceio.lab` | `cliente5` | `192.168.26.38` |
| `cliente6` | `cliente6.grupo3.bsi-26-1.maceio.lab` | `cliente6` | `192.168.26.39` |
| `cliente7` | `cliente7.grupo3.bsi-26-1.maceio.lab` | `cliente7` | `192.168.26.40` |

---

## 6. Configuração de Rede — Netplan

O Ubuntu Server utiliza o **Netplan** para gerenciamento de interfaces de rede. O arquivo de configuração foi editado em cada VM:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

**Conteúdo do arquivo** — apenas o campo `addresses` muda de acordo com o IP de cada VM:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.26.33/28   # alterar para o IP correspondente (.33 a .40)
    enp0s8:
      dhcp4: yes
```

Após editar, as configurações foram aplicadas com:

```bash
sudo netplan apply
```

E a verificação foi feita com:

```bash
ifconfig
```

---

## 7. Arquivo `/etc/hosts`

O mapeamento estático entre endereços IP e nomes foi inserido no arquivo `/etc/hosts` de **todas as VMs**:

```bash
sudo nano /etc/hosts
```

**Conteúdo adicionado em cada máquina:**

```
192.168.26.33 servidor.grupo3.bsi-26-1.maceio.lab servidor
192.168.26.34 cliente1.grupo3.bsi-26-1.maceio.lab cliente1
192.168.26.35 cliente2.grupo3.bsi-26-1.maceio.lab cliente2
192.168.26.36 cliente3.grupo3.bsi-26-1.maceio.lab cliente3
192.168.26.37 cliente4.grupo3.bsi-26-1.maceio.lab cliente4
192.168.26.38 cliente5.grupo3.bsi-26-1.maceio.lab cliente5
192.168.26.39 cliente6.grupo3.bsi-26-1.maceio.lab cliente6
192.168.26.40 cliente7.grupo3.bsi-26-1.maceio.lab cliente7
```

---

## 8. Usuários do Sistema

Em cada VM foram criados os usuários dos integrantes do grupo, além do usuário administrador `servidor` definido durante a instalação:

```bash
sudo adduser pedro.henrique
sudo adduser wallex.kaua
sudo adduser marcelo.alves
sudo adduser werython.rocha
```

Para garantir privilégios administrativos a todos os integrantes:

```bash
sudo usermod -aG sudo pedro.henrique
sudo usermod -aG sudo wallex.kaua
sudo usermod -aG sudo marcelo.alves
sudo usermod -aG sudo werython.rocha
```

| Usuário | Perfil |
|:---|:---|
| `servidor` | Administrador — criado na instalação |
| `pedro.henrique` | Integrante do grupo — sudo |
| `wallex.kaua` | Integrante do grupo — sudo |
| `marcelo.alves` | Integrante do grupo — sudo |
| `werython.rocha` | Integrante do grupo — sudo |

---

## 9. Testes de Conectividade — Ping

Todos os testes foram executados a partir da VM **`servidor` (192.168.26.33)**, enviando pacotes ICMP para cada uma das demais VMs. O resultado em todos os casos foi **0% de perda de pacotes**, confirmando conectividade plena na sub-rede do Grupo 3.

---

### 9.1 servidor → cliente1 (192.168.26.34)

![Ping de servidor para cliente1](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente1.jpeg)

> **Figura 2 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT médio: 2,853 ms

---

### 9.2 servidor → cliente2 (192.168.26.35)

![Ping de servidor para cliente2](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente2.jpeg)

> **Figura 3 —** 3 pacotes transmitidos · 3 recebidos · **0% de perda** · RTT médio: 1,419 ms

---

### 9.3 servidor → cliente3 (192.168.26.36)

![Ping de servidor para cliente3](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente3.jpeg)

> **Figura 4 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT médio: 0,945 ms

---

### 9.4 servidor → cliente4 (192.168.26.37)

![Ping de servidor para cliente4](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente4.jpeg)

> **Figura 5 —** 3 pacotes transmitidos · 3 recebidos · **0% de perda** · RTT médio: 1,134 ms

---

### 9.5 servidor → cliente5 (192.168.26.38)

![Ping de servidor para cliente5](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente5.jpeg)

> **Figura 6 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT médio: 5,118 ms
>
> *Nota: o RTT máximo de 17,710 ms no 3.º pacote é pontual, decorrente de variação de carga na VM no momento do teste. Os demais pacotes apresentaram latência regular.*

---

### 9.6 servidor → cliente6 (192.168.26.39)

![Ping de servidor para cliente6](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente%206.jpeg)

> **Figura 7 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT médio: 0,935 ms

---

### 9.7 servidor → cliente7 (192.168.26.40)

![Ping de servidor para cliente7](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente%207.jpeg)

> **Figura 8 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT médio: 0,833 ms

---

### 9.8 Tabela de Resultados

| Origem | Destino | IP Destino | Enviados | Recebidos | Perda | RTT mín. | RTT méd. | RTT máx. |
|:---|:---|:---|:---:|:---:|:---:|:---|:---|:---|
| servidor | cliente1 | 192.168.26.34 | 4 | 4 | **0%** | 0,611 ms | 2,853 ms | 9,165 ms |
| servidor | cliente2 | 192.168.26.35 | 3 | 3 | **0%** | 0,760 ms | 1,419 ms | 2,679 ms |
| servidor | cliente3 | 192.168.26.36 | 4 | 4 | **0%** | 0,696 ms | 0,945 ms | 1,583 ms |
| servidor | cliente4 | 192.168.26.37 | 3 | 3 | **0%** | 0,737 ms | 1,134 ms | 1,819 ms |
| servidor | cliente5 | 192.168.26.38 | 4 | 4 | **0%** | 0,684 ms | 5,118 ms | 17,710 ms |
| servidor | cliente6 | 192.168.26.39 | 4 | 4 | **0%** | 0,699 ms | 0,935 ms | 1,472 ms |
| servidor | cliente7 | 192.168.26.40 | 4 | 4 | **0%** | 0,566 ms | 0,833 ms | 1,330 ms |

---

## 10. Acesso Remoto — SSH via Termius

O acesso SSH entre as VMs foi realizado por meio do aplicativo **[Termius](https://termius.com/)**, conectando via endereço IP da sub-rede `/28`. O endereço IP de destino foi o único campo alterado entre os hosts; todas as demais credenciais foram compartilhadas.

### 10.1 Hosts cadastrados no Termius

A imagem abaixo apresenta os 8 hosts do Grupo 3 cadastrados no Termius, todos com credenciais compartilhadas (`ssh, servidor`):

![Painel de hosts no Termius — 8 VMs do Grupo 3](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/Hosts%20Termius.jpeg)

> **Figura 9 —** Painel de hosts no Termius exibindo as 8 VMs do Grupo 3 (`servidor`, `cliente1` a `cliente7`). O uso de credenciais compartilhadas permite acesso imediato a qualquer máquina sem reconfiguração individual.

### 10.2 Configuração de cada host

A imagem abaixo ilustra os parâmetros de conexão SSH configurados no Termius para acesso às VMs:

![Configuração de host SSH no Termius](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ssh%20via%20termius.jpeg)

> **Figura 10 —** Painel de configuração de um host no Termius. O campo *Address* recebe o IP da VM destino; a porta SSH utilizada foi a **2222** (em substituição à porta padrão 22); a autenticação é feita com o usuário `servidor` e senha, com credenciais reutilizadas nos 8 hosts.

### 10.3 Parâmetros de conexão

| Parâmetro | Valor |
|:---|:---|
| Address | IP da VM destino (ex: `192.168.26.34`) |
| Porta SSH | `2222` |
| Usuário | `servidor` |
| Autenticação | Senha |
| Credenciais | Compartilhadas entre os 8 hosts |

### 10.4 Acesso via linha de comando

Para acessar qualquer VM diretamente pelo terminal:

```bash
# Sintaxe geral
ssh servidor@<ip-da-vm> -p 2222

# Exemplos
ssh servidor@192.168.26.34 -p 2222   # acessa cliente1
ssh servidor@192.168.26.37 -p 2222   # acessa cliente4
```

Na primeira conexão, confirmar a chave do host quando solicitado:

```
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

Para verificar a identidade da máquina após o login:

```bash
hostname   # exibe o hostname da VM acessada
whoami     # exibe o usuário da sessão ativa
```

---

## Estrutura do Repositório

```
projetoredes-final/
├── README.md
├── Adaptador de rede interna.jpeg
├── Hosts Termius.jpeg
├── ssh via termius.jpeg
├── ping cliente1.jpeg
├── ping cliente2.jpeg
├── ping cliente3.jpeg
├── ping cliente4.jpeg
├── ping cliente5.jpeg
├── ping cliente 6.jpeg
└── ping cliente 7.jpeg
```

---

<div align="center">

Projeto Final · Fundamentos de Redes de Computadores · Turma bsi-26-1 · 2026.1

</div>
