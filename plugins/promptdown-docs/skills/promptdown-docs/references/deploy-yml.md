A cada push em 'main', primeiro roda os testes. Só se eles passarem (os jobs de deploy dependem do job 'test' via 'needs: test') é que o workflow publica em dois destinos independentes: a API/DB na Vultr via SSH, e o Worker (frontend + SSR + GEO) no Cloudflare via `wrangler deploy` — não há ordem de dependência entre esses dois deploys, pois são sistemas desacoplados.

```yaml
jobs:
    test:
        runs-on: ubuntu-latest
        steps:
            - run: npm install
            - run: npm run test
            - run: npm run test:coverage

    deploy-api:
        needs: test  # só roda se os testes passarem
        steps:
            - uses: appleboy/ssh-action@v1
              with:
                  host: ${{ secrets.VULTR_HOST }}
                  script: |
                      cd /var/www/promptdown-ui-v3
                      git pull origin main
                      docker compose up -d --build  # só o serviço json-server/api

    deploy-worker:
        needs: test  # só roda se os testes passarem
        steps:
            - uses: cloudflare/wrangler-action@v3
              with:
                  apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
```

## Secrets necessários no GitHub

- VULTR_HOST — IP público da VM (só api/db)
- VULTR_USER — usuário SSH
- VULTR_SSH_KEY — chave privada SSH
- CLOUDFLARE_API_TOKEN — token com permissão de deploy de Workers (usado por `wrangler deploy` e também pela API na Vultr para disparar purge de cache, ver `worker-js.md`)

## Por que dois jobs de deploy, não um

Antes, um único `docker compose up -d --build` na Vultr reconstruía frontend e API juntos, porque ambos viviam na mesma VM. Agora o frontend/SSR não tem mais presença na Vultr — ele é publicado separadamente no Cloudflare via `wrangler deploy`. Rodar os dois jobs em paralelo (ambos dependendo só de 'test') reflete que são dois sistemas de deploy independentes, não duas metades do mesmo serviço.
