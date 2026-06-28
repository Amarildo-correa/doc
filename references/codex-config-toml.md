`.codex/config.toml` é o exemplo concreto de configuração de projeto do Codex CLI — onde papéis de agente, limites de execução e outras opções são definidos (ver correção em `codex-agents.md`: não existe uma pasta `agents/*.toml` separada por agente).

```toml
[agents.codegen]
description = "Synthesis Engineer: polyglot, production-grade output."
config_file = "ordl/agents/codegen.toml"

agents.max_threads = 12
agents.max_depth = 2
agents.job_max_runtime_seconds = 1800

[model_providers.openai]
# configuração de provedor de modelo
```

## Por que esse design

- `[agents.codegen]` define um papel de agente chamado `codegen`, com sua própria `description` (mostrada ao spawnar esse agente) e um `config_file` opcional apontando para uma camada TOML separada — é essa convenção de `config_file` que permite, na prática, organizar configuração detalhada por agente em arquivos próprios, mesmo sem uma pasta `agents/` formal.
- `agents.max_threads`/`agents.max_depth`/`agents.job_max_runtime_seconds` são limites globais de execução (paralelismo, profundidade de agentes aninhados, timeout) — valores padrão são 6, 1 e 1800s respectivamente, caso omitidos.

## Localização

| Escopo | Caminho |
|---|---|
| Projeto | `.codex/config.toml` |
| Usuário/global | `$CODEX_HOME/config.toml` (por padrão, `~/.codex/config.toml`) |

## Fontes

- https://developers.openai.com/codex/config-reference
