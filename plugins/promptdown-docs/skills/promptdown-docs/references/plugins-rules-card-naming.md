`plugins/promptdown-helpers/rules/card-naming.md` é a fonte canônica da rule
de nomenclatura de cards — distribuída para todos os harnesses via Makefile.

## Onde é gerado

```
make generate HARNESS=claude       → .claude/rules/card-naming.md
make generate HARNESS=cursor       → .cursor/rules/card-naming.mdc
make generate HARNESS=antigravity  → .agents/rules/card-naming.md
```

## Fontes

- `plugins.md`
- `makefile.md`
- `card-js.md`
