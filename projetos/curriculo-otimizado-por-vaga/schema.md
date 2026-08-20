# Schema de Dados — Currículo otimizado por vaga

> Modelo conceitual **agnóstico de armazenamento** dos dados do candidato (`ResumeData`).
> A aplicação usará um banco de dados ainda não especificado; a exportação em **JSON** e **YAML** é desejável e ambas são derivadas deste mesmo modelo.
> Referências: [PRD](prd.md) (RF-1, RF-2) e [SDD](sdd.md) (contrato `ResumeData`).

---

## 1. Convenções

### 1.1 Tipos

| Tipo | Descrição | Exemplo |
|---|---|---|
| `string` | Texto curto (nome, cargo) | `"Engenheira de Dados"` |
| `text` | Texto longo (descrições, resumos) | `"Experiência de 5 anos com..."` |
| `int` | Inteiro | `40` (carga horária) |
| `decimal` | Número com casas | `8.5` (nota) |
| `bool` | Verdadeiro/falso | `true` |
| `date` | Data ou referência de ano/mês | `2021-03` |
| `dateRange` | Período com status (ver 1.2) | `{ início: 2021-03, fim: null, status: atual }` |
| `enum` | Valor de uma lista fechada | `"graduação"` |
| `list` | Lista homogênea de itens | `[ "Python", "SQL" ]` |
| `ref` | Referência a outra entidade do modelo | `projetos.equipe[].pessoa` |

### 1.2 Datas e períodos (`dateRange`)

- Data completa: `AAAA-MM` (mês e ano) ou `AAAA` (somente ano).
- Campo `fim` pode ser nulo quando a atividade ainda está em curso.
- `status`: `concluído | em_andamento | incompleto | atual`.
  - `atual` aplica-se a vínculos/experiências em curso (equivale a "Atual" no CV / "Em andamento" no Lattes).
- O preenchimento mínimo é o ano de início; mês é opcional.

### 1.3 Visibilidade e privacidade

- Todo módulo e todo campo sensível carrega um atributo de visibilidade:
  - `privado` — armazenado, nunca renderizado em currículos de exibição (ex.: CPF, nascimento, filiação).
  - `público` — pode ser exibido conforme o template.
