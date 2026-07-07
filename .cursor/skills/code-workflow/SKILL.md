---
name: code-workflow
description: >-
  Executa ciclo completo de implementação em 9 fases — detecta stack,
  implementa via dev-agent, valida escopo, revisa código e testes, roda
  lint/tests, atualiza docs e reporta. Use para tarefas de código pontuais
  ou quando invocado pelo lead-dev.
disable-model-invocation: true
---

# code-workflow

**Regra de ouro:** implementar apenas o que foi pedido. Proibido scope creep.

Usa `TodoWrite` para acompanhar as 9 fases.

## Pipeline (ordem obrigatória)

### Fase 1 — DETECT

- Lê `AGENTS.md`, `package.json`, `go.mod`, `Makefile`, `pyproject.toml`.
- Detecta: `lint_command`, `test_command`, `format_command`.
- Falha → pergunta ao usuário.

### Fase 2 — CODE

```
Task {
  subagent_type: "generalPurpose",
  description: "dev-agent: implementar",
  prompt: "Siga .cursor/skills/dev-agent/SKILL.md. Tarefa: <...>. Stack: <...>"
}
```

- Verifica `git diff --stat`.
- Falha → retry 1x, depois aborta.

### Fase 3 — LINT

- Executa `lint_command` via shell.
- Issues → `format_command`, re-lint (máx 2 ciclos).
- Persiste → pergunta ao usuário.

### Fase 4 — SCOPE

```
Task {
  subagent_type: "generalPurpose",
  readonly: true,
  description: "scope-guard: pós-código",
  prompt: "Siga .cursor/skills/scope-guard/SKILL.md — modo pós-código.
    Objetivo: <...>. Arquivos: <...>. Diff: <git diff>"
}
```

| Resultado | Ação |
|-----------|------|
| Aprovado | Continua |
| Borderline | Reporta ao lead-dev (não pergunta ao usuário) |
| Bloqueado | Reverte alterações (`git checkout`), reporta ao lead-dev |

### Fase 5 — CODE REVIEW

```
Task {
  subagent_type: "generalPurpose",
  readonly: true,
  description: "review-agent: diff",
  prompt: "Siga .cursor/skills/review-agent/SKILL.md — modo code review.
    Diff: <git diff>"
}
```

Alternativa: `Task(subagent_type: "bugbot", readonly: true)` para review automatizado.

| Finding | Ação |
|---------|------|
| blocker | ABORTA |
| bug | Corrige + re-review (máx 2 ciclos) |
| alerta/sugestão | Registra, prossegue |

### Fase 6 — TEST

- Executa `test_command`.
- Passou → Fase 7.
- Sem testes → continua com nota.
- Falhou → `dev-agent` corrige (1x). Flag se persistir.

### Fase 7 — TEST REVIEW

```
Task {
  subagent_type: "generalPurpose",
  readonly: true,
  description: "review-agent: testes",
  prompt: "Siga .cursor/skills/review-agent/SKILL.md — modo test review.
    Arquivos de teste: <...>"
}
```

Issues → auto-fix, flag no relatório. **Não bloqueia.**

### Fase 8 — DOCS

```
Task {
  subagent_type: "generalPurpose",
  description: "doc-agent: atualizar docs",
  prompt: "Siga .cursor/skills/doc-agent/SKILL.md.
    Arquivos alterados: <...>. Resumo: <...>"
}
```

Skip sem docs → nota no relatório. **Não bloqueia.**

### Fase 9 — REPORT

Tabela com 9 linhas (uma por fase):

```markdown
| Fase | Status | Notas |
|------|--------|-------|
| 1 Detect | ✅/❌/⏭ | ... |
| ... | ... | ... |
```

Incluir: arquivos alterados (+N/-N), riscos, status final.

## Convenções (quando aplicável)

- Branches: `feature/<id>`, `fix/<id>` de `development`; hotfix de `main`.
- Commits: Conventional Commits.
