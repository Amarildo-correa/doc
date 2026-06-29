`.claude/rules/testing.md` define como escrever e validar testes neste projeto.
Gerado de `plugins/promptdown-helpers/rules/testing.md`.

## Conteúdo da rule

```markdown
# Testing — promptdown-ui-v3

Framework: **Vitest** em `tests/unit/`.

## Obrigações

- Todo componente novo em `public/js/components/*.js` precisa de teste
  unitário em `tests/unit/`.
- Funções puras em `public/js/lib/` devem ter 100% de cobertura de branches.
- Mockar apenas `public/js/api.js` — nunca `fetch` diretamente nos componentes.

## Convenções

- Nome do arquivo: `<nome>.test.js`.
- Um `describe` por arquivo; `it`/`test` em inglês.
- Sem `console.log` em testes.

## Gate

Nunca propor PR sem que `npm run test` passe localmente.
```

## Fonte canônica

`plugins/promptdown-helpers/rules/testing.md` — edite lá, nunca aqui.

## Fontes

- `claude-rules.md`
- `tests.md`
- `unit.md`
