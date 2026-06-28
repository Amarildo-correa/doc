Usar 'var(--color-bg)' em vez de valores fixos significa que trocar o tema é uma mudança em um único arquivo, não uma busca-e-substitui em dezenas de arquivos.

```css
:root {
    /* Cores: nomeadas pela função (bg, accent) e não pelo valor visual
       (azul, cinza) — assim o nome continua fazendo sentido mesmo que
       a cor exata mude num futuro re-design */
    --color-bg: #181a18;
    --color-accent: #b09eff;

    /* Espaçamento numa escala consistente, nunca valores soltos como
       13px ou 22px espalhados pelo CSS */
    --space-2: 0.5rem;
    --space-4: 1rem;

    /* Tipografia — rem obrigatório; px proibido exceto em border: 1px */
    --text-base: 1rem;
}
```

## Design tokens — o conceito por trás do nome do arquivo

'Design token' é o termo usado por sistemas de design (Material, Carbon, etc.) para um valor de estilo nomeado e centralizado, em vez de um valor 'mágico' espalhado pelo código. A vantagem prática: mudar '--color-accent' uma vez aqui propaga instantaneamente para botões, links e bordas em destaque por toda a aplicação, sem tocar em nenhum outro arquivo CSS.

## Por que 'rem' e não 'px' (convenção do projeto)

'rem' é relativo ao tamanho de fonte raiz do documento ('html { font-size }'), não a um valor absoluto de pixels. Se um usuário aumenta o tamanho de fonte padrão do navegador (uma necessidade comum de acessibilidade visual), elementos dimensionados em 'rem' escalam proporcionalmente; em 'px', eles permaneceriam do mesmo tamanho fixo, ignorando essa preferência de acessibilidade do usuário.

## Caso de uso real: dark mode/light mode

```css
/* O segundo tema sobrescreve os mesmos nomes de variável dentro de uma
   media query — button.css/card.css/modal.css não precisam saber que
   o tema mudou, e não há JavaScript envolvido na troca */
@media (prefers-color-scheme: light) {
    :root {
        --color-bg: #f4f3f7;
        --color-accent: #6b4fe8;
    }
}
```

## Armadilha comum

Definir uma variável aqui e, ainda assim, hardcodar o valor equivalente em outro arquivo CSS 'só para ajustar um detalhe' quebra a centralização — qualquer ajuste futuro do token não vai refletir nesse lugar esquecido, criando inconsistência visual silenciosa.
