`wrangler.toml` configura o Cloudflare Worker que substitui o Nginx tanto em desenvolvimento local quanto em produção — não existe ambiente nesta arquitetura em que o frontend seja servido por um container Nginx.

## Conteúdo

```toml
name = "promptdown-ui-v3"
main = "worker.js"
compatibility_date = "2025-01-01"

[assets]
directory = "./public"
binding = "ASSETS"

[vars]
API_URL = "http://localhost:3001"

[env.production.vars]
API_URL = "https://api.promptdown.com"
```

## Localmente: `wrangler dev` substitui o Nginx

```bash
wrangler dev --port 8787
```

Esse comando sobe o mesmo `worker.js` que roda em produção, servindo os arquivos estáticos de `./public` via `[assets]` e processando SSR/hidratação/GEO na mesma porta — não há `docker-compose` nem container de frontend envolvido em nenhum momento do desenvolvimento local.

## Em produção: Workers Assets na edge

O binding `[assets]` é resolvido pelos Workers Assets do Cloudflare, que replicam os arquivos de `public/` na edge global — sem passar pela VM Vultr. A Vultr, nesta arquitetura, nunca serve um único arquivo estático de frontend (ver `docker-compose-yml.md` e a tabela de responsabilidades em `promptdown-ui-v3.md`).

## Por que `API_URL` muda por ambiente

`env.API_URL` é lido pelo `worker.js` (ver `worker-js.md`, função `fetchPrompt`) para buscar os dados do prompt durante o SSR. Em dev aponta para o `json-server` rodando localmente (`localhost:3001`); em produção aponta para a API Node.js na Vultr. O frontend hidratado no navegador (`public/js/api.js`) não usa essa variável — ele chama a API da Vultr diretamente por uma URL absoluta própria, já que não há mais um proxy Nginx fazendo esse papel (ver `api-js.md`).

## Papel na arquitetura geral

- Elimina a necessidade de `Dockerfile.frontend`/`nginx.conf` — o Worker e o binding `[assets]` cobrem exatamente o que o Nginx fazia (servir estáticos + fallback de SPA), mais SSR e detecção de bots.
- `docker-compose.yml` na Vultr passa a orquestrar só `api`/`db` (ver `docker-compose-yml.md`).

## Fontes

- `worker-js.md`
- `docker-compose-yml.md`
- `deploy-yml.md`
