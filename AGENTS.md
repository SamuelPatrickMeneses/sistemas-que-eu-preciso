# AGENTS.md

## Commit Convention

This repository follows the commit message format used across Samuel's projects (e.g., ~/Documentos/com-quem-seraa/).

### Format

Every commit message **must** follow this multi-line YAML format:

```yaml
type: <type>           # Required. One of: feat, fix, refactor, test, docs, style, chore, perf, ci, build
files:                 # Optional. List of changed files (use `file:` for single file)
  - <path>             # Each changed file path
description:           # Required. Description in English (can be multi-line with bullet points)
  - <point 1>
  - <point 2>
```

### Rules

- **type**: Must be one of: `feat`, `fix`, `refactor`, `test`, `docs`, `style`, `chore`, `perf`, `ci`, `build`
- **files**: Use `file:` for a single changed file, or `files:` (with list notation) for multiple changed files
- **description**: Required. Describe what the commit changes. Use bullet points (`-`) to list multiple changes or details.
- **Mantenha a formatação consistente**: use sempre o mesmo padrão de linha única observado nos commits anteriores do repositório (ex: `type: feat files: - arquivo.md description: - descrição`), garantindo que o comando-workflow consiga gerar o commit corretamente.
- **Before committing or pushing, you must ask the user for permission.**
- This convention matches the format used in ~/Documentos/com-quem-seraa/ and ensures consistency across projects.

### Examples

#### Single file:

```yaml
type: fix
file: src/main.py
description: Fix typo in function name and correct return value
```

### Multiple files:

```yaml
type: feat
files:
  - src/main.py
  - src/utils.py
description:
  - Add new authentication flow
  - Update session management
  - Fix token expiration bug
```

### Pull Request Template

PRs should follow this template:

```markdown
**Origem:** `<branch>`
**Destino:** `<develop|main>`
**Issues relacionadas:** Closes #X

---

## Descrição

<propósito do PR>

## O que foi feito

- <item 1>
- <item 2>

## Arquivos modificados

```
<tree com paths relativos>
```

## Como testar

```bash
<comandos de validação>
```
```