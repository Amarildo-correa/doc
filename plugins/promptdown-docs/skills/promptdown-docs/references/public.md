Não existe mais um container Nginx nesta arquitetura: `public/` é servido pelos **Workers Assets** do Cloudflare (binding `[assets]` em `wrangler.toml`, ver `wrangler-toml.md`) — na edge global em produção, e via `wrangler dev` localmente. O `worker.js` (ver `worker-js.md`) é quem decide, por rota, se delega para `env.ASSETS.fetch(request)` (estáticos) ou renderiza SSR/hidratação/Markdown dinamicamente.

```js
// dentro de worker.js — qualquer rota que não seja /prompts/:id
// é servida diretamente pelos Workers Assets, sem lógica adicional
if (!match) return env.ASSETS.fetch(request);
```

## Por que não há mais 'try_files' nem fallback de servidor

Numa SPA com roteamento via History API, uma URL como '/prompts/42' não corresponde a nenhum arquivo físico. Com Nginx, isso exigia um `try_files ... /index.html` para evitar 404. Aqui o problema desaparece de outra forma: `/prompts/:id` é justamente a rota que o `worker.js` intercepta e renderiza com SSR (nunca delega para `env.ASSETS`), então ela sempre recebe uma resposta com conteúdo real — nunca um 404 nem um `index.html` genérico esperando o JavaScript decidir o que mostrar.

## Estrutura interna

```text
public/
├── index.html   # único ponto de entrada HTML
├── llms.txt     # índice para crawlers de LLM (ver llms-txt.md)
├── js/          # toda a lógica da aplicação
└── css/         # toda a apresentação visual
```

## Papel na arquitetura geral

Esta pasta representa a camada de apresentação pura — ela não sabe como os dados são persistidos (isso é responsabilidade de 'api/'), apenas como buscá-los via HTTP e exibi-los. Essa fronteira é o que permite trocar o backend inteiro (de JSON Server para qualquer linguagem) sem alterar uma única linha aqui, desde que o contrato de 'openapi.yaml' seja respeitado.

## Boas práticas e armadilhas comuns

- Nunca referenciar caminhos absolutos do sistema de arquivos do host — tudo deve ser relativo à raiz servida pelos Workers Assets.
- O frontend hidratado (`public/js/api.js`) chama a API da Vultr por uma URL absoluta própria, habilitada via CORS — não há mais um proxy `/api/` no mesmo domínio (ver `api-js.md`).
- Lembrar que qualquer arquivo colocado aqui é público — nunca commitar segredos, chaves ou configurações sensíveis dentro de 'public/'.

## Fontes

- `worker-js.md`
- `wrangler-toml.md`
- `llms-txt.md`
