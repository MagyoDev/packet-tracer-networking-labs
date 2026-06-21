# Lab 01 — Configuração Básica de Switch no Cisco Packet Tracer

Este laboratório apresenta a configuração inicial de switches Cisco utilizando o Cisco Packet Tracer.

O objetivo é montar uma topologia simples com dois switches e dois PCs, aplicar configurações básicas de gerenciamento e segurança, configurar IP de gerenciamento via SVI, testar conectividade e validar o funcionamento da rede com comandos de verificação.

---

## Objetivo

Praticar a configuração inicial de switches Cisco, incluindo:

- Acesso ao modo privilegiado
- Configuração de hostname
- Senha de modo privilegiado
- Senhas para console e VTY
- Criptografia de senhas
- Configuração de SVI para gerenciamento
- Gateway padrão do switch
- Configuração de interfaces físicas
- Descrição de portas
- Velocidade e duplex
- Salvamento da configuração
- Testes de conectividade
- Comandos de verificação e troubleshooting

---

## Topologia

```text
[PC1] --- [SW1] --- [SW2] --- [PC2]
         192.168.1.0/24
```

---

## Dispositivos utilizados

|Dispositivo|Quantidade|Modelo|
|---|---|---|
|Switch|2|Cisco 2960|
|PC|2|End Device|

---

## Endereçamento IP

|Dispositivo|Endereço IP|Máscara|Gateway|
|---|---|---|---|
|SW1|192.168.1.10|255.255.255.0|192.168.1.1|
|SW2|192.168.1.20|255.255.255.0|192.168.1.1|
|PC1|192.168.1.100|255.255.255.0|192.168.1.1|
|PC2|192.168.1.101|255.255.255.0|192.168.1.1|

---

# Configuração do SW1

## Acessando o modo de configuração

```bash
enable
configure terminal
hostname SW1
```

|Comando|Explicação|
|---|---|
|`enable`|Entra no modo EXEC privilegiado, permitindo visualizar e alterar configurações avançadas.|
|`configure terminal`|Entra no modo de configuração global do switch.|
|`hostname SW1`|Define o nome do switch como `SW1`, facilitando a identificação do dispositivo durante a administração.|

---

## Configurando senhas de acesso

```bash
enable secret cisco
```

|Comando|Explicação|
|---|---|
|`enable secret cisco`|Define a senha do modo privilegiado como `cisco`. Essa senha é armazenada de forma criptografada na configuração.|

---

## Configurando acesso via console

```bash
line console 0
password cisco
login
exit
```

|Comando|Explicação|
|---|---|
|`line console 0`|Acessa a configuração da linha de console, usada para acesso local ao switch.|
|`password cisco`|Define a senha `cisco` para acesso via console.|
|`login`|Obriga o switch a solicitar a senha configurada ao acessar a linha console.|
|`exit`|Sai do modo de configuração da linha console.|

---

## Configurando acesso remoto via VTY

```bash
line vty 0 4
password cisco
login
exit
```

|Comando|Explicação|
|---|---|
|`line vty 0 4`|Acessa as linhas virtuais VTY, usadas para acesso remoto via Telnet ou SSH.|
|`password cisco`|Define a senha para acesso remoto.|
|`login`|Ativa a solicitação de senha nas conexões remotas.|
|`exit`|Sai da configuração das linhas VTY.|

---

## Criptografando senhas em texto claro

```bash
service password-encryption
```

|Comando|Explicação|
|---|---|
|`service password-encryption`|Criptografa senhas simples que aparecem no arquivo de configuração, como senhas de console e VTY.|

> [!NOTE]  
> O comando `enable secret` já utiliza criptografia mais forte. O `service password-encryption` protege principalmente senhas simples configuradas com o comando `password`.

---

## Configurando a SVI para gerenciamento

```bash
interface vlan 1
ip address 192.168.1.10 255.255.255.0
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface vlan 1`|Acessa a interface virtual da VLAN 1, chamada de SVI.|
|`ip address 192.168.1.10 255.255.255.0`|Define o IP de gerenciamento do switch.|
|`no shutdown`|Ativa a interface virtual, caso ela esteja administrativamente desligada.|
|`exit`|Sai da configuração da interface VLAN.|

> [!NOTE]  
> Um switch de camada 2 não precisa de IP para encaminhar quadros Ethernet. O IP na SVI é usado para gerenciamento remoto, testes e administração do equipamento.

---

## Configurando o gateway padrão do switch

```bash
ip default-gateway 192.168.1.1
```

|Comando|Explicação|
|---|---|
|`ip default-gateway 192.168.1.1`|Define o gateway padrão usado pelo switch para se comunicar com redes externas.|

> [!NOTE]  
> Mesmo sendo um switch de camada 2, ele precisa de gateway se for gerenciado a partir de outra rede.

---

## Configurando a porta de ligação com o SW2

```bash
interface FastEthernet0/24
duplex full
speed 100
description Link para SW2
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/24`|Acessa a porta física FastEthernet0/24.|
|`duplex full`|Configura a interface para enviar e receber dados simultaneamente.|
|`speed 100`|Define a velocidade da porta como 100 Mbps.|
|`description Link para SW2`|Adiciona uma descrição administrativa à interface.|
|`no shutdown`|Garante que a porta esteja ativa.|
|`exit`|Sai da configuração da interface.|

> [!TIP]  
> Usar descrições nas interfaces facilita a administração e o troubleshooting, principalmente em redes com muitos links.

---

## Configurando várias portas de acesso

