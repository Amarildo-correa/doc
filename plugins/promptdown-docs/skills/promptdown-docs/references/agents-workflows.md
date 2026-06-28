`.agents/workflows/` contém os **Workflows** do Antigravity IDE — sequências de passos definidos para guiar o agente por um conjunto repetitivo de tarefas (deploy de um serviço, responder a comentários de PR). Workflows são salvos como Markdown e invocados via slash command no formato `/workflow-name`.

## Rules vs Workflows

Confirmado pela documentação oficial: **Rules** fornecem ao modelo orientação persistente e reutilizável **no nível do prompt**; **Workflows** fornecem uma sequência estruturada de passos/prompts **no nível da trajetória** — guiando o modelo por uma série de tarefas interconectadas.

## Limite de tamanho

Cada arquivo de workflow é limitado a **12.000 caracteres**.

## Como criar um Workflow

Um workflow tem título, descrição e uma série de passos com instruções específicas para o agente seguir. Exemplo (do codelab oficial — define o comando `/startcycle`, salvo em `.agents/workflows/startcycle.md`):

```markdown
---
name: startcycle
description: Orchestrates the full PM → Engineer development cycle
---

# Start Development Cycle

## Steps
1. Act as the PM agent and write a spec for the requested feature
2. Act as the Engineer agent and implement the spec
3. Loop back to the PM for review; rework if needed
```

Ao salvar esse arquivo em `.agents/workflows/`, você registra um novo comando diretamente na interface de chat do Antigravity — em vez de repetir manualmente "Act as the PM... agora act as the Engineer...", `/startcycle` encadeia as personas automaticamente.

## Workflows gerados pelo próprio agente

Você também pode pedir ao agente para **gerar** um Workflow — funciona particularmente bem depois de trabalhar manualmente com ele numa sequência de passos, já que o agente pode usar o histórico da conversa para criar o Workflow.

## Fontes

- https://antigravity.google/docs/rules-workflows
- https://codelabs.developers.google.com/autonomous-ai-developer-pipelines-antigravity
