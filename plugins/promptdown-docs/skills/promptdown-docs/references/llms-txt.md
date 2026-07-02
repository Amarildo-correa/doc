`llms.txt`, na raiz do site (`/llms.txt`), é um índice em Markdown pensado para crawlers de LLM (`GPTBot`, `ClaudeBot`, `PerplexityBot`...) — o equivalente, para agentes de IA, ao que `sitemap.xml`/`robots.txt` são para buscadores tradicionais.

## Conteúdo

```markdown
# Promptdown

> Catálogo de prompts reutilizáveis, com busca e filtro por tag.

## Prompts

- [Lista completa](/prompts): todos os prompts cadastrados
- [Detalhe de um prompt](/prompts/:id): conteúdo completo em Markdown limpo
```

## Por que existe além do `worker.js` já servir Markdown por rota

`llms.txt` responde "o que existe neste site e onde", enquanto a detecção de bot em `worker.js` (ver `worker-js.md`) responde "como servir uma página específica que o crawler já decidiu visitar". Um crawler de LLM que ainda não conhece a estrutura do site lê `llms.txt` primeiro para decidir quais URLs vale a pena buscar — só depois chega a `/prompts/:id`, onde recebe o Markdown limpo.

## Papel na arquitetura geral

Servido como asset estático (via `env.ASSETS`, mesmo caminho de `public/`), sem precisar de lógica no Worker — é só mais um arquivo em `public/llms.txt`.

## Fontes

- `worker-js.md`
- `public.md`