```bash
interface range FastEthernet0/1 - 10
description Portas de acesso para usuarios
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface range FastEthernet0/1 - 10`|Seleciona um intervalo de portas para configurar várias interfaces ao mesmo tempo.|
|`description Portas de acesso para usuarios`|Adiciona uma descrição nas portas selecionadas.|
|`no shutdown`|Ativa todas as interfaces selecionadas.|
|`exit`|Sai da configuração do intervalo de interfaces.|

---

## Salvando a configuração

```bash
copy running-config startup-config
```

|Comando|Explicação|
|---|---|
|`copy running-config startup-config`|Salva a configuração atual na memória NVRAM, garantindo que ela seja mantida após reiniciar o switch.|

> [!WARNING]  
> A configuração ativa fica no `running-config`, que está na RAM. Se o equipamento reiniciar sem salvar, as alterações são perdidas.

---

# Configuração do SW2

A configuração do SW2 segue a mesma lógica do SW1, alterando apenas o hostname, o IP da SVI e a descrição da porta de uplink.

```bash
enable
configure terminal
hostname SW2

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

interface vlan 1
ip address 192.168.1.20 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.1.1

interface FastEthernet0/24
duplex full
speed 100
description Link para SW1
no shutdown
exit

copy running-config startup-config
```

## Diferenças em relação ao SW1

|Item|SW1|SW2|
|---|---|---|
|Hostname|`SW1`|`SW2`|
|IP da SVI|`192.168.1.10`|`192.168.1.20`|
|Descrição da porta Fa0/24|`Link para SW2`|`Link para SW1`|

---

# Configuração dos PCs

Os PCs foram configurados manualmente na aba:

```text
Desktop > IP Configuration
```

|Dispositivo|IP|Máscara|Gateway|
|---|---|---|---|
|PC1|192.168.1.100|255.255.255.0|192.168.1.1|
|PC2|192.168.1.101|255.255.255.0|192.168.1.1|

---

# Testes de conectividade

A partir do PC1, foram realizados testes de conectividade com o PC2 e com os IPs de gerenciamento dos switches.

```bash
ping 192.168.1.101
ping 192.168.1.10
ping 192.168.1.20
```

|Comando|Explicação|
|---|---|
|`ping 192.168.1.101`|Testa a conectividade entre PC1 e PC2.|
|`ping 192.168.1.10`|Testa a conectividade entre PC1 e o IP de gerenciamento do SW1.|
|`ping 192.168.1.20`|Testa a conectividade entre PC1 e o IP de gerenciamento do SW2.|

Resultado esperado:

```text
Reply from 192.168.1.101
Reply from 192.168.1.10
Reply from 192.168.1.20
```

> [!TIP]  
> O primeiro ping pode falhar porque o switch ainda precisa aprender os endereços MAC dos dispositivos. Após gerar tráfego, a tabela MAC é populada e a comunicação tende a funcionar normalmente.

---

# Comandos de verificação

## Verificar tabela MAC

```bash
show mac address-table
show mac address-table dynamic
```

|Comando|Explicação|
|---|---|
|`show mac address-table`|Exibe a tabela de endereços MAC aprendidos pelo switch.|
|`show mac address-table dynamic`|Mostra apenas os endereços MAC aprendidos dinamicamente.|

A tabela MAC indica por qual interface o switch aprendeu cada dispositivo conectado.

---

## Verificar status das interfaces

```bash
show interfaces status
```

|Comando|Explicação|
|---|---|
|`show interfaces status`|Mostra um resumo das interfaces, incluindo status, VLAN, duplex e velocidade.|

Esse comando ajuda a identificar portas conectadas, desconectadas ou com configurações incorretas.

---

## Verificar uma interface específica

```bash
show interfaces FastEthernet0/24
```

|Comando|Explicação|
|---|---|
|`show interfaces FastEthernet0/24`|Exibe informações detalhadas da interface Fa0/24, incluindo status, pacotes, erros, velocidade e duplex.|

---

## Verificar a configuração ativa

```bash
show running-config
```

|Comando|Explicação|
|---|---|
|`show running-config`|Exibe a configuração atualmente em execução na memória RAM.|

Esse comando permite conferir se hostname, senhas, interfaces e SVI foram configurados corretamente.

---

## Verificar a SVI

```bash
show interfaces vlan 1
```

|Comando|Explicação|
|---|---|
|`show interfaces vlan 1`|Mostra o estado da interface virtual VLAN 1.|

O ideal é que a SVI apareça como `up/up`.

---

## Verificar tabela MAC por VLAN

```bash
show mac address-table dynamic vlan 1
```

|Comando|Explicação|
|---|---|
|`show mac address-table dynamic vlan 1`|Mostra os endereços MAC aprendidos dinamicamente na VLAN 1.|

---

# Troubleshooting

|Problema|Possível causa|Solução|
|---|---|---|
|Ping entre PCs falha|Cabo incorreto ou porta errada|Verificar conexões e usar `show interfaces status`|
|SVI aparece como `down/down`|VLAN sem porta ativa associada|Verificar se existe alguma porta ativa na VLAN 1|
|Interface aparece como `administratively down`|Porta desligada manualmente|Entrar na interface e aplicar `no shutdown`|
|Senha não é solicitada|Comando `login` ausente|Configurar `login` nas linhas console ou VTY|
|Configuração sumiu após reinício|Configuração não foi salva|Executar `copy running-config startup-config`|
|MAC não aparece na tabela|Nenhum tráfego foi gerado|Executar ping entre os dispositivos|

---

