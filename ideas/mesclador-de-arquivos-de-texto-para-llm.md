# Mesclador de arquivos de texto para LLM

## O problema
Pegar todos os arquivos de texto selecionados de uma pasta (de preferência via regex) e mesclá-los em um único arquivo de texto, com marcações de onde cada um começa e termina — para alimentar LLMs mais facilmente.

## Contexto
Alimentar um LLM com vários arquivos avulsos é trabalhoso (copiar um por um, perder contexto de origem). Um arquivo único com delimitadores claros de início/fim de cada arquivo facilita o paste/upload e a compreensão do modelo.

## Ideia de solução (rascunho)
- CLI simples: `merge-text --dir <pasta> --include "<regex>"` (e talvez `--exclude`).
- Saída: um arquivo único concatenado com marcadores, ex.:
  ```
  <arquivo: rel/path/arquivo.md>
  <início>
  ...conteúdo...
  <fim>
  ```
- Opções úteis: ignorar pastas (.git), limite de tamanho, seguir ordem, sumário inicial com a lista de arquivos incluídos.

## Alternativas atuais
- **Em uso desde 2026-08-19:** [Mergerocket](https://github.com/dawidrylko/mergerocket) — CLI com marcadores de início/fim customizáveis, blacklist por extensão e respeito ao `.gitignore`.
- Copiar manualmente cada arquivo; `cat` com headers ad-hoc; outras ferramentas: [merge-to-md](https://github.com/freakynit/merge-to-md) (regex + code fences), [repo-to-txt](https://github.com/thenarfer/repo-to-txt), [gitingest](https://github.com/coderamp-labs/gitingest).

## Status
- **Adotada ferramenta pronta (Mergerocket) — ainda NÃO resolvida.** Usar enquanto avalia a necessidade. Critérios de resolução: decidir se Mergerocket cobre os casos reais (seleção por regex de inclusão, limites de tamanho) ou se vale desenvolver uma ferramenta própria que integre com os demais sistemas deste repo.

## Notas
- Prioridade: baixa
- Data: 2026-08-18

## Critérios de "promoção" para projeto
- Definir formato dos marcadores e os casos de uso reais (ex.: alimentar o agente de codificação, montar contexto para o [agente de IA](agente-ia-anytype.md)).