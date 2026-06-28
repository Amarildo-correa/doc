A ordem de carregamento importa — cada camada depende da anterior, em especificidade crescente.

- tokens.css — variáveis
- base.css — reset
- layout.css — estrutura
- components/ — peças específicas
- utilities.css — ajustes pontuais

## Por que a ordem de '<link>' no index.html é parte do design

```html
<link rel="stylesheet" href="/css/tokens.css" />
<link rel="stylesheet" href="/css/base.css" />
<link rel="stylesheet" href="/css/layout.css" />
<link rel="stylesheet" href="/css/components/button.css" />
<link rel="stylesheet" href="/css/components/card.css" />
<link rel="stylesheet" href="/css/components/modal.css" />
<link rel="stylesheet" href="/css/utilities.css" />
```

CSS resolve conflitos entre regras de mesma especificidade pela ordem de declaração — a última regra lida 'ganha'. Por isso 'utilities.css' vem por último (pode sobrepor qualquer outra camada para ajustes pontuais) e 'tokens.css' vem primeiro (define variáveis que todas as demais camadas consomem via 'var(--token)').

## Por que camadas em vez de um único arquivo CSS

Um único 'style.css' gigante cresce sem fronteiras claras: qualquer um pode adicionar uma regra em qualquer lugar, dificultando saber o que afeta o quê. Separar por responsabilidade (tokens/reset/layout/componente/utilitário) cria uma convenção onde cada novo estilo tem um lugar óbvio para ir, reduzindo a chance de duplicação e de regras conflitantes silenciosas.

## Escalabilidade e manutenção

Conforme novos componentes são criados (em 'components/' no JS), o padrão é adicionar um novo arquivo CSS correspondente dentro de 'css/components/' e referenciá-lo no index.html — mantendo a relação 1:1 entre componente JS e seu estilo, sem inflar arquivos já existentes.
