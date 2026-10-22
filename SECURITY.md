# Security

Este projeto é um template de monitoramento e não deve armazenar segredos.

## Não publicar

- SNMP communities
- Usuários/senhas SNMPv3
- Endereços IP internos
- DNS internos
- nomes de clientes/ambientes
- screenshots com credenciais ou dados de produção

Use macros, configurações do host e mecanismos de secrets do ambiente para manter credenciais fora do Git.

Se publicar um fork, revise o histórico do Git antes de torná-lo público: remover um segredo apenas do último commit não o remove dos commits anteriores.
