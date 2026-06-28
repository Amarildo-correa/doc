Única folha de estilos de entrada de dados do projeto — cobre `<input>`, `<textarea>`, `<label>` e os estados visuais de foco, erro e desabilitado. Complementa `button.css` para que formulários tenham aparência consistente com o restante do design system.

```css
/* Todos os valores numéricos via tokens — nunca hardcode */
.prompt-form {
    display: flex;
    flex-direction: column;
    gap: var(--space-4);
}

/* Label */
.prompt-form label {
    display: block;
    font-size: var(--text-sm);
    font-weight: var(--font-medium);
    color: var(--color-muted);
    margin-bottom: var(--space-1);
}

/* Campo base — compartilhado por input e textarea */
.prompt-form input[type="text"],
.prompt-form textarea {
    width: 100%;
    padding: var(--space-2) var(--space-3);
    border: 1px solid var(--color-border);
    /* cantos sempre retos — nunca border-radius */
    /* fundo transparente — a delimitação do campo vem do grid, não de um fundo próprio */
    background: transparent;
    font-size: var(--text-base);
    color: var(--color-text);
    transition: border-color var(--transition-fast);
}

/* Foco — outline, nunca box-shadow */
.prompt-form input[type="text"]:focus-visible,
.prompt-form textarea:focus-visible {
    outline: 2px solid var(--color-accent);
    outline-offset: -2px;
    border-color: var(--color-accent);
}

/* Erro — ativado via classe .is-invalid adicionada pelo caller */
.prompt-form input.is-invalid,
.prompt-form textarea.is-invalid {
    border-color: var(--color-error);
}

/* outline, nunca box-shadow, também no foco de campo inválido */
.prompt-form input.is-invalid:focus-visible,
.prompt-form textarea.is-invalid:focus-visible {
    outline: 2px solid var(--color-error);
    outline-offset: -2px;
}

/* Mensagem de erro inline abaixo do campo */
.prompt-form__error {
    font-size: var(--text-sm);
    color: var(--color-error);
    margin-top: var(--space-1);
}

/* Textarea — altura mínima e redimensionamento só vertical */
.prompt-form textarea {
    min-height: 12.5rem;
    resize: vertical;
}

/* Desabilitado — sem background-color própria, o campo já é transparente */
.prompt-form input:disabled,
.prompt-form textarea:disabled {
    opacity: 0.5;
    cursor: not-allowed;
}

/* Área de ações (botões) */
.prompt-form__actions {
    display: flex;
    justify-content: flex-end;
    gap: var(--space-2);
    margin-top: var(--space-2);
}
```

## Por que um arquivo separado e não regras em `base.css`

`base.css` define o reset e a tipografia raiz — estilos que se aplicam a qualquer elemento do documento. Regras de campos de formulário são contextuais (só fazem sentido dentro de `.prompt-form`) e têm estados próprios (`:focus`, `.is-invalid`, `:disabled`). Misturá-las em `base.css` violaria o princípio de responsabilidade única que separa cada camada de CSS do projeto.

## Relação com `tokens.css`

Cada valor numérico (cor, espaçamento, tipografia, borda) vem de uma variável CSS definida em `tokens.css`. Nenhum valor hardcoded. Isso garante que trocar o tema visual do projeto (ex.: dark mode) seja uma mudança apenas em `tokens.css`, sem tocar neste arquivo.

## Estado de erro — por que classe e não `:invalid`

A pseudo-classe CSS nativa `:invalid` dispara assim que o campo é renderizado vazio, antes do usuário interagir — o formulário apareceria com bordas vermelhas ao abrir. A convenção do projeto é aplicar `.is-invalid` via JavaScript apenas após a primeira tentativa de submit, comunicando erro somente quando faz sentido para o usuário.

## Tokens esperados de `tokens.css`

Este arquivo assume que os seguintes tokens existem. Se algum ainda não foi declarado, deve ser adicionado a `tokens.css` antes de usar `input.css`:

| Token | Uso |
|---|---|
| `--color-border` | Borda padrão dos campos |
| `--color-accent` | Borda e outline de foco |
| `--color-error` | Borda, outline e texto de erro |
| `--color-text` | Texto digitado pelo usuário |
| `--color-muted` | Texto dos labels |
| `--space-1..3` | Espaçamentos internos e gaps |
| `--text-sm`, `--text-base` | Tamanhos de fonte |
| `--font-medium` | Peso da fonte dos labels |
| `--transition-fast` | Duração da transição de borda no foco |

## Por que `:focus-visible` e não `:focus`

`:focus` aplica o contorno também quando o campo é clicado com o mouse, o que muitos designers consideram visualmente poluído. `:focus-visible` mostra o contorno só quando o navegador detecta navegação por teclado (Tab) — acessível para quem navega sem mouse, sem incomodar quem usa mouse.
