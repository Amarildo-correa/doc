`.claude/settings.json` é o mecanismo oficial de configuração do Claude Code, usando o sistema hierárquico de escopos (Managed > Local > Project > User). Esta é especificamente a versão de escopo **Project** — commitada no controle de versão, compartilhada com todos os colaboradores do repositório.

## Localização por escopo

| Escopo | Arquivo |
|---|---|
| User | `~/.claude/settings.json` — aplica a todos os seus projetos |
| Project | `.claude/settings.json` — commitado, compartilhado com o time |
| Local | `.claude/settings.local.json` — gitignored automaticamente, preferências pessoais para este repo |
| Managed | `managed-settings.json` em diretório de sistema (ex.: `C:\Program Files\ClaudeCode\` no Windows) — implantado por TI, não pode ser sobrescrito |

## Exemplo completo

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm run test:*)",
      "Read(~/.zshrc)"
    ],
    "deny": [
      "Bash(curl:*)",
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)"
    ]
  },
  "env": {
    "CLAUDE_CODE_ENABLE_TELEMETRY": "1",
    "OTEL_METRICS_EXPORTER": "otlp"
  },
  "companyAnnouncements": [
    "Welcome to Acme Corp! Review our code guidelines at docs.acme.com",
    "Reminder: Code reviews required for all PRs"
  ]
}
```

## Principais chaves disponíveis

| Chave | Descrição |
|---|---|
| `permissions` | Regras `allow`/`deny`/`ask` de uso de ferramentas (ex.: `Bash(npm run test:*)`) |
| `hooks` | Comandos customizados antes/depois de execuções de ferramentas — ver `agents-hooks-json.md` para o equivalente no Antigravity IDE |
| `env` | Variáveis de ambiente aplicadas a toda sessão |
| `model` | Sobrescreve o modelo padrão usado pelo Claude Code |
| `statusLine` | Configura uma status line customizada |
| `outputStyle` | Ajusta o system prompt via um output style |
| `cleanupPeriodDays` | Dias de inatividade antes de sessões serem apagadas no startup (padrão: 30) |
| `apiKeyHelper` | Script que gera um valor de autenticação customizado, enviado como header `X-Api-Key`/`Authorization` |
| `allowManagedHooksOnly` | (Só em settings gerenciado) Bloqueia hooks de usuário/projeto/plugin, permitindo só hooks gerenciados |

## Diferença em relação a `.mcp.json`

`.mcp.json`, na raiz do projeto, é lido diretamente pelo Claude Code para MCP servers de projeto compartilhados via git. Já MCP servers de escopo **user** ou **local** ficam registrados em `~/.claude.json` (não em `settings.json`). O campo `hooks` dentro de `settings.json` é independente de MCP — controla automações no ciclo de execução de ferramentas (lint automático, gates de segurança, logging), não conexões com servidores externos.

## Outras configurações fora de settings.json

`~/.claude.json` guarda preferências de UI (tema, modo de notificação, editor), sessão OAuth, configuração de MCP em escopo user/local, estado por projeto (ferramentas permitidas, trust settings) e caches — distinto de `settings.json`, que é o mecanismo "oficial" e documentado de configuração.

## Fontes

- https://code.claude.com/docs/en/settings
