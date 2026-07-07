---
name: business-rules
description: >-
  Mapeia e valida regras de negócio do domínio no código existente.
  Use durante análise de features, antes de decompor tarefas, ou quando
  o usuário pedir entendimento de regras de domínio.
disable-model-invocation: true
---

# business-rules

Auxilia entendimento de domínio. **Não implementa código.**

## Fluxo

1. Recebe contexto da feature ou domínio.
2. Inspeciona código em busca de regras de negócio (validações, estados, invariantes).
3. Documenta regras encontradas e restrições aplicáveis.
4. Reporta ao lead-dev para orientar decomposição.

## Formato

```markdown
## Regras identificadas

| # | Regra | Onde | Impacto na feature |
|---|-------|------|-------------------|
| 1 | ... | `path/file` | ... |

## Restrições
- ...

## Lacunas
- <regras não encontradas no código que precisam esclarecimento>
```

## Proibido

- Inventar regras sem evidência no código ou input do usuário
- Implementar ou alterar código
