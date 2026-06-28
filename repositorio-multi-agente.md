# Guia de Implementação — Repositório Multi-Agente

## (Antigravity IDE · Claude Code · Codex CLI · Cursor · Copilot)

---

## Estrutura de Arquivos

- `AGENTS.md` (file) — Source of truth universal — escreva aqui tudo
- `CLAUDE.md` (file) — Contém apenas: `@AGENTS.md`
- `GEMINI.md` (file) — Contém: `@AGENTS.md` + quirks do Antigravity IDE
- `.claude` (folder) — Claude Code — ignorado por todos os outros
- `.claude/settings.json` (file) — `{ "mcpServers": {}, "hooks": {} }`
- `.claude/agents` (folder) — Gerado de `plugins/`
- `.claude/commands` (folder) — Slash commands
- `.claude/skills` (folder) — Auto-descoberta via `SKILL.md`
- `.codex` (folder) — Codex CLI — ignorado por todos os outros
- `.codex/agents` (folder) — Formato `.toml`, gerado de `plugins/`
- `.codex/skills` (folder) — Cap 8KB por arquivo
- `.cursor` (folder) — Cursor — ignorado por todos os outros
- `.cursor/rules` (folder) — Contém os arquivos `.mdc` abaixo
- `.cursor/rules/general.mdc` (file) — Regras globais
- `.cursor/rules/frontend.mdc` (file) — `applyTo: "src/**/*.tsx"`
- `.cursor/rules/backend.mdc` (file) — `applyTo: "src/api/**/*.py"`
- `.agents` (folder) — Antigravity IDE — ignorado por todos os outros
- `.agents/skills` (folder) — Workspace skills → gerado de `plugins/`
- `.agents/skills/[skill-name]/SKILL.md` (file) — Definição da skill
- `.agents/skills/[skill-name]/scripts` (folder) — Scripts auxiliares
- `.agents/skills/[skill-name]/examples` (folder) — Exemplos de referência
- `.agents/skills/[skill-name]/references` (folder) — Templates, style guides, docs
- `.agents/rules` (folder) — Workspace rules → gerado de `plugins/`
- `.agents/rules/[rule-name].md` (file) — Regra individual
- `.agents/agents` (folder) — Agentes por projeto (ou `agents.md` único)
- `.agents/workflows` (folder) — Pipelines invocados via `/workflow-name`
- `.agents/workflows/[workflow-name].md` (file) — Workflow individual
- `.agents/plugins` (folder) — Plugins locais do workspace (ou `_agents/plugins/`)
- `.agents/plugins/[plugin-name]/plugin.json` (file) — Marker do plugin
- `.agents/plugins/[plugin-name]/mcp_config.json` (file) — MCP servers do plugin
- `.agents/plugins/[plugin-name]/hooks.json` (file) — Hooks do plugin
- `.agents/plugins/[plugin-name]/skills` (folder) — Skills do plugin
- `.agents/plugins/[plugin-name]/rules` (folder) — Rules do plugin
- `.agents/hooks.json` (file) — Hooks de workspace (fora de plugins)
- `.github` (folder) — GitHub Copilot — ignorado por todos os outros
- `.github/copilot-instructions.md` (file) — Instruções globais do Copilot
- `.github/instructions` (folder) — Instruções por glob
- `.github/instructions/api.instructions.md` (file) — `applyTo: "src/api/**"`
- `.mcp.json` (file) — MCP compartilhado (Claude Code lê direto)
- `plugins` (folder) — FONTE — escrita uma vez, gerada para todos
- `plugins/[plugin-name]/plugin.json` (file) — Marker Antigravity: `{ "name": "plugin-name" }`
- `plugins/[plugin-name]/.claude-plugin/plugin.json` (file) — Marker Claude Code
- `plugins/[plugin-name]/mcp_config.json` (file) — MCP servers do plugin (Antigravity)
- `plugins/[plugin-name]/hooks.json` (file) — Hooks do plugin (Antigravity)
- `plugins/[plugin-name]/agents/[agent-name].md` (file) — Definição de agente
- `plugins/[plugin-name]/commands/[command].md` (file) — Definição de slash command
- `plugins/[plugin-name]/rules/[rule-name].md` (file) — Rules para Antigravity
- `plugins/[plugin-name]/skills/[skill-name]/SKILL.md` (file) — Definição da skill
- `plugins/[plugin-name]/skills/[skill-name]/references/style-guide.md` (file) — Style guide de referência
- `.specs/project/PROJECT.md` (file) — Descrição do projeto
- `.specs/project/ROADMAP.md` (file) — Roadmap do projeto
- `.specs/project/STATE.md` (file) — Estado atual do projeto
- `.specs/features/[feature]/spec.md` (file) — Especificação da feature
- `.specs/features/[feature]/design.md` (file) — Design da feature
- `.specs/features/[feature]/tasks.md` (file) — Tarefas da feature
- `Makefile` (file) — Geração de artefatos por harness
- `src` (folder) — Código-fonte
- `data` (folder) — Dados do projeto
- `output` (folder) — Saídas geradas
- `.gitignore` (file) — Arquivos ignorados pelo git

