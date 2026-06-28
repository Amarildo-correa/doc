`.agents/skills/` contém as **Agent Skills** do Antigravity IDE — pastas com um `SKILL.md` de instruções que o agente segue para tarefas específicas. Quando você inicia uma conversa, o agente vê uma lista das skills disponíveis (nome + descrição); se uma parecer relevante, ele lê as instruções completas e as segue.

## Onde Skills vivem

| Localização | Escopo |
|---|---|
| `<workspace-root>/.agents/skills/<skill-folder>/` | Específico do workspace |
| `~/.gemini/config/skills/<skill-folder>/` | Global (IDE — todos os workspaces) |
| `~/.gemini/antigravity-cli/skills/` | Global (CLI `agy` — todos os diretórios) |

Para a CLI `agy` especificamente: qualquer skill Markdown nessa pasta global é importada automaticamente como slash command global, disponível em qualquer diretório onde você rode `agy`.

## Frontmatter

```yaml
---
name: my-skill
description: Helps with a specific task. Use when you need to do X or Y.
---

# My Skill

Detailed instructions for the agent go here.

## When to use this skill
- Use this when...

## How to use it
Step-by-step guidance, conventions, and patterns the agent should follow.
```

| Campo | Obrigatório | Descrição |
|---|---|---|
| `name` | Não | Identificador único (minúsculas, hífens); usa o nome da pasta se omitido |
| `description` | **Sim** | O que a skill faz e quando usá-la — é o que o agente vê para decidir se aplica a skill |

Dica oficial: escreva a `description` em terceira pessoa, com palavras-chave que ajudem o agente a reconhecer quando a skill é relevante (ex.: "Generates unit tests for Python code using pytest conventions").

## Estrutura de pasta

```
my-skill/
├── SKILL.md       # obrigatório
├── scripts/       # opcional — Python, Bash, Node
├── references/    # opcional — documentação ou templates
└── assets/        # opcional — imagens, logos
```

## Skills vs MCP vs Rules vs Workflows

Segundo a documentação oficial: **MCP** funciona como as "mãos" do agente — fornece conexões persistentes a sistemas externos (GitHub, PostgreSQL). **Skills** são o "cérebro" que direciona essas mãos — encapsulam metodologia/expertise sobre quando e como usar essas conexões. **Rules** dão contexto persistente reutilizável no nível do prompt; **Workflows** dão uma sequência estruturada de passos no nível da trajetória completa da tarefa.

## Fontes

- https://antigravity.google/docs/skills
- https://antigravity.google/docs/cli-plugins
- https://codelabs.developers.google.com/getting-started-with-antigravity-skills
