O `worker.js` é o único ponto de entrada HTTP da aplicação em produção — roda na edge do Cloudflare e decide, por requisição, se serve markdown para bots de LLM, HTML semântico para bots de SEO, ou HTML + hidratação para humanos. Não existem dois serviços (um de SSR, outro de SPA): é um único Worker que atende a mesma rota de três formas diferentes.

## Papel de "porteiro"

```js
export default {
  async fetch(request, env, ctx) {
    const url = new URL(request.url);
    const match = url.pathname.match(/^\/prompts\/([^/]+)$/);

    // Só /prompts/:id passa por lógica de SSR — todo o resto (assets
    // estáticos, JS, CSS) é servido diretamente pelo binding [assets]
    if (!match) return env.ASSETS.fetch(request);

    const id = match[1];
    const userAgent = request.headers.get("User-Agent") ?? "";

    const cacheKey = new Request(url.toString(), request);
    const cache = caches.default;
    const cached = await cache.match(cacheKey);
    if (cached) return cached;

    const prompt = await fetchPrompt(id, env.API_URL);
    const response = renderFor(userAgent, prompt, url);

    response.headers.set(
      "Cache-Control",
      "public, max-age=3600, stale-while-revalidate=86400"
    );
    ctx.waitUntil(cache.put(cacheKey, response.clone()));
    return response;
  },
};
```

## Três respostas para a mesma URL

| Cliente | Detecção | Resposta |
|---|---|---|
| Bot de LLM (`GPTBot`, `ClaudeBot`, `PerplexityBot`...) | `User-Agent` contém o nome do bot | `text/markdown` limpo, sem HTML/CSS/JS |
| Bot de SEO (`Googlebot`, `Bingbot`...) | `User-Agent` contém o nome do bot | HTML semântico + JSON-LD + Open Graph, **sem** `<script>` |
| Humano (navegador) | fallback (nenhum bot detectado) | HTML semântico + `<script type="module" src="/js/app.js">` no final do body, para hidratar `#app` |

```js
function renderFor(userAgent, prompt, url) {
  if (isLlmBot(userAgent)) return renderMarkdown(prompt);
  if (isSeoBot(userAgent)) return renderHtml(prompt, url, { includeScript: false });
  return renderHtml(prompt, url, { includeScript: true });
}
```

## Por que isso elimina cloaking por design

Cloaking é servir conteúdo diferente para bot e humano com a intenção de enganar o ranqueamento. Aqui a diferença entre as três respostas é estritamente de **formato de apresentação** do mesmo dado (o mesmo `prompt` vindo da mesma chamada a `fetchPrompt`) — nunca de conteúdo. Bots de SEO recebem o mesmo HTML que o humano recebe, só sem o `<script>` de hidratação; bots de LLM recebem a mesma informação em Markdown. Nenhum caminho esconde ou infla dados que os outros não veem.

## Hidratação, não reconstrução

O HTML que o Worker devolve para humanos já é a página completa e semântica — o `<script src="/js/app.js">` no final do body não reconstrói `#app` do zero, ele hidrata o que já está no DOM (ver `router-js.md`, que detecta o atributo `data-route` deixado pelo SSR). Isso é o que garante que bot e humano recebem exatamente o mesmo arquivo: o JavaScript é aditivo, nunca substitui o conteúdo inicial antes de montar os event listeners.

## Cache na edge

`caches.default` guarda a resposta já renderizada por até 1 hora (`max-age=3600`), servindo versões um pouco desatualizadas por até 24h (`stale-while-revalidate=86400`) enquanto revalida em segundo plano. Quando a API no Vultr recebe uma edição de prompt, ela dispara um purge via API do Cloudflare para essa URL específica — e um cache warming (uma requisição imediata ao Worker) logo após a criação de um prompt novo, para que a primeira visita já encontre cache quente.

## Papel na arquitetura geral

- Demais rotas (`/`, `/js/*`, `/css/*`) não passam pela lógica de SSR — `env.ASSETS.fetch(request)` delega diretamente para os Workers Assets, que servem os arquivos de `public/` (ver `public.md`).
- `/prompts/:id` é a única rota que o Worker trata de forma especial, porque é a única que precisa de dados dinâmicos (o prompt específico) para montar HTML/Markdown com conteúdo real antes de responder.
- Nginx não existe em nenhum ambiente desta arquitetura — nem local (`wrangler dev`), nem produção (Cloudflare) — ver `wrangler-toml.md` e `dot-specs.md`/`deploy-md.md`.

## Fontes

- `wrangler-toml.md`
- `router-js.md`
- `index-html.md`
- `llms-txt.md`
