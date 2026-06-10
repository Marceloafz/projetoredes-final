<div align="center">

# Projeto Final — Fundamentos de Redes de Computadores

**Instituto Federal de Alagoas — IFAL**
**Curso de Bacharelado em Sistemas de Informacao — bsi-26-1 (2026.1)**
**Disciplina:** Fundamentos de Redes de Computadores &nbsp;·&nbsp; **Professor:** Alaelson Jatobá

</div>

---

## Secao 1 — Identificacao

| Campo | Informacao |
|:---|:---|
| Instituicao | Instituto Federal de Alagoas — IFAL |
| Disciplina | Fundamentos de Redes de Computadores |
| Turma | bsi-26-1 (2026.1) |
| Professor | Alaelson Jatobá |
| Numero do Grupo | Grupo 3 |
| Dominio do Grupo | `grupo3.bsi-26-1.maceio.lab` |
| Repositorio GitHub | https://github.com/Marceloafz/projetoredes-final |

### Integrantes do Grupo

| Integrante | Usuario no Sistema | Perfil |
|:---|:---|:---|
| Pedro Henrique | `pedro.henrique` | Integrante — sudo |
| Wallex Kaua | `wallex.kaua` | Integrante — sudo |
| Marcelo Alves | `marcelo.alves` | Integrante — sudo |
| Werython Rocha | `werython.rocha` | Integrante — sudo |
| (administrador) | `servidor` | Admin — criado na instalacao (senha: `adminifal`) |

---

## Secao 2 — Especificacoes de Hardware e Ambiente

O ambiente e composto por exatamente **8 maquinas virtuais** rodando **Ubuntu Server 22.04 LTS**, distribuidas em 4 computadores fisicos (2 VMs por PC), interligadas por um switch de 8 portas em topologia estrela.

### 2.1 Configuracao de Hardware de Cada VM

| Recurso | Configuracao |
|:---|:---|
| Sistema Operacional | Ubuntu Server 22.04 LTS |
| Memoria RAM | 1024 MB (1 GB) |
| Processadores / Cores | 3 vCPUs |
| Espaco em Disco | 10 GB — VDI, alocacao dinamica |
| Adaptador 1 (`enp0s3`) | Placa em modo Bridge (rede do projeto) |
| Adaptador 2 (`enp0s8`) | NAT — DHCP automatico (acesso a internet) |
| Hypervisor | Oracle VirtualBox 7.x |

### 2.2 Distribuicao das VMs por PC Fisico

| PC Fisico | VM 1 (Lab01) | VM 2 (Lab02) |
|:---|:---|:---|
| PC1 | `servidor` — 192.168.26.33 | `cliente1` — 192.168.26.34 |
| PC2 | `cliente2` — 192.168.26.35 | `cliente3` — 192.168.26.36 |
| PC3 | `cliente4` — 192.168.26.37 | `cliente5` — 192.168.26.38 |
| PC4 | `cliente6` — 192.168.26.39 | `cliente7` — 192.168.26.40 |

### 2.3 Verificacao dos Adaptadores de Rede

Saida do comando `ifconfig` na VM `servidor`, confirmando o IP estatico no adaptador `enp0s3` e o DHCP NAT no adaptador `enp0s8`:

![Saida do ifconfig na VM servidor](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/Adaptador%20de%20rede%20interna.jpeg)

> **Figura 1 —** `enp0s3`: IP estatico `192.168.26.33/28`, broadcast `192.168.26.47`. `enp0s8`: IP `10.0.3.15` via NAT (adaptador de acesso a internet).

---

## Secao 3 — Redes, IPs e Dominios

### 3.1 Sub-rede do Grupo 3

A turma bsi-26-1 utiliza a rede `192.168.26.0/24`. O Grupo 3 recebeu a sub-rede abaixo, dentro da faixa designada para a turma, com mascara `/28 (255.255.255.240)`:

| Parametro | Valor |
|:---|:---|
| Sub-rede do Grupo 3 | `192.168.26.32/28` |
| Endereco de rede | `192.168.26.32` |
| Primeiro host utilizavel | `192.168.26.33` |
| Ultimo host utilizavel | `192.168.26.46` |
| Endereco de broadcast | `192.168.26.47` |
| Mascara de sub-rede | `255.255.255.240` (/28) |
| Total de hosts utilizaveis | 14 |
| Hosts em uso (8 VMs) | `192.168.26.33` a `192.168.26.40` |

### 3.2 Tabela de Enderecos IP das VMs

Todos os IPs estao dentro da faixa `192.168.26.0/24` designada para a turma, com mascara `/28`:

