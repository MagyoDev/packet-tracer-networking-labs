# Packet Tracer Networking Labs

Repositório com laboratórios práticos de redes de computadores desenvolvidos no Cisco Packet Tracer.

O objetivo deste projeto é documentar, de forma organizada e progressiva, configurações fundamentais de redes, abordando switching, roteamento, VLANs, redundância, agregação de links e roteamento dinâmico.

Os laboratórios foram criados para estudo, prática e construção de portfólio técnico na área de redes, infraestrutura e cibersegurança.

---

## Objetivo do repositório

Este repositório tem como objetivo reunir laboratórios práticos que demonstram conceitos essenciais de redes de computadores, utilizando equipamentos Cisco simulados no Packet Tracer.

Cada laboratório contém:

- Topologia da rede
- Objetivo do exercício
- Endereçamento IP
- Configurações aplicadas
- Explicação dos principais comandos
- Testes de conectividade
- Comandos de verificação
- Troubleshooting
- Aprendizados obtidos

---

## Ferramentas utilizadas

- Cisco Packet Tracer
- Cisco IOS CLI
- Markdown
- Git e GitHub

---

## Estrutura do repositório

```text
packet-tracer-networking-labs/
│
├── README.md
│
├── labs/
│   ├── 01-configuracao-basica-switch/
│   │   ├── README.md
│   │   └── lab-01-configuracao-basica-switch.pkt
│   │
│   ├── 02-roteamento-estatico/
│   │   ├── README.md
│   │   └── lab-02-roteamento-estatico.pkt
│   │
│   ├── 03-vlans-trunking-intervlan-routing/
│   │   ├── README.md
│   │   └── lab-03-vlans-trunking-intervlan-routing.pkt
│   │
│   ├── 04-stp-redundancia/
│   │   ├── README.md
│   │   └── lab-04-stp-redundancia.pkt
│   │
│   ├── 05-etherchannel/
│   │   ├── README.md
│   │   └── lab-05-etherchannel.pkt
│   │
│   └── 06-ospf-roteamento-dinamico/
│       ├── README.md
│       └── lab-06-ospf-roteamento-dinamico.pkt
```

---

## Ordem dos laboratórios

|Ordem|Laboratório|Conteúdo principal|
|---|---|---|
|01|Configuração Básica de Switch|Hostname, senhas, SVI, interfaces, comandos `show` e testes de conectividade|
|02|Roteamento Estático|Configuração de roteadores, interfaces LAN/WAN, rotas estáticas e gateways|
|03|VLANs, Trunking e InterVLAN Routing|VLANs, portas access, trunk, Router-on-a-Stick e roteamento entre VLANs|
|04|STP e Redundância|Loops de camada 2, Root Bridge, portas bloqueadas, PortFast e BPDU Guard|
|05|EtherChannel|Agregação de links com LACP, PAgP e modo estático|
|06|OSPF - Roteamento Dinâmico|Configuração de OSPF, adjacências, rotas dinâmicas e verificação de convergência|

---

## Conceitos abordados

- Switching
- Routing
- VLANs
- Trunking
- InterVLAN Routing
- STP
- Redundância de camada 2
- EtherChannel
- LACP
- PAgP
- Roteamento estático
- OSPF
- Endereçamento IP
- Troubleshooting de redes
- Comandos de verificação Cisco IOS

---

## Como utilizar

1. Abra o laboratório desejado dentro da pasta `labs/`.
2. Leia o `README.md` do laboratório.
3. Abra o arquivo `.pkt` correspondente no Cisco Packet Tracer.
4. Reproduza ou analise a configuração apresentada.
5. Execute os testes de conectividade.
6. Utilize os comandos de verificação para validar o funcionamento da rede.

---

## Exemplo de execução

Para acessar o primeiro laboratório:

```text
labs/01-configuracao-basica-switch/
```

Dentro da pasta, estarão disponíveis:

```text
README.md
lab-01-configuracao-basica-switch.pkt
```

O arquivo `README.md` explica a topologia, os comandos utilizados, os testes realizados e os principais pontos de troubleshooting.

---

## Finalidade

Este repositório foi desenvolvido como material autoral de estudo e prática, com foco em consolidar conhecimentos fundamentais de redes de computadores.

A proposta é transformar laboratórios técnicos em documentação clara, organizada e reutilizável, servindo como base de aprendizado e também como portfólio profissional.

---

## Observação

Este projeto não possui vínculo oficial com a Cisco, instituições de ensino ou empresas.

Os laboratórios foram desenvolvidos para fins educacionais, utilizando o Cisco Packet Tracer como ambiente de simulação.

---

