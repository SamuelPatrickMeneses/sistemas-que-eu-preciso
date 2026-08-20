# Commit Convention

This repository follows the commit message format used in ~/Documentos/com-quem-seraa/.

## Format

Every commit message **must** follow this multi-line format:

```yaml
type: <type>           # Required. One of: feat, fix, refactor, test, docs, style, chore, perf, ci, build
files:                 # Optional. List of changed files (use 'file:' for single file)
  - <path>             # Each changed file path
description:           # Required. Description in English (can be multi-line with bullet points)
  - <point 1>
  - <point 2>
```

## Rules

- **type**: Must be one of: `feat`, `fix`, `refactor`, `test`, `docs`, `style`, `chore`, `perf`, `ci`, `build`
- **files**: Use `file:` for a single changed file, or `files:` (with list notation) for multiple changed files
- **description**: Required. Describe what the commit changes. Use bullet points (`-`) to list multiple changes or details.
- This convention matches the format used in ~/Documentos/com-quem-seraa/

## Examples

### Single file:

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