# Currículo Otimizado por Vaga

> Projeto para gerenciar e gerar currículos otimizados para vagas de emprego, com fidelidade visual ao template escolhido e layout expansível.

## Visão Geral

A partir de uma **fonte única de dados estruturados** e de **templates curados manualmente**, este projeto gera PDFs de currículo com formatação consistente, layouts de duas colunas e suporte a múltiplas páginas sem quebrar a diagramação.

## Arquivos deste Projeto

| Arquivo | Descrição |
|---------|-----------|
| [`prd.md`](prd.md) | Requisitos funcionais (RF-1 a RF-19): fonte de dados, templates, pipeline de geração, tipos de currículo, verificação automática, LGPD |
| [`sdd.md`](sdd.md) | Documento de design em duas seções: Seção 1 - Arquitetura (agnóstica de tecnologia) e Seção 2 - Tecnologias candidatas |
| [`schema.md`](schema.md) | Modelo conceitual agnóstico de armazenamento com 17 módulos (profile → additionalInfo), tipagem, serialização JSON/YAML e regras de validação |
| `referencia/dump_gemini.txt` | Conversa com o Gemini que fundamentou a especificação do projeto |

## Personas

- **Administrador**: Fornece documentos (PDF/DOCX) que se tornarão templates; valida, testa e aprova; persiste templates em JSON no repositório
- **Usuário**: Insere dados via formulários (futuramente pode enviar PDF), escolhe entre templates já testados/aprovados e exporta PDF

## Tipos de Currículo Suportados

- **`vitae`** (Currículo Vitae) - Para candidaturas a vagas
- **`lattes`** (Currículo Lattes) - Registro acadêmico completo

## Recursos Principais

- **Verificação automática**: Relatório de conformidade informando se o currículo se encaixa no tipo escolhido e o que falta/está mal formatado
- **Exportação multi-versão**: A partir da mesma fonte, gerar versões `vitae` e `lattes` distintas
- **LGPD compliant**: Dados sensíveis (CPF, nascimento, filiação) são opcionais; armazenamento observa a Lei 13.709/2018
- **Layout expansível**: Documento pode crescer para múltiplas páginas sem quebras feias (usando `unbreakable: true`)
- **Fidelidade visual**: PDF gerado se parece com o template escolhido (colunas, cores, seções)

## Estrutura de Dados

O modelo `ResumeData` possui 17 módulos modulares:

1. `profile` (obrigatório) - identity, contact, ids, objective, summary
2. `experience` - experiência profissional/vínculos
3. `education` - formação acadêmica
4. `complementaryCourses` - cursos complementares
5. `skills` - competências técnicas e idiomas
6. `certifications` - certificações
7. `projects` - projetos
8. `publications` - produções (bibliográfica, técnica, artística)
8. `patents` - patentes e registros
9. `supervisions` - orientações
10. `events` - eventos e bancas
11. `awards` - prêmios e títulos
12. `editorialActivities` - corpo editorial/revisor
13. `researchLines` - linhas de pesquisa
13. `areasOfExpertise` - áreas do conhecimento (árvore CNPq)
14. `additionalInfo` - informações adicionais

## Licença

Este projeto é para uso pessoal. Consulte os arquivos individuais para termos específicos.