- O mecanismo de renderização filtra pelo template; dados privados não aparecem em nenhum CV.
- **Fornecimento opcional**: dados sensíveis (CPF, nascimento, filiação) são **opcionais**. Armazená-los implica observar a **LGPD** — ver [Seção 6](#6-lgpd-e-dados-sensíveis).
- O objetivo é manter uma **fonte única** que sirva tanto a um CV (marketing) quanto a um registro acadêmico (Lattes), sem duplicação.

### 1.4 Multi-idioma

- Campos de texto com possibilidade de tradução (ex.: `summary`) usam um mapa de idioma → texto:
  `{ "pt": "...", "en": "..." }`.

### 1.5 Modularidade

- **Somente `profile` é obrigatório.** Todos os demais módulos são omitíveis.
- Um módulo ausente significa "não declarado" (não existe → não é renderizado, não é validado).
- A renderização escolhe módulos via `TemplateConfig`; dados existentes mas não selecionados ficam de fora do PDF.

---

## 2. Módulos

> Convenção das tabelas: campo · tipo · obrigatoriedade · descrição · exemplos.

### 2.1 `profile` — Núcleo (sempre presente)

#### `profile.identity` — Identificação
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `nome` | `string` | sim | Nome completo. Topo da página nos CVs. |
| `nomeEmCitações` | `list[string]` | não | Variantes do nome para citações bibliográficas (registro Lattes). |
| `sexo` | `enum` | não | `feminino \| masculino \| outro \| não_informado` (registro). |
| `nascimento` | `date` | não | Data de nascimento. **Privado.** |
| `paísDeNascimento` | `string` | não | País de nascimento. **Privado.** |
| `ufDeNascimento` | `string` | não | UF de nascimento. **Privado.** |
| `cidadeDeNascimento` | `string` | não | Cidade de nascimento. **Privado.** |
| `filiação` | `list[{ pai, mãe }]` | não | Nomes dos pais (registro Lattes). **Privado.** |

#### `profile.contact` — Contato (exibível)
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `cidade` | `string` | não | Cidade de residência atual. |
| `uf` | `string` | não | Estado de residência atual. |
| `país` | `string` | não | País de residência atual. |
| `telefones` | `list[Telefone]` | não | Telefone: `{ número, tipo, whatsapp }` — `tipo`: `celular \| fixo \| ramal`. |
| `emails` | `list[Email]` | não | Email: `{ endereço, tipo }` — `tipo`: `pessoal \| profissional`. |
| `redes` | `list[Rede]` | não | Rede: `{ nome, url }` — ex.: LinkedIn, GitHub, site pessoal. |
| `endereçoCompleto` | `Endereço` | não | Endereço completo (registro Lattes). Campo `privado` por padrão. |

> `Endereço`: `{ tipo (residencial/profissional), instituição, órgão, logradouro, bairro, país, uf, cidade, cep, telefone, ramal }`.

#### `profile.ids` — Identificadores (registro)
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `cpf` | `string` | não | CPF. **Privado** — nunca exibido em CV. |
| `passaporte` | `string` | não | Passaporte (estrangeiros). **Privado.** |
| `idLattes` | `string` | não | ID Lattes (16 dígitos). |
| `urlLattes` | `string` | não | URL do currículo Lattes. |
| `orcid` | `string` | não | ORCID. |

#### `profile.objective` — Objetivo profissional
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `cargos` | `list[string]` | não | Cargo ou área de atuação. **Um por vaga** — lista permite múltiplos perfis sem duplicar o resto dos dados. |
| `foco` | `text` | não | Descrição do foco pretendido (contribuição à empresa). |

#### `profile.summary` — Resumo profissional
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `texto` | `map[idioma]→text` | não | Resumo/biografia. Multi-idioma (PT/EN). Unifica o "Resumo Profissional" do CV e o "Texto de Resumo" do Lattes. |
| `tempoDeExperiência` | `string` | não | Tempo total de atuação (ex.: "5 anos em engenharia de dados"). |
| `conquistasPrincipais` | `list[text]` | não | Resultados/feitos de destaque (o CV pede para mostrar, não autoelogiar). |

### 2.2 `experience` — Experiência profissional / vínculos

Unifica o "Experiência Profissional" do CV com o "Vínculo Institucional" do Lattes.

| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `organização` | `string` | sim | Nome da empresa/instituição. |
| `cargo` | `string` | sim | Cargo/função ocupada (título oficial). |
| `tipo` | `enum` | não | `empregador \| servidor_publico \| celetista \| bolsista \| colaborador \| autônomo \| outro` |
| `enquadramento` | `string` | não | Enquadramento funcional (Lattes). |
| `período` | `dateRange` | sim | Início/fim + status (`atual` para "Atual"). |
| `local` | `string` | não | Cidade/UF do vínculo (se relevante). |
| `cargaHoráriaSemanal` | `int` | não | Carga horária semanal (Lattes). |
| `dedicaçãoExclusiva` | `bool` | não | Dedicação exclusiva (Lattes). |
| `atividades` | `list[Atividade]` | não | `{ tipo (ensino/pesquisa/direção/extensão/assistência_técnica/outra), órgão, período, especificação }` |
| `resultados` | `list[text]` | não | Conquistas numéricas/melhorias (ex.: "reduziu custos em 20%"). |
| `unbreakable` | `bool` | não | Sugestão de renderização: manter bloco inteiro numa página. Default `true`. |

### 2.3 `education` — Formação acadêmica / titulação

Unifica a "Formação Acadêmica" do CV com "Formação/Titulação" do Lattes.

| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `nível` | `enum` | sim | `técnico \| graduação \| especialização \| mestrado \| doutorado \| pós_doutorado \| livre_docência` |
| `curso` | `string` | sim | Nome do curso. |
| `instituição` | `string` | sim | Nome da instituição de ensino. |
| `status` | `enum` | sim | `concluído \| em_andamento \| incompleto` |
| `período` | `dateRange` | sim | Início/fim (previsão de término quando `em_andamento`). |
| `títuloDoTrabalho` | `string` | não | Dissertação/tese/monografia. |
| `orientador` | `string` | não | Nome do orientador. |
| `coorientador` | `string` | não | Nome do coorientador (opcional). |
| `bolsa` | `list[{ agência, tipo }]` | não | Agência de fomento/bolsa (CNPq, CAPES...). |
| `palavrasChave` | `list[string]` | não | Palavras-chave do trabalho. |
| `códigoCapes` | `string` | não | Código do curso CAPES (se aplicável). |

### 2.4 `complementaryCourses` — Formação complementar
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `nome` | `string` | sim | Nome do curso. |
| `tipo` | `enum` | não | `curta_duração \| extensão \| aperfeiçoamento \| mba \| outro` |
| `instituição` | `string` | sim | Instituição promotora. |
| `status` | `enum` | sim | `concluído \| em_andamento` |
| `período` | `dateRange` | sim | Início/fim. |
| `cargaHorária` | `int` | não | Em horas. |

### 2.5 `skills`

#### `skills.technical` — Competências técnicas
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `nome` | `string` | sim | Ferramenta/software/habilidade (Excel, SAP, Python...). |
| `categoria` | `string` | não | Agrupamento (linguagem, framework, ferramenta, infra...). |
| `nível` | `enum` | não | `básico \| intermediário \| avançado` (opcional; CV geralmente não usa). |

#### `skills.languages` — Idiomas
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `idioma` | `string` | sim | Nome da língua. |
| `nível` | `string` | não | Nível simplificado do CV ("Avançado", "Intermediário"). |
| `proficiências` | `map` | não | Nível por habilidade (Lattes): `{ leitura, escrita, fala, compreensão }` com valores `pouco \| razoável \| bem`. |

> Suporta os dois níveis de granularidade: um OU outro pode ser preenchido, ou ambos.

### 2.6 `certifications` — Certificações
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `nome` | `string` | sim | Nome da certificação/licença. |
| `emissor` | `string` | sim | Entidade emissora. |
| `ano` | `int` | sim | Ano de obtenção. |
| `url` | `string` | não | Link/credencial de verificação. |
| `validade` | `date` | não | Data de expiração (se houver). |

### 2.7 `projects` — Projetos (pessoais e acadêmicos)
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `nome` | `string` | sim | Nome do projeto. |
| `tipo` | `enum` | não | `pessoal \| pesquisa \| desenvolvimento \| extensão \| ensino \| outro` |
| `descrição` | `text` | não | Objetivo/descrição. |
| `status` | `enum` | sim | `em_andamento \| concluído \| descontinuado` |
| `período` | `dateRange` | sim | Início/fim. |
| `instituição` | `string` | não | Instituição de execução (Lattes). |
| `papel` | `enum` | não | `coordenador \| integrante \| autor` |
| `equipe` | `list[Pessoa]` | não | `{ nome, função }` (relação N:M). |
| `financiamento` | `list[{ agência, natureza }]` | não | Bolsa/auxílio financeiro. |
| `links` | `list[string]` | não | Repositórios/artefatos (GitHub, portfólio). |

### 2.8 `publications` — Produções (Lattes)

> Módulo pesado do registro acadêmico; subdivide-se em três vertentes. Cada vertente é uma lista com o próprio esquema.

#### `publications.bibliographic`
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `tipo` | `enum` | sim | `artigo \| livro \| capítulo \| anais \| jornal \| organização` |
| `título` | `string` | sim | Título da obra/trabalho. |
| `veículo` | `string` | não | Periódico/evento/jornal. |
| `autores` | `list[string]` | sim | Lista ordenada de autores. |
| `ano` | `int` | sim | Ano de publicação. |
| `detalhes` | `map` | não | ISSN, DOI, ISBN, volume, páginas, editora, idioma, país. |

#### `publications.technical`
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `tipo` | `enum` | sim | `software \| produto \| processo \| parecer \| relatório \| consultoria \| curso_ministrado \| mídia \| outro` |
| `título` | `string` | sim | Título/nome comercial. |
| `ano` | `int` | sim | Ano de finalização. |
| `disponibilidade` | `enum` | não | `restrita \| pública` |
| `finalidade` | `text` | não | Descrição/finalidade. |
| `instituição` | `string` | não | Instituição promotora/contratante. |
| `autores` | `list[string]` | sim | Lista ordenada. |
| `cargaHorária` | `int` | não | Para cursos ministrados. |

#### `publications.artistic`
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `natureza` | `enum` | sim | `música \| artes_visuais \| teatro \| cinema \| dança \| literatura` |
| `título` | `string` | sim | Título da obra. |
| `ano` | `int` | sim | Ano. |
| `local` | `string` | não | Local de apresentação/exibição. |
| `instituição` | `string` | não | Instituição promotora. |
| `autores` | `list[string]` | sim | Autores/diretores. |

### 2.9 `patents` — Patentes e registros
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `tipo` | `enum` | sim | `invenção \| modelo_utilidade \| marca \| desenho_industrial \| cultivar \| topografia_circuito` |
| `título` | `string` | sim | Título do registro. |
| `instituição` | `string` | não | Depositante/titular. |
| `númeroRegistro` | `string` | não | Número INPI/internacional. |
| `dataDepósito` | `date` | não | Data do depósito. |
| `dataConcessão` | `date` | não | Data da concessão. |
| `status` | `enum` | sim | `depositada \| concedida \| caducada` |
| `inventores` | `list[string]` | sim | Inventores/autores. |

### 2.10 `supervisions` — Orientações
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `papel` | `enum` | sim | `orientador \| coorientador` |
| `tipo` | `enum` | sim | `tcc \| iniciação_científica \| especialização \| mestrado \| doutorado \| pós_doutorado` |
| `orientado` | `string` | sim | Nome do orientando. |
| `título` | `string` | não | Título do trabalho. |
| `instituição` | `string` | não | Instituição. |
| `período` | `dateRange` | sim | Início/fim (fim nulo = em andamento). |
| `bolsa` | `list[{ agência, tipo }]` | não | Agência de fomento (se aplicável). |

### 2.11 `events` — Eventos e bancas
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `tipo` | `enum` | sim | `participação \| organização \| banca_examinadora \| banca_concurso` |
| `nome` | `string` | sim | Nome do evento/concurso. |
| `ano` | `int` | sim | Ano de realização. |
| `local` | `string` | não | País/UF/cidade. |
| `formaParticipação` | `enum` | não | `convidado \| participante \| ouvinte \| apresentador \| coordenador \| membro_comitê` |
| `títuloTrabalho` | `string` | não | Trabalho apresentado/avaliado. |
| `instituição` | `string` | não | Instituição promotora. |

### 2.12 `awards` — Prêmios e títulos
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `nome` | `string` | sim | Nome do prêmio/título. |
| `entidade` | `string` | não | Entidade promotora/concedente. |
| `ano` | `int` | sim | Ano de premiação. |

### 2.13 `editorialActivities` — Corpo editorial / revisor
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `veículo` | `string` | sim | Periódico/revista. |
| `issn` | `string` | não | ISSN. |
| `papel` | `enum` | sim | `corpo_editorial \| revisor` |
| `período` | `dateRange` | sim | Início/fim. |

### 2.14 `researchLines` — Linhas de pesquisa
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `título` | `string` | sim | Título da linha. |
| `descrição` | `text` | não | Objetivos/descrição. |
| `status` | `enum` | sim | `ativa \| inativa` |

### 2.15 `areasOfExpertise` — Áreas do conhecimento (árvore CNPq)
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `grandeÁrea` | `string` | sim | Ex.: Ciências Exatas e da Terra. |
| `área` | `string` | sim | Ex.: Ciência da Computação. |
| `subárea` | `string` | não | Ex.: Metodologia e Técnicas de Computação. |
| `especialidade` | `string` | não | Ex.: Engenharia de Software. |

### 2.16 `additionalInfo` — Outras informações relevantes
| Campo | Tipo | Obrig. | Descrição |
|---|---|---|---|
| `itens` | `list[text]` | não | Texto livre (licenças, situações especiais, observações). |
| `trabalhoVoluntário` | `list[text]` | não | Experiências não remuneradas relevantes (CV). |

---

## 3. Tipos de currículo e verificação automática

> Como sabemos quais partes são exigidas por cada tipo de currículo e os formatos exigidos, o sistema pode **verificar automaticamente** se um conjunto de dados se enquadra no tipo e exportar versões distintas a partir da mesma fonte.

### 3.1 Tipos suportados

| Tipo | Finalidade | Referência |
|---|---|---|
| `vitae` | Candidatura a vagas (marketing, 1-2 páginas) | Currículo Vitae |
| `lattes` | Registro acadêmico completo | Currículo Lattes |

> Extensível: novos tipos podem ser definidos seguindo a mesma tabela de exigências (ex.: `linkedin`).

### 3.2 Exigências por tipo

> Cada tipo declara: **módulos/campos obrigatórios**, **campos proibidos** e **regras de formato**. A verificação automática consome exatamente esta declaração.

#### `vitae` (Currículo Vitae)

**Obrigatórios:**
- `profile.identity.nome`
- `profile.contact`: `cidade` + `uf`, ao menos um `telefones[]` (com DDD), ao menos um `emails[]`
- `profile.objective.cargos` — **um único** cargo/área (foco único; "um currículo por área")
- `profile.summary.texto`
- `experience[]`: `organização`, `cargo`, `período`, `atividades[]` e/ou `resultados[]`
- `education[]`: `curso`, `instituição`, `status`
- `skills.technical[]`
- `skills.languages[]` com `nível`

**Recomendados:** `redes[].linkedin`; `certifications[]`; `projects[]`; `additionalInfo.trabalhoVoluntário`.

**Proibidos (nunca renderizar):**
- `profile.ids.cpf`, `passaporte`
- `profile.identity`: `nascimento`, `filiação` (e `sexo`, salvo se o candidato desejar)
- Salário/pretensão, motivo de saída, referências de chefes, autoelogios vazios — não há campos para isto no schema; `additionalInfo` não deve ser usado para isso.

**Formatos exigidos:**
- Telefone: `+55 (DDD) número` com indicador de WhatsApp.
- E-mail: formato válido, preferencialmente com nome/sobrenome.
- Períodos: `AAAA-MM` ou `AAAA`; "Atual" = `status: atual`.
- Idiomas: nível explícito (`Avançado`, `Intermediário`, `Básico`).

#### `lattes` (Currículo Lattes)

**Obrigatórios (registro):**
- `profile.identity`: `nome`, `nomeEmCitações`, `sexo`, `nascimento`, `paísDeNascimento`, `ufDeNascimento`, `cidadeDeNascimento`, `filiação`
- `profile.ids`: `cpf`, `idLattes`, `urlLattes`, `orcid`
- `profile.contact.endereçoCompleto` (endereço de referência)
- `profile.summary.texto` — idealmente PT **e** EN
- `skills.languages[]` com `proficiências` (`leitura`, `escrita`, `fala`, `compreensão`)
- `education[]` completo: `nível`, `status`, `período`, `instituição`, `curso`, `orientador`
- `experience[]` com perfil de vínculo: `tipo`, `enquadramento`, `cargaHoráriaSemanal`, `dedicaçãoExclusiva`, `atividades[]`
- `areasOfExpertise[]`
- `publications` (bibliográfica, técnica e/ou artística)

**Opcionais:** `projects[]` (com `financiamento`), `patents[]`, `supervisions[]`, `events[]`, `awards[]`, `editorialActivities[]`, `researchLines[]`.

**Proibidos:** nenhum campo do schema é proibido no Lattes; a restrição é de **completude**.

**Formatos exigidos:**
- `idLattes`: 16 dígitos.
- `orcid`: formato `0000-0000-0000-0000`.
- `cpf`: 11 dígitos.
- Datas completas para nascimento.
- `proficiências`: valores `pouco | razoável | bem`.

### 3.3 Verificação automática

- A **função de verificação** consome a declaração do tipo (Seção 3.2) + os dados e produz um **relatório de conformidade**:
  - itens presentes ✓
  - itens ausentes ✗ (campos obrigatórios faltando)
  - itens mal formatados ✗ (regra de formato violada)
  - itens proibidos ⚠ (presentes nos dados, bloqueados para o tipo)
- Saída: lista de pendências com referência ao campo (`module.campo`) e a regra violada — o usuário sabe **se o currículo se enquadra** e **o que falta**.
- A verificação roda antes da exportação (gate) e independe do renderizador.

### 3.4 Exportação por tipo (multi-versão)

- A exportação escolhe um **tipo** (`vitae` ou `lattes`) + um template.
- Se os dados forem suficientes → gera o PDF (motor de injeção → renderizador).
- Se faltarem dados → retorna o relatório de pendências em vez do PDF.
- Permite gerar **versões diferentes do mesmo currículo** (ex.: versão vitae e versão lattes) a partir da fonte única, desde que as informações sejam suficientes para o tipo escolhido.

---

## 4. Regras de validação

| Regra | Descrição |
|---|---|
| **Obrigatoriedade mínima** | Somente `profile` (e dentro dele, no mínimo `identity.nome` + `contact.emails` ou `contact.telefones`). |
| **Modularidade** | Módulos ausentes são ignorados (não validados, não renderizados). |
| **Privacidade** | Campos `privado` nunca são renderizados; validação de CV deve rejeitar se um template tentar inclui-los. |
| **Períodos** | `fim` antes de `início` é erro. `status` `atual/em_andamento` exige `fim` nulo. |
| **Sanitização** | Aplicada no pipeline (SDD, Seção 1): normalizar espaços, remover quebras fantasmas, limitar comprimento de strings da sidebar e listas longas (ex.: max 15 competências). |
| **Limites por módulo** | Listas com limite recomendado (competencias, certificações) são restrições de *renderização/template*, não de armazenamento. |

---

## 5. Serialização

O modelo conceitual independe de formato. As representações **JSON** e **YAML** são derivadas diretamente das seções 1-4 (mesmos nomes de campos, mesmas regras).

### 4.1 Exemplo YAML (fonte de verdade editável manualmente)

```yaml
profile:
  identity:
    nome: "Bruno Albuquerque"
  contact:
    cidade: "Curitiba"
    uf: "PR"
    telefones:
      - numero: "+55 41 99999-9999"
        tipo: celular
        whatsapp: true
    emails:
      - endereco: "bruno.albuquerque@email.com"
        tipo: profissional
    redes:
      - nome: LinkedIn
        url: "https://linkedin.com/in/brunoalbuquerque"
  objective:
    cargos: ["Engenheiro de Dados"]
  summary:
    texto:
      pt: "Engenheiro de dados com 5 anos de experiência em pipelines..."
      en: "Data engineer with 5 years of experience building pipelines..."
    tempoDeExperiencia: "5 anos"
experience:
  - organizacao: "Tech Solutions"
    cargo: "Tech Lead"
    tipo: empregador
    periodo: { inicio: 2024-01, fim: null, status: atual }
    atividades:
      - tipo: desenvolvimento
        especificacao: "Automação de pipelines de dados em larga escala"
    resultados:
      - "Reduziu custo de processamento em 30%"
education:
  - nivel: graduacao
    curso: "Ciência da Computação"
    instituicao: "UFPR"
    status: concluido
    periodo: { inicio: 2016, fim: 2020 }
skills:
  technical:
    - nome: Python
      categoria: linguagem
    - nome: SQL
      categoria: banco de dados
  languages:
    - idioma: Inglês
      nivel: Avançado
      proficiencias: { leitura: bem, escrita: bem, fala: razoavel, compreensao: bem }
```

### 4.2 Exemplo JSON (exportação programática)

```json
{
  "profile": {
    "identity": { "nome": "Bruno Albuquerque" },
    "contact": {
      "cidade": "Curitiba",
      "uf": "PR",
      "telefones": [{ "numero": "+55 41 99999-9999", "tipo": "celular", "whatsapp": true }],
      "emails": [{ "endereco": "bruno.albuquerque@email.com", "tipo": "profissional" }],
      "redes": [{ "nome": "LinkedIn", "url": "https://linkedin.com/in/brunoalbuquerque" }]
    },
    "objective": { "cargos": ["Engenheiro de Dados"] },
    "summary": {
      "texto": { "pt": "Engenheiro de dados com 5 anos de experiência...", "en": "Data engineer..." },
      "tempoDeExperiencia": "5 anos"
    }
  },
  "experience": [
    {
      "organizacao": "Tech Solutions",
      "cargo": "Tech Lead",
      "tipo": "empregador",
      "periodo": { "inicio": "2024-01", "fim": null, "status": "atual" },
      "atividades": [{ "tipo": "desenvolvimento", "especificacao": "Automação de pipelines" }],
      "resultados": ["Reduziu custo de processamento em 30%"]
    }
  ],
  "education": [
    {
      "nivel": "graduacao",
      "curso": "Ciência da Computação",
      "instituicao": "UFPR",
      "status": "concluido",
      "periodo": { "inicio": 2016, "fim": 2020 }
    }
  ],
  "skills": {
    "technical": [
      { "nome": "Python", "categoria": "linguagem" },
      { "nome": "SQL", "categoria": "banco de dados" }
    ],
    "languages": [
      { "idioma": "Inglês", "nivel": "Avançado", "proficiencias": { "leitura": "bem", "escrita": "bem", "fala": "razoavel", "compreensao": "bem" } }
    ]
  }
}
```

### 4.3 Notas de armazenamento

- O banco futuro (não especificado) implementará o **mesmo modelo conceitual**; JSON/YAML são apenas formatos de troca/persistência atual.
- Campos com mapa de idioma podem ser normalizados em tabelas de tradução no banco, mas o contrato de dados permanece o do schema.
- A exportação deve ser determinística (ordem estável dos campos/itens) para permitir diff entre versões.
- **Templates** (`TemplateConfig`) seguem persistência própria: **JSON versionado no repositório do projeto** (ver [SDD §1.6](sdd.md#16-ciclo-de-vida-do-template)), curado e aprovado pelo administrador.

---

## 6. LGPD e dados sensíveis

> Lei Geral de Proteção de Dados (Lei 13.709/2018).

### 6.1 Princípios aplicados ao schema

- **Fornecimento opcional**: CPF, passaporte, nascimento, filiação e endereço completo são **opcionais**. O sistema funciona sem eles (o tipo `vitae` não os exige).
- **Minimização**: não coletar dados além do necessário para a finalidade do currículo.
- **Finalidade e base legal**: o armazenamento de dados pessoais (inclusive sensíveis) exige finalidade definida e base legal (normalmente **consentimento** do titular — o próprio candidato).
- **Segurança**: dados marcados `privado` devem ser **criptografados em repouso e em trânsito** no banco/armazenamento.
- **Direitos do titular (art. 18)**: garantir acesso, correção, anonimização, portabilidade e **exclusão** dos dados armazenados.
- **Retenção**: definir prazo de guarda e política de descarte quando os dados deixarem de ser necessários.
- **Exibição**: campos `privado` jamais são renderizados em CVs; a validação por tipo rejeita qualquer template que tente incluí-los.

### 6.2 Obrigações de implementação

| Obrigação | Onde se aplica |
|---|---|
| Consentimento registrado | Antes de persistir dados `privado` |
| Criptografia em repouso | Banco de dados (campos `privado`) |
| Tela de acesso/correção/exclusão | Aplicação (direitos do titular) |
| Política de retenção/descarte | Aplicação + banco |
| Registro das operações | Logs/auditoria de acesso a dados `privado` |

> Nota: esta seção orienta o desenho; detalhes legais definitivos devem ser revisados com fonte jurídica especializada antes da implantação.

---

## 7. Backlog e fora de escopo

- **Mapeamento XML do Lattes** — adiado (decisão explícita). Quando entrar, o alvo do import será este mesmo modelo.
- **Otimização por vaga** — futura; consumirá `profile.objective` e `skills` para filtrar/reescrever conteúdo.
- **Edição visual** — fora de escopo (fonte é manual).
- **Migração de dados** — quando o banco for definido, criar procedimento de import de YAML/JSON.
- **Verificação por tipo** — a declaração de exigências (Seção 3.2) é a base; a implementação do verificador e da exportação multi-versão entra no desenvolvimento do pipeline (ver SDD).