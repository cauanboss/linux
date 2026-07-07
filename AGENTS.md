# Ecossistema de Agentes — Cursor

Adaptação do pipeline Kilo (`workflow-agent1.md`) para Cursor via Skills + Task subagents.

## Entry points

| Objetivo | Como invocar |
|----------|--------------|
| Feature completa (planejar → executar → consolidar) | `/lead-dev` ou "siga o skill lead-dev" |
| Tarefa de código já definida | `/code-workflow` |
| Só revisão | `/review-agent` ou Task `bugbot` / `security-review` |
| Só validação de escopo | `/scope-guard` |
| Explorar arquitetura | `/ideas-agent` |

## Arquitetura

```
Usuário
  └── lead-dev (orquestrador — NUNCA edita código)
        ├── ideas-agent (opcional, readonly)
        ├── business-rules (opcional, readonly)
        ├── scope-guard (pré-código, readonly)
        ├── code-workflow (até 3 em paralelo via Task)
        │     ├── dev-agent (implementação)
        │     ├── scope-guard (pós-código, readonly)
        │     ├── review-agent (readonly)
        │     └── doc-agent
        └── consolidação + relatório final
```

## Skills

Local: `.cursor/skills/` (projeto) ou `~/.cursor/skills/` (global).

| Skill | Papel | Edita código? |
|-------|-------|---------------|
| `lead-dev` | Orquestrador | Não |
| `code-workflow` | Pipeline 9 fases | Ordena; delega implementação |
| `dev-agent` | Implementação | Sim |
| `review-agent` | Revisão (blocker/bug/alerta/sugestão) | Não |
| `scope-guard` | Escopo (aprovado/borderline/bloqueado) | Não |
| `ideas-agent` | Design/arquitetura | Não |
| `doc-agent` | Documentação | Só docs |
| `business-rules` | Regras de domínio | Não |

## Mapeamento Kilo → Cursor

| Kilo | Cursor |
|------|--------|
| `subagent_type: "code-workflow"` | `Task(subagent_type: "generalPurpose")` + skill `code-workflow` |
| `subagent_type: "dev-agent"` | `Task(subagent_type: "generalPurpose")` + skill `dev-agent` |
| `subagent_type: "review-agent"` | `Task(subagent_type: "generalPurpose", readonly: true)` + skill `review-agent` |
| `subagent_type: "scope-guard-agent"` | `Task(subagent_type: "generalPurpose", readonly: true)` + skill `scope-guard` |
| `subagent_type: "ideas-agent"` | `Task(subagent_type: "explore", readonly: true)` + skill `ideas-agent` |
| `todowrite` | `TodoWrite` |
| `mode: primary` | Usuário invoca skill `lead-dev` no Agent mode |
| `default_agent: code-workflow` | Atalho: invocar `/code-workflow` para tarefas pontuais |

## Critérios de aceitação (por tarefa)

1. Subagent reportou sucesso.
2. Relatório Fase 9 do code-workflow: Lint + Test (ou justificativa).
3. `git diff --stat` mostra apenas arquivos esperados.
4. Nenhum secret/PII no diff.

## Documentação

- Spec original Kilo: [workflow-agent1.md](workflow-agent1.md)
- Guia de adaptação Cursor: [workflow-cursor.md](workflow-cursor.md)
- Fluxo lead-dev resumido: [resumo-workflow.text](resumo-workflow.text)
