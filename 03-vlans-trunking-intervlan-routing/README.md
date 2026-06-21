# Lab 03 — VLANs, Trunking e InterVLAN Routing no Cisco Packet Tracer

Este laboratório apresenta a configuração de VLANs, portas de acesso, links trunk e roteamento entre VLANs utilizando o método Router-on-a-Stick no Cisco Packet Tracer.

O objetivo é segmentar a rede em VLANs diferentes, validar o isolamento entre elas e, em seguida, permitir a comunicação entre as VLANs por meio de um roteador conectado a um switch através de um link trunk.

---

## Objetivo

Praticar conceitos fundamentais de switching e roteamento entre VLANs, incluindo:

- Criação de VLANs
- Nomeação de VLANs
- Configuração de portas de acesso
- Configuração de links trunk
- Segmentação lógica da rede
- Testes de isolamento entre VLANs
- Configuração de Router-on-a-Stick
- Criação de subinterfaces em roteador Cisco
- Encapsulamento IEEE 802.1Q
- Configuração de gateways por VLAN
- Testes de conectividade
- Comandos de verificação e troubleshooting

---

## Topologia

```text
[PC1-VLAN10] ---|               |--- [PC3-VLAN10]
[PC2-VLAN20] ---[SW1]===trunk===[SW2]--- [PC4-VLAN20]
                  |
                 [R1]
          Router-on-a-Stick
```

---

## Dispositivos utilizados

|Dispositivo|Quantidade|Modelo|
|---|---|---|
|Roteador|1|Router-PT|
|Switch|2|Cisco 2960|
|PC|4|End Device|

---

## VLANs utilizadas

|VLAN|Nome|Função|
|---|---|---|
|10|TI|Rede do setor de TI|
|20|RH|Rede do setor de RH|

---

## Endereçamento IP

|Dispositivo|VLAN|Endereço IP|Máscara|Gateway|
|---|---|---|---|---|
|PC1|VLAN 10|192.168.10.10|255.255.255.0|192.168.10.1|
|PC2|VLAN 20|192.168.20.10|255.255.255.0|192.168.20.1|
|PC3|VLAN 10|192.168.10.11|255.255.255.0|192.168.10.1|
|PC4|VLAN 20|192.168.20.11|255.255.255.0|192.168.20.1|
|R1|Subinterface Fa0/0.10|192.168.10.1|255.255.255.0|—|
|R1|Subinterface Fa0/0.20|192.168.20.1|255.255.255.0|—|

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
|`enable`|Entra no modo EXEC privilegiado do switch.|
|`configure terminal`|Entra no modo de configuração global.|
|`hostname SW1`|Define o nome do switch como `SW1`, facilitando a identificação durante a configuração.|

---

## Criando as VLANs no SW1

```bash
vlan 10
name TI
exit

vlan 20
name RH
exit
```

|Comando|Explicação|
|---|---|
|`vlan 10`|Cria a VLAN 10 ou entra no modo de configuração dela, caso já exista.|
|`name TI`|Define o nome da VLAN 10 como `TI`.|
|`vlan 20`|Cria a VLAN 20 ou entra no modo de configuração dela, caso já exista.|
|`name RH`|Define o nome da VLAN 20 como `RH`.|
|`exit`|Sai do modo de configuração da VLAN.|

> [!NOTE]  
> VLANs permitem separar logicamente a rede em domínios de broadcast diferentes, mesmo usando a mesma infraestrutura física de switches.

---

## Configurando portas de acesso no SW1

```bash
interface FastEthernet0/1
switchport mode access
switchport access vlan 10
no shutdown
exit

interface FastEthernet0/2
switchport mode access
switchport access vlan 20
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/1`|Acessa a porta conectada ao PC1.|
|`switchport mode access`|Define a porta como porta de acesso, usada por dispositivos finais como PCs.|
|`switchport access vlan 10`|Associa a porta à VLAN 10.|
|`interface FastEthernet0/2`|Acessa a porta conectada ao PC2.|
|`switchport access vlan 20`|Associa a porta à VLAN 20.|
|`no shutdown`|Garante que a interface esteja ativa.|
|`exit`|Sai do modo de configuração da interface.|

> [!NOTE]  
> Portas em modo access pertencem a apenas uma VLAN. Elas são usadas para conectar dispositivos finais, como computadores, impressoras e servidores.

---

## Configurando o trunk entre SW1 e SW2

```bash
interface FastEthernet0/24
switchport mode trunk
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/24`|Acessa a porta usada para conectar o SW1 ao SW2.|
|`switchport mode trunk`|Configura a porta como trunk, permitindo o tráfego de múltiplas VLANs.|
|`no shutdown`|Ativa a interface.|
|`exit`|Sai do modo de configuração da interface.|

