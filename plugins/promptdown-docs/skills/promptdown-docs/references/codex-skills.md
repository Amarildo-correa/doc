`.codex/skills/` contém as **Agent Skills** do Codex CLI — pacotes reutilizáveis de instruções, scripts e assets que estendem as capacidades do agente, conceitualmente equivalentes às Skills do Claude Code.

**Correção em relação ao guia genérico original**: não há confirmação oficial de um limite fixo de "8KB por arquivo" para Skills do Codex CLI — esse dado não aparece na documentação oficial pesquisada e foi removido desta versão por falta de fonte confiável.

## Onde Skills vivem

| Escopo | Localização |
|---|---|
| Projeto | `.codex/skills/` |
| Usuário/global | `$CODEX_HOME/skills` (por padrão, `~/.codex/skills`) |

## Estrutura de uma pasta de Skill

```
.codex/skills/[skill-name]/
├── SKILL.md       # Markdown com frontmatter YAML opcional
├── scripts/        # opcional
├── references/     # opcional
└── assets/         # opcional
```

## Progressive disclosure

Igual ao Claude Code: o Codex carrega inicialmente só o `name`, a `description` e o caminho de cada Skill — o conteúdo completo do `SKILL.md` só é carregado quando a Skill é de fato invocada. Isso mantém o início de sessão rápido mesmo com muitas Skills instaladas.

## Como instalar Skills

- Descompactando um pacote direto na pasta de skills (`.codex/skills/` ou `~/.codex/skills`).
- Clonando um repositório do GitHub que contenha a Skill.
- Via comandos de **plugin/marketplace** da própria CLI, que suportam adicionar, listar, atualizar e fixar (`pin`) fontes de marketplace (aceitam shorthand `owner/repo`, URLs, e refs específicas via `--ref`).

Plugins são, no Codex CLI, a unidade instalável que empacota Skills (e outras extensões) para distribuição — análogo ao conceito de plugin no Claude Code, mas com seu próprio mecanismo de marketplace.

## Diferença em relação ao Claude Code

Conceitualmente as Skills são equivalentes (Markdown + progressive disclosure), mas o Codex CLI não documenta publicamente um registro/marketplace oficial centralizado com a mesma maturidade do ecossistema de plugins do Claude Code — a pesquisa oficial confirma os comandos de marketplace, mas não um workflow de publicação canônico único.

## Fontes

- https://developers.openai.com/codex/skills
- https://developers.openai.com/codex/cli/reference