---

## Como Cada Agente Inicializa

```
Claude Code      →  lê CLAUDE.md               →  segue para .claude/
Antigravity IDE  →  lê GEMINI.md + AGENTS.md   →  segue para .agents/
Codex CLI        →  lê AGENTS.md               →  segue para .codex/
Cursor           →  lê .cursor/rules/           →  aplica por glob
Copilot          →  lê .github/                →  aplica por glob
```

Todos chegam em AGENTS.md. Cada um ignora as pastas dos outros.

Notas do Antigravity IDE:

- Lê GEMINI.md e AGENTS.md nativamente, sem precisar de settings.json no workspace.
- Se ambos existirem, AGENTS.md tem precedência.
- Rules globais vivem em `~/.gemini/GEMINI.md` (escopo de usuário, não de projeto).

---

## Conteúdo Mínimo dos Arquivos Raiz

```
AGENTS.md (escreva aqui de verdade):
┌─────────────────────────────────────────────────┐
│ # Project                                       │
│ [Uma frase do que faz e para quem]              │
│                                                 │
│ # Stack                                         │
│ # Key Files                                     │
│ # Conventions                                   │
│ # Constraints                                   │
│ # Workflow                                      │
│ # Skills → plugins/[skill]/SKILL.md             │
└─────────────────────────────────────────────────┘

CLAUDE.md (só isto):
┌─────────────────────────────────────────────────┐
│ @AGENTS.md                                      │
└─────────────────────────────────────────────────┘

GEMINI.md (lido pelo Antigravity IDE como contexto de projeto):
┌─────────────────────────────────────────────────┐
│ @AGENTS.md                                      │
│                                                 │
│ ## Antigravity-specific                         │
│ [quirks, contexto extra se necessário]          │
└─────────────────────────────────────────────────┘
```

---

## Formato SKILL.md — Frontmatter YAML obrigatório

O campo `description` é obrigatório — é o que o agente indexa para decidir
ativar ou não a skill (progressive disclosure). O agente lê só o frontmatter
até saber que a skill é relevante; o corpo só é carregado quando ativado.

```
plugins/[plugin]/skills/[skill-name]/SKILL.md:
┌─────────────────────────────────────────────────────────────┐
│ ---                                                         │
│ name: skill-name          # opcional; default = nome pasta  │
│ description: >            # obrigatório                     │
│   Faz X. Use quando Y ou Z. Escreva em terceira pessoa      │
│   com palavras-chave que o agente reconheça.                │
│ ---                                                         │
│                                                             │
│ # Título da Skill                                           │
│                                                             │
│ ## When to use this skill                                   │
│ ## How to use it                                            │
│ ## Constraints                                              │
└─────────────────────────────────────────────────────────────┘

Estrutura completa de uma pasta de skill:

- `.agents/skills/[skill-name]/SKILL.md` (file) — definição — obrigatório
- `.agents/skills/[skill-name]/scripts` (folder) — Python, Bash, Node — opcional
- `.agents/skills/[skill-name]/examples` (folder) — implementações de referência — opcional
- `.agents/skills/[skill-name]/references` (folder) — templates, style guides, docs — opcional

Use scripts como caixas-pretas: instrua o agente a rodar --help
antes de ler o fonte completo. Isso mantém o contexto enxuto.
```

