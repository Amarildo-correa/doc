`.cursor/rules/general.mdc` contém as regras globais do Cursor para este projeto — tipicamente configurada com `alwaysApply: true`, para ser injetada em **toda** sessão de chat, independente do arquivo aberto.

```yaml
---
description: "Convenções gerais do projeto, aplicadas sempre."
alwaysApply: true
---

(diretrizes gerais aqui)
```

Como `alwaysApply: true` faz o Cursor **ignorar qualquer `globs`** definido no mesmo arquivo (ver `cursor-rules.md`), não há necessidade de combinar os dois campos numa regra "geral" — escolha um ou outro propósito por arquivo.

## Fontes

- https://cursor.com/docs/rules
