`.codex/agents/` — **correção em relação ao guia genérico original**: a documentação oficial do Codex CLI não confirma uma pasta `agents/` dedicada para definições por arquivo, como existe no Claude Code (`.claude/agents/*.md`). No Codex CLI, papéis de agente são definidos como **entradas dentro do próprio `config.toml`**, sob a tabela `[agents.<name>]`:

```toml
[agents.codegen]
description = "Synthesis Engineer: polyglot, production-grade output."
config_file = "ordl/agents/codegen.toml"

agents.max_threads = 12
agents.max_depth = 2
```

## Campos de um papel de agente

| Campo | Descrição |
|---|---|
| `config_file` | Caminho (relativo ao arquivo declarante) para uma camada TOML separada com configuração específica daquele papel — é essa convenção que permite, na prática, organizar arquivos por agente numa pasta como `agents/`, mesmo sem ser um requisito formal da CLI |
| `description` | Texto mostrado ao spawnar aquele agente |
| `nickname_candidates` | Array de apelidos possíveis |

## Limites de execução (globais, em `agents.*`)

| Chave | Padrão | Descrição |
|---|---|---|
| `agents.job_max_runtime_seconds` | 1800 | Tempo máximo de execução de um agente |
| `agents.max_depth` | 1 | Profundidade máxima de agentes aninhados |
| `agents.max_threads` | 6 | Threads paralelas máximas |

## Diferença em relação ao modelo do Claude Code

No Claude Code, cada subagente é um arquivo Markdown independente com frontmatter YAML (`name`, `description`, `tools`, `model`, etc.), descoberto automaticamente em `.claude/agents/`. No Codex CLI, a unidade de configuração central é o `config.toml` — a "pasta de agentes" é uma convenção de organização do `config_file`, não uma estrutura de descoberta automática por diretório.

## Fontes

- https://developers.openai.com/codex/config-reference
- https://developers.openai.com/codex/cli