---

## Rules — Constraints do Agente

Rules são arquivos Markdown com restrições e diretrizes que o agente segue.
Diferente de skills (ativadas por tarefa), rules são aplicadas de forma
persistente ou condicional conforme seu modo de ativação.

```
WORKSPACE RULES (por projeto):
  .agents/rules/[rule-name].md

GLOBAL RULES (todos os workspaces do usuário):
  ~/.gemini/GEMINI.md
```

### Modos de ativação por rule

```
Manual         →  ativada via @mention no input do agente
Always On      →  sempre aplicada em toda conversa
Model Decision →  o modelo decide com base na descrição da rule
Glob           →  aplicada quando arquivos matching o padrão são tocados
                  ex: *.js, src/**/*.ts
```

### Sintaxe de importação em rules

Rules suportam `@filename` para referenciar outros arquivos:

- Path relativo → relativo à localização do arquivo de rule
- Path absoluto → resolvido como caminho absoluto; se não existir,
  tenta `workspace/path/to/file.md`

Exemplo:

```markdown
@/path/to/style-guide.md
@../shared/conventions.md
```

Limite: 12.000 caracteres por arquivo de rule.

---

## Workflows

Workflows definem sequências de passos para tarefas repetitivas.
Invocados via slash command `/workflow-name` no input do agente.
Workflows podem chamar outros workflows: `/workflow-1` pode conter
"Call /workflow-2" e "Call /workflow-3".

```
WORKSPACE WORKFLOWS:
  .agents/workflows/[workflow-name].md

GLOBAL WORKFLOWS:
  ~/.gemini/config/workflows/[workflow-name].md  (gerado pelo IDE)
```

Estrutura de um arquivo de workflow:

```markdown
---
name: deploy-staging
description: Faz deploy do serviço no ambiente de staging
---

# Deploy Staging

## Steps

1. Rode os testes: `npm test`
2. Faça o build: `npm run build`
3. Envie para staging: `./scripts/deploy.sh staging`
4. Verifique o health check em https://staging.exemplo.com/health
```

Limite: 12.000 caracteres por arquivo de workflow.

---

## Plugins — Formato Nativo Antigravity

Plugins são bundles que agrupam skills, rules, MCP servers e hooks em
um único pacote instalável. O arquivo `plugin.json` é o marker obrigatório.

### Estrutura de um plugin

- `plugins/[plugin-name]/plugin.json` (file) — marker obrigatório: `{ "name": "plugin-name" }`
- `plugins/[plugin-name]/mcp_config.json` (file) — MCP servers do plugin (opcional)
- `plugins/[plugin-name]/hooks.json` (file) — Hooks do plugin (opcional)
- `plugins/[plugin-name]/skills` (folder) — Skills do plugin
- `plugins/[plugin-name]/skills/[skill-name]/SKILL.md` (file) — Definição da skill
- `plugins/[plugin-name]/rules` (folder) — Rules do plugin
- `plugins/[plugin-name]/rules/[rule-name].md` (file) — Regra individual

### plugin.json

```json
{
    "name": "my-plugin"
}
```

O campo `name` é opcional — default é o nome da pasta.

### Onde instalar plugins

```
WORKSPACE (só neste projeto):
  .agents/plugins/[plugin-name]/       ← padrão
  _agents/plugins/[plugin-name]/       ← alternativa com underscore

GLOBAL (todos os workspaces):
  ~/.gemini/config/plugins/[plugin-name]/
```

---

## Hooks — Automação no Loop de Execução

