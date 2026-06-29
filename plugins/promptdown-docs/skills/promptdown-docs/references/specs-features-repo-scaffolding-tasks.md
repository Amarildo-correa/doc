`promptdown-ui-v3/.specs/features/repo-scaffolding/tasks.md` quebra a feature de scaffolding em tasks atômicas por subsistema, com plano de execução paralela.

**Nota sobre testes**: não há `.specs/codebase/TESTING.md` (projeto greenfield, sem código real ainda — ver memória de projeto). Como toda task aqui só cria arquivo/pasta vazia com placeholder (nenhuma lógica), a decisão tomada foi `Tests: none` / `Gate: none` em todas as tasks, em vez de parar para perguntar — não há nada testável num placeholder.

---

# Scaffolding do Repositório — Tasks

**Design**: `.specs/features/repo-scaffolding/design.md`
**Status**: Draft

---

## Execution Plan

### Phase 1: Foundation (Sequential)

```
T1
```

### Phase 2: Scaffold por subsistema (Parallel OK)

Depois de T1 (raiz), todas as outras tasks são independentes entre si — cada uma cria uma subárvore que não depende de nenhuma outra.

```
       ┌→ T2  (plugins/)
       ├→ T3  (.claude/)
       ├→ T4  (.codex/)
       ├→ T5  (.cursor/)
T1 ────┼→ T6  (.agents/)
       ├→ T7  (.github/)
       ├→ T8  (api/)
       ├→ T9  (public/js)
       ├→ T10 (public/css)
       ├→ T11 (.specs/)
       └→ T12 (scripts/ + tests/)
```

### Diagram-Definition Cross-Check

| Task | Depends on (definição) | Depends on (diagrama) | OK? |
|---|---|---|---|
| T1 | None | — (raiz do diagrama) | ✅ |
| T2–T12 | T1 | T1 | ✅ |

---

## Task Breakdown

### T1: Scaffold da raiz do repositório

