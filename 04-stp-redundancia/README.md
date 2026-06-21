# Lab 04 — STP e Redundância no Cisco Packet Tracer

Este laboratório apresenta o funcionamento do STP, Spanning Tree Protocol, em uma topologia com switches interligados por links redundantes.

O objetivo é observar como o STP evita loops de camada 2, identificar a Root Bridge, alterar a prioridade STP, testar failover e aplicar PortFast com BPDU Guard corretamente em portas conectadas a hosts finais.

---

## Objetivo

Praticar conceitos fundamentais de redundância em camada 2, incluindo:

- Funcionamento básico do STP
- Prevenção de loops entre switches
- Eleição da Root Bridge
- Alteração da prioridade STP
- Identificação de portas Root, Designated e Blocking
- Teste de failover
- Uso correto de PortFast
- Uso correto de BPDU Guard
- Troubleshooting de portas em estado `err-disabled`

---

## Topologia

```text
        [SW1]
       /     \
    [SW2] --- [SW3]
     |           |
   [PC1]       [PC2]
```

---

## Dispositivos utilizados

|Dispositivo|Quantidade|Modelo|
|---|---|---|
|Switch|3|Cisco 2960|
|PC|2|End Device|

---

## Conexões da topologia

|Origem|Porta|Destino|Porta|Tipo|
|---|---|---|---|---|
|SW1|Fa0/1|SW2|Fa0/1|Link entre switches|
|SW1|Fa0/2|SW3|Fa0/1|Link entre switches|
|SW2|Fa0/2|SW3|Fa0/2|Link redundante entre switches|
|PC1|Fa0|SW2|Fa0/10|Porta de acesso|
|PC2|Fa0|SW3|Fa0/10|Porta de acesso|

---

## Endereçamento IP dos PCs

|Dispositivo|Endereço IP|Máscara|
|---|---|---|
|PC1|192.168.1.10|255.255.255.0|
|PC2|192.168.1.11|255.255.255.0|

> [!NOTE]  
> Como os dois PCs estão na mesma rede, o foco do laboratório é a conectividade em camada 2. Não é necessário configurar gateway para este teste.

---

# Conceito principal

A topologia possui três switches interligados em formato de triângulo. Essa estrutura cria redundância, mas também pode gerar um loop de camada 2.

Sem o STP, esse loop poderia causar:

- Tempestade de broadcast
- Quadros circulando indefinidamente
- Instabilidade na tabela MAC
- Queda de desempenho da rede
- Alto uso de CPU nos switches

O STP evita esse problema bloqueando logicamente uma das portas redundantes, mantendo a rede funcional e sem loops.

---

# Observando o STP em funcionamento

Após conectar os switches, o STP começa automaticamente o processo de convergência.

No Packet Tracer, é comum observar uma porta ficando laranja durante a convergência ou quando está bloqueada pelo STP.

## Estados visuais no Packet Tracer

|Cor|Significado comum|
|---|---|
|Verde|Link ativo e encaminhando tráfego|
|Laranja|Porta em convergência ou bloqueada pelo STP|
|Vermelho|Link down, shutdown ou porta em erro|

> [!IMPORTANT]  
> Porta vermelha normalmente não significa STP bloqueando corretamente. Geralmente indica link desligado, `shutdown` ou estado `err-disabled`.

---

# Verificando a Root Bridge

Em cada switch, execute:

```bash
show spanning-tree
```

|Comando|Explicação|
|---|---|
|`show spanning-tree`|Exibe informações do STP, incluindo Root Bridge, Bridge ID, prioridade, portas e estados.|

Na saída do comando, procure por:

```text
This bridge is the root
```

Se essa mensagem aparecer, significa que aquele switch foi eleito como Root Bridge.

---

## Como o STP escolhe a Root Bridge

O STP escolhe a Root Bridge com base no menor Bridge ID.

O Bridge ID é formado por:

```text
Prioridade + endereço MAC
```

Por padrão, switches Cisco usam prioridade `32768`.

Se todos estiverem com a mesma prioridade, o switch com menor endereço MAC será eleito como Root Bridge.

---

# Forçando o SW1 como Root Bridge

Para tornar a topologia mais previsível, o SW1 foi configurado com uma prioridade menor.

## Configuração no SW1

```bash
enable
configure terminal
spanning-tree vlan 1 priority 4096
end
copy running-config startup-config
```

