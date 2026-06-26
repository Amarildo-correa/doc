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
- `promptdown-ui-v3/.github` (folder) → `doc/references/dot-github.md` — Pasta de configurações do GitHub — não faz parte do código da aplicação em si.
- `promptdown-ui-v3/.github/workflows` (folder) → `doc/references/workflows.md` — Define os pipelines de automação executados pelo GitHub Actions.
- `promptdown-ui-v3/.github/workflows/deploy.yml` (file) → `doc/references/deploy-yml.md` — CI/CD — GitHub Actions
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
- `promptdown-ui-v3/public/css` (folder) → `doc/references/css.md` — Estilos da aplicação, organizados em camadas isoladas e com responsabilidade única cada.
- `promptdown-ui-v3/public/css/tokens.css` (file) → `doc/references/tokens-css.md` — O 'dicionário visual' do projeto — cores, espaçamentos e tipografia como variáveis CSS.
- `promptdown-ui-v3/public/css/base.css` (file) → `doc/references/base-css.md` — Reset de estilos do navegador e definição da tipografia raiz.
- `promptdown-ui-v3/public/css/layout.css` (file) → `doc/references/layout-css.md` — Estrutura macro da página: grid, containers e as grandes regiões da tela.
- `promptdown-ui-v3/public/css/components` (folder) → `doc/references/components.md` — Peças de UI reutilizáveis entre várias views (botões, modais, cards, etc.).
- `promptdown-ui-v3/public/css/components/button.css` (file) → `doc/references/button-css.md` — Estilo do componente de botão — a peça de UI mais reutilizada do projeto, com variantes e estados de foco acessíveis.
- `promptdown-ui-v3/public/css/components/card.css` (file) → `doc/references/card-css.md` — Estilo do componente de card — item visual usado para representar um prompt em formato de grade.
- `promptdown-ui-v3/public/css/components/modal.css` (file) → `doc/references/modal-css.md` — Estilo do componente de modal — overlay de tela cheia com a caixa de diálogo centralizada.
- `promptdown-ui-v3/public/css/utilities.css` (file) → `doc/references/utilities-css.md` — Pequenas classes de uso pontual, como '.hidden' ou espaçamentos avulsos.
- `promptdown-ui-v3/.specs` (folder) → `doc/references/dot-specs.md` — Especificações de features, escritas para guiar desenvolvimento dirigido por agentes de IA.
- `promptdown-ui-v3/scripts` (folder) → `doc/references/scripts.md` — Scripts auxiliares de operação — rodam na infraestrutura, não fazem parte do build da aplicação.
- `promptdown-ui-v3/scripts/backup-db.sh` (file) → `doc/references/backup-db-sh.md` — Backup diário e automático do database.json — a única fonte de dados 'reais' do projeto mock.
- `promptdown-ui-v3/tests` (folder) → `doc/references/tests.md` — Testes automatizados do projeto — a rede de segurança que bloqueia deploys quebrados.
- `promptdown-ui-v3/tests/unit` (folder) → `doc/references/unit.md` — Testes unitários escritos com Vitest (describe/it/expect).
- `promptdown-ui-v3/docker-compose.yml` (file) → `doc/references/docker-compose-yml.md` — Orquestra os dois containers da aplicação como um único sistema.
- `promptdown-ui-v3/Dockerfile.frontend` (file) → `doc/references/dockerfile-frontend.md` — Receita da imagem Docker do Nginx que serve o frontend estático.
- `promptdown-ui-v3/Dockerfile.api` (file) → `doc/references/dockerfile-api.md` — Receita da imagem Docker Node.js que executa o JSON Server.
