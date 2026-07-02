Define apenas o serviço 'json-server' (Node, porta 3001) e o volume nomeado que persiste o database.json fora do ciclo de vida do container — não existe (nem nunca existiu, nesta arquitetura) um serviço 'frontend' na Vultr. O frontend inteiro (SSR, hidratação, GEO, assets estáticos) é responsabilidade do `worker.js` na edge do Cloudflare, não de um container aqui (ver `worker-js.md`, `wrangler-toml.md`).

```yaml
services:
    json-server:
        build:
            context: .
            dockerfile: Dockerfile.api
        ports:
            - "3001:3001"
        volumes:
            - db-data:/app/api/data

volumes:
    db-data:
```

## Por que não há mais um serviço 'frontend'

Antes, um container Nginx precisava rodar na mesma VM para servir `public/` e fazer proxy de `/api/`. Como os Workers Assets do Cloudflare agora servem os estáticos na edge global e o `worker.js` faz SSR/hidratação diretamente, a Vultr passa a rodar só o que não pode viver na edge: a API com estado (Node.js) e, futuramente, o banco de dados (PostgreSQL). Isso reduz o `docker-compose.yml` a um único serviço e elimina `Dockerfile.frontend` do repositório.

## Fontes

- `worker-js.md`
- `wrangler-toml.md`
- `dockerfile-api.md`