| VM | Hostname | Endereco IP | Mascara |
|:---|:---|:---|:---|
| Lab01 @ PC1 | `servidor` | `192.168.26.33` | `255.255.255.240` |
| Lab02 @ PC1 | `cliente1` | `192.168.26.34` | `255.255.255.240` |
| Lab01 @ PC2 | `cliente2` | `192.168.26.35` | `255.255.255.240` |
| Lab02 @ PC2 | `cliente3` | `192.168.26.36` | `255.255.255.240` |
| Lab01 @ PC3 | `cliente4` | `192.168.26.37` | `255.255.255.240` |
| Lab02 @ PC3 | `cliente5` | `192.168.26.38` | `255.255.255.240` |
| Lab01 @ PC4 | `cliente6` | `192.168.26.39` | `255.255.255.240` |
| Lab02 @ PC4 | `cliente7` | `192.168.26.40` | `255.255.255.240` |

### 3.3 Nomenclatura e Dominio (FQDN)

O dominio do grupo segue estritamente o formato exigido: `grupo3.bsi-26-1.maceio.lab`.
O FQDN de cada VM obedece ao padrao `<hostname>.<dominio>`:

| Hostname | FQDN Completo | Alias | IP |
|:---|:---|:---|:---|
| `servidor` | `servidor.grupo3.bsi-26-1.maceio.lab` | `servidor` | `192.168.26.33` |
| `cliente1` | `cliente1.grupo3.bsi-26-1.maceio.lab` | `cliente1` | `192.168.26.34` |
| `cliente2` | `cliente2.grupo3.bsi-26-1.maceio.lab` | `cliente2` | `192.168.26.35` |
| `cliente3` | `cliente3.grupo3.bsi-26-1.maceio.lab` | `cliente3` | `192.168.26.36` |
| `cliente4` | `cliente4.grupo3.bsi-26-1.maceio.lab` | `cliente4` | `192.168.26.37` |
| `cliente5` | `cliente5.grupo3.bsi-26-1.maceio.lab` | `cliente5` | `192.168.26.38` |
| `cliente6` | `cliente6.grupo3.bsi-26-1.maceio.lab` | `cliente6` | `192.168.26.39` |
| `cliente7` | `cliente7.grupo3.bsi-26-1.maceio.lab` | `cliente7` | `192.168.26.40` |

### 3.4 Configuracao de Rede — Netplan

O IP estatico foi configurado editando o arquivo `/etc/netplan/50-cloud-init.yaml` em cada VM. O campo `addresses` e o unico que muda entre as maquinas:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.26.33/28   # alterar para o IP de cada VM (.33 a .40)
    enp0s8:
      dhcp4: yes
```

Apos editar, aplicar as configuracoes:

```bash
sudo netplan apply
```

### 3.5 Mapeamento IP/Nomes — Arquivo `/etc/hosts`

O mapeamento foi adicionado ao arquivo `/etc/hosts` de **todas as 8 VMs**:

```bash
sudo nano /etc/hosts
```

Conteudo adicionado em cada maquina:

```
192.168.26.33  servidor.grupo3.bsi-26-1.maceio.lab  servidor
192.168.26.34  cliente1.grupo3.bsi-26-1.maceio.lab  cliente1
192.168.26.35  cliente2.grupo3.bsi-26-1.maceio.lab  cliente2
192.168.26.36  cliente3.grupo3.bsi-26-1.maceio.lab  cliente3
192.168.26.37  cliente4.grupo3.bsi-26-1.maceio.lab  cliente4
192.168.26.38  cliente5.grupo3.bsi-26-1.maceio.lab  cliente5
192.168.26.39  cliente6.grupo3.bsi-26-1.maceio.lab  cliente6
192.168.26.40  cliente7.grupo3.bsi-26-1.maceio.lab  cliente7
```

### 3.6 Hostname Configurado no Sistema Operacional

O hostname de cada VM foi definido com o comando `hostnamectl`:

```bash
# Exemplo — VM servidor:
sudo hostnamectl set-hostname servidor.grupo3.bsi-26-1.maceio.lab

