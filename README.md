# Linux Setup

Registro de configurações e instalações do ambiente Linux.

## Estrutura

| Arquivo / pasta | Descrição |
|-----------------|-----------|
| `installs.md` | Lista de pacotes e ferramentas para instalar |
| `workflow-agent1.md` | Spec do ecossistema Kilo (fonte) |
| `workflow-cursor.md` | Adaptação Kilo → Cursor |
| `AGENTS.md` | Entry point do pipeline no Cursor |
| `.cursor/skills/` | Skills: lead-dev, code-workflow, dev-agent, review-agent, scope-guard, ideas-agent, doc-agent, business-rules |

### Usar no Cursor

1. Copie `.cursor/skills/` para o projeto alvo, ou globalmente: `cp -r .cursor/skills/* ~/.cursor/skills/`
2. Copie/adapte `AGENTS.md` para a raiz do projeto
3. No Agent mode: `/lead-dev` (feature completa) ou `/code-workflow` (tarefa pontual)

## Como usar

1. Adicione itens pendentes em `installs.md` na seção "Pendentes"
2. Após instalar, marque como concluído na seção "Concluídos"