Hooks executam scripts ou comandos em pontos específicos do ciclo do agente.
Úteis para linters automáticos, gates de segurança, logging e diagnósticos.

```
WORKSPACE:  .agents/hooks.json
GLOBAL:     ~/.gemini/config/hooks.json
PLUGIN:     plugins/[plugin-name]/hooks.json
```

### Eventos disponíveis

```
PreToolUse     →  antes de uma tool ser executada
PostToolUse    →  após uma tool completar
PreInvocation  →  antes do modelo ser chamado
PostInvocation →  após as tool calls terminarem
Stop           →  quando o loop de execução encerra
```

### Estrutura do hooks.json

```json
{
    "lint-on-write": {
        "PostToolUse": [
            {
                "matcher": "write_to_file",
                "hooks": [
                    {
                        "type": "command",
                        "command": "./scripts/lint.sh",
                        "timeout": 10
                    }
                ]
            }
        ]
    },
    "safety-gate": {
        "enabled": true,
        "PreToolUse": [
            {
                "matcher": "run_command",
                "hooks": [
                    {
                        "command": "./scripts/safety-check.sh"
                    }
                ]
            }
        ]
    },
    "context-reminder": {
        "PreInvocation": [
            {
                "command": "./scripts/inject-context.sh"
            }
        ]
    }
}
```

### Contrato de input/output (stdin/stdout como JSON)

**PreToolUse — output:**

```json
{
    "decision": "allow", // "allow" | "deny" | "ask" | "force_ask"
    "reason": "Motivo exibido ao usuário ou agente"
}
```

**PreInvocation — output (injeção de contexto):**

```json
{
    "injectSteps": [{ "ephemeralMessage": "Lembre-se de rodar lint antes de commitar" }]
}
```

**Stop — output (retomar execução):**

```json
{
    "decision": "continue",
    "reason": "Tarefa ainda não concluída"
}
```

**Matcher patterns para PreToolUse / PostToolUse:**

```
""  ou  "*"           →  qualquer tool
"run_command"         →  exatamente run_command
"run_command|view_file" →  qualquer uma das duas
"browser_.*"          →  qualquer tool começando com browser_
```

**Campos comuns recebidos no stdin de todos os hooks:**

```
conversationId        →  UUID da conversa ativa
workspacePaths        →  paths dos workspaces montados
transcriptPath        →  ~/.gemini/antigravity-ide/brain/<id>/.../transcript.jsonl
artifactDirectoryPath →  diretório de artifacts da conversa
```

---

## MCP — Model Context Protocol

```
CLAUDE CODE (lê direto do projeto):
  .mcp.json                            ← raiz do projeto

ANTIGRAVITY IDE (configuração global do usuário):
  ~/.gemini/config/mcp_config.json     ← config global do IDE

ANTIGRAVITY IDE (por plugin):
  .agents/plugins/[plugin]/mcp_config.json

TOKENS OAUTH (gerado automaticamente):
  ~/.gemini/antigravity/mcp_oauth_tokens.json
```

### Estrutura do mcp_config.json (Antigravity)

```json
{
    "mcpServers": {
        "postgres": {
            "command": "npx",
            "args": ["-y", "@modelcontextprotocol/server-postgres"],
            "env": {
                "DATABASE_URL": "postgresql://..."
            }
        },
        "remote-service": {
            "serverUrl": "https://api.exemplo.com/mcp/",
            "authProviderType": "google_credentials"
        },
        "oauth-server": {
            "serverUrl": "https://api.exemplo.com/mcp/",
            "oauth": {
                "clientId": "your-client-id",
                "clientSecret": "your-client-secret"
            }
        },
        "disabled-server": {
            "command": "npx mcp-server",
            "disabled": true
        }
    }
}
```

---

## Makefile — Geração de Artefatos por Harness

```makefile
make generate HARNESS=claude        →  plugins/ → .claude/agents .claude/skills
make generate HARNESS=codex         →  plugins/ → .codex/agents  .codex/skills
make generate HARNESS=antigravity   →  plugins/ → .agents/skills .agents/rules
                                                   .agents/workflows .agents/plugins
make generate HARNESS=cursor        →  plugins/ → .cursor/rules/*.mdc
make generate-all                   →  todos acima (rodar antes de cada commit)
```

