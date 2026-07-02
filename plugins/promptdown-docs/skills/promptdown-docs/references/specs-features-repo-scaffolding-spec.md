`promptdown-ui-v3/.specs/features/repo-scaffolding/spec.md` é um exemplo concreto de spec **Large**: bootstrap completo da estrutura de pastas/arquivos do repositório novo, a partir de tudo que já está documentado em `doc/SKILL.md` — sem nenhum conteúdo/lógica real ainda (isso fica para features futuras).

---

# Scaffolding do Repositório — Specification

## Problem Statement

O repositório `promptdown-ui-v3` ainda não existe — só a documentação/planejamento dele, em `doc/SKILL.md` e `doc/references/*.md` (~90 itens). Antes de implementar qualquer lógica real, é preciso que a árvore de pastas e arquivos exista no disco, vazia ou com placeholders mínimos, exatamente como documentado — para que features futuras tenham onde escrever código, em vez de cada uma precisar criar sua própria estrutura ad-hoc.

## Goals

- [ ] 100% dos itens listados em `doc/SKILL.md` existem no novo repositório como pasta ou arquivo real.
- [ ] Nenhum arquivo tem conteúdo de lógica real — só placeholders (ou vazio) indicando que aquilo é responsabilidade de uma feature futura.
- [ ] A estrutura é versionável no git imediatamente após o scaffold (pastas vazias não somem por causa do git não rastrear diretórios vazios).

## Out of Scope

| Feature | Motivo |
|---|---|
| Lógica de qualquer arquivo (views, components, api.js, middlewares, etc.) | É exatamente o que esta feature **não** faz — cada peça fica para uma feature futura dedicada (ex.: a feature de busca/filtro já especificada assume que `prompt-list-view.js` já existe como arquivo, não que ele tenha lógica) |
| Configuração funcional de CI/CD (`deploy.yml` rodando de verdade) | O arquivo existe como placeholder; o pipeline real é outra feature |
| Conteúdo real de `database.json` (dados de exemplo) | Fica vazio/com array vazio nesta feature; popular dados é decisão de uma feature futura |
| Instalação de dependências (`npm install`, `package.json` com versões reais) | Scaffolding cria a estrutura de arquivos; configurar dependências é parte da feature que primeiro precisar delas |

---

## User Stories

### P1: Scaffold do frontend (`public/`) ⭐ MVP

**User Story**: Como desenvolvedor iniciando o projeto, quero que toda a árvore de `public/js` e `public/css` já exista (vazia), para começar a escrever views/components sem precisar criar pastas manualmente.

**Why P1**: É o subsistema com mais itens documentados (views, components, lib, css em camadas) e o que toda feature futura de UI vai precisar primeiro.

**Acceptance Criteria**:

1. WHEN o scaffold é executado THEN o sistema SHALL criar `public/index.html`, `public/llms.txt`, `public/js/{app,router,store,api}.js`, `public/js/lib/{truncate,format-date}.js`, `public/js/views/{home-view,prompt-list-view,prompt-detail-view}.js`, `public/js/components/{card,modal,toast,prompt-form}.js` e `public/css/{tokens,base,layout,utilities}.css` + `public/css/components/{button,input,card,modal}.css`.
2. WHEN um arquivo já existe no destino THEN o sistema SHALL pular esse arquivo sem sobrescrever (idempotência — rodar o scaffold de novo não destrói trabalho já feito).
3. WHEN um arquivo é criado pela primeira vez THEN o sistema SHALL inserir um comentário placeholder apontando para o `references/<slug>.md` correspondente (ex.: `// TODO: implementar — ver doc/references/app-js.md`).

**Independent Test**: Rodar o scaffold num diretório vazio, verificar que todos os arquivos de `public/` existem com o comentário placeholder; rodar de novo e confirmar que nada foi sobrescrito.

---

### P1: Scaffold do backend mock (`api/`) ⭐ MVP

**User Story**: Como desenvolvedor, quero que `api/` (server, middlewares, `database.json`, OpenAPI spec) já exista, para poder rodar `json-server` desde o primeiro commit, mesmo sem lógica de middleware ainda.

**Why P1**: Sem isso, não há backend nenhum para o frontend conversar, mesmo que mockado — bloqueia testar qualquer view que busque dados.

**Acceptance Criteria**:

1. WHEN o scaffold é executado THEN o sistema SHALL criar `api/server.js`, `api/middleware/{auth,logger,request-delay}.js`, `api/openapi.yaml` e `api/database.json` com um JSON vazio válido (`{}` ou `{"prompts": []}`).
2. WHEN `database.json` já existe THEN o sistema SHALL preservá-lo (nunca sobrescrever dados reais com o placeholder).

**Independent Test**: Rodar o scaffold, confirmar que `api/database.json` é JSON válido e que `node api/server.js` ao menos não falha por arquivo ausente (mesmo sem lógica real ainda).

