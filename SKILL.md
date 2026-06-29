# Índice e árvore — Repo Tree Viewer

Este arquivo é a ÚNICA fonte de verdade da árvore de arquivos do projeto:
cada linha descreve um nó (pasta ou arquivo) e a hierarquia é inferida
pelos segmentos de cada `path`. O `summary` de cada linha é a descrição
curta desse item. O conteúdo completo de cada "aula" continua em
`doc/references/<slug>.md`.

Para adicionar um novo item à árvore: crie o `.md` em `doc/references/` e
acrescente uma linha abaixo com o `path` completo (incluindo a raiz
`promptdown-ui-v3`), o tipo (`folder`/`file`) e o summary.

## Itens

- `promptdown-ui-v3` (folder) → `doc/references/promptdown-ui-v3.md` — Raiz do projeto — uma SPA vanilla (JS puro, sem framework) preparada para deploy em produção.
- `promptdown-ui-v3/AGENTS.md` (file) → `doc/references/agents-md.md` — Source of truth universal para agentes de IA — todo o contexto do projeto é escrito aqui primeiro.
- `promptdown-ui-v3/CLAUDE.md` (file) → `doc/references/claude-md.md` — Ponte para o Claude Code — contém apenas `@AGENTS.md`.
- `promptdown-ui-v3/GEMINI.md` (file) → `doc/references/gemini-md.md` — Ponte para o Antigravity IDE — contém `@AGENTS.md` + quirks específicos do IDE.
- `promptdown-ui-v3/.mcp.json` (file) → `doc/references/dot-mcp-json.md` — Configuração de MCP servers compartilhada, lida diretamente pelo Claude Code.
- `promptdown-ui-v3/.gitignore` (file) → `doc/references/dot-gitignore.md` — Lista de arquivos/pastas ignorados pelo git, incluindo artefatos gerados por agentes e credenciais.
- `promptdown-ui-v3/plugins` (folder) → `doc/references/plugins.md` — Fonte única de verdade de skills/rules/hooks agnósticos de harness, gerada para cada agente via Makefile.
- `promptdown-ui-v3/plugins/promptdown-helpers` (folder) → `doc/references/plugins-promptdown-helpers.md` — Exemplo concreto de plugin-fonte, com skills/agents/commands/rules.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules/code-style.md` (file) → `doc/references/plugins-rules-code-style.md` — Fonte canônica da rule de estilo.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules/testing.md` (file) → `doc/references/plugins-rules-testing.md` — Fonte canônica da rule de testes.
- `promptdown-ui-v3/plugins/promptdown-helpers/rules/security.md` (file) → `doc/references/plugins-rules-security.md` — Fonte canônica dos boundaries de segurança.
- `promptdown-ui-v3/.claude` (folder) → `doc/references/dot-claude.md` — Configuração do Claude Code — ignorada por todos os outros agentes.
- `promptdown-ui-v3/.claude/settings.json` (file) → `doc/references/claude-settings-json.md` — Configuração de MCP servers e hooks específicos do Claude Code.
- `promptdown-ui-v3/.claude/agents` (folder) → `doc/references/claude-agents.md` — Subagentes gerados a partir de `plugins/`.
- `promptdown-ui-v3/.claude/agents/code-reviewer.md` (file) → `doc/references/claude-agents-code-reviewer.md` — Exemplo concreto de subagente: revisor de código somente leitura.
- `promptdown-ui-v3/.claude/commands` (folder) → `doc/references/claude-commands.md` — Slash commands do Claude Code.
- `promptdown-ui-v3/.claude/commands/deploy-staging.md` (file) → `doc/references/claude-commands-deploy-staging.md` — Exemplo concreto de slash command: `/deploy-staging`.
- `promptdown-ui-v3/.claude/skills` (folder) → `doc/references/claude-skills.md` — Skills auto-descobertas via `SKILL.md`.
- `promptdown-ui-v3/.claude/skills/tavily-search/SKILL.md` (file) → `doc/references/claude-skills-tavily-search.md` — Exemplo real de skill instalada nesta máquina (busca web via Tavily CLI).
- `promptdown-ui-v3/.claude/rules` (folder) → `doc/references/claude-rules.md` — Artefato gerado pelo Makefile; nunca editar diretamente.
- `promptdown-ui-v3/.claude/rules/code-style.md` (file) → `doc/references/claude-rules-code-style.md` — Convenções JS/CSS geradas de plugins/.
- `promptdown-ui-v3/.claude/rules/testing.md` (file) → `doc/references/claude-rules-testing.md` — Regras Vitest geradas de plugins/.
- `promptdown-ui-v3/.claude/rules/security.md` (file) → `doc/references/claude-rules-security.md` — Boundaries de segurança gerados de plugins/.
- `promptdown-ui-v3/.codex` (folder) → `doc/references/dot-codex.md` — Configuração do Codex CLI — ignorada por todos os outros agentes.
- `promptdown-ui-v3/.codex/config.toml` (file) → `doc/references/codex-config-toml.md` — Exemplo concreto de configuração de papéis de agente do Codex CLI.
- `promptdown-ui-v3/.codex/agents` (folder) → `doc/references/codex-agents.md` — Papéis de agente configurados em `config.toml` (não arquivos `.toml` por agente — ver correção no arquivo).
- `promptdown-ui-v3/.codex/skills` (folder) → `doc/references/codex-skills.md` — Skills do Codex CLI, com progressive disclosure (sem limite de tamanho confirmado oficialmente).
- `promptdown-ui-v3/.codex/skills/tavily-cli/SKILL.md` (file) → `doc/references/codex-skills-tavily-cli.md` — Exemplo real de skill instalada nesta máquina (CLI da Tavily).
- `promptdown-ui-v3/.cursor` (folder) → `doc/references/dot-cursor.md` — Configuração do Cursor — ignorada por todos os outros agentes.
- `promptdown-ui-v3/.cursor/rules` (folder) → `doc/references/cursor-rules.md` — Regras do Cursor aplicadas por glob de arquivo.
- `promptdown-ui-v3/.cursor/rules/general.mdc` (file) → `doc/references/general-mdc.md` — Regras globais do Cursor, aplicadas em qualquer arquivo.
- `promptdown-ui-v3/.cursor/rules/frontend.mdc` (file) → `doc/references/frontend-mdc.md` — Regras do Cursor aplicadas via `applyTo: "src/**/*.tsx"`.
- `promptdown-ui-v3/.cursor/rules/backend.mdc` (file) → `doc/references/backend-mdc.md` — Regras do Cursor aplicadas via `applyTo: "src/api/**/*.py"`.
- `promptdown-ui-v3/.agents` (folder) → `doc/references/dot-agents.md` — Configuração do Antigravity IDE — ignorada por todos os outros agentes.
- `promptdown-ui-v3/.agents/skills` (folder) → `doc/references/agents-skills.md` — Workspace skills do Antigravity IDE, geradas a partir de `plugins/`.
- `promptdown-ui-v3/.agents/rules` (folder) → `doc/references/agents-rules.md` — Workspace rules do Antigravity IDE, geradas a partir de `plugins/`.
- `promptdown-ui-v3/.agents/rules/frontend-stack.md` (file) → `doc/references/agents-rules-frontend-stack.md` — Exemplo concreto de Rule: convenções de frontend aplicadas via glob.
- `promptdown-ui-v3/.agents/agents` (folder) → `doc/references/agents-agents.md` — Agentes por projeto do Antigravity IDE.
- `promptdown-ui-v3/.agents/workflows` (folder) → `doc/references/agents-workflows.md` — Pipelines de passos invocados via slash command `/workflow-name`.
- `promptdown-ui-v3/.agents/workflows/deploy-staging.md` (file) → `doc/references/agents-workflows-deploy-staging.md` — Exemplo concreto de workflow: `/deploy-staging`.
- `promptdown-ui-v3/.agents/plugins` (folder) → `doc/references/agents-plugins.md` — Plugins locais do workspace instalados no Antigravity IDE.
- `promptdown-ui-v3/.agents/plugins/promptdown-helpers` (folder) → `doc/references/agents-plugins-promptdown-helpers.md` — Exemplo concreto de plugin instalado neste workspace.
- `promptdown-ui-v3/.agents/hooks.json` (file) → `doc/references/agents-hooks-json.md` — Hooks de workspace do Antigravity IDE, fora de qualquer plugin.
- `promptdown-ui-v3/.github` (folder) → `doc/references/dot-github.md` — Pasta de configurações do GitHub — não faz parte do código da aplicação em si.
- `promptdown-ui-v3/.github/workflows` (folder) → `doc/references/workflows.md` — Define os pipelines de automação executados pelo GitHub Actions.
- `promptdown-ui-v3/.github/workflows/deploy.yml` (file) → `doc/references/deploy-yml.md` — CI/CD — GitHub Actions
- `promptdown-ui-v3/.github/copilot-instructions.md` (file) → `doc/references/copilot-instructions-md.md` — Instruções globais lidas pelo GitHub Copilot.
- `promptdown-ui-v3/.github/instructions` (folder) → `doc/references/github-instructions.md` — Instruções do Copilot aplicadas por glob de arquivo.
- `promptdown-ui-v3/.github/instructions/api.instructions.md` (file) → `doc/references/api-instructions-md.md` — Instruções do Copilot aplicadas via `applyTo: "src/api/**"`.
- `promptdown-ui-v3/api` (folder) → `doc/references/api.md` — Backend mock — simula uma API REST completa enquanto não existe um backend real.
- `promptdown-ui-v3/api/database.json` (file) → `doc/references/database-json.md` — dados persistidos
- `promptdown-ui-v3/api/server.js` (file) → `doc/references/server-js.md` — JSON Server init
- `promptdown-ui-v3/api/middleware` (folder) → `doc/references/middleware.md` — custom middlewares
- `promptdown-ui-v3/api/middleware/auth.js` (file) → `doc/references/auth-js.md` — Middleware que simula autenticação — valida um token fictício antes de liberar rotas protegidas do backend mock.
- `promptdown-ui-v3/api/middleware/logger.js` (file) → `doc/references/logger-js.md` — Middleware de log — registra método, caminho e duração de cada requisição recebida pela API mock.
- `promptdown-ui-v3/api/middleware/request-delay.js` (file) → `doc/references/request-delay-js.md` — Atraso artificial de rede — simula latência para testar estados de loading da SPA.
- `promptdown-ui-v3/api/openapi.yaml` (file) → `doc/references/openapi-yaml.md` — OpenAPI 3.0.3
- `promptdown-ui-v3/public` (folder) → `doc/references/public.md` — Tudo dentro desta pasta é servido estaticamente pelo container Nginx — é literalmente o frontend em produção.
- `promptdown-ui-v3/public/index.html` (file) → `doc/references/index-html.md` — Página HTML única da SPA — todo o resto é renderizado via JavaScript a partir dela.
- `promptdown-ui-v3/public/js` (folder) → `doc/references/js.md` — Todo o código JavaScript da aplicação cliente.
- `promptdown-ui-v3/public/js/app.js` (file) → `doc/references/app-js.md` — Entry point da SPA — o primeiro código a executar quando a página carrega.
- `promptdown-ui-v3/public/js/router.js` (file) → `doc/references/router-js.md` — Implementa o roteamento client-side usando a History API do navegador.
- `promptdown-ui-v3/public/js/store.js` (file) → `doc/references/store-js.md` — Store global de estado da aplicação — Proxy + pub/sub em vez de objeto mutável simples.
- `promptdown-ui-v3/public/js/api.js` (file) → `doc/references/api-js.md` — Camada única de comunicação HTTP entre a SPA e o backend.
- `promptdown-ui-v3/public/js/lib` (folder) → `doc/references/lib.md` — Funções utilitárias puras — sem efeitos colaterais, sem DOM, sem dependência de estado global.
- `promptdown-ui-v3/public/js/lib/truncate.js` (file) → `doc/references/truncate-js.md` — Função pura para truncar texto, por exemplo nomes longos de arquivos exibidos na UI.
- `promptdown-ui-v3/public/js/lib/format-date.js` (file) → `doc/references/format-date-js.md` — Função pura para formatar datas (ex.: data de criação de um prompt) em pt-BR para exibição na UI.
- `promptdown-ui-v3/public/js/views` (folder) → `doc/references/views.md` — As 'páginas' da SPA — cada arquivo aqui representa uma tela completa.
- `promptdown-ui-v3/public/js/views/home-view.js` (file) → `doc/references/home-view-js.md` — Tela inicial da SPA — primeira view que o usuário vê ao abrir a aplicação.
- `promptdown-ui-v3/public/js/views/prompt-list-view.js` (file) → `doc/references/prompt-list-view-js.md` — Lista completa de prompts — busca os dados via api.js e renderiza um card por item via components/card.js.
- `promptdown-ui-v3/public/js/views/prompt-detail-view.js` (file) → `doc/references/prompt-detail-view-js.md` — Detalhe de um único prompt, identificado por um parâmetro de rota (ex.: '/prompts/42').
- `promptdown-ui-v3/public/js/components` (folder) → `doc/references/components.md` — Peças de UI reutilizáveis entre várias views (botões, modais, cards, etc.).
- `promptdown-ui-v3/public/js/components/card.js` (file) → `doc/references/card-js.md` — Componente de card — representa um prompt em formato resumido, reutilizado em várias views.
- `promptdown-ui-v3/public/js/components/modal.js` (file) → `doc/references/modal-js.md` — Componente de modal — overlay reutilizável com abrir/fechar e tecla Esc, diferente de uma view por ser efêmero.
- `promptdown-ui-v3/public/js/components/toast.js` (file) → `doc/references/toast-js.md` — Componente de notificação efêmera (toast) — aparece, some sozinho após um tempo, e suporta múltiplas mensagens em fila.
- `promptdown-ui-v3/public/js/components/prompt-form.js` (file) → `doc/references/prompt-form-js.md` — Componente de formulário DRY — fábrica de nó <form> que serve tanto para criar quanto para editar um prompt, comunicando o submit via CustomEvent.
- `promptdown-ui-v3/public/css` (folder) → `doc/references/css.md` — Estilos da aplicação, organizados em camadas isoladas e com responsabilidade única cada.
- `promptdown-ui-v3/public/css/tokens.css` (file) → `doc/references/tokens-css.md` — O 'dicionário visual' do projeto — cores, espaçamentos e tipografia como variáveis CSS.
- `promptdown-ui-v3/public/css/base.css` (file) → `doc/references/base-css.md` — Reset de estilos do navegador e definição da tipografia raiz.
- `promptdown-ui-v3/public/css/layout.css` (file) → `doc/references/layout-css.md` — Estrutura macro da página: grid, containers e as grandes regiões da tela.
- `promptdown-ui-v3/public/css/components` (folder) → `doc/references/components.md` — Peças de UI reutilizáveis entre várias views (botões, modais, cards, etc.).
- `promptdown-ui-v3/public/css/components/button.css` (file) → `doc/references/button-css.md` — Estilo do componente de botão — a peça de UI mais reutilizada do projeto, com variantes e estados de foco acessíveis.
- `promptdown-ui-v3/public/css/components/input.css` (file) → `doc/references/input-css.md` — Estilo dos campos de formulário (input, textarea, label, estados de erro) — única folha de estilos de entrada de dados do projeto.
- `promptdown-ui-v3/public/css/components/card.css` (file) → `doc/references/card-css.md` — Estilo do componente de card — item visual usado para representar um prompt em formato de grade.
- `promptdown-ui-v3/public/css/components/modal.css` (file) → `doc/references/modal-css.md` — Estilo do componente de modal — overlay de tela cheia com a caixa de diálogo centralizada.
- `promptdown-ui-v3/public/css/utilities.css` (file) → `doc/references/utilities-css.md` — Pequenas classes de uso pontual, como '.hidden' ou espaçamentos avulsos.
- `promptdown-ui-v3/.specs` (folder) → `doc/references/dot-specs.md` — Especificações de features, escritas para guiar desenvolvimento dirigido por agentes de IA.
- `promptdown-ui-v3/.specs/DESIGN.md` (file) → `doc/references/design-md.md` — SSoT de decisões visuais — tokens, grid, componentes e acessibilidade para toda a aplicação. Consumido pelo Claude Code antes de qualquer implementação de UI.
- `promptdown-ui-v3/.specs/project` (folder) → `doc/references/specs-project.md` — Documentos de contexto do projeto como um todo (visão, roadmap, estado atual).
- `promptdown-ui-v3/.specs/project/PROJECT.md` (file) → `doc/references/project-md.md` — Descrição do projeto: o que faz e para quem.
- `promptdown-ui-v3/.specs/project/ROADMAP.md` (file) → `doc/references/roadmap-md.md` — Plano de evolução do projeto a médio/longo prazo.
- `promptdown-ui-v3/.specs/project/STATE.md` (file) → `doc/references/state-md.md` — Snapshot do estado atual do desenvolvimento.
- `promptdown-ui-v3/.specs/features` (folder) → `doc/references/specs-features.md` — Especificações por feature (`spec.md`, `design.md`, `tasks.md`), escritas para guiar desenvolvimento dirigido por agentes de IA.
- `promptdown-ui-v3/.specs/features/prompt-search-filter` (folder) → `doc/references/specs-features-prompt-search-filter.md` — Exemplo concreto de feature: busca/filtro de prompts por título ou tag.
- `promptdown-ui-v3/.specs/features/prompt-search-filter/spec.md` (file) → `doc/references/specs-features-prompt-search-filter-spec.md` — Spec da feature (user stories, critérios de aceite, requirement IDs).
- `promptdown-ui-v3/.specs/features/repo-scaffolding` (folder) → `doc/references/specs-features-repo-scaffolding.md` — Exemplo concreto de feature Large: bootstrap de todo o repositório a partir do SKILL.md, só estrutura de pastas/arquivos.
- `promptdown-ui-v3/.specs/features/repo-scaffolding/spec.md` (file) → `doc/references/specs-features-repo-scaffolding-spec.md` — Spec da feature (user stories por subsistema, critérios de aceite, requirement IDs).
- `promptdown-ui-v3/.specs/features/repo-scaffolding/design.md` (file) → `doc/references/specs-features-repo-scaffolding-design.md` — Arquitetura do gerador de scaffold (parser do SKILL.md → árvore real de arquivos).
- `promptdown-ui-v3/.specs/features/repo-scaffolding/tasks.md` (file) → `doc/references/specs-features-repo-scaffolding-tasks.md` — Breakdown em tasks paralelas, uma por subsistema do repositório.
- `promptdown-ui-v3/scripts` (folder) → `doc/references/scripts.md` — Scripts auxiliares de operação — rodam na infraestrutura, não fazem parte do build da aplicação.
- `promptdown-ui-v3/scripts/backup-db.sh` (file) → `doc/references/backup-db-sh.md` — Backup diário e automático do database.json — a única fonte de dados 'reais' do projeto mock.
- `promptdown-ui-v3/scripts/generate-harness.mjs` (file) → `doc/references/generate-harness-mjs.md` — Lê plugins/ e escreve artefatos no destino correto de cada harness.
- `promptdown-ui-v3/tests` (folder) → `doc/references/tests.md` — Testes automatizados do projeto — a rede de segurança que bloqueia deploys quebrados.
- `promptdown-ui-v3/tests/unit` (folder) → `doc/references/unit.md` — Testes unitários escritos com Vitest (describe/it/expect).
- `promptdown-ui-v3/docker-compose.yml` (file) → `doc/references/docker-compose-yml.md` — Orquestra os dois containers da aplicação como um único sistema.
- `promptdown-ui-v3/Dockerfile.frontend` (file) → `doc/references/dockerfile-frontend.md` — Receita da imagem Docker do Nginx que serve o frontend estático.
- `promptdown-ui-v3/Dockerfile.api` (file) → `doc/references/dockerfile-api.md` — Receita da imagem Docker Node.js que executa o JSON Server.
- `promptdown-ui-v3/Makefile` (file) → `doc/references/makefile.md` — Orquestra geração de harnesses a partir de plugins/ via make generate-all.
