Cobre a escolha do servidor, a divisão de responsabilidades entre Cloudflare e Vultr, o pipeline de CI/CD e a estratégia de backup. Este próprio painel usa o conteúdo deste arquivo como base para as aulas exibidas aqui.

- Cloudflare Worker + Workers Assets: frontend, SSR para SEO, Markdown para LLM, hidratação para humanos, cache na edge (ver `worker-js.md`, `wrangler-toml.md`)
- Infraestrutura Vultr (vc2-1c-1gb): só a API Node.js e o banco de dados — container Docker único (`json-server`/API), sem frontend
- CI/CD via GitHub Actions: dois destinos de deploy independentes (`wrangler deploy` para o Worker, SSH para a Vultr)
- Backup do database.json via cron
