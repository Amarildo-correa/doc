`.cursor/rules/` contém as **Rules** do Cursor — arquivos `.mdc` (Markdown + frontmatter) que injetam contexto no modelo quando aplicados. É o mecanismo do Cursor equivalente a Skills (Claude Code/Codex) e a Rules do Antigravity IDE, mas com um formato de ativação próprio.

## Estrutura do frontmatter

```yaml
---
description: "Project linting and commit rules — recommend fixes and enforce style."
globs: *.js, src/**/*.ts
alwaysApply: true
---

(conteúdo Markdown da regra)
```

| Campo | Tipo | Descrição |
|---|---|---|
| `description` | string | Usada pelo Cursor Agent para avaliar relevância (ativação "Apply Intelligently") |
| `globs` | string (lista separada por vírgula) | Padrões de caminho para inclusão automática quando um arquivo aberto corresponde |
| `alwaysApply` | boolean | Se `true`, a regra é injetada em **toda** sessão de chat, **e os `globs` são ignorados** |

**Atenção a um detalhe pouco intuitivo, confirmado por exemplos oficiais e da comunidade**: `globs` é escrito como string separada por vírgulas (`*.js, src/**/*.ts`), **não** como array entre colchetes/aspas (`["*.js", "src/**/*.ts"]`). O parser do Cursor faz split por vírgula — espaços após a vírgula, vírgulas não-escapadas dentro de um padrão, ou uso de colchetes/aspas podem causar **falha silenciosa** (a regra simplesmente não é aplicada, sem erro visível).

## Quatro modos de ativação (UI)

| Modo (label na UI) | Equivalente em frontmatter | Quando dispara |
|---|---|---|
| **Always Apply** | `alwaysApply: true` | Toda requisição ao agente, incondicionalmente |
| **Apply to Specific Files** | `globs: <padrão>` | Quando o arquivo atual corresponde ao glob |
| **Apply Intelligently** | só `description`, sem `globs`/`alwaysApply` | O próprio agente decide, com base na descrição |
| **Apply Manually** | — | Só quando referenciada explicitamente via `@nome-da-regra` no chat |

## Pegadinhas conhecidas (reportadas pela comunidade, não documentação oficial)

- Uma regra criada/editada **no meio de uma conversa** pode não ser aplicada até abrir um **novo chat**.
- Houve relatos de regressão (Cursor 3.0.16 macOS) em que regras `alwaysApply: true` passaram a ser tratadas como "requestable" em vez de auto-injetadas — comportamento de runtime pode mudar entre versões.
- `src/` casa só um nível de diretório; para recursivo use `src/**/*`.
- Exclusão de padrão com `!` é suportada em exemplos da comunidade (ex.: `*.py, !test_*.py`), mas não há uma especificação formal de gramática de glob publicada pela Cursor.

## Boas práticas

- Mantenha cada `.mdc` pequeno (algumas centenas de linhas) — descrições concisas ajudam a relevância no modo "Apply Intelligently".
- Use `alwaysApply: true` só para regras genuinamente globais (lembre: isso ignora `globs`).
- Prefira um padrão de glob por linha/regra simples a listas complexas com chaves (`{a,b}`) — fragilidade reportada no parser.

## Fontes

- https://cursor.com/docs/rules