# Verificacao:
hostnamectl
```

---

## Secao 4 — Usuarios e Acessos

Em cada uma das 8 VMs foram criados o usuario administrador (`servidor` — senha: `adminifal`) e os usuarios individuais de todos os integrantes do grupo, conforme a lista oficial da disciplina.

### 4.1 Tabela de Usuarios

| Usuario | Nome Completo | Perfil | Senha |
|:---|:---|:---|:---|
| `servidor` | Administrador | Admin (root) | `adminifal` |
| `pedro.henrique` | Pedro Henrique | Integrante — sudo | definida no adduser |
| `wallex.kaua` | Wallex Kaua | Integrante — sudo | definida no adduser |
| `marcelo.alves` | Marcelo Alves | Integrante — sudo | definida no adduser |
| `werython.rocha` | Werython Rocha | Integrante — sudo | definida no adduser |

### 4.2 Comandos de Criacao dos Usuarios

Executados em cada VM:

```bash
# Criar usuarios dos integrantes
sudo adduser pedro.henrique
sudo adduser wallex.kaua
sudo adduser marcelo.alves
sudo adduser werython.rocha

# Conceder privilegios administrativos
sudo usermod -aG sudo pedro.henrique
sudo usermod -aG sudo wallex.kaua
sudo usermod -aG sudo marcelo.alves
sudo usermod -aG sudo werython.rocha
```

### 4.3 Verificacao dos Usuarios

```bash
# Listar usuarios criados
cat /etc/passwd | grep -E 'pedro|wallex|marcelo|werython|servidor'

# Verificar grupos de um usuario
groups pedro.henrique
```

---

## Secao 5 — Testes e Evidencias

### 5.1 Testes de Ping por Endereco IP

Todos os testes foram executados a partir da VM **`servidor` (192.168.26.33)**, enviando pacotes ICMP para cada uma das demais 7 VMs. Resultado em todos os casos: **0% de perda de pacotes**.

---

#### 5.1.1 servidor → cliente1 (192.168.26.34)

![Ping para cliente1](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente1.jpeg)

> **Figura 2 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT medio: 2,853 ms

---

#### 5.1.2 servidor → cliente2 (192.168.26.35)

![Ping para cliente2](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente2.jpeg)

> **Figura 3 —** 3 pacotes transmitidos · 3 recebidos · **0% de perda** · RTT medio: 1,419 ms

---

#### 5.1.3 servidor → cliente3 (192.168.26.36)

![Ping para cliente3](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente3.jpeg)

> **Figura 4 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT medio: 0,945 ms

---

#### 5.1.4 servidor → cliente4 (192.168.26.37)

![Ping para cliente4](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente4.jpeg)

> **Figura 5 —** 3 pacotes transmitidos · 3 recebidos · **0% de perda** · RTT medio: 1,134 ms

---

#### 5.1.5 servidor → cliente5 (192.168.26.38)

![Ping para cliente5](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente5.jpeg)

> **Figura 6 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT medio: 5,118 ms
>
> *Nota: o RTT maximo de 17,710 ms no 3. pacote e pontual, decorrente de variacao de carga na VM. Os demais pacotes apresentaram latencia regular.*

---

#### 5.1.6 servidor → cliente6 (192.168.26.39)

![Ping para cliente6](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente%206.jpeg)

> **Figura 7 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT medio: 0,935 ms

---

#### 5.1.7 servidor → cliente7 (192.168.26.40)

![Ping para cliente7](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ping%20cliente%207.jpeg)

> **Figura 8 —** 4 pacotes transmitidos · 4 recebidos · **0% de perda** · RTT medio: 0,833 ms

---

### 5.2 Tabela de Resultados dos Testes de Ping

| Origem | Destino | IP Destino | Enviados | Recebidos | Perda | RTT min. | RTT med. | RTT max. |
|:---|:---|:---|:---:|:---:|:---:|:---|:---|:---|
| servidor | cliente1 | 192.168.26.34 | 4 | 4 | **0%** | 0,611 ms | 2,853 ms | 9,165 ms |
| servidor | cliente2 | 192.168.26.35 | 3 | 3 | **0%** | 0,760 ms | 1,419 ms | 2,679 ms |
| servidor | cliente3 | 192.168.26.36 | 4 | 4 | **0%** | 0,696 ms | 0,945 ms | 1,583 ms |
| servidor | cliente4 | 192.168.26.37 | 3 | 3 | **0%** | 0,737 ms | 1,134 ms | 1,819 ms |
| servidor | cliente5 | 192.168.26.38 | 4 | 4 | **0%** | 0,684 ms | 5,118 ms | 17,710 ms |
| servidor | cliente6 | 192.168.26.39 | 4 | 4 | **0%** | 0,699 ms | 0,935 ms | 1,472 ms |
| servidor | cliente7 | 192.168.26.40 | 4 | 4 | **0%** | 0,566 ms | 0,833 ms | 1,330 ms |

### 5.3 Testes de Ping por FQDN

Alem dos testes por IP, a resolucao de nomes via FQDN e alias foi verificada a partir da VM `servidor`:

```bash
# Por FQDN completo
ping -c 4 cliente1.grupo3.bsi-26-1.maceio.lab
ping -c 4 cliente2.grupo3.bsi-26-1.maceio.lab
ping -c 4 cliente3.grupo3.bsi-26-1.maceio.lab

