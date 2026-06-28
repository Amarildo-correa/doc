Recebe o ID do prompt a partir do router (que extrai esse valor da URL via pushState) e busca os dados correspondentes. É um bom exemplo de view com estado próprio de carregamento (loading/erro/sucesso), além do estado global da store.

```js
import { getPromptById } from "../api.js";

export function PromptDetailView(root, { id }) {
    // Estado de "loading" imediato — o usuário nunca vê uma tela em
    // branco enquanto a requisição de rede está pendente
    root.textContent = "Carregando…";

    // Flag de cancelamento lógico: não existe um "abort" real de fetch
    // aqui, apenas a decisão de ignorar o resultado se já não for relevante
    let cancelled = false;

    getPromptById(id).then((prompt) => {
        if (cancelled) return; // a view já foi destruída — não atualiza nada
        root.textContent = prompt.title;
    });

    return {
        destroy() {
            cancelled = true; // marca antes de qualquer resolução pendente
        },
    };
}
```

## A flag 'cancelled' — por que ela existe

Se o usuário navegar para outra tela antes da requisição terminar, a Promise ainda resolve — só que 'destroy()' já rodou. Sem a flag, a view tentaria escrever em um 'root' que já não está mais na tela, um bug clássico de 'condição de corrida' em SPAs.

## Caso de uso real: navegação rápida entre prompts

Imagine o usuário clicando em '/prompts/1', mudando de ideia e clicando em '/prompts/2' antes da primeira requisição terminar. Sem a flag, a resposta de '/prompts/1' poderia chegar DEPOIS da troca de rota e sobrescrever o título já correto de '/prompts/2' com o título errado do prompt 1 — um bug visualmente confuso e difícil de reproduzir manualmente, mas comum em produção sob conexões lentas e cliques rápidos.

## Por que não usar 'AbortController' em vez da flag

'AbortController' cancelaria a própria requisição de rede no nível do 'fetch', economizando banda — uma melhoria real possível em 'api.js' (passando um 'signal'). A flag 'cancelled' resolve apenas o sintoma visível (escrever em DOM obsoleto) com bem menos código, sendo uma escolha pragmática para este projeto enquanto o volume de chamadas simultâneas for baixo.

## Estado de erro ausente — uma lacuna deliberada do exemplo

Diferente de 'prompt-list-view.js', esta view não trata rejeição de 'getPromptById' com '.catch()' — se a Promise rejeitar, o erro vira um 'unhandledrejection' capturado globalmente em 'app.js', mas a tela ficaria parada em 'Carregando…' para sempre. Uma extensão recomendada seria adicionar tratamento de erro simétrico ao de 'prompt-list-view.js'.

## Relação com o router

O parâmetro '{ id }' chega exatamente como o router o extraiu da URL (ver 'router.js'). Esta view não sabe e não precisa saber como a URL foi parseada — apenas consome o contrato de parâmetros que o router lhe entrega, outro exemplo de desacoplamento por responsabilidade única.
