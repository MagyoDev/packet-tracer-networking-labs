# Lab 02 — Roteamento Estático no Cisco Packet Tracer

Este laboratório apresenta a configuração de uma topologia com dois roteadores, dois switches e dois PCs utilizando o Cisco Packet Tracer.

O objetivo é configurar duas redes locais diferentes, conectadas por um link WAN serial entre roteadores, e permitir a comunicação entre elas por meio de rotas estáticas.

---

## Objetivo

Praticar a configuração básica de roteadores Cisco, incluindo:

- Configuração de hostname
- Senhas de acesso
- Criptografia de senhas
- Configuração de interfaces LAN
- Configuração de interface WAN serial
- Uso de máscara `/30` em link ponto a ponto
- Configuração de clock rate no lado DCE
- Configuração de rotas estáticas
- Configuração de gateways nos PCs
- Testes de conectividade
- Comandos de verificação e troubleshooting

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

|Dispositivo|Interface|Endereço IP|Máscara|Gateway|
|---|---|---|---|---|
|R1|FastEthernet0/0|192.168.1.1|255.255.255.0|—|
|R1|Serial2/0|10.0.0.1|255.255.255.252|—|
|R2|FastEthernet0/0|192.168.2.1|255.255.255.0|—|
|R2|Serial2/0|10.0.0.2|255.255.255.252|—|
|PC1|FastEthernet0|192.168.1.10|255.255.255.0|192.168.1.1|
|PC2|FastEthernet0|192.168.2.10|255.255.255.0|192.168.2.1|

---

# Configuração do R1

## Acessando o modo de configuração

```bash
enable
configure terminal
hostname R1
```

|Comando|Explicação|
|---|---|
|`enable`|Entra no modo EXEC privilegiado do roteador.|
|`configure terminal`|Entra no modo de configuração global.|
|`hostname R1`|Define o nome do roteador como `R1`, facilitando a identificação durante a configuração.|

---

## Configurando senhas de acesso

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
|`enable secret cisco`|Define a senha do modo privilegiado como `cisco`, armazenada de forma criptografada.|
|`line console 0`|Acessa a configuração da linha de console, usada para acesso local.|
|`password cisco`|Define a senha da linha atual.|
|`login`|Faz o roteador solicitar a senha configurada ao acessar a linha.|
|`line vty 0 4`|Acessa as linhas virtuais usadas para acesso remoto via Telnet ou SSH.|
|`service password-encryption`|Criptografa senhas simples que aparecem no arquivo de configuração.|

> [!NOTE]  
> O comando `enable secret` já utiliza criptografia mais forte. O `service password-encryption` protege principalmente senhas simples configuradas com `password`.

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
|`interface FastEthernet0/0`|Acessa a interface do roteador conectada ao switch da LAN 1.|
|`ip address 192.168.1.1 255.255.255.0`|Define o IP da interface. Esse endereço será o gateway do PC1.|
|`no shutdown`|Ativa a interface, que por padrão vem desligada em roteadores Cisco.|
|`exit`|Sai do modo de configuração da interface.|

> [!NOTE]  
> O IP `192.168.1.1` será usado pelo PC1 como gateway padrão para acessar outras redes.

---

## Configurando a interface WAN serial do R1

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
|`ip address 10.0.0.1 255.255.255.252`|Define o IP do R1 no link WAN entre os roteadores.|
|`clock rate 64000`|Define o clock do link serial no lado DCE.|
|`no shutdown`|Ativa a interface serial.|
|`exit`|Sai da configuração da interface.|

> [!IMPORTANT]  
> O comando `clock rate` só deve ser configurado no lado DCE do cabo serial. No Packet Tracer, é possível identificar o lado DCE passando o mouse sobre o cabo conectado ao roteador.

> [!NOTE]  
> A máscara `255.255.255.252`, também conhecida como `/30`, é comum em links ponto a ponto, pois fornece apenas dois endereços IP utilizáveis.

---

## Configurando rota estática no R1

```bash
ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

|Comando|Explicação|
|---|---|
|`ip route`|Cria uma rota estática manualmente.|
|`192.168.2.0`|Rede de destino que o R1 precisa alcançar.|
|`255.255.255.0`|Máscara da rede de destino.|
|`10.0.0.2`|Próximo salto, ou seja, o IP da interface serial do R2.|

Essa rota informa ao R1 que, para alcançar a rede `192.168.2.0/24`, os pacotes devem ser enviados para o endereço `10.0.0.2`.

---

## Salvando a configuração do R1

```bash
exit
copy running-config startup-config
```

|Comando|Explicação|
|---|---|
|`exit`|Sai do modo de configuração global.|
|`copy running-config startup-config`|Salva a configuração ativa na NVRAM para que ela não seja perdida após reiniciar o roteador.|

> [!WARNING]  
> Se a configuração não for salva, ela será perdida caso o roteador reinicie.

---

# Configuração do R2

A configuração do R2 segue a mesma lógica do R1, alterando os endereços IP e a rota estática para apontar para a rede oposta.

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

ip route 192.168.1.0 255.255.255.0 10.0.0.1

exit
copy running-config startup-config
```

