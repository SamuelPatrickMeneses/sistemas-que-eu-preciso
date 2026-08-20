# Monitor de editais de concursos

## O problema
Monitorar as páginas de publicação de editais de concursos: acessar periodicamente, detectar mudanças desde a última verificação e recolher novos documentos e suas informações automaticamente (web scraping), passando o conteúdo para o agente de IA a fim de explicar o que há de novo.

## Contexto
Editais saem em portais variados e em datas imprevisíveis; acompanhar manualmente é custoso. Quero automação tipo cron job que verifique as páginas e me traga um resumo do que mudou.

## Ideia de solução (rascunho)
- Cron job / scheduler que acessa as páginas de editais periodicamente.
- Verificação de mudanças desde a última visita (diff/hash do conteúdo ou do índice).
- Web scraping da página para extrair novos documentos (PDFs de editais, retificações, avisos) e metadados (órgão, cargo, prazo, vagas).
- Encaminhamento do material novo para o agente de IA, que explica o que há de novo em linguagem simples.
- Integra com o [sistema de notificações multimodais](sistema-notificacoes-multimodal.md) para avisar quando houver novidades.

## Alternativas atuais
- Acompanhamento manual dos portais; avisos por e-mail de alguns portais; ferramentas genéricas de monitoramento de mudanças (ChangeDetection.io, Distill).

## Notas
- Prioridade: alta
- Data: 2026-08-18

## Critérios de "promoção" para projeto
- Listar os portais/alvos concretos a monitorar e decidir entre monitorar por "mudança genérica" (diff) vs. scraping estruturado (extração de campos).