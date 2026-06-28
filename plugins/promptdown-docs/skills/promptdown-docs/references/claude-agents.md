`.claude/agents/` contém os **subagentes** do Claude Code disponíveis neste projeto — assistentes de IA especializados que rodam em sua própria janela de contexto, com system prompt customizado, acesso a ferramentas específico e permissões independentes da conversa principal.

## Por que usar subagentes

- **Preservar contexto**: exploração e implementação ficam fora da conversa principal.
- **Impor restrições**: limitar quais ferramentas um subagente pode usar.
- **Reutilizar configurações**: subagentes de escopo user funcionam em todos os projetos.
- **Especializar comportamento**: system prompts focados para domínios específicos.
- **Controlar custo**: rotear tarefas para modelos mais rápidos/baratos como Haiku.

Claude usa a `description` de cada subagente para decidir quando delegar — escreva descrições claras e específicas.

## Subagentes nativos (built-in)

| Subagente         | Modelo                      | Ferramentas                      | Propósito                        |
| ----------------- | --------------------------- | -------------------------------- | -------------------------------- |
| `Explore`         | Haiku                       | Somente leitura (sem Write/Edit) | Busca e análise de código rápida |
| `Plan`            | Herda da conversa principal | Somente leitura                  | Pesquisa durante o plan mode     |
| `general-purpose` | Herda da conversa principal | Todas                            | Tarefas complexas multi-etapa    |

Subagentes não podem gerar outros subagentes (sem aninhamento recursivo).

## Escopos — onde armazenar subagentes

| Localização            | Escopo                        | Prioridade     | Como criar                       |
| ---------------------- | ----------------------------- | -------------- | -------------------------------- |
| `--agents` (flag CLI)  | Sessão atual                  | 1 (mais alta)  | JSON ao iniciar o Claude Code    |
| `.claude/agents/`      | Projeto atual                 | 2              | Interativo (`/agents`) ou manual |
| `~/.claude/agents/`    | Todos os seus projetos        | 3              | Interativo ou manual             |
| `agents/` de um plugin | Onde o plugin está habilitado | 4 (mais baixa) | Instalado com plugins            |

Quando há subagentes com o mesmo nome em escopos diferentes, o de prioridade mais alta vence.

## Estrutura de um arquivo de subagente

Markdown com frontmatter YAML — só `name` e `description` são obrigatórios:

```markdown
---
name: code-reviewer
description: Expert code review specialist. Proactively reviews code for quality, security, and maintainability. 
Use immediately after writing or modifying code.
tools: Read, Grep, Glob, Bash
model: inherit
---

You are a senior code reviewer ensuring high standards of code quality and security.

When invoked:

1. Run git diff to see recent changes
2. Focus on modified files
3. Begin review immediately

Review checklist:

- Code is clear and readable
- Proper error handling
- No exposed secrets or API keys
- Good test coverage

Provide feedback organized by priority: Critical / Warnings / Suggestions.
```

### Campos de frontmatter suportados

| Campo             | Obrigatório | Descrição                                                                                                 |
| ----------------- | ----------- | --------------------------------------------------------------------------------------------------------- |
| `name`            | Sim         | Identificador único, minúsculas e hífens                                                                  |
| `description`     | Sim         | Quando o Claude deve delegar para este subagente                                                          |
| `tools`           | Não         | Lista de ferramentas permitidas (herda todas se omitido)                                                  |
| `disallowedTools` | Não         | Ferramentas a negar, removidas da lista herdada/especificada                                              |
| `model`           | Não         | `sonnet`, `opus`, `haiku` ou `inherit` (padrão: `sonnet`)                                                 |
| `permissionMode`  | Não         | `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan`                                          |
| `skills`          | Não         | Skills a injetar no contexto do subagente no startup (subagentes não herdam skills da conversa principal) |
| `hooks`           | Não         | Hooks de ciclo de vida (`PreToolUse`, `PostToolUse`, `Stop`) escopados a este subagente                   |

## Foreground vs background

- **Foreground**: bloqueia a conversa principal até terminar; prompts de permissão e perguntas (`AskUserQuestion`) passam para você.
- **Background**: roda em paralelo enquanto você continua trabalhando; herda as permissões do pai e nega automaticamente o que não foi pré-aprovado. Ferramentas MCP não estão disponíveis em subagentes em background.

## Desabilitar subagentes específicos

```json
{
    "permissions": {
        "deny": ["Task(Explore)", "Task(my-custom-agent)"]
    }
}
```

## Padrões comuns

- **Isolar operações verbosas**: delegar testes, fetch de documentação ou logs para um subagente, mantendo só o resumo na conversa principal.
- **Pesquisa paralela**: investigações independentes em subagentes simultâneos (ex.: autenticação, banco de dados e API em paralelo).
- **Encadear subagentes**: um subagente encontra problemas, outro corrige — cada um retorna resultado antes do próximo ser chamado.

## Subagente vs conversa principal

Use a **conversa principal** quando a tarefa precisa de ida-e-volta frequente, várias fases compartilham contexto significativo, ou latência importa. Use **subagentes** quando a tarefa produz saída verbosa que não precisa entrar no contexto principal, você quer impor restrições de ferramentas, ou o trabalho é autocontido.

## Fontes

- https://code.claude.com/docs/en/sub-agents
