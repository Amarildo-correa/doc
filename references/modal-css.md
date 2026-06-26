Um modal tem duas camadas visuais: o 'overlay' (fundo escuro semitransparente que cobre a tela toda e bloqueia clique) e o 'dialog' (a caixa de conteúdo em si, centralizada). 'modal.js' aplica essas classes; este arquivo define como elas se comportam.

```css
.modal-overlay {
    position: fixed; /* sai do fluxo normal, sobrepõe TODO o conteúdo */
    inset: 0;
    background: rgba(0, 0, 0, 0.6); /* escurece o fundo, sinaliza foco no modal */
    display: flex;
    align-items: center;
    justify-content: center; /* centraliza .modal__dialog em qualquer
                                 tamanho de tela, sem cálculos manuais */
    z-index: 50; /* garante que fica acima de qualquer outro elemento da página */
}

.modal__dialog {
    background: var(--color-panel);
    border-radius: 0.5rem;
    padding: var(--spacing-lg);
    max-width: 90vw;
    max-height: 90vh; /* nunca maior que a viewport, mesmo com conteúdo extenso */
    overflow-y: auto; /* permite rolar o conteúdo interno em vez de
                          estourar a tela */
}
```

## 'inset: 0' no lugar de top/right/bottom/left

'inset: 0' é um atalho CSS que equivale a definir 'top: 0; right: 0; bottom: 0; left: 0;' de uma só vez — faz o overlay cobrir a viewport inteira independente do tamanho da tela, sem repetir quatro propriedades.

## Por que 'position: fixed' e não 'absolute'

'fixed' posiciona o elemento relativo à viewport, ignorando qualquer rolagem da página por trás — essencial para um overlay que precisa cobrir a tela inteira mesmo se o usuário tiver rolado para o meio de uma lista longa. 'absolute' seria relativo ao ancestral posicionado mais próximo, o que poderia deixar parte do overlay fora da área visível dependendo de onde o modal foi inserido no DOM.

## 'z-index: 50' — por que um valor específico e não '9999'

Usar valores arbitrariamente altos ('9999', '99999') é uma armadilha comum: cada novo componente que precisa ficar 'por cima de tudo' tende a usar um valor ainda maior, criando uma corrida sem fim. O projeto reserva uma faixa conhecida (ex.: 0–10 para elementos normais, 50 para overlays, 100 para notificações críticas) para que a ordem de camadas seja previsível e documentada.

## Performance e GPU

Como o overlay usa 'rgba()' com transparência sobre uma área grande da tela, ele força o navegador a compor essa camada — geralmente leve, mas relevante notar em dispositivos de baixo desempenho caso animações (ex.: fade-in) sejam adicionadas no futuro; nesses casos, animar 'opacity' (composto via GPU) é preferível a animar a própria cor de fundo.
