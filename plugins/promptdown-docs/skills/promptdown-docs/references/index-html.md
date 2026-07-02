Este arquivo é o template estático que carrega o app.js como módulo — mas em produção nenhum cliente recebe exatamente este HTML vazio: o `worker.js` intercepta cada rota e devolve uma versão já preenchida com `data-route` e (para humanos) o conteúdo semântico da página, antes mesmo do JavaScript rodar (ver `worker-js.md`). O navegador nunca recarrega este documento ao navegar entre 'páginas' internas da SPA — só na primeira carga de cada URL, que já chega renderizada pelo Worker.

```html
<body>
  <div id="app" data-route="/prompts/:id">
    <!-- Preenchido pelo worker.js no SSR: HTML semântico real da rota,
         nunca um placeholder vazio — bots e humanos recebem o mesmo
         conteúdo aqui, só a presença do <script> abaixo muda. -->
  </div>
  <div id="toast-container"></div> <!-- fila visual de notificações -->

  <!-- <template> ficam inertes no DOM: navegador as analisa mas não
       renderiza nem executa <script>/<img> internos até serem clonadas -->
  <template id="card-template">...</template>
  <template id="modal-template">...</template>

  <!-- Presente apenas na resposta para humanos e bots de LLM/SEO em modo
       de hidratação; bots de SEO puro recebem o mesmo HTML sem este
       <script> (ver worker-js.md) -->
  <script type="module" src="/js/app.js"></script>
</body>
```

## Por que 'type="module"' importa

Scripts do tipo 'module' são carregados em modo estrito automaticamente, suportam 'import'/'export' nativos (sem bundler obrigatório) e são deferidos por padrão — o navegador só os executa depois de parsear todo o HTML, o que garante que '#app' e os '<template>' já existam quando 'app.js' rodar.

## Single Page = single template, múltiplas respostas renderizadas

Este arquivo é o único template HTML do projeto, mas cada rota recebe uma resposta já renderizada a partir dele pelo `worker.js` — nunca o esqueleto vazio diretamente. Depois da carga inicial, toda 'navegação' interna visível ao usuário é, de fato, JavaScript trocando o conteúdo de '#app' (ver `router-js.md`); a diferença em relação a uma SPA tradicional é que a primeira renderização de cada URL já chega pronta, sem esperar o JavaScript rodar.

## Acessibilidade

Como a navegação não recarrega a página, leitores de tela não recebem o anúncio automático de 'nova página' que um navegador dá em navegação tradicional. Uma boa prática (a evoluir neste projeto) é o router mover o foco programaticamente para um heading da nova view e usar uma região 'aria-live' para anunciar a troca de contexto.

## Alternativa e trade-off

Frameworks como Vite/Webpack ofereceriam code-splitting automático e HMR, mas introduziriam uma etapa de build. Aqui, módulos ES nativos no navegador eliminam o build step por completo — trade-off de simplicidade de deploy contra otimizações automáticas de bundle.

## Fontes

- `worker-js.md`
- `router-js.md`
