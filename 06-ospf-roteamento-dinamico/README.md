# Lab 06 — OSPF no Cisco Packet Tracer

Este laboratório apresenta a configuração do protocolo OSPF em uma topologia com dois roteadores, dois switches e dois PCs utilizando o Cisco Packet Tracer.

O objetivo é substituir rotas estáticas por roteamento dinâmico, permitindo que os roteadores aprendam automaticamente as redes remotas por meio do OSPF.

---

## Objetivo

Praticar conceitos fundamentais de roteamento dinâmico com OSPF, incluindo:

- Configuração básica de roteadores
- Configuração de interfaces LAN e WAN
- Configuração de interface loopback
- Definição de Router ID
- Ativação do processo OSPF
- Anúncio de redes com wildcard mask
- Formação de adjacência entre roteadores
- Verificação da tabela de roteamento
- Testes de conectividade
- Uso de comandos de verificação e troubleshooting
- Exploração de vizinhança com CDP

---

## Topologia

```text
[PC1] --- [SW1] --- [R1] --- Serial --- [R2] --- [SW2] --- [PC2]

LAN 1: 192.168.1.0/24
WAN:   10.0.0.0/30
LAN 2: 192.168.2.0/24
```

---

## Dispositivos utilizados

|Dispositivo|Quantidade|Modelo|
|---|---|---|
|Roteador|2|Router-PT|
|Switch|2|Cisco 2960|
|PC|2|End Device|

---

## Endereçamento IP

|Dispositivo|Interface|Endereço IP|Máscara|Função|
|---|---|---|---|---|
|R1|FastEthernet0/0|192.168.1.1|255.255.255.0|Gateway da LAN 1|
|R1|Serial2/0|10.0.0.1|255.255.255.252|Link WAN|
|R1|Loopback0|1.1.1.1|255.255.255.255|Router ID|
|R2|FastEthernet0/0|192.168.2.1|255.255.255.0|Gateway da LAN 2|
|R2|Serial2/0|10.0.0.2|255.255.255.252|Link WAN|
|R2|Loopback0|2.2.2.2|255.255.255.255|Router ID|
|PC1|FastEthernet0|192.168.1.10|255.255.255.0|Host da LAN 1|
|PC2|FastEthernet0|192.168.2.10|255.255.255.0|Host da LAN 2|

---

## Gateways dos PCs

|PC|Gateway padrão|
|---|---|
|PC1|192.168.1.1|
|PC2|192.168.2.1|

---

# Conceito principal

O OSPF, Open Shortest Path First, é um protocolo de roteamento dinâmico do tipo link-state.

Diferente das rotas estáticas, em que o administrador configura manualmente cada caminho, o OSPF permite que os roteadores troquem informações entre si e aprendam automaticamente as redes disponíveis.

Neste laboratório, o OSPF será usado para que:

- R1 aprenda a rede `192.168.2.0/24`
- R2 aprenda a rede `192.168.1.0/24`
- Os PCs de redes diferentes consigam se comunicar

---

# Configuração do R1

## Configuração básica

```bash
enable
configure terminal
hostname R1
```

|Comando|Explicação|
|---|---|
|`enable`|Entra no modo EXEC privilegiado.|
|`configure terminal`|Entra no modo de configuração global.|
|`hostname R1`|Define o nome do roteador como `R1`.|

---

## Configurando senhas

```bash
enable secret cisco

line console 0
password cisco
login
exit

line vty 0 4
password cisco
login
exit

service password-encryption
```

|Comando|Explicação|
|---|---|
|`enable secret cisco`|Define a senha do modo privilegiado.|
|`line console 0`|Acessa a linha de console, usada para acesso local.|
|`password cisco`|Define a senha da linha atual.|
|`login`|Faz o roteador solicitar a senha configurada.|
|`line vty 0 4`|Acessa as linhas virtuais usadas para acesso remoto.|
|`service password-encryption`|Criptografa senhas simples no arquivo de configuração.|

---

## Configurando a interface LAN do R1

```bash
interface FastEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/0`|Acessa a interface conectada à LAN 1.|
|`ip address 192.168.1.1 255.255.255.0`|Define o IP da interface, que será o gateway do PC1.|
|`no shutdown`|Ativa a interface.|
|`exit`|Sai do modo de configuração da interface.|

---

## Configurando a interface WAN do R1

