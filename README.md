# Sistemas que eu preciso

Repositório para organizar necessidades pessoais que quero transformar em projetos de software.

## Estrutura

```
├── README.md          <- este índice
├── ideas/             <- necessidades brutas, sem formato definido
│   └── <nome-da-ideia>.md
└── projetos/          <- ideias que viraram specs detalhadas
    └── <nome-do-projeto>/
        ├── prd.md     <- Product Requirement Document (o quê e por quê)
        └── sdd.md     <- Software Design Document (como implementar)
```

## Fluxo

1. Anote a necessidade em `ideas/` usando o [template de ideia](ideas/_TEMPLATE.md).
2. Quando a ideia estiver madura, promova para `projetos/<nome>/` com `prd.md` e `sdd.md`.
3. Renomeie o arquivo em `ideas/` para `_arquivado/` ou remova após a promoção.

## Índice de ideias

- [Sistema de notificações multimodais](ideas/sistema-notificacoes-multimodal.md)
- [Agente de IA integrado com Anytype](ideas/agente-ia-anytype.md)
- [Monitor de editais de concursos](ideas/monitor-de-editais-de-concursos.md)
- [Infraestrutura acessível via VPN (AWS → servidor caseiro)](ideas/infraestrutura-acessivel-via-vpn.md)
- [Integração do chat DeepSeek com o agente](ideas/integracao-chat-deepseek-com-agente.md)
- [Mesclador de arquivos de texto para LLM](ideas/mesclador-de-arquivos-de-texto-para-llm.md)

## Índice de projetos

- [Currículo otimizado por vaga](projetos/curriculo-otimizado-por-vaga/) — [PRD](projetos/curriculo-otimizado-por-vaga/prd.md) · [SDD](projetos/curriculo-otimizado-por-vaga/sdd.md) · [Schema](projetos/curriculo-otimizado-por-vaga/schema.md)

## Ideias arquivadas

- [Currículo otimizado por vaga](ideas/_arquivado/curriculo-otimizado-por-vaga.md) — promovida a projeto