# Lab 05 — EtherChannel no Cisco Packet Tracer

Este laboratório apresenta a configuração de EtherChannel entre dois switches Cisco no Packet Tracer.

O objetivo é agregar múltiplos links físicos em um único canal lógico, permitindo que os links trabalhem como uma única conexão entre switches. O laboratório aborda EtherChannel com LACP, PAgP e modo estático, além dos comandos de verificação e cuidados necessários ao trocar entre os modos.

---

## Objetivo

Praticar conceitos fundamentais de agregação de links, incluindo:

- Funcionamento do EtherChannel
- Diferença entre link físico e canal lógico
- Agregação de portas entre switches
- Configuração de LACP
- Configuração de PAgP
- Configuração de EtherChannel estático
- Verificação do estado do Port-channel
- Relação entre EtherChannel e STP
- Limpeza de configurações anteriores antes de trocar o modo do canal
- Troubleshooting de portas `disabled` e Port-channel `notconnect`

---

## Topologia

```text
[PC1] --- [SW1] ====== [SW2] --- [PC2]
          Fa0/1       Fa0/1
          Fa0/2       Fa0/2

       Dois links físicos agregados
       em um único Port-channel
```

---

## Dispositivos utilizados

|Dispositivo|Quantidade|Modelo|
|---|---|---|
|Switch|2|Cisco 2960|
|PC|2|End Device|

---

## Conexões da topologia

|Origem|Porta|Destino|Porta|Função|
|---|---|---|---|---|
|PC1|Fa0|SW1|Fa0/10|Host final|
|SW1|Fa0/1|SW2|Fa0/1|Link físico do EtherChannel|
|SW1|Fa0/2|SW2|Fa0/2|Link físico do EtherChannel|
|PC2|Fa0|SW2|Fa0/10|Host final|

---

## Endereçamento IP dos PCs

|Dispositivo|Endereço IP|Máscara|
|---|---|---|
|PC1|192.168.1.10|255.255.255.0|
|PC2|192.168.1.11|255.255.255.0|

> [!NOTE]  
> Como os dois PCs estão na mesma rede, não é necessário configurar gateway para este teste.

---

# Conceito principal

O EtherChannel permite agrupar múltiplos links físicos entre switches e tratá-los como um único link lógico.

Sem EtherChannel, dois links entre os mesmos switches poderiam gerar redundância de camada 2. Nesse caso, o STP bloquearia um dos links para evitar loop.

Com EtherChannel, os links físicos são agrupados em uma interface lógica chamada `Port-channel`. O STP passa a enxergar o canal como um único link, permitindo que as portas físicas trabalhem juntas.

---

## Benefícios do EtherChannel

- Agrega largura de banda
- Reduz desperdício de links redundantes
- Trabalha melhor com o STP
- Fornece redundância entre switches
- Simplifica a visão lógica da topologia

---

# Observando o comportamento sem EtherChannel

Antes de configurar o EtherChannel, dois links físicos entre os mesmos switches podem fazer o STP bloquear um deles.

No SW1 ou SW2, execute:

```bash
show spanning-tree
```

|Comando|Explicação|
|---|---|
|`show spanning-tree`|Mostra como o STP enxerga os links da topologia e quais portas estão encaminhando ou bloqueadas.|

Nesse momento, é esperado que apenas um dos links seja usado para encaminhamento, enquanto o outro pode ficar bloqueado para evitar loop.

---

# EtherChannel com LACP

O LACP, Link Aggregation Control Protocol, é um protocolo aberto baseado no padrão IEEE 802.3ad.

Ele permite que os switches negociem automaticamente a formação do EtherChannel.

## Modos do LACP

|Modo|Função|
|---|---|
|`active`|Tenta formar o canal ativamente|
|`passive`|Espera o outro lado iniciar a negociação|

> [!NOTE]  
> Para o LACP formar o canal, pelo menos um dos lados precisa estar em modo `active`.

