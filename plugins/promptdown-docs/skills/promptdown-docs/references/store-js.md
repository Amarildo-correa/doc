Um 'Proxy' intercepta leituras e escritas no estado, disparando notificações para quem está inscrito — diferente de um objeto comum, em que mudar uma propriedade não avisa ninguém.

```js
// Set, não Array: garante que a mesma função de callback nunca seja
// registrada duas vezes, e dá O(1) para adicionar/remover
const listeners = new Set();

export const store = new Proxy(
    { user: null, prompts: [] }, // estado inicial
    {
        set(target, key, value) {
            target[key] = value; // grava a mudança real no objeto
            listeners.forEach((fn) => fn(target)); // notifica todos os inscritos
            return true; // obrigatório: Proxy.set precisa retornar truthy
        },
    },
);

export function subscribe(fn) {
    listeners.add(fn);
    return () => listeners.delete(fn); // função de "unsubscribe" devolvida
}
```

## Por que Proxy em vez de um objeto simples

Num objeto comum, 'store.user = novoUsuario' apenas muda um valor em memória — nenhuma view seria notificada automaticamente, exigindo chamadas manuais de 're-render()' espalhadas pelo código toda vez que o estado muda. O 'Proxy' centraliza essa notificação: a armadilha de UI desincronizada com o estado (um bug clássico em apps sem framework) desaparece, porque toda escrita já dispara os listeners automaticamente.

## Caso de uso real: HomeView reagindo a login

```js
import { store, subscribe } from "../store.js";

// Esta view não sabe QUANDO o usuário vai logar — apenas se inscreve
// e reage sempre que store.user mudar, de qualquer lugar do app
const unsubscribe = subscribe((state) => {
    nameEl.textContent = state.user?.name ?? "Visitante";
});
```

## Padrão pub/sub — conceito e por que ele desacopla

Pub/sub (publish/subscribe) separa quem PRODUZ uma mudança de quem REAGE a ela: a store não precisa saber quais views existem, e as views não precisam saber quem mudou o estado. Esse desacoplamento é o que permite adicionar uma nova view que reage à store sem alterar nenhum código existente — uma aplicação direta do princípio aberto/fechado.

## Armadilha: vazamento de memória por falta de unsubscribe

Se uma view se inscreve via 'subscribe()' mas nunca chama a função de retorno (o 'unsubscribe') ao ser destruída, o listener continua na 'Set' para sempre — recebendo notificações e mantendo na memória referências a um DOM que já não existe mais na tela. É exatamente por isso que o contrato '{ destroy }' das views (ver 'views/') existe: cada view DEVE desinscrever-se no seu 'destroy()'.

## Limitações do Proxy raso (shallow)

Este Proxy intercepta apenas o nível mais externo do objeto. Mutar um array ou objeto aninhado diretamente (ex.: 'store.prompts.push(novoPrompt)') NÃO passa pelo 'set' do Proxy e não notifica ninguém — é preciso sempre reatribuir a referência completa ('store.prompts = [...store.prompts, novoPrompt]') para acionar a reatividade.

## Alternativa e trade-off

Bibliotecas como Redux ou Zustand oferecem middlewares, dev tools de time-travel e imutabilidade garantida por convenção, mas adicionam uma dependência e uma API a aprender. Aqui, ~15 linhas de Proxy nativo cobrem 90% do que uma store pub/sub precisa fazer numa SPA pequena, sem nenhuma dependência externa.