|Comando|Explicação|
|---|---|
|`enable`|Entra no modo EXEC privilegiado.|
|`configure terminal`|Entra no modo de configuração global.|
|`spanning-tree vlan 1 priority 4096`|Define a prioridade STP da VLAN 1 como 4096, aumentando a chance do SW1 ser eleito Root Bridge.|
|`end`|Retorna ao modo EXEC privilegiado.|
|`copy running-config startup-config`|Salva a configuração aplicada.|

> [!NOTE]  
> Quanto menor a prioridade, maior a chance de o switch ser eleito Root Bridge.

---

# Configurando o SW2 como Root Bridge secundária

O SW2 foi configurado com prioridade menor que o padrão, mas maior que a do SW1.

Assim, se o SW1 falhar, o SW2 tende a assumir como Root Bridge.

## Configuração no SW2

```bash
enable
configure terminal
spanning-tree vlan 1 priority 8192
end
copy running-config startup-config
```

|Comando|Explicação|
|---|---|
|`spanning-tree vlan 1 priority 8192`|Define o SW2 como candidato secundário à Root Bridge.|

---

# Identificando os papéis das portas

Após a convergência do STP, execute:

```bash
show spanning-tree
```

ou:

```bash
show spanning-tree detail
```

|Comando|Explicação|
|---|---|
|`show spanning-tree`|Mostra o estado geral do STP e o papel das portas.|
|`show spanning-tree detail`|Exibe informações mais detalhadas, como timers, custos e mudanças de estado.|

---

## Papéis comuns das portas

|Papel|Significado|
|---|---|
|Root Port|Melhor caminho de um switch não-root até a Root Bridge|
|Designated Port|Porta responsável por encaminhar tráfego em um segmento|
|Blocking Port|Porta bloqueada para evitar loop de camada 2|

---

## Estados esperados

|Situação|Estado esperado|
|---|---|
|Portas do SW1, caso ele seja Root Bridge|Designated / Forwarding|
|Melhor caminho do SW2 até o SW1|Root / Forwarding|
|Melhor caminho do SW3 até o SW1|Root / Forwarding|
|Link redundante entre SW2 e SW3|Uma das portas pode ficar Blocking|

---

# Testando conectividade entre PCs

Após o STP convergir, a comunicação entre PC1 e PC2 deve funcionar.

## IPs utilizados no teste

|Dispositivo|IP|
|---|---|
|PC1|192.168.1.10|
|PC2|192.168.1.11|

No PC1, execute:

```bash
ping 192.168.1.11
```

No PC2, também é possível testar o caminho inverso:

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

> [!NOTE]  
> Mesmo com uma porta bloqueada pelo STP, a comunicação deve funcionar porque ainda existe um caminho ativo entre os dispositivos.

---

# Configurando PortFast e BPDU Guard

O PortFast deve ser aplicado apenas em portas conectadas a dispositivos finais, como PCs, servidores ou impressoras.

O BPDU Guard complementa essa proteção: se uma porta com BPDU Guard receber uma BPDU, o switch entende que existe um equipamento de rede conectado onde deveria haver apenas um host final e pode colocar a porta em estado `err-disabled`.

Neste laboratório, PortFast e BPDU Guard devem ser aplicados apenas nas portas dos PCs:

|Porta|Conexão|Usar PortFast/BPDU Guard|
|---|---|---|
|SW2 Fa0/10|PC1|Sim|
|SW3 Fa0/10|PC2|Sim|
|SW1 Fa0/1|SW2|Não|
|SW1 Fa0/2|SW3|Não|
|SW2 Fa0/1|SW1|Não|
|SW2 Fa0/2|SW3|Não|
|SW3 Fa0/1|SW1|Não|
|SW3 Fa0/2|SW2|Não|

---

## Configuração correta no SW2

```bash
enable
configure terminal
interface FastEthernet0/10
spanning-tree portfast
spanning-tree bpduguard enable
end
copy running-config startup-config
```

## Configuração correta no SW3

```bash
enable
configure terminal
interface FastEthernet0/10
spanning-tree portfast
spanning-tree bpduguard enable
end
copy running-config startup-config
```

