`.gitignore` recomendado para um repositório consumido por múltiplos agentes de IA, considerando o que cada harness gera ou guarda localmente:

```gitignore
# Ambiente
.env
.env.local

# Artefatos gerados por harness (opcional — commitá-los permite uso direto em CI)
# .claude/agents/
# .claude/skills/
# .codex/skills/
# .agents/skills/
# .agents/rules/
# .agents/plugins/
# .cursor/rules/*.mdc

# Específicos de cada harness (sempre ignorar — são locais/pessoais)
.claude/settings.local.json
CLAUDE.local.md

# Dependências
node_modules/

# OS
.DS_Store

# Credenciais
*.pem
*.key
credentials.json
```

## O que cada harness já ignora automaticamente por você

| Harness | Arquivo | Comportamento |
|---|---|---|
| Claude Code | `.claude/settings.local.json` | Adicionado ao `.gitignore` automaticamente pelo próprio Claude Code quando criado |
| Claude Code | `CLAUDE.local.md` | Também adicionado automaticamente ao `.gitignore` |

Mesmo sendo automático, é uma boa prática listar esses padrões explicitamente no `.gitignore` do projeto — protege contra o caso de alguém commitar manualmente antes da automação rodar, ou de usar uma versão do Claude Code anterior a esse comportamento.

## Por que os artefatos gerados aparecem comentados

Pastas geradas a partir de `plugins/` (ex.: `.claude/skills/`) podem ser commitadas ou ignoradas — commitá-las permite que CI/colaboradores tenham as skills disponíveis sem rodar a geração antes; ignorá-las mantém o repositório enxuto, assumindo que o pipeline sempre regenera os artefatos. A escolha depende do fluxo de deploy do projeto — ver `Makefile` e a regra de ouro em `plugins.md`.

## Fontes

- https://code.claude.com/docs/en/memory
- https://code.claude.com/docs/en/settings
