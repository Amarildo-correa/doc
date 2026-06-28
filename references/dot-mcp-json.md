`.mcp.json` na raiz do projeto define MCP (Model Context Protocol) servers de **projeto**, compartilhados via git. Cada harness tem sua própria convenção — não há um único arquivo universal lido por todos.

## MCP por ferramenta (comparativo)

| Ferramenta | Configuração de projeto | Configuração global/usuário |
|---|---|---|
| **Claude Code** | `.mcp.json` na raiz (lido direto) | `~/.claude.json` (servers de escopo user/local) |
| **Antigravity IDE** | `mcp_config.json` dentro de um plugin de workspace (`.agents/plugins/<plugin>/mcp_config.json`) | `~/.gemini/config/mcp_config.json` |
| **Antigravity CLI (`agy`)** | `mcp_config.json` dentro de plugin | `~/.gemini/antigravity-cli/plugins/<plugin>/mcp_config.json` |
| **Codex CLI** | Seção `model_provider`/`model_providers.<id>` em `.codex/config.toml` | Mesma estrutura em `~/.codex/config.toml` |
| **Cursor** | Não documentado como arquivo de projeto equivalente nesta pesquisa | Configurado via Settings da IDE |

## Estrutura do .mcp.json (Claude Code)

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": { "DATABASE_URL": "postgresql://..." }
    }
  }
}
```

## Por que isso importa para um repositório multi-agente

Se um MCP server (ex.: um servidor de banco de dados ou de busca) precisa estar disponível para **todos** os agentes que tocam o repositório, ele precisa ser configurado **uma vez por harness** — não existe hoje um formato de MCP único lido por Claude Code, Codex CLI, Antigravity e Cursor simultaneamente a partir de um só arquivo de projeto. O `.mcp.json` cobre só o Claude Code.

## Fontes

- https://code.claude.com/docs/en/settings
- https://antigravity.google/docs/plugins
- https://developers.openai.com/codex/config-reference
