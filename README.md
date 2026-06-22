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
├── 01-configuracao-basica-switch/
│   ├── README.md
│   └── lab-01-configuracao-basica-switch.pkt
│
├── 02-roteamento-estatico/
│   ├── README.md
│   └── lab-02-roteamento-estatico.pkt
│
├── 03-vlans-trunking-intervlan-routing/
│   ├── README.md
│   └── lab-03-vlans-trunking-intervlan-routing.pkt
│
├── 04-stp-redundancia/
│   ├── README.md
│   └── lab-04-stp-redundancia.pkt
│
├── 05-etherchannel/
│   ├── README.md
│   └── lab-05-etherchannel.pkt
│
└── 06-ospf-roteamento-dinamico/
    ├── README.md
    └── lab-06-ospf-roteamento-dinamico.pkt
```

---

## Ordem dos laboratórios

| Ordem | Laboratório                                                                                                                                    | Conteúdo principal                                                               |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| 01    | [Configuração Básica de Switch](https://github.com/MagyoDev/packet-tracer-networking-labs/tree/main/01-configuracao-basica-switch)             | Hostname, senhas, SVI, interfaces, comandos `show` e testes de conectividade     |
| 02    | [Roteamento Estático](https://github.com/MagyoDev/packet-tracer-networking-labs/tree/main/02-roteamento-estatico)                              | Configuração de roteadores, interfaces LAN/WAN, rotas estáticas e gateways       |
| 03    | [VLANs, Trunking e InterVLAN Routing](https://github.com/MagyoDev/packet-tracer-networking-labs/tree/main/03-vlans-trunking-intervlan-routing) | VLANs, portas access, trunk, Router-on-a-Stick e roteamento entre VLANs          |
| 04    | [STP e Redundância](https://github.com/MagyoDev/packet-tracer-networking-labs/tree/main/04-stp-redundancia)                                    | Loops de camada 2, Root Bridge, portas bloqueadas, PortFast e BPDU Guard         |
| 05    | [EtherChannel](https://github.com/MagyoDev/packet-tracer-networking-labs/tree/main/05-etherchannel)                                            | Agregação de links com LACP, PAgP e modo estático                                |
| 06    | [OSPF - Roteamento Dinâmico](https://github.com/MagyoDev/packet-tracer-networking-labs/tree/main/06-ospf-roteamento-dinamico)                  | Configuração de OSPF, adjacências, rotas dinâmicas e verificação de convergência |

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

1. Escolha um laboratório na lista de laboratórios.
2. Acesse a pasta correspondente.
3. Leia o `README.md` do laboratório.
4. Abra o arquivo `.pkt` no Cisco Packet Tracer.
5. Analise ou reproduza a configuração apresentada.
6. Execute os testes de conectividade.
7. Utilize os comandos de verificação para validar o funcionamento da rede.

---

## Exemplo de uso

Para acessar o primeiro laboratório:

```text
01-configuracao-basica-switch/
```

Dentro da pasta, estão disponíveis:

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