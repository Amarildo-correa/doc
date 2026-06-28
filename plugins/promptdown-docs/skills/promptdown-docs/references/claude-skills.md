`.claude/skills/` contém as **Agent Skills** do Claude Code — arquivos Markdown que ensinam o Claude a fazer algo específico (revisar PRs com o padrão do time, gerar mensagens de commit num formato preferido, consultar o schema de um banco de dados). Quando um pedido do usuário casa com o propósito de uma Skill, o Claude a aplica automaticamente.

## Como as Skills funcionam

Skills são **model-invoked**: o Claude decide quais usar com base no seu pedido — você não precisa chamá-las explicitamente.

1. **Discovery**: no início da sessão, o Claude carrega só o `name` e a `description` de cada Skill disponível — mantém o startup rápido.
2. **Activation**: quando seu pedido casa com a descrição, o Claude pede para usar a Skill (com confirmação) antes do `SKILL.md` completo ser carregado.
3. **Execution**: o Claude segue as instruções da Skill, carregando arquivos referenciados ou rodando scripts conforme necessário.

## Onde as Skills vivem (escopos)

| Localização | Path | Aplica-se a |
|---|---|---|
| Enterprise | managed settings | Todos os usuários da organização |
| Personal | `~/.claude/skills/` | Você, em todos os projetos |
| Project | `.claude/skills/` | Qualquer pessoa trabalhando neste repositório |
| Plugin | empacotado com plugins | Qualquer pessoa com o plugin instalado |

Em caso de nomes duplicados, a linha de prioridade mais alta vence: managed > personal > project > plugin.

## Escrevendo o SKILL.md

```yaml
---
name: your-skill-name
description: Brief description of what this Skill does and when to use it
---

# Your Skill Name

## Instructions
Provide clear, step-by-step guidance for Claude.

## Examples
Show concrete examples of using this Skill.
```

### Campos de frontmatter disponíveis

| Campo | Obrigatório | Descrição |
|---|---|---|
| `name` | Sim | Minúsculas, números e hífens; máx. 64 caracteres; deve combinar com o nome da pasta |
| `description` | Sim | O que a Skill faz e quando usá-la (máx. 1024 caracteres) — é o que o Claude lê para decidir ativar |
| `allowed-tools` | Não | Ferramentas que o Claude pode usar sem pedir permissão enquanto a Skill está ativa |
| `model` | Não | Modelo a usar quando a Skill está ativa |
| `context: fork` | Não | Roda a Skill num subagente isolado, com histórico de conversa próprio |
| `agent` | Não | Tipo de agente a usar com `context: fork` (padrão: `general-purpose`) |
| `hooks` | Não | Hooks escopados ao ciclo de vida da Skill (`PreToolUse`, `PostToolUse`, `Stop`) |
| `user-invocable` | Não | Controla se aparece no menu de slash command (padrão: `true`) |

A `description` é o campo mais importante — é o que o Claude usa para decidir quando aplicar a Skill. Uma boa descrição responde duas perguntas: o que a Skill faz, e quando o Claude deve usá-la (incluindo palavras-chave que o usuário diria naturalmente).

## Progressive disclosure (arquivos de suporte)

Skills compartilham a janela de contexto com o histórico da conversa, outras Skills e seu pedido. Para manter o contexto focado, coloque informação essencial no `SKILL.md` e material de referência detalhado em arquivos separados, carregados só quando necessário:

```
my-skill/
├── SKILL.md (obrigatório — visão geral e navegação)
├── reference.md (docs de API detalhadas — carregado quando necessário)
├── examples.md (exemplos de uso — carregado quando necessário)
└── scripts/
    └── helper.py (script utilitário — executado, não carregado)
```

Mantenha o `SKILL.md` abaixo de 500 linhas; divida o que passar disso em arquivos de referência. Mantenha as referências em um nível só de profundidade (o `SKILL.md` linka direto para o arquivo de referência) — referências encadeadas (A → B → C) podem fazer o Claude ler só parcialmente.

