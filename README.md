# Projeto Final — Fundamentos de Redes de Computadores

**Turma:** bsi-26-1 (2026.1)  
**Grupo:** 3  
**Domínio:** `grupo3.bsi-26-1.maceio.lab`  
**Integrantes:** pedro.henrique · wallex.kaua · marcelo.alves · werython.rocha

---

##  Sumário

1. [Objetivo](#objetivo)
2. [Topologia](#topologia)
3. [Hardware das VMs](#hardware-das-vms)
4. [Endereçamento IP](#endereçamento-ip)
5. [Nomenclatura e Domínio (FQDN)](#nomenclatura-e-domínio-fqdn)
6. [Configuração de Rede (Netplan)](#configuração-de-rede-netplan)
7. [Arquivo /etc/hosts](#arquivo-etchosts)
8. [Usuários](#usuários)
9. [Testes de Ping](#testes-de-ping)
10. [Acesso SSH via Termius](#acesso-ssh-via-termius)

---

## Objetivo

Criar um ambiente de rede virtualizada com **8 máquinas virtuais** rodando **Ubuntu Server**, interligadas por um switch de 8 portas, seguindo a topologia definida pela disciplina. Cada VM recebeu um IP estático dentro da sub-rede `/28` atribuída ao Grupo 3.

---

## Topologia

O ambiente segue a topologia estrela definida no projeto:

- **4 PCs físicos** (PC1, PC2, PC3, PC4) conectados a um switch de 8 portas via cabo Ethernet
- Cada PC hospeda **2 VMs** no VirtualBox com adaptador de rede em modo **Bridge**
- As VMs se comunicam como máquinas físicas na mesma rede local

```
         [VM Lab01@PC3]  [VM Lab02@PC3]
                  \         /
                   \       /
        [PC1] ---- [SWITCH 8P] ---- [PC2]
                   /       \
                  /         \
         [VM Lab01@PC4]  [VM Lab02@PC4]
```

> **Adaptadores configurados em cada VM:**
> - `enp0s3` → modo **Bridge** (rede do projeto — sub-rede `/28`)
> - `enp0s8` → **NAT/DHCP interno** (acesso à internet — rede `10.0.3.x`)

---

## Hardware das VMs

Configuração utilizada em cada uma das 8 máquinas virtuais:

| Recurso | Configuração |
|---|---|
| Sistema Operacional | Ubuntu Server 22.04 LTS |
| Memória RAM | 1024 MB (1 GB) |
| Processadores | 1 vCPU |
| Espaço em Disco | 10 GB (VDI, alocação dinâmica) |
| Adaptador 1 (`enp0s3`) | Placa em modo Bridge |
| Adaptador 2 (`enp0s8`) | NAT (DHCP automático) |

---

## Endereçamento IP

A turma bsi-26-1 usa a rede `192.168.26.0/24`. O **Grupo 3** recebeu a sub-rede:

| Campo | Valor |
|---|---|
| Sub-rede | `192.168.26.32/28` |
| Endereço de rede | `192.168.26.32` |
| Primeiro host | `192.168.26.33` |
| Último host | `192.168.26.46` |
| Broadcast | `192.168.26.47` |
| Máscara | `255.255.255.240` |

### Tabela de IPs das VMs

| VM | Hostname | IP | Máscara |
|---|---|---|---|
| Lab01@PC1 | servidor | `192.168.26.33` | `255.255.255.240` |
| Lab02@PC1 | cliente1 | `192.168.26.34` | `255.255.255.240` |
| Lab01@PC2 | cliente2 | `192.168.26.35` | `255.255.255.240` |
| Lab02@PC2 | cliente3 | `192.168.26.36` | `255.255.255.240` |
| Lab01@PC3 | cliente4 | `192.168.26.37` | `255.255.255.240` |
| Lab02@PC3 | cliente5 | `192.168.26.38` | `255.255.255.240` |
| Lab01@PC4 | cliente6 | `192.168.26.39` | `255.255.255.240` |
| Lab02@PC4 | cliente7 | `192.168.26.40` | `255.255.255.240` |

---

## Nomenclatura e Domínio (FQDN)

Domínio do grupo: `grupo3.bsi-26-1.maceio.lab`

| Hostname curto | FQDN completo | Alias | IP |
|---|---|---|---|
| servidor | `servidor.grupo3.bsi-26-1.maceio.lab` | servidor | `192.168.26.33` |
| cliente1 | `cliente1.grupo3.bsi-26-1.maceio.lab` | cliente1 | `192.168.26.34` |
| cliente2 | `cliente2.grupo3.bsi-26-1.maceio.lab` | cliente2 | `192.168.26.35` |
| cliente3 | `cliente3.grupo3.bsi-26-1.maceio.lab` | cliente3 | `192.168.26.36` |
| cliente4 | `cliente4.grupo3.bsi-26-1.maceio.lab` | cliente4 | `192.168.26.37` |
| cliente5 | `cliente5.grupo3.bsi-26-1.maceio.lab` | cliente5 | `192.168.26.38` |
| cliente6 | `cliente6.grupo3.bsi-26-1.maceio.lab` | cliente6 | `192.168.26.39` |
| cliente7 | `cliente7.grupo3.bsi-26-1.maceio.lab` | cliente7 | `192.168.26.40` |

---

## Configuração de Rede (Netplan)

O IP estático foi configurado editando o arquivo Netplan em cada VM:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

**Conteúdo do arquivo**

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.26.33/28
    enp0s8:
      dhcp4: yes
```

Após editar, aplicar as configurações:

```bash
sudo netplan apply
```

Verificar o IP configurado:

```bash
ifconfig
# ou
ip addr show
```

> **Exemplo de saída do `ifconfig` na VM `servidor` (192.168.26.33):**
> - `enp0s3`: `192.168.26.33` / `255.255.255.240` / broadcast `192.168.26.47`
> - `enp0s8`: `10.0.3.15` (DHCP interno — adaptador NAT para internet)

---

## Arquivo /etc/hosts

O mapeamento IP/Nome foi adicionado ao arquivo `/etc/hosts` de **todas as VMs**:

```bash
sudo nano /etc/hosts
```

**Conteúdo adicionado:**

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

## Usuários

Em cada VM foram criados os seguintes usuários:

```bash
sudo adduser pedro.henrique
sudo adduser wallex.kaua
sudo adduser marcelo.alves
sudo adduser werython.rocha
```

> O usuário **`servidor`** é o administrador (root) criado durante a instalação do Ubuntu Server.

Para conceder privilégios administrativos aos integrantes:

```bash
sudo usermod -aG sudo pedro.henrique
sudo usermod -aG sudo wallex.kaua
sudo usermod -aG sudo marcelo.alves
sudo usermod -aG sudo werython.rocha
```

---

## Testes de Ping

Todos os testes foram realizados a partir da VM **`servidor` (192.168.26.33)**, pingando cada uma das demais VMs. Resultado: **0% de perda de pacotes** em todos os testes.

### servidor → cliente1 (192.168.26.34)

```
PING 192.168.26.34 (192.168.26.34) 56(84) bytes of data.
64 bytes from 192.168.26.34: icmp_seq=1 ttl=64 time=9.17 ms
64 bytes from 192.168.26.34: icmp_seq=2 ttl=64 time=0.621 ms
64 bytes from 192.168.26.34: icmp_seq=3 ttl=64 time=0.611 ms
64 bytes from 192.168.26.34: icmp_seq=4 ttl=64 time=1.02 ms

--- 192.168.26.34 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3077ms
rtt min/avg/max/mdev = 0.611/2.853/9.165/3.647 ms
```

### servidor → cliente2 (192.168.26.35)

```
PING 192.168.26.35 (192.168.26.35) 56(84) bytes of data.
64 bytes from 192.168.26.35: icmp_seq=1 ttl=64 time=2.68 ms
64 bytes from 192.168.26.35: icmp_seq=2 ttl=64 time=0.760 ms
64 bytes from 192.168.26.35: icmp_seq=3 ttl=64 time=0.818 ms

--- 192.168.26.35 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2067ms
rtt min/avg/max/mdev = 0.760/1.419/2.679/0.891 ms
```

### servidor → cliente4 (192.168.26.37)

```
PING 192.168.26.37 (192.168.26.37) 56(84) bytes of data.
64 bytes from 192.168.26.37: icmp_seq=1 ttl=64 time=1.82 ms
64 bytes from 192.168.26.37: icmp_seq=2 ttl=64 time=0.847 ms
64 bytes from 192.168.26.37: icmp_seq=3 ttl=64 time=0.737 ms

--- 192.168.26.37 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2865ms
rtt min/avg/max/mdev = 0.737/1.134/1.819/0.486 ms
```

### servidor → cliente5 (192.168.26.38)

```
PING 192.168.26.38 (192.168.26.38) 56(84) bytes of data.
64 bytes from 192.168.26.38: icmp_seq=1 ttl=64 time=1.36 ms
64 bytes from 192.168.26.38: icmp_seq=2 ttl=64 time=0.684 ms
64 bytes from 192.168.26.38: icmp_seq=3 ttl=64 time=17.7 ms
64 bytes from 192.168.26.38: icmp_seq=4 ttl=64 time=0.719 ms

--- 192.168.26.38 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 8276ms
rtt min/avg/max/mdev = 0.684/5.118/17.710/7.274 ms
```

### servidor → cliente6 (192.168.26.39)

```
PING 192.168.26.39 (192.168.26.39) 56(84) bytes of data.
64 bytes from 192.168.26.39: icmp_seq=1 ttl=64 time=1.47 ms
64 bytes from 192.168.26.39: icmp_seq=2 ttl=64 time=0.699 ms
64 bytes from 192.168.26.39: icmp_seq=3 ttl=64 time=0.847 ms
64 bytes from 192.168.26.39: icmp_seq=4 ttl=64 time=0.723 ms

--- 192.168.26.39 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3046ms
rtt min/avg/max/mdev = 0.699/0.935/1.472/0.314 ms
```

### servidor → cliente7 (192.168.26.40)

```
PING 192.168.26.40 (192.168.26.40) 56(84) bytes of data.
64 bytes from 192.168.26.40: icmp_seq=1 ttl=64 time=1.33 ms
64 bytes from 192.168.26.40: icmp_seq=2 ttl=64 time=0.690 ms
64 bytes from 192.168.26.40: icmp_seq=3 ttl=64 time=0.746 ms
64 bytes from 192.168.26.40: icmp_seq=4 ttl=64 time=0.566 ms

--- 192.168.26.40 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3470ms
rtt min/avg/max/mdev = 0.566/0.833/1.330/0.294 ms
```

### Resumo dos testes de ping

| Origem | Destino | IP Destino | Pacotes | Perda | RTT médio |
|---|---|---|---|---|---|
| servidor | cliente1 | 192.168.26.34 | 4/4 | 0% | 2.853 ms |
| servidor | cliente2 | 192.168.26.35 | 3/3 | 0% | 1.419 ms |
| servidor | cliente4 | 192.168.26.37 | 3/3 | 0% | 1.134 ms |
| servidor | cliente5 | 192.168.26.38 | 4/4 | 0% | 5.118 ms |
| servidor | cliente6 | 192.168.26.39 | 4/4 | 0% | 0.935 ms |
| servidor | cliente7 | 192.168.26.40 | 4/4 | 0% | 0.833 ms |

---

## Acesso SSH via Termius

O acesso SSH entre as VMs foi realizado utilizando o aplicativo **[Termius](https://termius.com/)**, conectando via endereço IP local da sub-rede `/28`, alterando apenas o IP de destino para cada VM.

**Configuração usada no Termius:**

| Campo | Valor |
|---|---|
| Address | IP da VM destino (ex: `192.168.26.34`) |
| Porta SSH | `2222` |
| Usuário | `servidor` |
| Autenticação | Senha |

> A porta `2222` foi utilizada no lugar da porta padrão `22`.

As 8 máquinas foram cadastradas como hosts no Termius:

| Host Termius | Descrição |
|---|---|
| `servidor` | VM principal — 192.168.26.33 |
| `cliente1` | 192.168.26.34 |
| `cliente2` | 192.168.26.35 |
| `cliente3` | 192.168.26.36 |
| `cliente4` | 192.168.26.37 |
| `cliente5` | 192.168.26.38 |
| `cliente6` | 192.168.26.39 |
| `cliente7` | 192.168.26.40 |

Todos os hosts foram configurados com o grupo de credenciais compartilhadas (`ssh, servidor`), permitindo acesso rápido a qualquer VM sem reconfiguração.



---

*Projeto Final — Fundamentos de Redes de Computadores · Turma bsi-26-1 · 2026.1*
