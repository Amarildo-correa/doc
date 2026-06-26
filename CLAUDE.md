# Convenções deste repositório

Este repositório é um índice educativo de "aulas" sobre a estrutura de um projeto fictício (uma SPA vanilla de exemplo). Não contém código de aplicação — só Markdown.

## Como responder perguntas sobre o projeto

Para qualquer pergunta sobre o que uma pasta ou arquivo do projeto fictício faz:

1. Leia primeiro `SKILL.md` — é o índice único da árvore. Cada linha tem o formato:
   `- \`path\` (folder|file) → \`references/slug.md\` — summary`O`summary` já responde perguntas rápidas/superficiais sem precisar abrir mais nada.
2. Se precisar do conteúdo completo (explicação detalhada, exemplos de código, trade-offs), leia **apenas** o arquivo `references/<slug>.md` correspondente ao item perguntado.
3. Não há necessidade de ler todos os arquivos de `references/` de uma vez — leia só o(s) relevante(s) para a pergunta feita.

## Por que essa ordem

`SKILL.md` tem poucas linhas e descreve a árvore inteira; cada arquivo em `references/` é pequeno e cobre um único item. Ler nessa ordem (índice → arquivo específico) evita carregar conteúdo irrelevante na janela de contexto.

## Fonte única de verdade

O conteúdo de cada aula existe em um único lugar: o arquivo `.md` listado em `SKILL.md`. Não há duplicação do conteúdo em nenhum outro lugar deste repositório.