> [!NOTE]  
> O trunk transporta tráfego de várias VLANs pelo mesmo link físico, usando marcação 802.1Q.

---

## Configurando o trunk entre SW1 e R1

```bash
interface FastEthernet0/23
switchport mode trunk
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/23`|Acessa a porta conectada ao roteador R1.|
|`switchport mode trunk`|Permite que o link transporte tráfego das VLANs 10 e 20 até o roteador.|
|`no shutdown`|Ativa a porta.|
|`exit`|Sai da configuração da interface.|

> [!IMPORTANT]  
> Para o Router-on-a-Stick funcionar, o link entre o switch e o roteador precisa estar configurado como trunk.

---

## Salvando a configuração do SW1

```bash
exit
copy running-config startup-config
```

|Comando|Explicação|
|---|---|
|`exit`|Sai do modo de configuração global.|
|`copy running-config startup-config`|Salva a configuração ativa na NVRAM, mantendo as alterações após reiniciar o switch.|

---

# Configuração do SW2

A configuração do SW2 segue a mesma lógica do SW1, criando as mesmas VLANs e associando as portas dos PCs às VLANs corretas.

```bash
enable
configure terminal
hostname SW2

vlan 10
name TI
exit

vlan 20
name RH
exit

interface FastEthernet0/1
switchport mode access
switchport access vlan 10
no shutdown
exit

interface FastEthernet0/2
switchport mode access
switchport access vlan 20
no shutdown
exit

interface FastEthernet0/24
switchport mode trunk
no shutdown
exit

exit
copy running-config startup-config
```

## Explicação dos principais comandos no SW2

|Comando|Explicação|
|---|---|
|`vlan 10` e `vlan 20`|Criam as VLANs usadas na topologia.|
|`switchport mode access`|Define as portas dos PCs como portas de acesso.|
|`switchport access vlan 10`|Coloca a porta na VLAN 10.|
|`switchport access vlan 20`|Coloca a porta na VLAN 20.|
|`switchport mode trunk`|Configura o link entre SW1 e SW2 para transportar múltiplas VLANs.|
|`copy running-config startup-config`|Salva a configuração aplicada.|

> [!WARNING]  
> As VLANs precisam existir em cada switch. Criar uma VLAN apenas no SW1 não faz com que ela exista automaticamente no SW2.

---

# Configuração dos PCs

Os PCs foram configurados manualmente na aba:

```text
Desktop > IP Configuration
```

|PC|VLAN|IP|Máscara|Gateway|
|---|---|---|---|---|
|PC1|VLAN 10|192.168.10.10|255.255.255.0|192.168.10.1|
|PC2|VLAN 20|192.168.20.10|255.255.255.0|192.168.20.1|
|PC3|VLAN 10|192.168.10.11|255.255.255.0|192.168.10.1|
|PC4|VLAN 20|192.168.20.11|255.255.255.0|192.168.20.1|

> [!NOTE]  
> O gateway de cada PC será o endereço IP da subinterface do roteador correspondente à VLAN daquele dispositivo.

---

# Testando o isolamento entre VLANs

Antes de configurar o roteador, as VLANs devem se comunicar apenas dentro da mesma VLAN.

## Testes esperados

|Origem|Destino|Resultado esperado|Motivo|
|---|---|---|---|
|PC1|PC3|Funciona|Ambos estão na VLAN 10|
|PC2|PC4|Funciona|Ambos estão na VLAN 20|
|PC1|PC2|Não funciona|Estão em VLANs diferentes|
|PC1|PC4|Não funciona|Estão em VLANs diferentes|

Exemplo de teste no PC1:

```bash
ping 192.168.10.11
ping 192.168.20.10
```

|Comando|Explicação|
|---|---|
|`ping 192.168.10.11`|Testa a comunicação entre PC1 e PC3, ambos na VLAN 10.|
|`ping 192.168.20.10`|Testa a comunicação entre PC1 e PC2, que estão em VLANs diferentes.|

> [!NOTE]  
> VLANs diferentes são domínios de broadcast separados. Sem um dispositivo de camada 3, elas não conseguem se comunicar entre si.

---

# Configuração do R1 — Router-on-a-Stick

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
|`hostname R1`|Define o nome do roteador como `R1`.|

---

## Ativando a interface física

```bash
interface FastEthernet0/0
no shutdown
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/0`|Acessa a interface física conectada ao SW1.|
|`no shutdown`|Ativa a interface física.|
|`exit`|Sai do modo de configuração da interface.|

