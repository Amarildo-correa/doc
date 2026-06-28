Diferente do modal (que bloqueia a interação até ser fechado), o toast é não-bloqueante: o usuário continua usando a aplicação normalmente enquanto a mensagem aparece e desaparece num canto da tela.

```js
const container = document.querySelector("#toast-container");

export function showToast(message, { duration = 3000 } = {}) {
    const el = document.createElement("div");
    el.className = "toast";
    el.textContent = message; // nunca innerHTML — message pode vir de erro de API
    container.appendChild(el);

    // Após "duration" ms, o próprio toast se remove — nenhum código
    // externo precisa "lembrar" de limpá-lo depois
    setTimeout(() => el.remove(), duration);
}
```

## Fila implícita via DOM

Não há um array de 'toasts pendentes' em memória: o próprio '#toast-container' (estilizado como flex-column em modal.css/toast.css) acumula os elementos na ordem em que chegam, e cada um se remove sozinho. O DOM é a fila.

## Caso de uso real: feedback de erro na lista de prompts

```js
import { showToast } from "../components/toast.js";

getPrompts().catch((err) => {
    showToast("Não foi possível carregar os prompts. Tente novamente.");
    console.error(err);
});
```

## Por que 'setTimeout' aqui é seguro, mas pode ser uma armadilha em outros contextos

Cada toast tem seu próprio timer independente, então múltiplos toasts simultâneos não interferem entre si. A armadilha clássica seria reutilizar UM único timer para 'o último toast' — nesse caso, abrir um segundo toast cancelaria/reagendaria o timer do primeiro incorretamente. Aqui, cada chamada de 'showToast' cria seu próprio elemento E seu próprio 'setTimeout', evitando esse acoplamento.

## Por que não há um botão de fechar manual

Um toast é, por definição, uma notificação de baixa prioridade que não exige ação do usuário — diferente do modal, que frequentemente bloqueia até uma decisão. Adicionar um botão de fechar é uma melhoria de UX legítima, mas o design atual prioriza simplicidade: a mensagem desaparece sozinha, sem exigir interação.

## Acessibilidade

Para que leitores de tela anunciem o toast automaticamente (sem o usuário precisar navegar até ele), o '#toast-container' deveria ter 'aria-live="polite"' e 'role="status"' — assim, qualquer texto inserido nele é lido em voz alta pela tecnologia assistiva no momento em que aparece, sem interromper o que o usuário estava fazendo.

## Escalabilidade: limite de toasts simultâneos

Se múltiplos erros disparam toasts em sequência rápida (ex.: falha de rede repetida), o container pode acumular mensagens indefinidamente. Uma extensão razoável seria limitar o número de toasts visíveis simultaneamente, removendo o mais antigo ao atingir um teto — não implementado aqui para manter o componente simples.