---

## Configuração do SW1 com LACP active

```bash
enable
configure terminal

interface range FastEthernet0/1-2
channel-group 1 mode active
no shutdown
exit

interface port-channel 1
switchport mode trunk
no shutdown
exit

end
copy running-config startup-config
```

## Explicação dos comandos

|Comando|Explicação|
|---|---|
|`interface range FastEthernet0/1-2`|Seleciona as portas físicas Fa0/1 e Fa0/2 ao mesmo tempo.|
|`channel-group 1 mode active`|Adiciona as interfaces ao grupo 1 usando LACP em modo ativo.|
|`no shutdown`|Garante que as portas físicas estejam ativadas.|
|`interface port-channel 1`|Acessa a interface lógica criada para o EtherChannel.|
|`switchport mode trunk`|Configura o Port-channel como trunk.|
|`no shutdown`|Garante que o Port-channel esteja ativado.|
|`copy running-config startup-config`|Salva a configuração aplicada.|

---

## Configuração do SW2 com LACP passive

```bash
enable
configure terminal

interface range FastEthernet0/1-2
channel-group 1 mode passive
no shutdown
exit

interface port-channel 1
switchport mode trunk
no shutdown
exit

end
copy running-config startup-config
```

## Explicação dos comandos

|Comando|Explicação|
|---|---|
|`channel-group 1 mode passive`|Adiciona as interfaces ao grupo 1 usando LACP em modo passivo.|
|`switchport mode trunk`|Define o canal lógico como trunk.|
|`no shutdown`|Garante que as portas e o Port-channel estejam ativos.|

---

# Verificando o EtherChannel

Após configurar o LACP, execute:

```bash
show etherchannel summary
```

|Comando|Explicação|
|---|---|
|`show etherchannel summary`|Mostra o estado do EtherChannel, o Port-channel criado e as interfaces participantes.|

Resultado esperado:

```text
Po1(SU)    LACP    Fa0/1(P) Fa0/2(P)
```

## Significado da saída

|Trecho|Significado|
|---|---|
|`Po1`|Port-channel 1 criado|
|`S`|Canal em Layer 2|
|`U`|Canal ativo/em uso|
|`LACP`|Protocolo de negociação usado|
|`Fa0/1(P)`|Interface Fa0/1 participando do canal|
|`Fa0/2(P)`|Interface Fa0/2 participando do canal|

---

# Testando conectividade

Após o EtherChannel subir, teste a comunicação entre os PCs.

No PC1:

```bash
ping 192.168.1.11
```

No PC2:

```bash
ping 192.168.1.10
```

|Comando|Explicação|
|---|---|
|`ping 192.168.1.11`|Testa a conectividade do PC1 para o PC2.|
|`ping 192.168.1.10`|Testa a conectividade do PC2 para o PC1.|

Resultado esperado:

```text
Reply from 192.168.1.11
Reply from 192.168.1.10
```

---

# Limpando a configuração antes de trocar o modo

Antes de trocar de LACP para PAgP, ou de PAgP para modo estático, é recomendado limpar a configuração anterior.

Isso evita que as portas fiquem presas em uma configuração antiga ou que o Port-channel não suba corretamente.

Execute nos dois switches:

```bash
enable
configure terminal

interface range FastEthernet0/1-2
no channel-group
no shutdown
exit

no interface port-channel 1

end
```

## Explicação dos comandos

|Comando|Explicação|
|---|---|
|`interface range FastEthernet0/1-2`|Seleciona as portas físicas usadas no EtherChannel.|
|`no channel-group`|Remove as interfaces do grupo EtherChannel atual.|
|`no shutdown`|Garante que as portas físicas continuem ativas após a remoção.|
|`no interface port-channel 1`|Remove a interface lógica Port-channel 1.|
|`end`|Retorna ao modo EXEC privilegiado.|

