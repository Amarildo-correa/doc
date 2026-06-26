Inicializa o router, conecta a store e registra um handler global de 'unhandledrejection' — uma rede de segurança contra Promises rejeitadas que ninguém tratou.

```js
// Único módulo carregado diretamente pelo <script type="module"> do index.html
import { initRouter } from "./router.js";

// Rede de segurança: captura qualquer Promise rejeitada que nenhuma view
// ou componente tenha tratado com .catch() — evita que erros "desapareçam"
// silenciosamente no console do navegador.
window.addEventListener("unhandledrejection", (event) => {
    console.error("Erro não tratado:", event.reason);
});

// Ponto de partida: a partir daqui o controle passa para o router,
// que decide qual view montar com base na URL atual.
initRouter();
```

## Por que este arquivo deve ficar minúsculo

'app.js' é deliberadamente o módulo com menos responsabilidades do projeto — sua única função é orquestrar a inicialização. Qualquer lógica de negócio colocada aqui (em vez de em 'router.js', 'store.js' ou numa view) tende a crescer descontroladamente, pois é o único arquivo que toda a aplicação carrega obrigatoriamente, mesmo em rotas que não precisam dela.

## Ciclo de vida da aplicação

- 1. Navegador parseia 'index.html', encontra '<script type="module">' e agenda seu carregamento (deferido).
- 2. 'app.js' executa uma única vez, registra o handler global de erros.
- 3. 'initRouter()' lê a URL atual e monta a primeira view dentro de '#app'.
- 4. A partir daqui, 'app.js' nunca executa novamente — toda interação subsequente é tratada por 'router.js', pelas views e pela 'store.js'.

## Por que capturar 'unhandledrejection' e não apenas 'error'

O evento global 'error' captura exceções síncronas e erros de recursos (scripts, imagens), mas não Promises rejeitadas sem '.catch()' — para essas, o navegador dispara especificamente 'unhandledrejection'. Como praticamente toda chamada de rede no projeto (via 'api.js') é assíncrona, esse listener é a malha de segurança mais relevante para esta arquitetura específica.

## Alternativa e trade-off

Um framework como React/Vue ofereceria 'Error Boundaries' declarativos por componente. Aqui, sem framework, a captura é necessariamente global e reativa (loga o erro, não recupera a UI automaticamente) — suficiente para depuração, mas exige que cada view trate seus próprios erros de UI explicitamente (ver 'prompt-list-view.js' e seu '.catch()').