> [!IMPORTANT]  
> A interface física não recebe IP diretamente neste laboratório. Os IPs serão configurados nas subinterfaces.

---

## Criando a subinterface para a VLAN 10

```bash
interface FastEthernet0/0.10
encapsulation dot1q 10
ip address 192.168.10.1 255.255.255.0
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/0.10`|Cria uma subinterface lógica associada à interface física FastEthernet0/0.|
|`encapsulation dot1q 10`|Define que essa subinterface processará tráfego marcado com a VLAN 10 usando 802.1Q.|
|`ip address 192.168.10.1 255.255.255.0`|Define o gateway da VLAN 10.|
|`exit`|Sai da configuração da subinterface.|

> [!TIP]  
> Usar o número da VLAN no número da subinterface, como `Fa0/0.10` para VLAN 10, facilita a organização e a administração.

---

## Criando a subinterface para a VLAN 20

```bash
interface FastEthernet0/0.20
encapsulation dot1q 20
ip address 192.168.20.1 255.255.255.0
exit
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/0.20`|Cria uma subinterface lógica para a VLAN 20.|
|`encapsulation dot1q 20`|Define que essa subinterface processará tráfego marcado com a VLAN 20.|
|`ip address 192.168.20.1 255.255.255.0`|Define o gateway da VLAN 20.|
|`exit`|Sai da configuração da subinterface.|

---

## Salvando a configuração do R1

```bash
exit
copy running-config startup-config
```

|Comando|Explicação|
|---|---|
|`exit`|Sai do modo de configuração global.|
|`copy running-config startup-config`|Salva a configuração ativa para que ela não seja perdida após reiniciar o roteador.|

---

# Testando o InterVLAN Routing

Após configurar o Router-on-a-Stick, os PCs de VLANs diferentes devem conseguir se comunicar.

Exemplo a partir do PC1:

```bash
ping 192.168.20.10
ping 192.168.20.11
```

|Comando|Explicação|
|---|---|
|`ping 192.168.20.10`|Testa a comunicação entre PC1, na VLAN 10, e PC2, na VLAN 20.|
|`ping 192.168.20.11`|Testa a comunicação entre PC1, na VLAN 10, e PC4, na VLAN 20.|

Resultado esperado:

```text
Reply from 192.168.20.10
Reply from 192.168.20.11
```

> [!NOTE]  
> Depois do roteamento entre VLANs, o roteador passa a encaminhar pacotes entre as redes `192.168.10.0/24` e `192.168.20.0/24`.

---

# Comandos de verificação

## Verificar VLANs criadas

```bash
show vlan brief
```

|Comando|Explicação|
|---|---|
|`show vlan brief`|Exibe as VLANs existentes no switch e as portas associadas a cada uma.|

Esse comando deve mostrar as VLANs 10 e 20 com as portas corretas.

---

## Verificar portas trunk

```bash
show interfaces trunk
```

|Comando|Explicação|
|---|---|
|`show interfaces trunk`|Mostra quais interfaces estão operando como trunk e quais VLANs estão sendo transportadas.|

No SW1, as portas conectadas ao SW2 e ao R1 devem aparecer como trunk.

---

## Verificar interfaces do roteador

```bash
show ip interface brief
```

|Comando|Explicação|
|---|---|
|`show ip interface brief`|Exibe um resumo das interfaces do roteador, seus IPs e seus estados.|

As subinterfaces `FastEthernet0/0.10` e `FastEthernet0/0.20` devem aparecer com os IPs configurados e estado `up/up`.

---

## Verificar tabela de roteamento

```bash
show ip route
```

|Comando|Explicação|
|---|---|
|`show ip route`|Exibe a tabela de roteamento do roteador.|

O R1 deve mostrar as redes `192.168.10.0/24` e `192.168.20.0/24` como redes diretamente conectadas.

---

# Troubleshooting

|Problema|Possível causa|Solução|
|---|---|---|
|PC não alcança outro da mesma VLAN|Porta associada à VLAN errada|Verificar com `show vlan brief`|
|PC de uma VLAN não alcança outra VLAN|Router-on-a-Stick não configurado corretamente|Verificar subinterfaces e gateways|
|Trunk não aparece|Faltou `switchport mode trunk`|Verificar com `show interfaces trunk`|
|VLAN não existe no SW2|VLAN criada apenas no SW1|Criar a VLAN manualmente no SW2|
|Subinterface aparece como down|Interface física está desligada|Aplicar `no shutdown` na interface física|
|Gateway do PC incorreto|PC configurado com gateway errado|Conferir o gateway de cada VLAN|
|Ping entre VLANs falha|Encapsulamento dot1q incorreto|Verificar `encapsulation dot1q` nas subinterfaces|

---
