# Relatório Técnico - Monitoramento Dell EMC N1124P-ON no Zabbix 7

**Data:** 21/08/2026  
**Escopo:** adaptação, correção e estabilização de template SNMP para Dell EMC Networking N1124P-ON em Zabbix 7.0.

## 1. Objetivo

Disponibilizar monitoramento SNMP consistente para o switch Dell EMC N1124P-ON, eliminando itens persistentemente `Not supported`, preservando métricas operacionais relevantes e adequando o template legado ao schema e comportamento do Zabbix 7.

## 2. Ambiente validado

- Equipamento: Dell EMC Networking N1124P-ON
- Firmware: 6.4.2.8
- Kernel reportado: Linux 3.6.5
- Zabbix: 7.0.x
- Coleta: SNMP + ICMP

Dados específicos de produção (IP, community SNMP, proxy, nomes internos e credenciais) foram omitidos desta versão para permitir publicação segura em GitHub.

## 3. Situação inicial

O template Dell N-Series disponível estava estruturado originalmente para versões 6.x do Zabbix. Durante a migração e validação foram observados:

1. falha de importação por dependência de template `ICMP Ping` inexistente;
2. incompatibilidades de schema 6.x -> 7.0;
3. dependência de trigger ICMP com nomenclatura antiga;
4. interfaces VLAN sem suporte a `ifHCInOctets/ifHCOutOctets`;
5. processos internos com SNMPINDEX volátil, causando `No Such Instance` intermitente;
6. prototypes legados de Fan e PSU baseados em OIDs Force10/S-Series;
7. colisão de keys em `TempUnit Discovery` por uso de `{#UNIT}` não único.

## 4. Correções executadas

### 4.1 Compatibilidade ICMP

A dependência foi ajustada para o módulo presente no Zabbix 7: `Template Module ICMP Ping`. As expressões de dependência foram adequadas ao trigger de indisponibilidade disponível.

### 4.2 Schema Zabbix 7

O export foi convertido para `version: '7.0'`, com estrutura de grupos compatível e ajustes em campos de LLD/preprocessing exigidos pelo schema 7.0.

### 4.3 VLAN - fallback IF-MIB 32-bit

Foi confirmado via teste pelo frontend que a VLAN validada não respondia aos HC counters de 64 bits, mas respondia a `ifInOctets` e `ifOutOctets`.

Foi criada uma regra LLD específica para VLANs, mantendo:

- interfaces físicas: 64-bit HC counters;
- VLANs: 32-bit counters;
- conversão para bps por `Change per second` + multiplicador 8.

### 4.4 Process Discovery

Os índices de processos internos apresentaram comportamento volátil. A coleta por processo alternava entre normal e `No Such Instance` conforme os índices mudavam.

Decisão operacional: deixar `Process Discovery` desabilitado por padrão. O monitoramento de CPU global permanece ativo, evitando ruído sem perda relevante para disponibilidade e capacidade do equipamento.

### 4.5 Fan e PSU

Foram identificados prototypes legados que consultavam `chSysFanTrayOperStatus` e `chSysPowerSupplyOperStatus`. Esses itens utilizavam OIDs que não eram suportados no N1124P-ON validado e eram recriados pelo LLD.

Os prototypes legados foram removidos, permanecendo as regras Dell/FastPath do template N-Series para Fan e PSU.

### 4.6 TempUnit Discovery

O LLD utilizava `{#UNIT}` na key, porém a regra não gerava um identificador único para esse macro. O resultado era erro `item with the same key already exists`.

Correção:

```text
sensor.tempUnit.state[boxServicesTempUnitState.{#SNMPINDEX}]
sensor.tempUnit.temperature[boxServicesTempUnitTemperature.{#SNMPINDEX}]
```

As referências relacionadas foram ajustadas para o mesmo padrão.

## 5. Estado final

A versão final do template apresenta uma base estável para o modelo validado, com as principais métricas de sistema, interfaces e hardware organizadas por LLD. Os itens persistentemente não suportados foram eliminados por correção de OID/LLD ou pela remoção de telemetria sem valor operacional suficiente.

### Cobertura entregue

- ICMP e disponibilidade SNMP
- identificação e uptime
- CPU global
- memória
- interfaces físicas
- VLAN traffic fallback
- erros/discards
- duplex
- Fan LLD
- PSU LLD
- temperatura
- traps fallback

## 6. Pontos pendentes / roadmap

1. acrescentar monitoramento PoE por porta;
2. monitorar budget/consumo PoE total, se disponível via MIB;
3. calibrar triggers de temperatura, Fan e PSU após coleta histórica;
4. validar o template em outros modelos/firmwares N-Series;
5. criar dashboard operacional padronizado para switches Dell.

## 7. Recomendação de produção

- manter `Process Discovery` desabilitado, salvo necessidade de troubleshooting específico;
- usar triggers de interface somente em portas críticas;
- preferir SNMPv3 em redes onde o suporte e a política de segurança permitirem;
- manter secrets fora do template e fora do Git;
- versionar qualquer mudança no YAML e registrar no changelog.
