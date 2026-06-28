`.codex/` é a pasta de configuração de **projeto** do Codex CLI (OpenAI) — ignorada por todos os outros agentes. O equivalente de escopo global/usuário fica em `~/.codex/` (o diretório-base é configurável via variável de ambiente `CODEX_HOME`, com default `~/.codex` em sistemas Unix-like; no Windows segue um caminho equivalente por usuário).

## Conteúdo

```
.codex/
├── config.toml     # configuração de projeto (agentes, modelo, hooks, sandbox)
└── skills/         # Skills de projeto (SKILL.md + scripts/, references/, assets/)
```

No escopo global (`~/.codex/`), a mesma estrutura se repete, mais arquivos de estado da CLI (sessões, cache, auth).

## Como o Codex CLI chega aqui

```
Codex CLI  →  lê AGENTS.md (cadeia de precedência)  →  carrega .codex/config.toml  →  carrega .codex/skills/
```

Diferente do Claude Code (que tem `CLAUDE.md` como ponte para `AGENTS.md`), o Codex CLI lê `AGENTS.md` **diretamente** — é o formato nativo dele, não um import.

## Como o Codex concatena AGENTS.md

O Codex CLI concatena arquivos `AGENTS.md` na cadeia de instruções no início da sessão, seguindo uma precedência por diretório: **global → projeto → subpasta mais próxima**. Arquivos mais profundos (mais próximos do diretório de trabalho atual) sobrescrevem os mais altos na hierarquia. Essa cadeia concatenada está sujeita a um limite de tamanho de contexto (~32 KiB por padrão).

Um `AGENTS.md` global fica armazenado sob `$CODEX_HOME` (ex.: `~/.codex/AGENTS.md`). Se existir um arquivo `AGENTS.override.md` em qualquer nível, ele tem precedência sobre o `AGENTS.md` correspondente naquele nível.

`AGENTS.md` é Markdown puro — **sem** frontmatter YAML obrigatório (diferente do `SKILL.md`, que pode ter frontmatter opcional). O comando `/init` no Codex CLI gera um `AGENTS.md` inicial no diretório atual.

## config.toml — chaves principais

| Chave | Tipo | Descrição |
|---|---|---|
| `agents.<name>.config_file` | string | Caminho (relativo ao arquivo declarante) para uma camada TOML específica daquele papel de agente |
| `agents.<name>.description` | string | Texto mostrado ao spawnar aquele agente |
| `agents.<name>.nickname_candidates` | array de strings | Apelidos possíveis para o agente |
| `agents.job_max_runtime_seconds` | número | Tempo máximo de execução (padrão: 1800s) |
| `agents.max_depth` | número | Profundidade máxima de agentes aninhados (padrão: 1) |
| `agents.max_threads` | número | Threads paralelas máximas (padrão: 6) |
| `allow_login_shell` | boolean | Permite shell de login (padrão: `true`) |
| `model_instructions_file` | string | Caminho para substituir `AGENTS.md` |
| `model_provider` / `model_providers.<id>` | tabela | Configuração e autenticação de provedor de modelo |
| `hooks.<Event>` | array | Handlers de hook por evento |
| `instructions` | — | Reservado para uso futuro — prefira `model_instructions_file` ou `AGENTS.md` |

Exemplo:

```toml
[agents.codegen]
description = "Synthesis Engineer: polyglot, production-grade output."
config_file = "ordl/agents/codegen.toml"

agents.max_threads = 12
agents.max_depth = 2
```

## CLI: comandos relevantes

| Comando | Propósito |
|---|---|
| `codex` | Inicia a CLI (primeira execução solicita login via OAuth ChatGPT ou API key) |
| `/init` | Gera um `AGENTS.md` inicial no diretório atual |
| `codex resume` | Retoma uma sessão interativa anterior |
| `codex exec "<prompt>"` | Execução não-interativa, ideal para scripts/CI |
| `codex mcp` / `codex mcp-server` | Recursos MCP (experimentais) |
| `codex config validate` / `codex config migrate --check/--write` | Valida/migra `config.toml` — importante após updates da CLI |

## Migração TypeScript → Rust

A CLI passou por uma reescrita de TypeScript para Rust (2025-2026); builds em Rust mudaram schemas de configuração e fluxos de autenticação. Recomenda-se rodar `codex config validate`/`migrate` antes de aplicar mudanças após upgrades. Suporte a Windows melhorou, mas WSL2 ainda é recomendado para melhor compatibilidade.

## Fontes

- https://developers.openai.com/codex/cli
- https://developers.openai.com/codex/config-reference
- https://developers.openai.com/codex/learn/best-practices
- https://developers.openai.com/codex/cli/reference
