---
name: promptdown-docs
description: >
  Base de conhecimento do projeto promptdown-ui-v3 — uma SPA vanilla (JS puro, sem framework)
  com design system dark minimalista, backend mock em JSON Server e setup multi-agente
  (Claude Code, Codex CLI, Cursor, Copilot, Antigravity IDE).
  Use esta skill sempre que o usuário perguntar sobre:
  - Estrutura de pastas ou arquivos do projeto promptdown-ui-v3
  - O design system (tokens, grid, componentes, acessibilidade)
  - Como configurar um repositório multi-agente com AGENTS.md / CLAUDE.md / plugins
  - Arquitetura da SPA (router, store, views, components, api.js)
  - Backend mock (JSON Server, middlewares, openapi.yaml)
  - Infraestrutura (Docker, GitHub Actions, scripts)
  - Specs de features (.specs/), roadmap ou estado atual do projeto
  Também use quando o usuário pedir para navegar, explicar ou detalhar qualquer
  arquivo ou pasta listada na árvore abaixo.
---

# promptdown-docs — Base de Conhecimento

Este skill contém toda a documentação do projeto **promptdown-ui-v3** organizada em
dois níveis de detalhe para uso eficiente do contexto.

---

## Como navegar esta documentação

1. **Consulte a árvore abaixo** — o `summary` de cada linha já responde perguntas rápidas.
2. **Para respostas detalhadas**, leia apenas o `references/<slug>.md` correspondente.
3. **Não leia todos os arquivos de `references/` de uma vez** — carregue só o relevante.
4. **Para o design system completo**, leia `references/design-md.md`.
5. **Para o guia multi-agente completo**, leia `references/repositorio-multi-agente.md`.

---

## Árvore do Projeto

