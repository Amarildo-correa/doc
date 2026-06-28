`.agents/hooks.json` define hooks de workspace do Antigravity IDE, fora de qualquer plugin — scripts/comandos executados em pontos específicos do ciclo de execução do agente. Úteis para linters automáticos, gates de segurança, logging e diagnósticos.

## Localização por escopo (confirmada oficialmente)

| Escopo | Localização |
|---|---|
| Plugin | `<plugin-root>/hooks.json` |
| Workspace | `<workspace>/.agents/hooks.json` |
| Global | `~/.gemini/config/hooks.json` |

## Eventos suportados (confirmados oficialmente)

| Evento | Descrição | Alvo do matcher |
|---|---|---|
| `PreToolUse` | Antes de uma tool ser executada | Nome da tool (ex.: `run_command`) |
| `PostToolUse` | Após uma tool completar | Nome da tool |
| `PreInvocation` | Antes do modelo ser chamado | N/A (matcher ignorado) |
| `PostInvocation` | Após as tool calls terminarem | N/A (matcher ignorado) |
| `Stop` | Quando o loop de execução termina | N/A (matcher ignorado) |

## Schema do arquivo

`hooks.json` mapeia **nomes de hook** (escolhidos por você) para suas configurações de evento:

```json
{
  "my-linter-hook": {
    "PostToolUse": [
      {
        "matcher": "run_command",
        "hooks": [
          {
            "type": "command",
            "command": "./scripts/lint.sh",
            "timeout": 10
          }
        ]
      }
    ]
  },
  "safety-gate": {
    "enabled": false,
    "PreToolUse": [
      {
        "matcher": "run_command",
        "hooks": [
          { "command": "./scripts/safety-check.sh" }
        ]
      }
    ]
  },
  "reminder": {
    "PreInvocation": [
      { "type": "command", "command": "./scripts/reminder.sh" }
    ]
  }
}
```

**Detalhe estrutural confirmado**: para `PreToolUse`/`PostToolUse`, os itens são objetos com `matcher` envolvendo uma lista de `hooks`. Para `PreInvocation`/`PostInvocation`/`Stop`, os itens são objetos de handler **diretos** (sem o wrapper `matcher`) — o `reminder` acima ilustra esse formato mais simples.

## Campos de definição de hook

| Campo | Tipo | Descrição |
|---|---|---|
| `enabled` | boolean | Opcional — `false` desabilita o hook sem remover a config. Padrão: `true` |
| `PreToolUse` / `PostToolUse` | array | Handlers que rodam antes/depois de uma tool, com wrapper `matcher` |
| `PreInvocation` / `PostInvocation` / `Stop` | array | Handlers diretos, sem `matcher` |

## Campos do handler

| Campo | Obrigatório | Descrição |
|---|---|---|
| `type` | Não | Hoje só `"command"` é suportado (default) |
| `command` | **Sim** | Comando shell a executar |
| `timeout` | Não | Timeout em segundos (padrão: 30) |

## Padrões de matcher

```
""  ou  "*"             →  qualquer tool
"run_command"            →  exatamente run_command
"run_command|view_file"  →  qualquer uma das duas
"browser_.*"             →  qualquer tool começando com browser_
```

## PreToolUse/PostToolUse via plugin — status

Há relatos da comunidade de que hooks `PreToolUse`/`PostToolUse` específicos de um plugin de terceiros (ex.: extração automática de entidades após `write_to_file`) ainda não estavam embarcados em certas integrações na época da pesquisa — a superfície de hook em si é real e documentada oficialmente (ver tabela de eventos acima), mas a adoção por plugins de terceiros pode variar.

## Inspecionar hooks ativos

Na CLI (`agy`), rode `/hooks` no TUI para ver todos os hooks carregados e ativos.

## Fontes

- https://antigravity.google/docs/hooks
- https://antigravity.google/docs/cli-plugins
- https://github.com/MemPalace/mempalace/blob/develop/hooks/antigravity/INVESTIGATION.md