**What**: Criar os arquivos de raiz documentados no `SKILL.md` que não pertencem a nenhum subsistema: `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `.mcp.json`, `.gitignore`, `docker-compose.yml`, `Dockerfile.frontend`, `Dockerfile.api`.
**Where**: raiz do repositório novo.
**Depends on**: None
**Reuses**: conteúdo de `agents-md.md`, `claude-md.md`, `gemini-md.md`, `dot-mcp-json.md`, `dot-gitignore.md`, `docker-compose-yml.md`, `dockerfile-frontend.md`, `dockerfile-api.md` como placeholder/comentário inicial.
**Requirement**: cobre a base para todas as demais (nenhum SCAFFOLD-XX específico — é pré-requisito estrutural)

**Tools**:
- MCP: NONE
- Skill: NONE

**Done when**:
- [ ] Os 8 arquivos de raiz existem.
- [ ] Cada um tem um comentário/placeholder apontando para seu `references/<slug>.md`.

**Tests**: none
**Gate**: none

---

### T2: Scaffold de `plugins/` (fonte) [P]

**What**: Criar `plugins/promptdown-helpers/` com `plugin.json`, `.claude-plugin/plugin.json`, `skills/format-prompt-card/SKILL.md` (+ `references/style-guide.md`), `rules/card-naming.md`.
**Where**: `plugins/`
**Depends on**: T1
**Reuses**: `plugins-promptdown-helpers.md`
**Requirement**: SCAFFOLD-06

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Estrutura completa de `plugins/promptdown-helpers/` existe.

**Tests**: none
**Gate**: none

---

### T3: Scaffold de `.claude/` [P]

**What**: Criar `.claude/settings.json`, `.claude/agents/code-reviewer.md`, `.claude/commands/deploy-staging.md`, `.claude/skills/tavily-search/SKILL.md` (+ pastas vazias com `.gitkeep` onde não há exemplo).
`.claude/rules/.gitkeep` — pasta rastreável no git antes da primeira execução de `make generate`.
**Where**: `.claude/`
**Depends on**: T1
**Reuses**: `claude-settings-json.md`, `claude-agents-code-reviewer.md`, `claude-commands-deploy-staging.md`, `claude-skills-tavily-search.md`
**Requirement**: SCAFFOLD-06, SCAFFOLD-07

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Todos os arquivos-exemplo de `.claude/` existem com o conteúdo documentado.
- [ ] Pastas sem exemplo têm `.gitkeep`.
- [ ] `.claude/rules/.gitkeep` existe.

**Tests**: none
**Gate**: none

---

### T4: Scaffold de `.codex/` [P]

**What**: Criar `.codex/config.toml` (com o exemplo de `[agents.codegen]`) e `.codex/skills/tavily-cli/SKILL.md`.
**Where**: `.codex/`
**Depends on**: T1
**Reuses**: `codex-config-toml.md`, `codex-skills-tavily-cli.md`
**Requirement**: SCAFFOLD-06, SCAFFOLD-07

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] `.codex/config.toml` e `.codex/skills/tavily-cli/SKILL.md` existem.

**Tests**: none
**Gate**: none

---

### T5: Scaffold de `.cursor/` [P]

**What**: Criar `.cursor/rules/{general,frontend,backend}.mdc`.
**Where**: `.cursor/rules/`
**Depends on**: T1
**Reuses**: `general-mdc.md`, `frontend-mdc.md`, `backend-mdc.md`
**Requirement**: SCAFFOLD-06

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Os 3 arquivos `.mdc` existem com o frontmatter `globs`/`alwaysApply` correto (ver correção de sintaxe nos próprios docs).

**Tests**: none
**Gate**: none

---

### T6: Scaffold de `.agents/` [P]

**What**: Criar `.agents/rules/frontend-stack.md`, `.agents/workflows/deploy-staging.md`, `.agents/plugins/promptdown-helpers/` (versão gerada), `.agents/hooks.json`, + `.gitkeep` em pastas sem exemplo (`.agents/skills/`, `.agents/agents/`).
**Where**: `.agents/`
**Depends on**: T1
**Reuses**: `agents-rules-frontend-stack.md`, `agents-workflows-deploy-staging.md`, `agents-plugins-promptdown-helpers.md`, `agents-hooks-json.md`
**Requirement**: SCAFFOLD-06, SCAFFOLD-07

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Todos os arquivos-exemplo de `.agents/` existem.
- [ ] `.agents/skills/` e `.agents/agents/` existem com `.gitkeep` (sem exemplo confirmado oficialmente — ver `agents-agents.md`).

**Tests**: none
**Gate**: none

---

### T7: Scaffold de `.github/` [P]

**What**: Criar `.github/workflows/deploy.yml`, `.github/copilot-instructions.md`, `.github/instructions/api.instructions.md`.
**Where**: `.github/`
**Depends on**: T1
**Reuses**: `deploy-yml.md`, `copilot-instructions-md.md`, `api-instructions-md.md`
**Requirement**: SCAFFOLD-08

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Os 3 arquivos existem.

**Tests**: none
**Gate**: none

---

### T8: Scaffold de `api/` [P]

**What**: Criar `api/server.js`, `api/middleware/{auth,logger,request-delay}.js`, `api/openapi.yaml`, `api/database.json` com `{"prompts": []}`.
**Where**: `api/`
**Depends on**: T1
**Reuses**: `server-js.md`, `auth-js.md`, `logger-js.md`, `request-delay-js.md`, `openapi-yaml.md`, `database-json.md`
**Requirement**: SCAFFOLD-04, SCAFFOLD-05

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Todos os arquivos de `api/` existem.
- [ ] `database.json` é JSON válido com `{"prompts": []}` — nunca sobrescrito se já existir (idempotência).

**Tests**: none
**Gate**: none

---

### T9: Scaffold de `public/js` [P]

**What**: Criar `public/js/{app,router,store,api}.js`, `public/js/lib/{truncate,format-date}.js`, `public/js/views/{home-view,prompt-list-view,prompt-detail-view}.js`, `public/js/components/{card,modal,toast,prompt-form}.js`, `public/index.html`.
**Where**: `public/`
**Depends on**: T1
**Reuses**: os `references/*-js.md` correspondentes a cada arquivo
**Requirement**: SCAFFOLD-01, SCAFFOLD-02, SCAFFOLD-03

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Todos os arquivos JS de `public/js/**` existem com placeholder `// TODO: implementar — ver doc/references/<slug>.md`.
- [ ] `public/index.html` existe.
- [ ] Rodar a task de novo não sobrescreve nenhum arquivo já existente.

**Tests**: none
**Gate**: none

---

### T10: Scaffold de `public/css` [P]

**What**: Criar `public/css/{tokens,base,layout,utilities}.css` e `public/css/components/{button,input,card,modal}.css`.
**Where**: `public/css/`
**Depends on**: T1
**Reuses**: os `references/*-css.md` correspondentes
**Requirement**: SCAFFOLD-01

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] Todos os arquivos CSS existem com comentário placeholder (`/* TODO: ... */`).

**Tests**: none
**Gate**: none

---

### T11: Scaffold de `.specs/` [P]

**What**: Criar `.specs/DESIGN.md`, `.specs/project/{PROJECT,ROADMAP,STATE}.md`, `.specs/features/` (pasta vazia com `.gitkeep` — as features em si, como `prompt-search-filter/` e esta própria `repo-scaffolding/`, são adicionadas por suas próprias specs, não por esta task).
**Where**: `.specs/`
**Depends on**: T1
**Reuses**: `design-md.md`, `project-md.md`, `roadmap-md.md`, `state-md.md`
**Requirement**: cobre a infraestrutura de planejamento usada pelas próprias features deste documento

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] `.specs/DESIGN.md` e `.specs/project/*.md` existem.

**Tests**: none
**Gate**: none

---

### T12: Scaffold de `scripts/` + `tests/` [P]

**What**: Criar `scripts/backup-db.sh` e `tests/unit/.gitkeep`.
**Where**: `scripts/`, `tests/`
**Depends on**: T1
**Reuses**: `backup-db-sh.md`, `unit.md`
**Requirement**: SCAFFOLD-08

**Tools**: MCP: NONE / Skill: NONE

**Done when**:
- [ ] `scripts/backup-db.sh` existe (com `chmod +x` documentado como passo manual — scaffold não altera permissões de execução).
- [ ] `tests/unit/` existe e é rastreável pelo git.

**Tests**: none
**Gate**: none

---

## Validation Checks

### Check 1: Task Granularity

| Task | Atômica? | Justificativa |
|---|---|---|
| T1–T12 | ✅ | Cada task corresponde a exatamente um subsistema independente do `SKILL.md` (mesmo critério usado para separar os grupos de pesquisa Tavily nesta sessão) — nenhuma mistura dois subsistemas não relacionados |

### Check 2: Diagram-Definition Cross-Check

Ver tabela em "Execution Plan" acima — todas batem (✅).

### Check 3: Test Co-location Validation

| Task | Tests (definição) | Tests (esperado pela natureza da task) | OK? |
|---|---|---|---|
| T1–T12 | none | none — nenhuma task cria lógica, só arquivos placeholder | ✅ |

## Fontes

- Template de tasks: skill `tlc-spec-driven`, `references/tasks.md`
- `doc/SKILL.md` (fonte dos paths agrupados por subsistema)
