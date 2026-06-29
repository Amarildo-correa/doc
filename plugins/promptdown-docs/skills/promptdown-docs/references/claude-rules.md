`.claude/rules/` é um **artefato gerado** pelo Makefile a partir de
`plugins/*/rules/` — nunca editar diretamente.

## Como funciona

```
plugins/*/rules/*.md
        ↓ make generate HARNESS=claude
.claude/rules/*.md
```

## Carregamento automático

Todos os `.md` dentro de `.claude/rules/` são carregados pelo Claude Code
no início de cada sessão, com a mesma prioridade do `CLAUDE.md` de projeto.

## Rules com escopo por arquivo

```yaml
---
paths:
  - "public/js/**/*.js"
---
```

Rules sem `paths` se aplicam a toda sessão incondicionalmente.

## Regra de ouro

Edite sempre a fonte em `plugins/*/rules/`, nunca os arquivos aqui.
Após editar, rode `make generate HARNESS=claude` para sincronizar.

## Fontes

- `claude-md.md`
- `plugins.md`
- `makefile.md`
