`AGENTS.md` é o **padrão aberto e universal** para instruções de agentes de IA num repositório — um "README para agentes" que carrega automaticamente no contexto de várias ferramentas diferentes, sem precisar ser reescrito para cada uma.

## Quem lê AGENTS.md nativamente (confirmado nesta pesquisa)

| Ferramenta | Lê AGENTS.md? | Como |
|---|---|---|
| **Codex CLI** | Sim, nativamente | Concatena com precedência global → projeto → subpasta mais próxima; `/init` gera um inicial |
| **Antigravity IDE** | Sim, nativamente | Prioridade `AGENTS.md` → `GEMINI.md` → defaults internos |
| **Claude Code** | Via import | `CLAUDE.md` (ou `.claude/CLAUDE.md`) importa com `@AGENTS.md` — não lê o arquivo diretamente, mas o padrão de uso recomendado faz a ponte |
| **VS Code / GitHub Copilot** | Sim, como alternativa | VS Code suporta `AGENTS.md` para instruções always-on, ao lado de `copilot-instructions.md` |
| **Cursor** | Não documentado oficialmente como fonte nativa | Usa Rules (`.cursor/rules/*.mdc`) como mecanismo primário — comunidade recomenda `AGENTS.md` por portabilidade, mas a leitura nativa pelo Cursor não foi confirmada na documentação oficial pesquisada |

## Conteúdo mínimo recomendado

```markdown
# Project
[Uma frase do que faz e para quem]

## Commands
- Install: ...
- Test: ...

## Boundaries
- Do not commit secrets or API keys
- No direct DB writes without explicit approval

## Project Structure
## Code Style
## Testing
```

A seção "Commands" (instalar, testar, lint) e "Boundaries" (o que o agente nunca deve fazer) aparecem como as mais recomendadas entre as fontes pesquisadas, junto com "Project Structure" e "Testing".

## Por que centralizar aqui

Manter um único arquivo de contexto evita que `CLAUDE.md`, `GEMINI.md` e instruções equivalentes divirjam entre si com o tempo — cada agente lê sua própria porta de entrada (ou o próprio `AGENTS.md`), mas todos chegam, na medida do possível, ao mesmo conteúdo central.

## Limite prático de tamanho

No Codex CLI, a cadeia concatenada de arquivos `AGENTS.md` (global + projeto + subpastas) está sujeita a um cap de contexto de ~32 KiB. Não há confirmação de um limite equivalente documentado para os outros harnesses, mas a prática recomendada universal é manter `AGENTS.md` focado em regras duráveis, não em documentação extensa (isso vai em `.specs/`/`README`/docs do projeto).

## AGENTS.override.md (Codex CLI)

No Codex CLI, um arquivo `AGENTS.override.md` em qualquer nível tem precedência sobre o `AGENTS.md` correspondente naquele nível — útil para sobrescrever regras herdadas sem editar o arquivo principal.

## Fontes

- https://developers.openai.com/codex/cli
- https://antigravity.google/docs/rules-workflows
- https://antigravity.md
- https://code.visualstudio.com/docs/copilot/customization/custom-instructions
- https://code.claude.com/docs/en/memory