# Por alias curto
ping -c 4 cliente4
ping -c 4 cliente5
```

Saida esperada:

```
PING cliente1.grupo3.bsi-26-1.maceio.lab (192.168.26.34) 56(84) bytes of data.
64 bytes from cliente1.grupo3.bsi-26-1.maceio.lab (192.168.26.34): icmp_seq=1 ttl=64
--- cliente1.grupo3.bsi-26-1.maceio.lab ping statistics ---
4 packets transmitted, 4 received, 0% packet loss
```

---

### 5.4 Acesso Remoto — SSH

O acesso SSH foi validado utilizando os usuarios criados na Secao 4. Os testes foram realizados pelo terminal e pelo aplicativo **Termius**, com a porta `2222` em todos os hosts.

#### 5.4.1 Hosts cadastrados no Termius

![Hosts no Termius](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/Hosts%20Termius.jpeg)

> **Figura 9 —** Painel de hosts no Termius exibindo as 8 VMs do Grupo 3. Credenciais compartilhadas entre todos os hosts.

#### 5.4.2 Configuracao de conexao SSH no Termius

![Configuracao SSH no Termius](https://raw.githubusercontent.com/Marceloafz/projetoredes-final/main/ssh%20via%20termius.jpeg)

> **Figura 10 —** Configuracao de host no Termius: campo *Address* com o IP da VM, porta `2222`, usuario `servidor`.

#### 5.4.3 Parametros de Conexao SSH

| Parametro | Valor |
|:---|:---|
| Address | IP da VM destino (ex: `192.168.26.34`) |
| Porta SSH | `2222` |
| Usuario | `servidor` |
| Autenticacao | Senha (`adminifal`) |
| Credenciais | Compartilhadas entre os 8 hosts |

#### 5.4.4 Acesso SSH por Endereco IP

```bash
# Sintaxe geral
ssh <usuario>@<ip-da-vm> -p 2222

# Exemplos com usuarios dos integrantes
ssh pedro.henrique@192.168.26.34 -p 2222
ssh wallex.kaua@192.168.26.35 -p 2222
ssh marcelo.alves@192.168.26.36 -p 2222
ssh werython.rocha@192.168.26.37 -p 2222
```

Saida esperada apos login bem-sucedido:

```
pedro.henrique@192.168.26.34's password:
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0 x86_64)

pedro.henrique@cliente1:~$ hostname
cliente1.grupo3.bsi-26-1.maceio.lab
pedro.henrique@cliente1:~$ whoami
pedro.henrique
pedro.henrique@cliente1:~$ exit
logout
Connection to 192.168.26.34 closed.
```

#### 5.4.5 Acesso SSH por Hostname e FQDN

```bash
# Por hostname curto
ssh wallex.kaua@cliente2 -p 2222

# Por FQDN completo
ssh marcelo.alves@cliente3.grupo3.bsi-26-1.maceio.lab -p 2222
ssh werython.rocha@cliente4.grupo3.bsi-26-1.maceio.lab -p 2222
```

#### 5.4.6 Tabela de Resultados dos Testes de SSH

| Origem | Destino | Metodo | Usuario | Resultado |
|:---|:---|:---|:---|:---|
| servidor | cliente1 | SSH por IP (`192.168.26.34`) | `pedro.henrique` | OK — login efetuado |
| servidor | cliente2 | SSH por FQDN | `wallex.kaua` | OK — login efetuado |
| servidor | cliente3 | SSH por alias curto | `marcelo.alves` | OK — login efetuado |
| servidor | cliente4 | SSH por FQDN | `werython.rocha` | OK — login efetuado |
| servidor | cliente5 | SSH via Termius | `servidor` | OK — login efetuado |
| servidor | cliente6 | SSH via Termius | `servidor` | OK — login efetuado |
| servidor | cliente7 | SSH via Termius | `servidor` | OK — login efetuado |

---

## Estrutura do Repositorio

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
**Grupo 3** · `grupo3.bsi-26-1.maceio.lab` · [github.com/Marceloafz/projetoredes-final](https://github.com/Marceloafz/projetoredes-final)

</div>