## Explicação dos comandos

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/10`|Acessa a porta conectada ao PC.|
|`spanning-tree portfast`|Faz a porta ir diretamente para o estado Forwarding, sem aguardar todo o processo normal de convergência do STP.|
|`spanning-tree bpduguard enable`|Habilita BPDU Guard na interface, bloqueando a porta caso ela receba BPDUs.|

---

## Por que não usar configuração global neste laboratório?

Neste laboratório, não foi utilizada a configuração global:

```bash
spanning-tree portfast default
spanning-tree portfast bpduguard default
```

Esses comandos aplicam PortFast e BPDU Guard automaticamente em portas de acesso.

O problema é que, nesta topologia, algumas portas entre switches continuam no modo padrão de acesso da VLAN 1. Como elas estão conectadas a outros switches, recebem BPDUs normalmente.

Se o BPDU Guard estiver ativo nessas portas, o switch interpreta o recebimento de BPDUs como uma violação e pode colocar as interfaces em:

```text
err-disabled
```

Isso pode derrubar links entre switches, fazendo com que eles apareçam em vermelho no Packet Tracer.

> [!WARNING]  
> Neste laboratório, PortFast e BPDU Guard devem ser configurados manualmente apenas nas portas dos PCs. Usar a configuração global pode fazer portas entre switches entrarem em `err-disabled`.

---

# Caso uma porta entre em err-disabled

Se uma porta entre switches for configurada incorretamente com BPDU Guard e receber BPDUs, ela pode entrar em `err-disabled`.

Para corrigir, primeiro remova PortFast e BPDU Guard da porta incorreta. Depois reative a interface.

Exemplo no SW2, caso as portas Fa0/1 e Fa0/2 tenham sido afetadas:

```bash
configure terminal
interface range FastEthernet0/1 - 2
no spanning-tree portfast
no spanning-tree bpduguard enable
shutdown
no shutdown
end
```

|Comando|Explicação|
|---|---|
|`interface range FastEthernet0/1 - 2`|Seleciona as portas Fa0/1 e Fa0/2 ao mesmo tempo.|
|`no spanning-tree portfast`|Remove PortFast das portas selecionadas.|
|`no spanning-tree bpduguard enable`|Remove BPDU Guard das portas selecionadas.|
|`shutdown`|Desliga administrativamente a interface.|
|`no shutdown`|Reativa a interface.|

> [!IMPORTANT]  
> O `shutdown` e `no shutdown` só resolvem depois que a causa do erro foi removida. Se o BPDU Guard continuar ativo em uma porta ligada a outro switch, a porta pode voltar para `err-disabled`.

---

# Testando failover

O failover foi testado desligando um dos links ativos e observando o STP recalcular a topologia.

Exemplo no SW1:

```bash
configure terminal
interface FastEthernet0/1
shutdown
end
```

Depois, verifique o STP:

```bash
show spanning-tree
```

Após a convergência, uma porta que antes estava bloqueada pode passar para o estado Forwarding.

Para reativar o link:

```bash
configure terminal
interface FastEthernet0/1
no shutdown
end
```

|Comando|Explicação|
|---|---|
|`interface FastEthernet0/1`|Acessa a interface que será desligada para simular falha.|
|`shutdown`|Desliga administrativamente a porta.|
|`show spanning-tree`|Verifica se o STP recalculou a topologia.|
|`no shutdown`|Reativa a interface desligada.|

---

# Comandos de verificação

|Comando|Função|
|---|---|
|`show spanning-tree`|Mostra a Root Bridge, os papéis das portas e os estados STP.|
|`show spanning-tree detail`|Exibe informações detalhadas do STP, incluindo eventos e mudanças de topologia.|
|`show spanning-tree vlan 1`|Mostra as informações do STP especificamente para a VLAN 1.|
|`show interfaces status`|Mostra o estado das portas, incluindo `connected`, `notconnect`, `disabled` ou `err-disabled`.|
|`show running-config`|Exibe a configuração atual em execução no switch.|

---

# Troubleshooting

|Problema|Possível causa|Solução|
|---|---|---|
|Porta ficou laranja|STP bloqueou ou está convergindo|Aguardar convergência e verificar com `show spanning-tree`|
|Porta ficou vermelha|Link down, shutdown ou err-disabled|Verificar com `show interfaces status`|
|Porta aparece como `err-disabled`|BPDU Guard recebeu BPDU em porta indevida|Remover BPDU Guard da porta entre switches e reativar|
|Root Bridge diferente do esperado|Prioridade STP não configurada corretamente|Verificar e ajustar com `spanning-tree vlan 1 priority`|
|PC não pinga outro PC|STP ainda convergindo ou porta errada|Verificar links e aguardar convergência|
|Porta volta a cair após `no shutdown`|A causa do erro ainda existe|Remover configuração incorreta antes de reativar|
|Loop ou instabilidade|PortFast aplicado em porta de switch|Remover PortFast das portas entre switches|

---