```bash
interface Serial2/0
ip address 10.0.0.1 255.255.255.252
clock rate 64000
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface Serial2/0`|Acessa a interface serial conectada ao R2.|
|`ip address 10.0.0.1 255.255.255.252`|Define o IP do R1 no link WAN.|
|`clock rate 64000`|Define o clock do link serial no lado DCE.|
|`no shutdown`|Ativa a interface serial.|

> [!NOTE]  
> O comando `clock rate` deve ser usado apenas no lado DCE do cabo serial.

---

## Configurando a interface Loopback do R1

```bash
interface loopback 0
ip address 1.1.1.1 255.255.255.255
exit
```

|Comando|Explicação|
|---|---|
|`interface loopback 0`|Cria uma interface lógica Loopback0.|
|`ip address 1.1.1.1 255.255.255.255`|Define o IP da loopback. Esse endereço será usado como Router ID do R1.|
|`exit`|Sai da configuração da interface.|

> [!NOTE]  
> A loopback é usada porque permanece ativa independentemente do estado das interfaces físicas. Isso torna o Router ID mais estável.

---

## Configurando o OSPF no R1

```bash
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0
network 1.1.1.1 0.0.0.0 area 0
exit
```

|Comando|Explicação|
|---|---|
|`router ospf 1`|Ativa o processo OSPF de número 1.|
|`network 192.168.1.0 0.0.0.255 area 0`|Anuncia a rede LAN 1 no OSPF.|
|`network 10.0.0.0 0.0.0.3 area 0`|Anuncia o link WAN entre R1 e R2.|
|`network 1.1.1.1 0.0.0.0 area 0`|Anuncia a interface loopback do R1.|
|`area 0`|Define que as redes fazem parte da área backbone do OSPF.|

> [!IMPORTANT]  
> No comando `network` do OSPF, é usada a wildcard mask, não a máscara de sub-rede tradicional.

---

## Salvando a configuração do R1

```bash
exit
copy running-config startup-config
```

|Comando|Explicação|
|---|---|
|`exit`|Sai do modo de configuração.|
|`copy running-config startup-config`|Salva a configuração ativa na NVRAM.|

---

# Configuração do R2

A configuração do R2 segue a mesma lógica do R1, alterando os IPs das interfaces e as redes anunciadas no OSPF.

```bash
enable
configure terminal
hostname R2

enable secret cisco

line console 0
password cisco
login
exit

line vty 0 4
password cisco
login
exit

service password-encryption

interface FastEthernet0/0
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

interface Serial2/0
ip address 10.0.0.2 255.255.255.252
no shutdown
exit

interface loopback 0
ip address 2.2.2.2 255.255.255.255
exit

router ospf 1
network 192.168.2.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0
network 2.2.2.2 0.0.0.0 area 0
exit

exit
copy running-config startup-config
```

---

## Diferenças entre R1 e R2

|Item|R1|R2|
|---|---|---|
|LAN|192.168.1.1/24|192.168.2.1/24|
|WAN|10.0.0.1/30|10.0.0.2/30|
|Loopback|1.1.1.1/32|2.2.2.2/32|
|Clock rate|Configurado no lado DCE|Não configurado|
|Rede LAN anunciada|192.168.1.0/24|192.168.2.0/24|

---

# Configuração dos PCs

Os PCs foram configurados manualmente na aba:

```text
Desktop > IP Configuration
```

## PC1

|Campo|Valor|
|---|---|
|IP Address|192.168.1.10|
|Subnet Mask|255.255.255.0|
|Default Gateway|192.168.1.1|

## PC2

|Campo|Valor|
|---|---|
|IP Address|192.168.2.10|
|Subnet Mask|255.255.255.0|
|Default Gateway|192.168.2.1|

---

# Testando conectividade

Após configurar o OSPF, teste a comunicação entre PC1 e PC2.

No PC1:

```bash
ping 192.168.2.10
```

No PC2:

```bash
ping 192.168.1.10
```

|Comando|Explicação|
|---|---|
|`ping 192.168.2.10`|Testa a comunicação do PC1 com o PC2, que está em outra rede.|
|`ping 192.168.1.10`|Testa a comunicação do PC2 com o PC1.|

Resultado esperado:

```text
Reply from 192.168.2.10
Reply from 192.168.1.10
```

> [!NOTE]  
> O OSPF pode levar alguns segundos para formar adjacência, trocar informações e instalar as rotas na tabela de roteamento.

---

# Verificando adjacência OSPF

Em R1 ou R2, execute:

```bash
show ip ospf neighbor
```