**Scripts bundled para execução com custo zero de contexto**: scripts dentro da pasta da Skill podem ser executados sem carregar seu conteúdo — só a saída consome tokens. Útil para lógica de validação complexa, processamento de dados, ou operações que precisam de consistência.

## Restringindo ferramentas com `allowed-tools`

```yaml
---
name: reading-files-safely
description: Read files without making changes. Use when you need read-only file access.
allowed-tools: Read, Grep, Glob
---
```

Quando a Skill está ativa, o Claude só pode usar as ferramentas listadas sem pedir permissão. Útil para Skills somente leitura, com escopo limitado, ou em workflows sensíveis à segurança.

## Controlando visibilidade

Skills podem ser invocadas de três formas: manual (`/skill-name`), programática (via ferramenta `Skill`), ou descoberta automática (o Claude lê a descrição e carrega quando relevante).

| Configuração | Menu de slash | Ferramenta `Skill` | Auto-descoberta |
|---|---|---|---|
| `user-invocable: true` (padrão) | Visível | Permitido | Sim |
| `user-invocable: false` | Oculto | Permitido | Sim |
| `disable-model-invocation: true` | Visível | Bloqueado | Sim |

## Skills e subagentes

Subagentes **não** herdam Skills automaticamente da conversa principal. Para dar acesso, liste-as no campo `skills` do subagente:

```yaml
# .claude/agents/code-reviewer.md
---
name: code-reviewer
description: Review code for quality and best practices
skills: pr-review, security-check
---
```

O conteúdo completo de cada Skill listada é injetado no contexto do subagente no startup — não apenas disponibilizado para invocação. Subagentes built-in (Explore, Plan, general-purpose) não têm acesso a Skills custom.

## Distribuindo Skills

- **Project Skills**: commitar `.claude/skills/` no controle de versão.
- **Plugins**: criar um diretório `skills/` no plugin, com pastas de Skill contendo `SKILL.md`, distribuído via marketplace de plugins.
- **Managed**: administradores podem implantar Skills em toda a organização via managed settings.

## Skills vs slash commands vs CLAUDE.md vs subagentes vs hooks vs MCP

| Use isto | Quando você quer | Quando roda |
|---|---|---|
| **Skills** | Dar conhecimento especializado ao Claude | O Claude escolhe quando é relevante |
| **Slash commands** | Criar prompts reutilizáveis | Você digita `/comando` explicitamente |
| **CLAUDE.md** | Definir instruções de projeto sempre ativas | Carregado em toda conversa |
| **Subagentes** | Delegar tarefas a um contexto separado | O Claude delega, ou você invoca explicitamente |
| **Hooks** | Rodar scripts em eventos | Dispara em eventos específicos de ferramenta |
| **MCP servers** | Conectar o Claude a ferramentas/dados externos | O Claude chama ferramentas MCP conforme necessário |

**Skills vs subagentes**: Skills adicionam conhecimento à conversa atual; subagentes rodam num contexto separado com ferramentas próprias.
**Skills vs MCP**: Skills dizem *como* usar ferramentas; MCP *fornece* as ferramentas.

## Troubleshooting

- **Skill não dispara**: a `description` provavelmente é vaga demais — inclua ações específicas e palavras-chave que o usuário diria.
- **Skill não carrega**: confira o caminho exato (`SKILL.md`, case-sensitive) e a sintaxe YAML do frontmatter (deve começar com `---` na linha 1, sem linha vazia antes).
- **Conflito entre Skills similares**: torne as descrições mais distintas, com termos de gatilho específicos.
- Use `claude --debug` para ver erros de carregamento de Skills.

## Fontes

- https://code.claude.com/docs/en/skills
- https://code.claude.com/docs/en/slash-commands (seção "Skills vs slash commands")
