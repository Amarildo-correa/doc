`.claude/rules/security.md` define os boundaries de segurança que o Claude Code
deve respeitar em toda sessão. Gerado de `plugins/promptdown-helpers/rules/security.md`.

## Conteúdo da rule

```markdown
# Security — promptdown-ui-v3

## Boundaries absolutos

- Nunca commitar secrets, API keys ou tokens — nem em comentários.
- Nunca usar `innerHTML` com conteúdo dinâmico — previne XSS.
- Nunca expor variáveis de ambiente no bundle do frontend.
- Nunca fazer write direto em `api/database.json` em produção sem aprovação.
- Nunca ignorar `api/middleware/auth.js` ao criar novos endpoints.

## Validação obrigatória

- Todo input do usuário que chega à API deve ser validado antes de persistir.
- Headers de segurança HTTP são responsabilidade do `worker.js` (Cloudflare) — não bypassar via código.

## Ao encontrar secret exposto

Parar imediatamente, não commitar, avisar o usuário.
```

## Fonte canônica

`plugins/promptdown-helpers/rules/security.md` — edite lá, nunca aqui.

## Fontes

- `claude-rules.md`
- `auth-js.md`
