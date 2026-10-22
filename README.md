# Dell EMC N1124P-ON - Zabbix 7 SNMP Template

Template SNMP adaptado para monitoramento do **Dell EMC Networking N1124P-ON** no **Zabbix 7.0**.

O projeto nasceu da adaptação de um template comunitário Dell N-Series 6.x para um ambiente Zabbix 7, com correções de compatibilidade, Low-Level Discovery (LLD), interfaces VLAN e sensores de hardware.

> **Segurança:** este repositório não contém endereços IP, communities SNMP, credenciais ou nomes internos do ambiente onde o template foi validado.

## Código, resultado e documentação

A adaptação e validação são de Samuel Guigo. O resultado documentado foi a redução de 13 para zero itens não suportados, combinando correções e retirada de coletas incompatíveis; isso não comprova cobertura universal de OIDs ou modelos.

- [Template YAML](template/Dell_N-Series_Zabbix7_N1124P-ON.yaml)
- [Relatório técnico](docs/RELATORIO_TECNICO.md)
- [Histórico de alterações](CHANGELOG.md)
- [Origem e atribuição](ORIGIN.md)
- [Política de segurança](SECURITY.md)
- [Portfólio técnico](https://github.com/SamuelGuigo/Portfolio-Tecnico-Samuel-Guigo)

Os arquivos de uso atual ficam na raiz, em docs/ e template/. O diretório antigo permanece como cópia de compatibilidade para links existentes; novos downloads e alterações devem usar os caminhos acima.

## Equipamento validado

| Item | Valor |
|---|---|
| Fabricante | Dell EMC Networking |
| Modelo | N1124P-ON |
| Firmware | 6.4.2.8 |
| Kernel reportado | Linux 3.6.5 |
| Zabbix | 7.0.x |
| Método | SNMP + ICMP |

## O que o template monitora

- Disponibilidade ICMP e SNMP
- Uptime e identificação do equipamento
- CPU geral: atual, 5 s, 1 min e 5 min
- Memória total, livre e utilização percentual
- Interfaces via IF-MIB
  - status administrativo/operacional
  - velocidade
  - RX/TX
  - erros e descartes
  - duplex via EtherLike-MIB
- Interfaces VLAN com fallback para contadores IF-MIB de 32 bits
- Fans: estado, RPM e duty level quando expostos pelo equipamento
- PSU: estado quando exposto pelo equipamento
- Sensores/unidades de temperatura
- SNMP traps fallback

## Principais correções realizadas

### 1. Compatibilidade com Zabbix 7

O template original referenciava `ICMP Ping`. No ambiente Zabbix 7 utilizado, o módulo disponível era `Template Module ICMP Ping`. Também foi ajustada a dependência do trigger de indisponibilidade ICMP e o schema de exportação para Zabbix 7.0.

### 2. VLANs sem contadores HC

A interface VLAN validada não expunha `ifHCInOctets/ifHCOutOctets` (64 bits), retornando `No Such Instance`. Os contadores clássicos `ifInOctets/ifOutOctets` respondiam normalmente.

Foi adicionada a discovery rule:

`VLAN interfaces traffic discovery (32-bit)`

Ela usa:

- RX: `1.3.6.1.2.1.2.2.1.10.{#SNMPINDEX}`
- TX: `1.3.6.1.2.1.2.2.1.16.{#SNMPINDEX}`
- preprocessing: `Change per second` + multiplicador `8`

As interfaces físicas continuam utilizando contadores HC de 64 bits.

### 3. Process Discovery volátil

Os índices da tabela de processos internos podem mudar durante a operação, causando `No Such Instance currently exists at this OID` de forma intermitente. Como CPU global continua sendo monitorada normalmente e PID/processos internos têm baixo valor operacional para NOC, `Process Discovery` fica **desabilitado por padrão** nesta versão.

### 4. Prototypes legados de Fan e PSU

Foram identificados prototypes antigos baseados na árvore Force10/S-Series (`chSysFanTrayOperStatus` e `chSysPowerSupplyOperStatus`) que recriavam itens não suportados no host. Eles não fazem parte da versão final do template.

O template mantém as descobertas Dell/FastPath apropriadas para N-Series:

- Fan: árvore `1.3.6.1.4.1.674.10895.5000.2.6132...43.1.6`
- PSU: árvore `1.3.6.1.4.1.674.10895.5000.2.6132...43.1.7`

### 5. TempUnit LLD - chave duplicada

A discovery de unidades de temperatura não populava `{#UNIT}` de forma única, fazendo múltiplos itens tentarem usar a mesma key.

As keys foram alteradas para utilizar `{#SNMPINDEX}`:

```text
sensor.tempUnit.state[boxServicesTempUnitState.{#SNMPINDEX}]
sensor.tempUnit.temperature[boxServicesTempUnitTemperature.{#SNMPINDEX}]
```

As referências dos trigger/graph prototypes foram atualizadas junto.

## Instalação

1. Faça download de `template/Dell_N-Series_Zabbix7_N1124P-ON.yaml`.
2. No Zabbix, acesse **Data collection > Templates > Import**.
3. Selecione o YAML.
4. Use:
   - Update existing: habilitado, caso esteja atualizando uma versão anterior;
   - Create new: habilitado;
   - Delete missing: use com cautela em ambientes que já tenham customizações locais.
5. Crie/edite o host e configure a interface SNMP (porta UDP 161).
6. Vincule `Dell N-Series by SNMP` ao host.
7. Aguarde os ciclos de LLD e valide em **Monitoring > Latest data**.

## Boas práticas

- Não publique community SNMP ou credenciais no repositório.
- Prefira SNMPv3 quando o ambiente permitir.
- Não habilite triggers de link-down indiscriminadamente em todas as portas; aplique criticidade somente a uplinks, trunks e interfaces relevantes.
- Valide Fan, PSU e sensores ambientais no modelo/firmware alvo, pois a disponibilidade de OIDs pode variar.
- Para interfaces de alto tráfego, prefira contadores HC de 64 bits. O fallback de 32 bits foi mantido apenas para VLANs que não oferecem HC counters.

## Status

- [x] Importação em Zabbix 7.0
- [x] ICMP/SNMP
- [x] CPU e memória
- [x] Interface LLD
- [x] VLAN 32-bit fallback
- [x] Limpeza de prototypes legados Fan/PSU
- [x] TempUnit LLD sem colisão de key
- [x] Process Discovery desabilitado por padrão para evitar ruído de índices voláteis
- [ ] PoE por porta / budget de potência - próxima evolução
- [ ] Validação em outros modelos Dell N-Series

## Estrutura do projeto

```text
.
├── README.md
├── CHANGELOG.md
├── SECURITY.md
├── ORIGIN.md
├── docs/
│   └── RELATORIO_TECNICO.md
└── template/
    └── Dell_N-Series_Zabbix7_N1124P-ON.yaml
```

## Créditos

Projeto derivado de template comunitário Dell N-Series e adaptado/testado para Zabbix 7 e Dell EMC N1124P-ON.

A base técnica foi identificada como [zbx-dell-n-series](https://github.com/business-communication/zbx-dell-n-series), historicamente referenciada como Elkropac/zbx-dell-n-series. A revisão efetivamente utilizada e a licença continuam pendentes de confirmação. Consulte [ORIGIN.md](ORIGIN.md) para evidências da comparação, atribuição e limites.
