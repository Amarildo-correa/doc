Carrega o app.js como módulo e define a estrutura mínima do DOM. O navegador nunca recarrega este arquivo ao navegar entre 'páginas' — apenas o conteúdo é troncado via JavaScript.

```html
<body>
  <div id="app"></div>           <!-- raiz onde as views são montadas -->
  <div id="toast-container"></div> <!-- fila visual de notificações -->

  <!-- <template> ficam inertes no DOM: navegador as analisa mas não
       renderiza nem executa <script>/<img> internos até serem clonadas -->
  <template id="card-template">...</template>
  <template id="modal-template">...</template>

  <script type="module" src="/js/app.js"></script>
</body>
```

## Por que 'type="module"' importa

Scripts do tipo 'module' são carregados em modo estrito automaticamente, suportam 'import'/'export' nativos (sem bundler obrigatório) e são deferidos por padrão — o navegador só os executa depois de parsear todo o HTML, o que garante que '#app' e os '<template>' já existam quando 'app.js' rodar.

## Single Page = single HTML

Esse é o único arquivo HTML servido pelo Nginx para qualquer rota da aplicação (graças ao fallback configurado em 'nginx.conf', explicado na descrição de 'public/'). Toda 'navegação' visível ao usuário é, na verdade, JavaScript substituindo o conteúdo de '#app' — o documento HTML em si nunca recarrega.

## Acessibilidade

Como a navegação não recarrega a página, leitores de tela não recebem o anúncio automático de 'nova página' que um navegador dá em navegação tradicional. Uma boa prática (a evoluir neste projeto) é o router mover o foco programaticamente para um heading da nova view e usar uma região 'aria-live' para anunciar a troca de contexto.

## Alternativa e trade-off

Frameworks como Vite/Webpack ofereceriam code-splitting automático e HMR, mas introduziriam uma etapa de build. Aqui, módulos ES nativos no navegador eliminam o build step por completo — trade-off de simplicidade de deploy contra otimizações automáticas de bundle.
