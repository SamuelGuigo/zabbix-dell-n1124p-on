# Changelog

## v6 - 2026-08-21

- Ajuste final para Zabbix 7.0.
- `Process Discovery` desabilitado por padrão devido a SNMPINDEX volátil da tabela de processos.
- Corrigidas keys do `TempUnit Discovery` para utilizar `{#SNMPINDEX}`.
- Atualizadas referências de trigger/graph relacionadas ao TempUnit.
- Mantida discovery VLAN com contadores IF-MIB 32-bit como fallback.
- Mantidas interfaces físicas com HC counters de 64 bits.
- Removidos prototypes legados de Fan/PSU que usavam OIDs incompatíveis de Force10/S-Series.

## v5

- Correção de preprocessing para schema Zabbix 7 (`CHECK_NOT_SUPPORTED`).
- Correções acumuladas de importação e LLD.

## v4

- Migração do schema de exportação para Zabbix 7.0.

## v3

- Inclusão inicial da discovery VLAN 32-bit.

## v2 / v1

- Ajustes de vínculo com `Template Module ICMP Ping` e dependências de trigger ICMP.
