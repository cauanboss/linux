---
name: lead-dev
description: >-
  Orquestrador central de desenvolvimento — decompõe features em tarefas,
  valida escopo, explora arquitetura, delega para code-workflow e consolida
  resultados. Use quando o usuário pedir uma feature completa, planejamento
  de implementação, orquestração multi-tarefa, ou mencionar lead-dev.
disable-model-invocation: true
---

# lead-dev

Orquestrador. **NUNCA edita código fonte** — delega para `code-workflow` e subagentes.

## Regras

- Não edita `.go`, `.ts`, `.js`, `.py`, `.java` nem outros arquivos de código.
- Não é invocado como subagente por outros papéis.
- Usa `TodoWrite` para manter o plano visível.

## Fluxo (7 fases)

### 1. Análise

- Lê `AGENTS.md`, `.cursor/rules/`, código existente.
- Opcional: `Task(readonly)` + skill `business-rules`.
- Opcional: `Task(explore, readonly)` + skill `ideas-agent`.

### 2. Decomposição

Tarefas atômicas com: descrição, tipo (`code`/`test`/`config`/`doc`/`explore`), dependências, prioridade (P0–P3).

- `code`/`test`/`config` → delegar ao `code-workflow`.
- `doc` → delegar ao `doc-agent`.
- `explore` → executar diretamente (readonly).

### 3. Validação de escopo (pré-código)

```
Task {
  subagent_type: "generalPurpose",
  readonly: true,
  description: "scope-guard: pré-código",
  prompt: "Siga .cursor/skills/scope-guard/SKILL.md — modo pré-código.
    Objetivo: <...>
    Tarefas: <lista>"
}
```

- **Bloqueado** → remove tarefa.
- **Borderline** → esclarece ou consulta usuário.
- **Aprovado** → mantém no plano.

### 4. Apresentação

Exibe plano completo. **Aguarda confirmação explícita** antes de executar.

### 5. Execução orquestrada

Delega via `Task(subagent_type: "generalPurpose")` referenciando skill `code-workflow`.

- **Paralelismo:** máx 3 Tasks simultâneos (mesma mensagem, respeitando dependências).
- Tarefas `doc` → skill `doc-agent`.

Template:

```
Task {
  subagent_type: "generalPurpose",
  description: "code-workflow: <título>",
  prompt: "
    Siga .cursor/skills/code-workflow/SKILL.md

    Objetivo: <...>
    Contexto: <arquivos, regras>
    Critérios de aceitação: <lista>
    Tipo: code | test | config
    Depende de: <tarefas>
  "
}
```

### 6. Verificação cross-task

- Naming e padrões consistentes.
- Lint sem regressões no conjunto.
- Sem secrets/PII no diff.
- Escopo preservado.

### 7. Relatório final

Tabela: tarefa | status | fases concluídas | arquivos (+/-) | notas.

## Critérios de aceitação por tarefa

1. Subagent reportou sucesso.
2. Relatório Fase 9 do code-workflow: Lint + Test (ou justificado).
3. `git diff --stat` mostra apenas arquivos esperados.
4. Nenhum secret/PII no diff.

## Tratamento de falhas

| Situação | Ação |
|----------|------|
| Borderline (escopo) | Avalia justificativa → aprovar / consultar / bloquear |
| Bloqueado (escopo) | Replaneja ou reporta inviabilidade |
| Falha code-workflow | Analisa Fase 9; divide tarefa ou adiciona contexto |
| Secrets no diff | Bloqueia e alerta usuário |
| Retentativas | Máx 2; na 3ª escala ao usuário |
