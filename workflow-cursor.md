# Adaptação Kilo → Cursor

> Mapeamento do ecossistema em `workflow-agent1.md` para Skills, Task subagents e AGENTS.md no Cursor.

**Data:** 2026-06-28

---

## 1. O que muda

| Conceito Kilo | Equivalente Cursor |
|---------------|-------------------|
| Arquivo `~/.config/kilo/agent/*.md` | Skill em `.cursor/skills/<nome>/SKILL.md` |
| `mode: primary` / `subagent` | Skill com `disable-model-invocation: true`; usuário ou agente pai invoca explicitamente |
| `subagent_type` customizado | `Task` com tipos nativos + prompt referenciando o skill |
| `kilo.jsonc` default_agent | Documentado em `AGENTS.md`; invocar `/code-workflow` manualmente |
| `agent-governance` | Skill `create-skill` do Cursor + este repositório como fonte |

---

## 2. Instalação

### Opção A — Por projeto (recomendado para times)

Copie a pasta `.cursor/skills/` deste repo para a raiz do projeto alvo.
Adicione ou mescle `AGENTS.md` na raiz do projeto.

### Opção B — Global (uso pessoal)

```bash
cp -r .cursor/skills/* ~/.cursor/skills/
```

Skills globais ficam disponíveis em todos os projetos.

---

## 3. Diagrama de delegação (Cursor)

```
Usuário (Agent mode)
  │
  ▼
lead-dev skill
  │
  ├── Task(explore, readonly) ──► ideas-agent skill
  ├── Task(generalPurpose, readonly) ──► business-rules skill
  ├── Task(generalPurpose, readonly) ──► scope-guard skill  [pré-código]
  │
  ├── Task(generalPurpose) ──► code-workflow skill  [até 3 paralelos]
  │     │
  │     ├── Fase 1 DETECT ──► shell / leitura direta
  │     ├── Fase 2 CODE ──► Task(generalPurpose) + dev-agent skill
  │     ├── Fase 3 LINT ──► shell
  │     ├── Fase 4 SCOPE ──► Task(generalPurpose, readonly) + scope-guard
  │     ├── Fase 5 REVIEW ──► Task(generalPurpose, readonly) + review-agent
  │     ├── Fase 6 TEST ──► shell; retry → dev-agent
  │     ├── Fase 7 TEST REVIEW ──► Task(readonly) + review-agent
  │     ├── Fase 8 DOCS ──► Task(generalPurpose) + doc-agent
  │     └── Fase 9 REPORT ──► relatório direto
  │
  └── consolidação final
```

---

## 4. Templates de delegação

### lead-dev → code-workflow

```
Task {
  subagent_type: "generalPurpose",
  description: "code-workflow: <tarefa>",
  prompt: "
    Siga o skill code-workflow em .cursor/skills/code-workflow/SKILL.md

    Objetivo: <descrição>
    Contexto: <arquivos, regras>
    Critérios de aceitação: <lista>
    Tipo: code | test | config
    Depende de: <tarefas anteriores>
  "
}
```

Lance até 3 Tasks em paralelo na mesma mensagem quando não houver dependência.

### code-workflow → dev-agent

```
Task {
  subagent_type: "generalPurpose",
  description: "dev-agent: implementar",
  prompt: "
    Siga o skill dev-agent em .cursor/skills/dev-agent/SKILL.md

    Tarefa: <o que implementar>
    Stack: <detectada na Fase 1>
    Arquivos esperados: <lista>
  "
}
```

### code-workflow → review-agent (readonly)

```
Task {
  subagent_type: "generalPurpose",
  readonly: true,
  description: "review-agent: code review",
  prompt: "
    Siga o skill review-agent em .cursor/skills/review-agent/SKILL.md

    Modo: code review (Fase 5)
    Objetivo da tarefa: <...>
    Diff: <output de git diff>
  "
}
```

### Alternativas nativas do Cursor

| Fase | Subagent nativo | Quando usar |
|------|-----------------|-------------|
| Fase 5 Review | `bugbot` | Review focado em bugs no diff |
| Segurança | `security-review` | Quando o diff toca auth, secrets, input |
| Fase 1 Detect | `explore` | Mapear stack/convenções rapidamente |
| Lint/Test | `shell` | Executar comandos detectados |

---

## 5. Tratamento de erros (inalterado em espírito)

| Situação | Quem decide | Ação Cursor |
|----------|-------------|-------------|
| Scope bloqueado (Fase 4) | code-workflow | `git checkout -- .` nos arquivos; reporta ao lead-dev |
| Scope borderline | lead-dev | Avalia; pode perguntar ao usuário |
| Review blocker | code-workflow | Aborta tarefa; lead-dev replaneja |
| Test falhou | dev-agent 1x retry | Flag no relatório Fase 9 |
| Erro inesperado | lead-dev | Máx 2 retentativas; na 3ª escala ao usuário |

---

## 6. Checklist pós-instalação

- [ ] Skills copiados para `.cursor/skills/` ou `~/.cursor/skills/`
- [ ] `AGENTS.md` na raiz do projeto alvo
- [ ] `/lead-dev` inicia fluxo completo sem editar código no orquestrador
- [ ] `/code-workflow` executa 9 fases em ordem
- [ ] scope-guard usa: aprovado / borderline / bloqueado
- [ ] review-agent categoriza: blocker / bug / alerta / sugestão
- [ ] Paralelismo: máx 3 code-workflow simultâneos no lead-dev

---

## 7. Diferenças que não têm equivalente 1:1

1. **Sem agent picker nativo** — skills substituem os arquivos `.md` do Kilo; invoque com `/nome-do-skill`.
2. **Subagent types fixos** — não existe `subagent_type: "dev-agent"`; o papel vem do prompt + skill.
3. **Modelos por agente** — configure no Cursor Settings por conversa; skills não fixam modelo.
4. **Cores/mode** — metadados visuais do Kilo não se aplicam.