- `promptdown-ui-v3` (folder) → `references/promptdown-ui-v3.md` — Raiz do projeto — uma SPA vanilla (JS puro, sem framework) preparada para deploy em produção.
- `promptdown-ui-v3/AGENTS.md` (file) → `references/agents-md.md` — Source of truth universal para agentes de IA.
- `promptdown-ui-v3/CLAUDE.md` (file) → `references/claude-md.md` — Ponte para o Claude Code — contém apenas `@AGENTS.md`.
- `promptdown-ui-v3/GEMINI.md` (file) → `references/gemini-md.md` — Ponte para o Antigravity IDE.
- `promptdown-ui-v3/.mcp.json` (file) → `references/dot-mcp-json.md` — Configuração de MCP servers compartilhada.
- `promptdown-ui-v3/.gitignore` (file) → `references/dot-gitignore.md` — Arquivos ignorados pelo git.
- `promptdown-ui-v3/plugins` (folder) → `references/plugins.md` — Fonte única de verdade de skills/rules/hooks.
- `promptdown-ui-v3/plugins/promptdown-helpers` (folder) → `references/plugins-promptdown-helpers.md` — Plugin-fonte do projeto; escrito uma vez, gerado para todos os harnesses via Makefile.
- `promptdown-ui-v3/plugins/promptdown-helpers/plugin.json` (file) → `references/plugins-promptdown-helpers-plugin-json.md` — Marker obrigatório de plugin agnóstico.
- `promptdown-ui-v3/plugins/promptdown-helpers/.claude-plugin` (folder) → `references/plugins-promptdown-helpers-claude-plugin-json.md` — Marker específico do Claude Code.
- `promptdown-ui-v3/plugins/promptdown-helpers/.claude-plugin/plugin.json` (file) → `references/plugins-promptdown-helpers-claude-plugin-json.md` — Habilita instalação via marketplace do Claude Code.
- `promptdown-ui-v3/plugins/promptdown-helpers/skills` (folder) → `references/plugins-skills-format-prompt-card.md` — Skills do plugin geradas para .claude/skills/ e .codex/skills/.
- `promptdown-ui-v3/plugins/promptdown-helpers/skills/format-prompt-card` (folder) → `references/plugins-skills-format-prompt-card.md` — Skill que formata dados de prompt no padrão de card.
- `promptdown-ui-v3/plugins/promptdown-helpers/skills/format-prompt-card/SKILL.md` (file) → `references/plugins-skills-format-prompt-card-skill-md.md` — Definição da skill com nome e descrição.
- `promptdown-ui-v3/plugins/promptdown-helpers/skills/format-prompt-card/references` (folder) → `references/plugins-skills-format-prompt-card-style-guide-md.md` — Documentação de apoio da skill.
- `promptdown-ui-v3/plugins/promptdown-helpers/skills/format-prompt-card/references/style-guide.md` (file) → `references/plugins-skills-format-prompt-card-style-guide-md.md` — Contrato de dados e regras de formatação do card.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules` (folder) → `references/plugins.md` — Rules do plugin; fontes canônicas geradas para todos os harnesses.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules/card-naming.md` (file) → `references/plugins-rules-card-naming.md` — Fonte canônica da rule de nomenclatura de cards.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules/code-style.md` (file) → `references/plugins-rules-code-style.md` — Fonte canônica da rule de estilo.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules/testing.md` (file) → `references/plugins-rules-testing.md` — Fonte canônica da rule de testes.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules/security.md` (file) → `references/plugins-rules-security.md` — Fonte canônica dos boundaries de segurança.
- `promptdown-ui-v3/.claude` (folder) → `references/dot-claude.md` — Configuração do Claude Code.
- `promptdown-ui-v3/.claude/settings.json` (file) → `references/claude-settings-json.md` — MCP servers e hooks do Claude Code.
- `promptdown-ui-v3/.claude/agents` (folder) → `references/claude-agents.md` — Subagentes gerados de `plugins/`.
- `promptdown-ui-v3/.claude/agents/code-reviewer.md` (file) → `references/claude-agents-code-reviewer.md` — Subagente revisor de código.
- `promptdown-ui-v3/.claude/commands` (folder) → `references/claude-commands.md` — Slash commands do Claude Code.
- `promptdown-ui-v3/.claude/commands/deploy-staging.md` (file) → `references/claude-commands-deploy-staging.md` — Slash command `/deploy-staging`.
- `promptdown-ui-v3/.claude/skills` (folder) → `references/claude-skills.md` — Skills auto-descobertas via `SKILL.md`.
- `promptdown-ui-v3/.claude/skills/tavily-search/SKILL.md` (file) → `references/claude-skills-tavily-search.md` — Skill Tavily instalada.
- `promptdown-ui-v3/.claude/rules` (folder) → `references/claude-rules.md` — Artefato gerado pelo Makefile; nunca editar diretamente.
- `promptdown-ui-v3/.claude/rules/code-style.md` (file) → `references/claude-rules-code-style.md` — Convenções JS/CSS geradas de plugins/.
- `promptdown-ui-v3/.claude/rules/testing.md` (file) → `references/claude-rules-testing.md` — Regras Vitest geradas de plugins/.
- `promptdown-ui-v3/.claude/rules/security.md` (file) → `references/claude-rules-security.md` — Boundaries de segurança gerados de plugins/.
- `promptdown-ui-v3/.codex` (folder) → `references/dot-codex.md` — Configuração do Codex CLI.
- `promptdown-ui-v3/.codex/config.toml` (file) → `references/codex-config-toml.md` — Configuração de papéis do Codex CLI.
- `promptdown-ui-v3/.codex/agents` (folder) → `references/codex-agents.md` — Papéis de agente do Codex.
- `promptdown-ui-v3/.codex/skills` (folder) → `references/codex-skills.md` — Skills do Codex CLI.
- `promptdown-ui-v3/.codex/skills/tavily-cli/SKILL.md` (file) → `references/codex-skills-tavily-cli.md` — Skill Tavily CLI.
- `promptdown-ui-v3/.cursor` (folder) → `references/dot-cursor.md` — Configuração do Cursor.
- `promptdown-ui-v3/.cursor/rules` (folder) → `references/cursor-rules.md` — Regras do Cursor por glob.
- `promptdown-ui-v3/.cursor/rules/general.mdc` (file) → `references/general-mdc.md` — Regras globais do Cursor.
- `promptdown-ui-v3/.cursor/rules/frontend.mdc` (file) → `references/frontend-mdc.md` — Regras frontend do Cursor.
- `promptdown-ui-v3/.cursor/rules/backend.mdc` (file) → `references/backend-mdc.md` — Regras backend do Cursor.
- `promptdown-ui-v3/.agents` (folder) → `references/dot-agents.md` — Configuração do Antigravity IDE.
- `promptdown-ui-v3/.agents/skills` (folder) → `references/agents-skills.md` — Workspace skills do Antigravity IDE.
- `promptdown-ui-v3/.agents/rules` (folder) → `references/agents-rules.md` — Workspace rules do Antigravity IDE.
- `promptdown-ui-v3/.agents/rules/frontend-stack.md` (file) → `references/agents-rules-frontend-stack.md` — Rule de frontend por glob.
- `promptdown-ui-v3/.agents/agents` (folder) → `references/agents-agents.md` — Agentes do Antigravity IDE.
- `promptdown-ui-v3/.agents/workflows` (folder) → `references/agents-workflows.md` — Pipelines do Antigravity IDE.
- `promptdown-ui-v3/.agents/workflows/deploy-staging.md` (file) → `references/agents-workflows-deploy-staging.md` — Workflow `/deploy-staging`.
- `promptdown-ui-v3/.agents/plugins` (folder) → `references/agents-plugins.md` — Plugins locais do Antigravity IDE.
- `promptdown-ui-v3/.agents/plugins/promptdown-helpers` (folder) → `references/agents-plugins-promptdown-helpers.md` — Plugin instalado no workspace.
- `promptdown-ui-v3/.agents/hooks.json` (file) → `references/agents-hooks-json.md` — Hooks de workspace do Antigravity IDE.
- `promptdown-ui-v3/.github` (folder) → `references/dot-github.md` — Configurações do GitHub.
- `promptdown-ui-v3/.github/workflows` (folder) → `references/workflows.md` — Pipelines do GitHub Actions.
- `promptdown-ui-v3/.github/workflows/deploy.yml` (file) → `references/deploy-yml.md` — CI/CD GitHub Actions.
- `promptdown-ui-v3/.github/copilot-instructions.md` (file) → `references/copilot-instructions-md.md` — Instruções do GitHub Copilot.
- `promptdown-ui-v3/.github/instructions` (folder) → `references/github-instructions.md` — Instruções do Copilot por glob.
- `promptdown-ui-v3/.github/instructions/api.instructions.md` (file) → `references/api-instructions-md.md` — Instruções Copilot para `src/api/**`.
- `promptdown-ui-v3/api` (folder) → `references/api.md` — Backend mock com JSON Server.
- `promptdown-ui-v3/api/database.json` (file) → `references/database-json.md` — Dados persistidos.
- `promptdown-ui-v3/api/server.js` (file) → `references/server-js.md` — JSON Server init.
- `promptdown-ui-v3/api/middleware` (folder) → `references/middleware.md` — Custom middlewares.
- `promptdown-ui-v3/api/middleware/auth.js` (file) → `references/auth-js.md` — Middleware de autenticação simulada.
- `promptdown-ui-v3/api/middleware/logger.js` (file) → `references/logger-js.md` — Middleware de log.
- `promptdown-ui-v3/api/middleware/request-delay.js` (file) → `references/request-delay-js.md` — Atraso artificial de rede.
- `promptdown-ui-v3/api/openapi.yaml` (file) → `references/openapi-yaml.md` — OpenAPI 3.0.3.
- `promptdown-ui-v3/public` (folder) → `references/public.md` — Frontend servido pelo Nginx.
- `promptdown-ui-v3/public/index.html` (file) → `references/index-html.md` — HTML único da SPA.
- `promptdown-ui-v3/public/js` (folder) → `references/js.md` — Código JavaScript da aplicação.
- `promptdown-ui-v3/public/js/app.js` (file) → `references/app-js.md` — Entry point da SPA.
- `promptdown-ui-v3/public/js/router.js` (file) → `references/router-js.md` — Roteamento client-side.
- `promptdown-ui-v3/public/js/store.js` (file) → `references/store-js.md` — Store global (Proxy + pub/sub).
- `promptdown-ui-v3/public/js/api.js` (file) → `references/api-js.md` — Camada HTTP da SPA.
- `promptdown-ui-v3/public/js/lib` (folder) → `references/lib.md` — Funções utilitárias puras.
- `promptdown-ui-v3/public/js/lib/truncate.js` (file) → `references/truncate-js.md` — Truncar texto.
- `promptdown-ui-v3/public/js/lib/format-date.js` (file) → `references/format-date-js.md` — Formatar datas em pt-BR.
- `promptdown-ui-v3/public/js/views` (folder) → `references/views.md` — Páginas da SPA.
- `promptdown-ui-v3/public/js/views/home-view.js` (file) → `references/home-view-js.md` — Tela inicial.
- `promptdown-ui-v3/public/js/views/prompt-list-view.js` (file) → `references/prompt-list-view-js.md` — Lista de prompts.
- `promptdown-ui-v3/public/js/views/prompt-detail-view.js` (file) → `references/prompt-detail-view-js.md` — Detalhe de prompt.
- `promptdown-ui-v3/public/js/components` (folder) → `references/components.md` — Componentes reutilizáveis.
- `promptdown-ui-v3/public/js/components/card.js` (file) → `references/card-js.md` — Componente card.
- `promptdown-ui-v3/public/js/components/modal.js` (file) → `references/modal-js.md` — Componente modal.
- `promptdown-ui-v3/public/js/components/toast.js` (file) → `references/toast-js.md` — Notificação efêmera.
- `promptdown-ui-v3/public/js/components/prompt-form.js` (file) → `references/prompt-form-js.md` — Formulário criar/editar.
- `promptdown-ui-v3/public/css` (folder) → `references/css.md` — Estilos em camadas.
- `promptdown-ui-v3/public/css/tokens.css` (file) → `references/tokens-css.md` — Dicionário visual CSS.
- `promptdown-ui-v3/public/css/base.css` (file) → `references/base-css.md` — Reset e tipografia raiz.
- `promptdown-ui-v3/public/css/layout.css` (file) → `references/layout-css.md` — Grid e regiões macro.
- `promptdown-ui-v3/public/css/components/button.css` (file) → `references/button-css.md` — Estilo do botão.
- `promptdown-ui-v3/public/css/components/input.css` (file) → `references/input-css.md` — Estilo de campos.
- `promptdown-ui-v3/public/css/components/card.css` (file) → `references/card-css.md` — Estilo do card.
- `promptdown-ui-v3/public/css/components/modal.css` (file) → `references/modal-css.md` — Estilo do modal.
- `promptdown-ui-v3/public/css/utilities.css` (file) → `references/utilities-css.md` — Classes utilitárias.
- `promptdown-ui-v3/.specs` (folder) → `references/dot-specs.md` — Especificações de features.
- `promptdown-ui-v3/.specs/DESIGN.md` (file) → `references/design-md.md` — SSoT do design system.
- `promptdown-ui-v3/.specs/project` (folder) → `references/specs-project.md` — Contexto do projeto.
- `promptdown-ui-v3/.specs/project/PROJECT.md` (file) → `references/project-md.md` — Descrição do projeto.
- `promptdown-ui-v3/.specs/project/ROADMAP.md` (file) → `references/roadmap-md.md` — Roadmap.
- `promptdown-ui-v3/.specs/project/STATE.md` (file) → `references/state-md.md` — Estado atual.
- `promptdown-ui-v3/.specs/features` (folder) → `references/specs-features.md` — Especificações por feature.
- `promptdown-ui-v3/.specs/features/prompt-search-filter` (folder) → `references/specs-features-prompt-search-filter.md` — Feature busca/filtro.
- `promptdown-ui-v3/.specs/features/prompt-search-filter/spec.md` (file) → `references/specs-features-prompt-search-filter-spec.md` — Spec com user stories.
- `promptdown-ui-v3/.specs/features/repo-scaffolding` (folder) → `references/specs-features-repo-scaffolding.md` — Feature bootstrap do repo.
- `promptdown-ui-v3/.specs/features/repo-scaffolding/spec.md` (file) → `references/specs-features-repo-scaffolding-spec.md` — Spec por subsistema.
- `promptdown-ui-v3/.specs/features/repo-scaffolding/design.md` (file) → `references/specs-features-repo-scaffolding-design.md` — Arquitetura do gerador.
- `promptdown-ui-v3/.specs/features/repo-scaffolding/tasks.md` (file) → `references/specs-features-repo-scaffolding-tasks.md` — Tasks paralelas.
- `promptdown-ui-v3/scripts` (folder) → `references/scripts.md` — Scripts auxiliares.
- `promptdown-ui-v3/scripts/backup-db.sh` (file) → `references/backup-db-sh.md` — Backup do database.json.
- `promptdown-ui-v3/scripts/generate-harness.mjs` (file) → `references/generate-harness-mjs.md` — Lê plugins/ e escreve artefatos no destino correto de cada harness.
- `promptdown-ui-v3/tests` (folder) → `references/tests.md` — Testes automatizados.
- `promptdown-ui-v3/tests/unit` (folder) → `references/unit.md` — Testes unitários Vitest.
- `promptdown-ui-v3/docker-compose.yml` (file) → `references/docker-compose-yml.md` — Orquestra os dois containers.
- `promptdown-ui-v3/Dockerfile.frontend` (file) → `references/dockerfile-frontend.md` — Imagem Nginx do frontend.
- `promptdown-ui-v3/Dockerfile.api` (file) → `references/dockerfile-api.md` — Imagem Node.js da API.
- `promptdown-ui-v3/Makefile` (file) → `references/makefile.md` — Orquestra geração de harnesses a partir de plugins/ via make generate-all.

---

## Documentos de apoio

- **Design system completo** → `references/design-md.md`
- **Guia multi-agente completo** → `references/repositorio-multi-agente.md`
- **Servidor de desenvolvimento local** → `references/start-server.md`