|Comando|Explicação|
|---|---|
|`show ip ospf neighbor`|Mostra os roteadores vizinhos que formaram adjacência OSPF.|

Resultado esperado:

```text
FULL
```

O estado `FULL` indica que os roteadores formaram adjacência e sincronizaram suas informações de roteamento.

---

# Verificando rotas OSPF

Em R1 ou R2, execute:

```bash
show ip route
```

|Comando|Explicação|
|---|---|
|`show ip route`|Exibe a tabela de roteamento do roteador.|

Rotas aprendidas por OSPF aparecem com a letra:

```text
O
```

No R1, deve aparecer uma rota OSPF para:

```text
192.168.2.0/24
```

No R2, deve aparecer uma rota OSPF para:

```text
192.168.1.0/24
```

---

# Verificando o processo OSPF

```bash
show ip ospf
```

|Comando|Explicação|
|---|---|
|`show ip ospf`|Mostra informações do processo OSPF, incluindo Router ID, área e timers.|

O Router ID esperado é:

|Roteador|Router ID esperado|
|---|---|
|R1|1.1.1.1|
|R2|2.2.2.2|

---

# Verificando o banco de dados OSPF

```bash
show ip ospf database
```

|Comando|Explicação|
|---|---|
|`show ip ospf database`|Exibe o banco de dados de estado de enlace do OSPF.|

Esse comando mostra as informações de topologia que o roteador usa para calcular as melhores rotas.

---

# Verificando interfaces OSPF

```bash
show ip ospf interface
```

|Comando|Explicação|
|---|---|
|`show ip ospf interface`|Mostra quais interfaces estão participando do OSPF e detalhes como custo, área e timers.|

---

# Explorando vizinhança com CDP

O CDP, Cisco Discovery Protocol, permite visualizar dispositivos Cisco diretamente conectados.

## Listar vizinhos diretos

```bash
show cdp neighbors
```

|Comando|Explicação|
|---|---|
|`show cdp neighbors`|Lista dispositivos Cisco diretamente conectados ao roteador.|

---

## Ver detalhes dos vizinhos

```bash
show cdp neighbors detail
```

|Comando|Explicação|
|---|---|
|`show cdp neighbors detail`|Mostra informações detalhadas dos vizinhos, como IP, modelo e versão do IOS.|

---

## Desativar CDP em uma interface

```bash
interface Serial2/0
no cdp enable
```

|Comando|Explicação|
|---|---|
|`interface Serial2/0`|Acessa a interface serial.|
|`no cdp enable`|Desativa o CDP apenas naquela interface.|

> [!NOTE]  
> Desativar CDP pode ser útil em links externos, onde não é desejável expor informações do dispositivo.

---

# Comandos de verificação

|Comando|Função|
|---|---|
|`show ip interface brief`|Verifica IPs e estado das interfaces.|
|`show ip route`|Exibe a tabela de roteamento.|
|`show ip ospf neighbor`|Verifica vizinhos OSPF e estado da adjacência.|
|`show ip ospf`|Mostra detalhes do processo OSPF e Router ID.|
|`show ip ospf database`|Exibe o banco de dados OSPF.|
|`show ip ospf interface`|Mostra interfaces participando do OSPF.|
|`show running-config`|Exibe a configuração atual do roteador.|
|`show cdp neighbors`|Lista vizinhos Cisco diretamente conectados.|
|`show cdp neighbors detail`|Mostra detalhes dos vizinhos Cisco.|

---

# Troubleshooting

|Problema|Possível causa|Solução|
|---|---|---|
|Interface aparece como `administratively down`|Faltou `no shutdown`|Ativar a interface com `no shutdown`|
|Link serial não sobe|Faltou `clock rate` no lado DCE|Configurar `clock rate 64000` no lado DCE|
|Vizinho OSPF não aparece|Rede não anunciada ou interface errada|Verificar comandos `network`|
|Estado OSPF diferente de `FULL`|Adjacência não completou|Verificar IPs, máscaras e conectividade|
|Rota OSPF não aparece|OSPF não aprendeu a rede remota|Verificar `show ip ospf neighbor` e `show ip route`|
|Router ID diferente do esperado|Loopback não configurada ou OSPF iniciado antes|Conferir loopback e processo OSPF|
|Ping entre PCs falha|Gateway, rota ou OSPF incorreto|Verificar gateways, adjacência e tabela de rotas|
|Wildcard mask incorreta|Erro no comando `network`|Corrigir a wildcard mask da rede anunciada|

---
