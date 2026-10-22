# Origem e atribuição

## Base comunitária identificada

Referência histórica: [Elkropac/zbx-dell-n-series](https://github.com/Elkropac/zbx-dell-n-series), atualmente em [business-communication/zbx-dell-n-series](https://github.com/business-communication/zbx-dell-n-series).

Arquivo consultado: [6.0/zbx-dell-n-series.yaml](https://github.com/business-communication/zbx-dell-n-series/blob/main/6.0/zbx-dell-n-series.yaml), blob c6543d176385e1d943adb5dfc1cb90e0bb005123. Exportação Zabbix 6.0 com data interna de 22/02/2024. O próprio arquivo informa derivação de um template Dell Force S-Series.

A comparação documental de 22/10/2026 encontrou todos os 92 UUIDs desse arquivo na versão adaptada, que contém 96 UUIDs. Isso corrobora a linhagem técnica; não comprova qual commit foi efetivamente baixado na ocasião da adaptação.

## Licença

A árvore pública consultada não contém arquivo de licença, e a API do GitHub não identifica uma licença para esse repositório. Não foi atribuída uma licença presumida ao YAML nem à base de terceiros. A licença e os avisos aplicáveis à base Dell S-Series também precisam ser recuperados.

## Contribuição de Samuel Guigo

Adaptação para Zabbix 7 e Dell N1124P-ON; diagnóstico de importação e itens não suportados; fallback VLAN IF-MIB; ajuste de Process Discovery; correções de Fan/PSU e TempUnit LLD; validação e documentação das alterações.

## Identificação a recuperar

- Commit/revisão efetivamente utilizado na adaptação e autoria individual, se disponível.
- Texto da licença e avisos de autoria presentes na distribuição original.
- Requisitos aplicáveis à redistribuição e às modificações.

## Estrutura e compatibilidade

O YAML em template/ é a cópia de uso atual, sem alteração funcional nesta reorganização. A cópia no caminho antigo foi preservada byte a byte para compatibilidade com links publicados. Alterações futuras devem atualizar ambas enquanto o caminho antigo permanecer suportado.
