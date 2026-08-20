# Infraestrutura acessível via VPN (AWS → servidor caseiro)

## O problema
Tornar todos esses sistemas acessíveis a partir do celular e do PC via VPN, inicialmente rodando em uma máquina AWS e futuramente em um servidor caseiro feito com um celular velho.

## Contexto
Os projetos (notificações, monitor de editais, IA, etc.) precisam ser acessíveis de qualquer lugar, com segurança. A infraestrutura deve começar na AWS (rápido e estável) e depois migrar para um servidor caseiro de baixo custo baseado em um celular antigo (ex.: Android + Linux via Termux/UserLAnd, ou custom ROM).

## Ideia de solução (rascunho)
- Acesso remoto seguro via VPN (ex.: WireGuard, Tailscale/Headscale) para celular e PC alcançarem os serviços.
- Fase 1 — AWS: instância pequena (EC2) rodando os serviços atrás da VPN, sem expor portas na internet.
- Fase 2 — Caseiro: celular velho como servidor (Linux no Android), mantendo a mesma VPN como ponto de entrada, para reduzir custo de hospedagem.
- Portabilidade: configurar serviços de forma que a migração AWS → caseiro seja simples (containers/scripts, DNS interno).

## Alternativas atuais
- Expor serviços diretamente na internet (com firewall); soluções de túnel (Cloudflare Tunnel, ngrok); VPS tradicional.

## Notas
- Prioridade: alta
- Data: 2026-08-18

## Critérios de "promoção" para projeto
- Decidir o modelo de VPN (Tailscale vs. WireGuard próprio) e o formato de deploy (Docker/scripts) que funcione tanto na AWS quanto no celular-servidor.