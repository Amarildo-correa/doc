Um modal tem duas camadas visuais: o 'overlay' (fundo que cobre a tela toda e bloqueia clique) e o 'dialog' (a caixa de conteúdo em si, centralizada). 'modal.js' aplica essas classes; este arquivo define como elas se comportam.

```css
.modal-overlay {
    position: fixed; /* sai do fluxo normal, sobrepõe TODO o conteúdo */
    inset: 0;
    /* cor sólida do token — nunca rgba() ou gradiente */
    background: var(--color-bg);
    display: flex;
    align-items: center;
    justify-content: center; /* centraliza .modal__dialog em qualquer
                                 tamanho de tela, sem cálculos manuais */
    z-index: 50; /* faixa reservada para overlays — ver comentário abaixo */
}

.modal__dialog {
    /* cantos sempre retos — nunca border-radius */
    /* sem background-color próprio — herda --color-bg do body */
    border: 1px solid var(--color-border);
    padding: var(--space-6);
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

O overlay usa uma cor sólida do token (`--color-bg`), sem transparência — a separação entre overlay e conteúdo de trás vem da borda de 1px do dialog, não de um fundo semitransparente. Caso animações (ex.: fade-in) sejam adicionadas no futuro, animar 'opacity' (composto via GPU) é preferível a animar a própria cor de fundo, que força o navegador a recompor uma área grande da tela a cada frame.