Exemplo do target `antigravity` no Makefile:

```makefile
generate-antigravity:
	mkdir -p .agents/skills .agents/rules .agents/workflows .agents/plugins
	@for plugin in plugins/*/; do \
		name=$$(basename $$plugin); \
		mkdir -p .agents/plugins/$$name; \
		cp $$plugin/plugin.json .agents/plugins/$$name/; \
		[ -f $$plugin/mcp_config.json ] && cp $$plugin/mcp_config.json .agents/plugins/$$name/; \
		[ -f $$plugin/hooks.json ]      && cp $$plugin/hooks.json .agents/plugins/$$name/; \
		[ -d $$plugin/skills ]          && cp -r $$plugin/skills/. .agents/plugins/$$name/skills/; \
		[ -d $$plugin/rules ]           && cp -r $$plugin/rules/.  .agents/plugins/$$name/rules/; \
	done
```

Regra de ouro: nunca edite os artefatos gerados.
A fonte de verdade vive apenas em `plugins/`.

---

## Caminhos por Escopo — Antigravity IDE

```
WORKSPACE (por projeto, commitado no repositório):
  .agents/skills/<skill-name>/SKILL.md
  .agents/rules/<rule-name>.md
  .agents/workflows/<workflow-name>.md
  .agents/plugins/<plugin-name>/        ← ou _agents/plugins/
  .agents/hooks.json

GLOBAL (todos os workspaces do usuário, não commitado):
  ~/.gemini/GEMINI.md                   ← global rules (todos os projetos)
  ~/.gemini/antigravity/skills/         ← global skills standalone
  ~/.gemini/config/plugins/             ← global plugins
  ~/.gemini/config/mcp_config.json      ← global MCP config
  ~/.gemini/config/hooks.json           ← global hooks

APP DATA (gerado pelo IDE, não editar):
  ~/.gemini/antigravity/                ← dados do IDE
  ~/.gemini/antigravity-ide/brain/      ← logs e artifacts de conversas
  ~/.gemini/antigravity-cli/            ← dados do CLI (agy)
```

Nota: `.agent/skills/` e `.agent/rules/` (singular) ainda funcionam
como fallback por compatibilidade retroativa.

---

## O Que É Compartilhado vs. Próprio

```
COMPARTILHADO (todos leem)        PRÓPRIO (cada agente, ignorado pelos outros)
──────────────────────────────    ────────────────────────────────────────────
AGENTS.md                         CLAUDE.md + .claude/
plugins/ (fonte de verdade)       GEMINI.md + .agents/
.specs/  (memória, planos)        .codex/
.mcp.json (Claude Code)           .cursor/rules/
                                  .github/copilot-instructions.md
```

---

## .gitignore Recomendado

```gitignore
# Ambiente
.env
.env.local

# Artefatos gerados (opcional — commitá-los permite install nativo no CI)
# .agents/skills/
# .agents/rules/
# .agents/plugins/

# Dependências
node_modules/

# OS
.DS_Store

# Credenciais
*.pem
*.key
credentials.json
```

---

## Ordem de Implementação

```
1. Crie AGENTS.md            →  escreva o contexto do projeto
2. Crie CLAUDE.md            →  só "@AGENTS.md"
3. Crie GEMINI.md            →  só "@AGENTS.md" + quirks do Antigravity
4. Configure .mcp.json       →  MCP compartilhado (Claude Code)
5. Crie plugins/             →  skills, rules, hooks, agents agnósticos
                                 com frontmatter YAML em cada SKILL.md
6. Configure cada .dotdir    →  .claude/ .codex/ .cursor/ .agents/ .github/
7. Escreva o Makefile        →  make generate-all (inclui HARNESS=antigravity)
8. git commit                →  artefatos gerados junto com a fonte
```
