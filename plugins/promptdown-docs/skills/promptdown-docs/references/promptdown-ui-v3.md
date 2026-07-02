Não usa React, Vue ou bundler obrigatório: a navegação é feita via History API, o estado via Proxy + pub/sub e os componentes via <template>. Em produção, um único Cloudflare Worker (`worker.js`) serve o frontend (via Workers Assets), faz SSR para bots de SEO, devolve Markdown para bots de LLM e hidrata a SPA para humanos — sempre na mesma rota. A API mock (JSON Server) roda sozinha num container Docker numa VM Vultr, sem nenhum frontend nela. Deploy automático a cada push em 'main', para os dois destinos (Cloudflare via `wrangler deploy`, Vultr via SSH).

## Visão geral da árvore

```text
promptdown-ui-v3/
├── public/        # frontend estático + llms.txt (servido pelos Workers Assets)
├── worker.js      # SSR + hidratação + GEO — porteiro de todas as rotas
├── wrangler.toml  # config do Worker (dev local e produção)
├── api/           # backend mock (JSON Server, só roda na Vultr)
├── tests/         # testes unitários (Vitest)
├── .specs/        # specs de features
├── scripts/       # automação de infraestrutura
├── notes/         # documentação interna
└── docker-compose.yml  # orquestra só a API/DB na Vultr
```

## Modelo de hidratação progressiva

O servidor (Worker) sempre entrega HTML semântico completo, com `<script>` incluso para humanos. O JavaScript hidrata `#app` em vez de reconstruí-lo do zero — bots leem o HTML e ignoram o script, humanos recebem o mesmo arquivo e o JavaScript se acopla por cima. Ver `worker-js.md` e `router-js.md`.

## Divisão de responsabilidades: Cloudflare vs. Vultr

| Responsabilidade | Cloudflare | Vultr |
|---|---|---|
| Assets SPA (JS, CSS) | Workers Assets | — |
| SSR para bots de SEO | Worker | — |
| Markdown para bots de LLM | Worker | — |
| Hidratação da SPA para humanos | Worker entrega HTML + script | — |
| Cache e CDN | Edge global (`caches.default`) | — |
| Sessão e autenticação | — | Node.js |
| Validação de formulário | — | Node.js |
| API REST | — | Node.js |
| Banco de dados | — | PostgreSQL |

O `database.json` do JSON Server (ver `api.md`, `database-json.md`) é o mock atual — a tabela acima descreve o destino-alvo da Vultr quando o backend real (não-mock) substituir o JSON Server por PostgreSQL, mantendo o mesmo papel: persistência que só existe na Vultr, nunca na edge.

## Convenções que valem para todo o projeto

- const/let, nunca var · === / !==, nunca == / !=
- JS puro com JSDoc opcional, sem TypeScript por padrão
- Nunca innerHTML com conteúdo dinâmico — sempre textContent ou <template>
- Roteamento via History API, nunca hash routing (#)

## Fontes

- `worker-js.md`
- `wrangler-toml.md`
- `docker-compose-yml.md`
