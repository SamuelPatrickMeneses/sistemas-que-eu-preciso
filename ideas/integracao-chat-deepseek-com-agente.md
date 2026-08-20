# Integração do chat DeepSeek com o agente

## O problema
Integrar o chat do DeepSeek (usado no app Android e no navegador) com o agente de IA, para que as conversas do DeepSeek alimentem/controlem o sistema de agentes.

## Contexto
Uso o DeepSeek como assistente no celular e no navegador, e também uso modelos DeepSeek v4 Flash dentro de agentes de codificação como este. A integração provavelmente não é difícil porque o mesmo provedor/modelo já é usado em agentes — a ponte seria expor o chat ao pipeline do agente (histórico, novas mensagens, comandos).

## Ideia de solução (rascunho)
- Usar a API oficial do DeepSeek para conectar o app/browser (ou os dados do chat) ao agente.
- Sincronizar conversas: o agente lê/escreve no mesmo contexto do chat.
- Disparar ações do agente a partir de mensagens do chat (ex.: pedir para monitorar um edital, gerar um documento).
- Potencialmente um gateway que roteia chamadas de modelos (o mesmo que o agente de codificação usa) para o chat.

## Alternativas atuais
- Copiar/colar conversas entre o app do DeepSeek e o agente; usar cada um de forma isolada.

## Notas
- Prioridade: média
- Data: 2026-08-18

## Critérios de "promoção" para projeto
- Definir o que "integrar" significa concretamente: apenas sincronizar histórico, ou permitir que o chat dispare ações do agente (ex.: "adiciona essa vaga ao monitor").