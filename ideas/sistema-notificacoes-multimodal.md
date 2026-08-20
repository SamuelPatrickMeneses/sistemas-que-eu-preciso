# Sistema de notificações multimodais

## O problema
Ser notificado de coisas importantes de forma multi modal: push notifications no celular e no PC, alarmes e afins.

## Contexto
Preciso de um sistema simples e fácil de integrar com outras coisas — ou seja, que outras ferramentas possam disparar notificações sem fricção.

## Ideia de solução (rascunho)
- Um serviço/API central de notificações com múltiplos canais:
  - Push no celular (ex.: ntfy, Pushover, FCM, Telegram).
  - Push/desktop no PC.
  - Alarmes sonoros/visuais.
- Fácil de integrar: HTTP simples (webhook/curl) ou biblioteca leve.

## Alternativas atuais
- Notificações nativas de cada app; apps de tarefas (TODO, calendário); serviços prontos como ntfy.sh, Pushover, etc.

## Notas
- Prioridade: alta
- Data: 2026-08-18

## Critérios de "promoção" para projeto
- Escolher o(s) canal(is) concreto(s) de push e definir a API de integração.