> [!IMPORTANT]  
> Sempre que trocar o modo do EtherChannel, limpe a configuração anterior nos dois switches antes de aplicar a nova configuração.

---

# EtherChannel com PAgP

O PAgP, Port Aggregation Protocol, é um protocolo proprietário da Cisco para negociação de EtherChannel.

## Modos do PAgP

|Modo|Função|
|---|---|
|`desirable`|Tenta formar o canal ativamente|
|`auto`|Espera o outro lado iniciar a negociação|

> [!NOTE]  
> Para o PAgP formar o canal, pelo menos um dos lados precisa estar em modo `desirable`.

---

## Configuração do SW1 com PAgP desirable

```bash
enable
configure terminal

interface range FastEthernet0/1-2
channel-group 1 mode desirable
no shutdown
exit

interface port-channel 1
switchport mode trunk
no shutdown
exit

end
copy running-config startup-config
```

## Configuração do SW2 com PAgP auto

```bash
enable
configure terminal

interface range FastEthernet0/1-2
channel-group 1 mode auto
no shutdown
exit

interface port-channel 1
switchport mode trunk
no shutdown
exit

end
copy running-config startup-config
```

## Explicação dos comandos

|Comando|Explicação|
|---|---|
|`channel-group 1 mode desirable`|Configura o PAgP no modo ativo, tentando formar o canal.|
|`channel-group 1 mode auto`|Configura o PAgP no modo passivo, aguardando negociação.|
|`switchport mode trunk`|Configura o Port-channel como trunk.|
|`no shutdown`|Garante que as portas físicas e o Port-channel estejam ativados.|

---

## Verificando o PAgP

```bash
show etherchannel summary
```

Resultado esperado:

```text
Po1(SU)    PAgP    Fa0/1(P) Fa0/2(P)
```

---

# EtherChannel em modo estático

No modo estático, o EtherChannel é forçado manualmente, sem protocolo de negociação.

Nesse modo, os dois lados devem usar:

```text
mode on
```

> [!WARNING]  
> O modo estático não negocia nem valida inconsistências como LACP ou PAgP. Por isso, configurações diferentes entre os switches podem causar falhas ou instabilidade.

---

## Configuração do SW1 em modo estático

```bash
enable
configure terminal

interface range FastEthernet0/1-2
channel-group 1 mode on
no shutdown
exit

interface port-channel 1
switchport mode trunk
no shutdown
exit

end
copy running-config startup-config
```

## Configuração do SW2 em modo estático

```bash
enable
configure terminal

interface range FastEthernet0/1-2
channel-group 1 mode on
no shutdown
exit

interface port-channel 1
switchport mode trunk
no shutdown
exit

end
copy running-config startup-config
```

## Explicação dos comandos

|Comando|Explicação|
|---|---|
|`channel-group 1 mode on`|Força a criação do EtherChannel sem usar protocolo de negociação.|
|`switchport mode trunk`|Define a interface lógica Port-channel como trunk.|
|`no shutdown`|Garante que as portas físicas e o Port-channel estejam ativados.|

---

## Verificando o modo estático

```bash
show etherchannel summary
```

Resultado esperado:

```text
Po1(SU)    -    Fa0/1(P) Fa0/2(P)
```

## Significado da saída

|Trecho|Significado|
|---|---|
|`Po1`|Port-channel 1 criado|
|`S`|Canal em Layer 2|
|`U`|Canal ativo/em uso|
|`-`|Modo estático, sem protocolo de negociação|
|`Fa0/1(P)`|Interface participando do canal|
|`Fa0/2(P)`|Interface participando do canal|

---

# Reativando portas desativadas

Durante a troca entre LACP, PAgP e modo estático, as portas físicas podem ficar desativadas ou presas em uma configuração anterior.

Quando isso acontece, o comando abaixo pode mostrar algo parecido com:

```bash
show interfaces status
```

```text
Po1     notconnect
Fa0/1   disabled
Fa0/2   disabled
```

