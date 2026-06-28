`promptdown-ui-v3/.specs/features/prompt-search-filter/spec.md` é um exemplo concreto de spec de feature, escrito seguindo o template **Specify** do fluxo de desenvolvimento dirigido por agentes (`.specs/features/[feature]/spec.md` — ver `specs-features.md`).

## Por que esta feature, e por que é "Medium"

`prompt-list-view.js` hoje lista todos os prompts sem nenhum mecanismo de busca; `prompt-form.js` já tem um campo `tags`, mas nada na UI consome essas tags para filtrar. É uma lacuna real e pequena: reaproveita o `store.js` (Proxy + pub/sub) já existente, sem novo padrão arquitetural e sem mudança de backend (filtragem client-side sobre o array já carregado via `api.js`) — por isso a fase de **Design** é pulada (sem decisão arquitetural nova) e **Tasks** fica implícita (poucos passos óbvios), conforme a regra de auto-sizing do skill `tlc-spec-driven`.

---

# Busca e Filtro de Prompts — Specification

## Problem Statement

A lista de prompts (`prompt-list-view.js`) cresce sem nenhuma forma de busca — encontrar um prompt específico exige rolar a página inteira. Cada prompt já tem `tags`, mas elas não são usadas para nada além de exibição no card. Isso piora conforme o número de prompts aumenta.

## Goals

- [ ] Usuário encontra um prompt por título em menos de 3 segundos, sem rolar a lista inteira.
- [ ] Usuário filtra a lista clicando numa tag já exibida em algum card, sem digitar nada.

## Out of Scope

| Feature | Motivo |
|---|---|
| Busca full-text no campo `body` do prompt | `body` pode ser longo; busca textual profunda é uma feature de busca avançada, não o problema imediato (encontrar pelo título/tag já resolve o caso comum) |
| Busca no backend (endpoint dedicado) | A lista de prompts já é carregada inteira via `GET /api/prompts`; filtrar no array em memória (client-side) é suficiente nesta escala e evita round-trip extra |
| Múltiplas tags combinadas (AND/OR) | Adiciona complexidade de UI (chips múltiplos) sem evidência de necessidade ainda — fica para uma iteração futura se o filtro por tag única não for suficiente |

---

## User Stories

### P1: Buscar por título ⭐ MVP

**User Story**: Como usuário da lista de prompts, quero digitar parte do título e ver a lista filtrar em tempo real, para encontrar um prompt específico sem rolar a página.

**Why P1**: É o caso de uso mais comum e a queixa direta do Problem Statement — sem isso, a feature não resolve nada.

**Acceptance Criteria**:

1. WHEN o usuário digita no campo de busca THEN o sistema SHALL filtrar a lista exibida para prompts cujo `title` contenha o texto digitado (case-insensitive, sem acento-sensitividade exigida).
2. WHEN o campo de busca está vazio THEN o sistema SHALL exibir todos os prompts (comportamento atual, sem filtro).
3. WHEN nenhum prompt corresponde ao texto digitado THEN o sistema SHALL exibir um estado vazio amigável (“Nenhum prompt encontrado para ‘X’”), reaproveitando o padrão visual já usado para listas vazias.

**Independent Test**: Abrir a lista com ao menos 2 prompts de títulos diferentes, digitar um trecho exclusivo de um deles, confirmar que só ele aparece; limpar o campo e confirmar que ambos voltam.

---

### P2: Filtrar por tag (clique no chip)

**User Story**: Como usuário da lista de prompts, quero clicar numa tag exibida em um card e ver só os prompts que têm aquela tag, para explorar prompts relacionados sem digitar nada.

**Why P2**: Importante para aproveitar dados já existentes (`tags`), mas não bloqueia o caso de uso principal (busca por título) — pode ser entregue depois do P1 sem ele ficar incompleto.

**Acceptance Criteria**:

