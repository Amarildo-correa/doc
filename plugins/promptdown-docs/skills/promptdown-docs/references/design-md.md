SSoT (Single Source of Truth) de todas as decisões visuais da aplicação. Nenhum arquivo CSS pode introduzir cor, medida, componente ou padrão de layout que não esteja definido aqui primeiro.

## Por que vive em `.specs/` e não em `public/css/`

Os arquivos em `public/css/` são entregues ao navegador — pertencem ao runtime da aplicação. `DESIGN.md` é um contrato de implementação consumido por agentes de IA (Claude Code) e desenvolvedores antes de escrever qualquer CSS. Colocá-lo em `.specs/` junto com as outras especificações de features deixa claro que é um artefato de processo, não de produção.

## Mapa seção → arquivo CSS

| Seção do DESIGN.md | Arquivo(s) que implementa |
|---|---|
| § 0 — Lei Fundamental (`.g-cell`) | `public/css/layout.css` |
| § 1 — Dependências (fontes, ícones) | `public/index.html` (`<link>` no `<head>`) |
| § 2 — Tokens | `public/css/tokens.css` |
| § 3 — Grid (`.g-row`, `.g-cell`, padrões) | `public/css/layout.css` |
| § 4 — Responsivo | `public/css/layout.css` |
| § 5 — Componentes | `public/css/components/*.css` |
| § 6 — Checklist | nenhum arquivo — é processo de revisão |
| § 7 — Fluxo SSoT | nenhum arquivo — é regra de processo |
| § 8 — Acessibilidade | distribuído: cada componente em `components/*.css` |

## Relação com os arquivos `doc/references/*.md`

Os arquivos de referência educativa explicam o **porquê** das decisões arquiteturais (por que `rem` em vez de `px`, por que separar layout de componente). `DESIGN.md` contém o **quê** canônico (os valores exatos, as regras `MUST`/`MUST NOT`, os exemplos de HTML). As duas camadas não se repetem:

- `doc/references/tokens-css.md` → explica o conceito de design token e a arquitetura de CSS variables; aponta para `DESIGN.md § 2` para os valores canônicos.
- `doc/references/layout-css.md` → explica a separação layout × componente e `grid` × `flexbox`; aponta para `DESIGN.md § 3` para o sistema de células.
- `doc/references/button-css.md` → explica convenção BEM-like e `:focus-visible`; aponta para `DESIGN.md § 5` para a spec visual canônica.
- `doc/references/modal-css.md` → explica `position: fixed` e z-index; aponta para `DESIGN.md § 5` e `§ 8.6` para a spec visual e acessibilidade.
- `doc/references/input-css.md` → explica estados de formulário e responsabilidade de validação; aponta para `DESIGN.md § 5` e `§ 8.3` para a spec canônica.

## Fluxo obrigatório ao adicionar qualquer elemento visual

```
1. Valor novo (cor, medida, tempo)?
   → Adicionar token em DESIGN.md § 2 primeiro.
   → Só então declarar em tokens.css via var().

2. Componente novo?
   → Documentar estrutura, CSS e estados em DESIGN.md § 5.
   → Só então criar o arquivo em public/css/components/.
   → Registrar no AGENTS.md.

3. Padrão de layout novo?
   → Verificar se é caso do grid (§ 3) ou genuinamente novo.
   → Se novo: descrever em DESIGN.md § 3 antes de usar.

4. Antes do PR: rodar o Checklist de DESIGN.md § 6 inteiro.
```

## Regras absolutas (resumo do § 6 do DESIGN.md)

Estas violações são detectáveis por revisão de código e bloqueiam merge:

- `border-radius` em qualquer valor — proibido em todo o projeto.
- `box-shadow` ou `text-shadow` — proibido.
- Gradiente (`linear-gradient`, `radial-gradient`) — proibido.
- Cor hardcoded fora de `tokens.css` (`#`, `rgb()`, `hsl()`) — proibido.
- `px` em fonte ou espaçamento (exceto `border: 1px`) — proibido.
- `background-color` em elemento sobreposto (modal, toast) — proibido; herda `--color-bg`.
- `background` em `<input>` ou `<textarea>` — proibido; transparente por padrão do grid.
- Emoji na interface — proibido; usar Tabler Icons.
- Novo componente usado sem estar documentado em `DESIGN.md § 5` — proibido.

## Por que este arquivo não lista os tokens nem os componentes

Porque isso seria duplicação. `DESIGN.md` já contém os valores, as tabelas e os exemplos de HTML canônicos. Este arquivo (`design-md.md`) explica apenas o papel e a mecânica de `DESIGN.md` dentro da arquitetura do projeto — é a "aula sobre a aula".

## Alternativa e trade-off

Uma abordagem alternativa seria distribuir as regras visuais dentro de cada arquivo CSS como comentários. O custo: agentes de IA e desenvolvedores teriam que ler N arquivos para entender as restrições globais antes de implementar qualquer tela nova. Um único `DESIGN.md` centralizado (como um `CLAUDE.md` para UI) resolve isso: um arquivo, todas as regras, zero dúvida sobre qual é a fonte de verdade.