Isso indica que o canal lógico não está ativo e que as portas físicas usadas no EtherChannel estão desativadas.

Para corrigir, reative as portas físicas e o Port-channel:

```bash
enable
configure terminal

interface range FastEthernet0/1-2
no shutdown
exit

interface port-channel 1
no shutdown
exit

end
```

## Explicação dos comandos

|Comando|Explicação|
|---|---|
|`interface range FastEthernet0/1-2`|Seleciona as portas físicas usadas no EtherChannel.|
|`no shutdown`|Reativa as interfaces físicas.|
|`interface port-channel 1`|Acessa a interface lógica do EtherChannel.|
|`no shutdown`|Reativa o Port-channel, caso ele esteja desligado.|

> [!IMPORTANT]  
> Se as portas físicas estiverem como `disabled`, o EtherChannel não consegue subir. Antes de testar novamente, confirme que Fa0/1, Fa0/2 e Port-channel1 estão ativados.

---

# Comando desconsiderado no Packet Tracer

O comando abaixo pode não funcionar em alguns switches do Packet Tracer:

```bash
show etherchannel 1 detail
```

Por isso, neste laboratório ele foi desconsiderado.

No lugar dele, foram utilizados:

```bash
show etherchannel summary
show interfaces port-channel 1
show interfaces status
show spanning-tree vlan 1
```

Esses comandos são suficientes para validar o funcionamento do EtherChannel no Packet Tracer.

---

# Comandos de verificação

|Comando|Função|
|---|---|
|`show etherchannel summary`|Mostra se o canal está ativo e se as portas fazem parte dele.|
|`show interfaces port-channel 1`|Mostra detalhes da interface lógica do EtherChannel.|
|`show interfaces status`|Mostra se as portas estão conectadas, desativadas ou em erro.|
|`show spanning-tree vlan 1`|Mostra como o STP enxerga o canal lógico.|
|`show running-config`|Exibe a configuração atual do switch.|

---

# Resultado final esperado

O EtherChannel estará funcionando corretamente quando o comando:

```bash
show etherchannel summary
```

mostrar algo como:

```text
Po1(SU)    -    Fa0/1(P) Fa0/2(P)
```

ou, dependendo do modo configurado:

```text
Po1(SU)    LACP    Fa0/1(P) Fa0/2(P)
Po1(SU)    PAgP    Fa0/1(P) Fa0/2(P)
```

O mais importante é observar:

|Indicador|Significado|
|---|---|
|`Po1(SU)`|Port-channel em Layer 2 e ativo|
|`Fa0/1(P)`|Fa0/1 participando do canal|
|`Fa0/2(P)`|Fa0/2 participando do canal|

---

# Troubleshooting

|Problema|Possível causa|Solução|
|---|---|---|
|`Po1` aparece como `notconnect`|Port-channel desligado ou portas físicas inativas|Aplicar `no shutdown` nas portas físicas e no Port-channel|
|`Fa0/1` ou `Fa0/2` aparecem como `disabled`|Interface desligada manualmente ou presa em configuração anterior|Aplicar `no shutdown` nas interfaces|
|Interfaces aparecem como `I` no EtherChannel|Canal não negociou corretamente|Verificar modos usados nos dois switches|
|LACP não sobe|Ambos os lados estão em `passive`|Configurar pelo menos um lado como `active`|
|PAgP não sobe|Ambos os lados estão em `auto`|Configurar pelo menos um lado como `desirable`|
|Modo estático não sobe|Um lado não está em `mode on`|Configurar `mode on` nos dois switches|
|Canal formado, mas sem tráfego|Port-channel não configurado como trunk|Aplicar `switchport mode trunk` no `interface port-channel 1`|
|Problemas após trocar o modo|Configuração anterior não foi removida|Usar `no channel-group` e `no interface port-channel 1` antes de reconfigurar|

---