---

### P2: Scaffold da configuração multi-agente (`.claude/`, `.codex/`, `.cursor/`, `.agents/`, `.github/`, `AGENTS.md`, etc.)

**User Story**: Como desenvolvedor que vai usar múltiplos agentes de IA neste projeto, quero que toda a estrutura documentada em `dot-claude.md`, `dot-codex.md`, `dot-cursor.md`, `dot-agents.md` já exista, para configurar cada harness sem redescobrir a convenção manualmente.

**Why P2**: Importante para o fluxo de trabalho com IA descrito no restante do `SKILL.md`, mas não bloqueia rodar a aplicação em si — pode vir depois do P1.

**Acceptance Criteria**:

1. WHEN o scaffold é executado THEN o sistema SHALL criar `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `.mcp.json`, `.gitignore` na raiz, e as pastas `.claude/`, `.codex/`, `.cursor/rules/`, `.agents/`, `plugins/promptdown-helpers/` com os exemplos concretos já documentados (`code-reviewer.md`, `deploy-staging.md`, `frontend-stack.md`, etc.).
2. WHEN uma pasta documentada no `SKILL.md` não tem nenhum arquivo-exemplo associado (só a pasta) THEN o sistema SHALL criar a pasta com um `.gitkeep`, para o git rastreá-la mesmo vazia.

**Independent Test**: Rodar o scaffold, confirmar que `.claude/agents/code-reviewer.md` existe com o conteúdo documentado em `claude-agents-code-reviewer.md`, e que pastas sem arquivo (ex.: `.codex/skills/`) têm `.gitkeep`.

---

### P3: Scaffold de infra (`scripts/`, `tests/`, Docker, CI)

**User Story**: Como desenvolvedor, quero que `docker-compose.yml`, `Dockerfile.api`, `worker.js`, `wrangler.toml`, `scripts/backup-db.sh`, `tests/unit/` e `.github/workflows/deploy.yml` já existam como placeholders, para não esquecer de criá-los quando chegar a hora.

**Why P3**: Não bloqueia desenvolvimento local nem o uso dos agentes de IA — só organização para não esquecer peças de infraestrutura.

**Acceptance Criteria**:

1. WHEN o scaffold é executado THEN o sistema SHALL criar `docker-compose.yml`, `Dockerfile.api`, `worker.js`, `wrangler.toml`, `scripts/backup-db.sh`, `tests/unit/.gitkeep`, `.github/workflows/deploy.yml`.

---

## Edge Cases

- WHEN o `doc/SKILL.md` (fonte da lista de paths) tem uma linha mal formatada THEN o sistema SHALL ignorá-la e registrar um aviso, em vez de falhar o scaffold inteiro.
- WHEN o diretório de destino já é um repositório git com arquivos não relacionados THEN o sistema SHALL só adicionar o que falta, nunca remover algo que já existe e não está no `SKILL.md`.
- WHEN uma pasta do `SKILL.md` não tem nenhum arquivo-filho documentado (pasta "folha" vazia) THEN o sistema SHALL criar um `.gitkeep` nela, já que git não versiona diretórios vazios.
- WHEN o scaffold roda em sistema de arquivos case-insensitive (Windows) THEN o sistema SHALL alertar se dois paths do `SKILL.md` diferem só por maiúscula/minúscula (colisão silenciosa em potencial).

---

## Requirement Traceability

| Requirement ID | Story | Phase | Status |
|---|---|---|---|
| SCAFFOLD-01 | P1: Scaffold do frontend | Design | Pending |
| SCAFFOLD-02 | P1: Scaffold do frontend (idempotência) | Design | Pending |
| SCAFFOLD-03 | P1: Scaffold do frontend (placeholder com referência) | Design | Pending |
| SCAFFOLD-04 | P1: Scaffold do backend mock | Design | Pending |
| SCAFFOLD-05 | P1: Scaffold do backend mock (preservar dados) | Design | Pending |
| SCAFFOLD-06 | P2: Scaffold multi-agente | Design | Pending |
| SCAFFOLD-07 | P2: Pastas vazias com `.gitkeep` | Design | Pending |
| SCAFFOLD-08 | P3: Scaffold de infra | Design | Pending |

**Coverage:** 8 total, 8 mapeados para o Design (ver `specs-features-repo-scaffolding-design.md`), 0 não mapeados.

---

## Success Criteria

- [ ] Rodar o gerador de scaffold uma vez cria 100% dos ~90 itens do `SKILL.md` ausentes no destino.
- [ ] Rodar o gerador uma segunda vez não altera nada (idempotente).
- [ ] `git status` depois do scaffold mostra todas as pastas documentadas como rastreadas (nenhuma pasta vazia "invisível" para o git).

## Fontes

- `doc/SKILL.md` (fonte de verdade dos ~90 paths a criar)
- Template de spec: skill `tlc-spec-driven`, `references/specify.md`
