Servem para ajustes rápidos que não justificam um arquivo CSS de componente próprio — usadas com moderação.

```css
.hidden {
    display: none; /* remove do fluxo E da árvore de acessibilidade */
}

.mt-md {
    margin-top: var(--spacing-md); /* usa token, nunca um valor solto */
}

.sr-only {
    /* visualmente invisível, mas ainda lido por leitores de tela —
       diferente de display:none, que oculta de todos, inclusive a11y */
    position: absolute;
    width: 1px;
    height: 1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
}
```

## O que é uma classe utilitária e por que usar com moderação

Classes utilitárias resolvem um ajuste visual pontual (esconder um elemento, adicionar uma margem) sem exigir uma nova regra CSS dedicada. O risco de usá-las em excesso é o HTML acumular muitas classes pequenas ('class="mt-md hidden sr-only"'), tornando a leitura do marcador mais difícil — por isso o projeto reserva utilitários para casos realmente pontuais, preferindo classes semânticas (como '.card', '.btn--primary') para tudo que tem significado estrutural.

## '.hidden' vs '.sr-only' — uma distinção de acessibilidade importante

'display: none' remove o elemento tanto visualmente quanto da árvore de acessibilidade — leitores de tela o ignoram completamente. '.sr-only' (técnica padrão da indústria) esconde visualmente mas mantém o conteúdo disponível para tecnologia assistiva, útil por exemplo para dar um rótulo textual a um ícone que, visualmente, já se explica por contexto, mas não para quem não pode ver o ícone.

## Caso de uso real

'.hidden' é útil para alternar a visibilidade de um elemento via JavaScript sem reescrever estilos inline (ex.: 'el.classList.toggle("hidden")' em vez de 'el.style.display = ...'), mantendo a lógica de exibição declarada no CSS, não espalhada em manipulações diretas de estilo no JS.

## Armadilha: utilitário virando regra de negócio

Se uma classe utilitária passa a ser usada para expressar uma regra de layout estrutural recorrente (ex.: '.mt-md' aplicada em toda a aplicação para espaçar seções), é sinal de que essa regra deveria migrar para 'layout.css' como parte do design do componente, em vez de continuar sendo remendada via utilitário em dezenas de lugares.