1. WHEN o usuário clica numa tag dentro de um card THEN o sistema SHALL filtrar a lista para prompts cujo array `tags` contenha exatamente aquela tag.
2. WHEN um filtro de tag está ativo THEN o sistema SHALL exibir visualmente qual tag está selecionada (ex.: chip destacado) e um controle para limpá-lo.
3. WHEN um filtro de tag está ativo E o usuário também digita no campo de busca THEN o sistema SHALL aplicar os dois filtros em conjunto (título E tag, AND implícito entre os dois mecanismos).

**Independent Test**: Com prompts tendo tags diferentes, clicar numa tag visível num card e confirmar que só prompts com aquela tag permanecem; clicar em “limpar filtro” e confirmar que a lista volta ao normal.

---

### P3: Filtro refletido na URL

**User Story**: Como usuário, quero que o texto de busca e a tag selecionada fiquem na URL (query string), para poder compartilhar ou recarregar a página sem perder o filtro.

**Why P3**: Melhora de conveniência, não bloqueia o uso da busca/filtro em si — pode ser adiada sem prejuízo funcional.

**Acceptance Criteria**:

1. WHEN o usuário aplica busca ou filtro de tag THEN o sistema SHALL refletir o estado atual na query string da URL (via `router.js`, History API — `pushState`, nunca hash routing, conforme convenção do projeto).
2. WHEN a página é carregada com query string de busca/tag já presente THEN o sistema SHALL aplicar esse filtro automaticamente ao montar a view.

**Independent Test**: Aplicar um filtro, copiar a URL, abrir em nova aba e confirmar que o mesmo filtro já vem aplicado.

---

## Edge Cases

- WHEN a lista de prompts está vazia (nenhum prompt cadastrado) THEN o sistema SHALL manter o estado vazio atual da view, sem exibir o campo de busca como se houvesse algo para filtrar (ou exibi-lo desabilitado — decisão de UI, não bloqueia o spec).
- WHEN o usuário digita rapidamente (cada tecla) THEN o sistema SHALL aplicar debounce (~200-300ms) antes de refiltrar, para não recalcular a cada tecla em listas grandes.
- WHEN uma tag contém caracteres especiais ou espaços THEN o sistema SHALL tratá-la como string exata (sem regex), evitando falsos positivos por substring parcial entre tags diferentes (ex.: `ia` não deve casar com `ia-generativa` no filtro por clique, que é exato — diferente da busca por título, que é substring).
- WHEN a API (`api.js`) falha ao carregar a lista original THEN o sistema SHALL manter o tratamento de erro já existente da view (toast de erro) — a busca/filtro não introduz um novo caminho de erro, opera só sobre dados já carregados com sucesso.

---

## Requirement Traceability

| Requirement ID | Story | Phase | Status |
|---|---|---|---|
| SEARCH-01 | P1: Buscar por título | Implementing | Pending |
| SEARCH-02 | P1: Buscar por título (estado vazio) | Implementing | Pending |
| SEARCH-03 | P2: Filtrar por tag | Implementing | Pending |
| SEARCH-04 | P2: Combinar busca + tag | Implementing | Pending |
| SEARCH-05 | P3: Refletir filtro na URL | Implementing | Pending |

**Coverage:** 5 total, 0 mapeados a tasks formais (feature Medium — tasks ficam implícitas no Execute), 5 não mapeados ⚠️ (esperado neste escopo).

---

## Success Criteria

- [ ] Usuário encontra um prompt específico digitando parte do título, sem rolar a lista, em qualquer tamanho de lista.
- [ ] Clicar numa tag filtra a lista corretamente, sem nenhum erro de console para tags com caracteres especiais.
- [ ] Recarregar a página com filtro na URL preserva o estado do filtro (P3).

## Fontes

- Template de spec: skill `tlc-spec-driven`, `references/specify.md`
- Componentes reaproveitados: `prompt-list-view-js.md`, `prompt-form-js.md`, `store-js.md`, `router-js.md`
