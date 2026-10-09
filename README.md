# Dell N1124P-ON — Template SNMP para Zabbix 7

Adaptei um template comunitário Dell N-Series para Zabbix 7, corrigindo compatibilidade, LLD, contadores VLAN e telemetria incompatível com o modelo validado.

## Problema e escopo

O template legado apresentava falhas de importação, `No Such Instance`, índices de processos voláteis e colisões de keys. Trabalhei nas definições de disponibilidade, recursos, interfaces e sensores.

## Atividades realizadas

- Ajustei schema e dependências ICMP.
- Criei fallback IF-MIB de 32 bits para VLANs sem HC counters.
- Desativei Process Discovery com índices voláteis.
- Removi prototypes Fan/PSU incompatíveis e corrigi TempUnit LLD.
- Documentei a instalação, o escopo validado e os pontos de evolução.

## Resultado documentado

Template importável e estabilizado no Dell N1124P-ON em Zabbix 7. O portfólio registra redução de 13 para zero itens não suportados após correções e retirada de coletas incompatíveis. Isso não representa suporte universal a outros modelos ou OIDs.

## Código e documentação

- [Template YAML](zabbix-dell-n1124p-on-github/zabbix-dell-n1124p-on/template/Dell_N-Series_Zabbix7_N1124P-ON.yaml)
- [Instalação e detalhes técnicos](zabbix-dell-n1124p-on-github/zabbix-dell-n1124p-on/README.md)
- [Relatório técnico](zabbix-dell-n1124p-on-github/zabbix-dell-n1124p-on/docs/RELATORIO_TECNICO.md)
- [Changelog](zabbix-dell-n1124p-on-github/zabbix-dell-n1124p-on/CHANGELOG.md)
- [Política de segurança](zabbix-dell-n1124p-on-github/zabbix-dell-n1124p-on/SECURITY.md)
- [Portfólio técnico](https://github.com/SamuelGuigo/Portfolio-Tecnico-Samuel-Guigo)

PoE e validação em outros modelos/firmwares permanecem no roadmap. Preservo o crédito da base comunitária; minha contribuição é a adaptação, diagnóstico e validação. O código YAML e a estrutura atual de arquivos foram preservados.

Dados específicos do ambiente de validação permanecem privados.