---

## Diferenças entre R1 e R2

|Item|R1|R2|
|---|---|---|
|Hostname|`R1`|`R2`|
|Interface LAN|`192.168.1.1/24`|`192.168.2.1/24`|
|Interface Serial|`10.0.0.1/30`|`10.0.0.2/30`|
|Clock rate|Configurado no lado DCE|Não configurado|
|Rota estática|Para `192.168.2.0/24` via `10.0.0.2`|Para `192.168.1.0/24` via `10.0.0.1`|

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

> [!NOTE]  
> O gateway padrão de cada PC deve ser o IP da interface do roteador que está na mesma rede local.

---

# Testes de conectividade

A partir do PC1, foi realizado um teste de conectividade com o PC2.

```bash
ping 192.168.2.10
```

|Comando|Explicação|
|---|---|
|`ping 192.168.2.10`|Testa se o PC1 consegue alcançar o PC2, que está em outra rede.|

Resultado esperado:

```text
Reply from 192.168.2.10
```

Também é possível testar os gateways e o link serial:

```bash
ping 192.168.1.1
ping 10.0.0.1
ping 10.0.0.2
ping 192.168.2.10
```

---

# Comandos de verificação

## Verificar interfaces e endereços IP

```bash
show ip interface brief
```

|Comando|Explicação|
|---|---|
|`show ip interface brief`|Mostra um resumo das interfaces, seus endereços IP e seus estados.|

O ideal é que as interfaces configuradas apareçam como:

```text
up    up
```

Se aparecer `administratively down`, significa que faltou aplicar o comando `no shutdown`.

---

## Verificar tabela de roteamento

```bash
show ip route
```

|Comando|Explicação|
|---|---|
|`show ip route`|Exibe a tabela de roteamento do roteador.|

No R1, deve aparecer uma rota estática para a rede `192.168.2.0/24`.

No R2, deve aparecer uma rota estática para a rede `192.168.1.0/24`.

Rotas estáticas aparecem com a letra `S`.

Exemplo:

```text
S    192.168.2.0/24 [1/0] via 10.0.0.2
```

---

## Verificar configuração ativa

```bash
show running-config
```

|Comando|Explicação|
|---|---|
|`show running-config`|Exibe a configuração atual carregada na memória RAM.|

Esse comando permite verificar se as interfaces, senhas e rotas foram configuradas corretamente.

---

## Verificar uma interface específica

```bash
show interfaces Serial2/0
```

|Comando|Explicação|
|---|---|
|`show interfaces Serial2/0`|Mostra informações detalhadas da interface serial, incluindo status, encapsulamento, pacotes e erros.|

Esse comando é útil para verificar problemas no link WAN.

---

# Troubleshooting

|Problema|Possível causa|Solução|
|---|---|---|
|Interface aparece como `administratively down`|Faltou o comando `no shutdown`|Entrar na interface e aplicar `no shutdown`|
|Interface aparece como `down/down`|Cabo desconectado ou porta errada|Verificar conexões físicas no Packet Tracer|
|Link serial não sobe|Faltou `clock rate` no lado DCE|Identificar o lado DCE e configurar `clock rate 64000`|
|PC não alcança o roteador|IP ou gateway incorreto no PC|Conferir endereço IP, máscara e gateway|
|PC1 não pinga PC2|Rota estática ausente ou incorreta|Verificar `show ip route` e corrigir `ip route`|
|Ping chega ao roteador, mas não chega ao outro PC|Gateway do PC remoto incorreto|Conferir gateway padrão dos dois PCs|
|Configuração sumiu após reinício|Configuração não foi salva|Executar `copy running-config startup-config`|

---

# Aprendizados

Neste laboratório, pratiquei a configuração de uma rede com duas LANs interligadas por roteadores Cisco.

Os principais aprendizados foram:

- Configuração básica de roteadores Cisco
- Diferença entre interfaces LAN e WAN
- Uso de IP em interfaces de roteadores
- Importância do comando `no shutdown`
- Uso de máscara `/30` em links ponto a ponto
- Configuração de `clock rate` no lado DCE
- Criação de rotas estáticas
- Verificação da tabela de roteamento
- Testes de conectividade entre redes diferentes
- Troubleshooting básico de roteamento

---

# Ferramentas utilizadas

- Cisco Packet Tracer
- Cisco IOS CLI
- Markdown

---

# Status

Concluído.