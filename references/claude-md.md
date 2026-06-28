`CLAUDE.md` é a memória de **projeto** do Claude Code — texto carregado automaticamente em toda sessão, usado para instruções compartilhadas com o time (arquitetura, convenções, workflows). No padrão multi-agente deste guia, seu conteúdo é deliberadamente mínimo:

```
@AGENTS.md
```

## Por que importar em vez de escrever

`AGENTS.md` é a source of truth universal entre agentes — `CLAUDE.md` apenas importa esse conteúdo via sintaxe `@filename`, evitando que o contexto do projeto precise ser escrito (e mantido) duas vezes.

## Os quatro tipos de memória do Claude Code

| Tipo | Localização | Propósito | Compartilhado com |
|---|---|---|---|
| **Enterprise policy** | `managed-settings.json`/CLAUDE.md em diretório de sistema | Instruções organizacionais geridas por TI | Todos os usuários da organização |
| **Project memory** | `./CLAUDE.md` ou `./.claude/CLAUDE.md` | Instruções compartilhadas com o time | Time, via controle de versão |
| **Project rules** | `./.claude/rules/*.md` | Instruções modulares por tópico | Time, via controle de versão |
| **User memory** | `~/.claude/CLAUDE.md` | Preferências pessoais para todos os projetos | Só você (todos os projetos) |
| **Project memory (local)** | `./CLAUDE.local.md` | Preferências pessoais deste projeto | Só você (este projeto) |

Todos os arquivos de memória são carregados automaticamente no início da sessão. Arquivos mais altos na hierarquia têm precedência e são carregados primeiro. `CLAUDE.local.md` é automaticamente adicionado ao `.gitignore`.

## Sintaxe de import (`@path`)

```
See @README for project overview and @package.json for available npm commands.

# Additional Instructions
- git workflow @docs/git-instructions.md
```

Paths relativos e absolutos são permitidos — importar arquivos do home do usuário é uma forma conveniente de membros do time terem instruções individuais não commitadas no repo:

```
# Individual Preferences
- @~/.claude/my-project-instructions.md
```

Imports não são avaliados dentro de code spans/blocks (para evitar colisões com pacotes como `@anthropic-ai/claude-code`). Imports podem encadear recursivamente, até 5 níveis de profundidade. Use `/memory` para ver quais arquivos foram carregados.

## Como o Claude Code busca memórias

O Claude Code lê memórias recursivamente: a partir do diretório de trabalho atual, sobe na árvore (sem incluir a raiz `/`) lendo qualquer `CLAUDE.md`/`CLAUDE.local.md` encontrado — útil em monorepos com memórias em `foo/CLAUDE.md` e `foo/bar/CLAUDE.md`. Arquivos `CLAUDE.md` aninhados em subpastas abaixo do diretório de trabalho são descobertos sob demanda (só carregados quando o Claude lê arquivos naquela subárvore), não no início.

## Memória modular com `.claude/rules/`

Para projetos maiores, em vez de um `CLAUDE.md` único e grande, organize instruções em `.claude/rules/*.md` — todos carregados automaticamente com a mesma prioridade do `CLAUDE.md` de projeto:

```
your-project/
├── .claude/
│   ├── CLAUDE.md
│   └── rules/
│       ├── code-style.md
│       ├── testing.md
│       └── security.md
```

Rules podem ser condicionais por caminho de arquivo via frontmatter `paths` (suporta glob, incluindo brace expansion como `**/*.{ts,tsx}`):

```yaml
---
paths:
  - "src/api/**/*.ts"
---

# API Development Rules
- All API endpoints must include input validation
```

Rules sem `paths` se aplicam incondicionalmente. `.claude/rules/` suporta subpastas e symlinks (útil para compartilhar rules entre vários projetos). Existe também um equivalente de escopo user: `~/.claude/rules/`, carregado antes (prioridade menor) das rules de projeto.

## Boas práticas

- Seja específico: "Use indentação de 2 espaços" é melhor que "Formate o código corretamente".
- Use estrutura: bullet points agrupados sob headings descritivos.
- Revise periodicamente — memórias desatualizadas levam o Claude a agir com contexto errado.
- Use `/init` para gerar um `CLAUDE.md` inicial a partir da base de código atual.

## Fontes

- https://code.claude.com/docs/en/memory
