Documenta endpoints, parâmetros, esquemas de request/response e códigos de status — fonte de verdade para quem consome a API.

```yaml
openapi: 3.0.3
info:
  title: PromptDown API
  version: 1.0.0
paths:
  /api/prompts:
    get:
      summary: Lista todos os prompts
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Prompt'
components:
  schemas:
    Prompt:
      type: object
      properties:
        id: { type: integer }
        title: { type: string }
        tags: { type: array, items: { type: string } }
```

## Por que manter um contrato separado da implementação

O JSON Server gera rotas automaticamente a partir de 'database.json' — ele não sabe nada sobre tipos, obrigatoriedade de campos ou erros possíveis. Este arquivo YAML preenche essa lacuna documental: é o único lugar do projeto que descreve explicitamente o que cada endpoint espera receber e devolver, sobrevivendo à eventual troca do JSON Server por um backend real (que precisará apenas continuar honrando este mesmo contrato).

## Caso de uso real

Ferramentas como Swagger UI ou Postman conseguem importar este arquivo diretamente e gerar uma interface interativa de exploração da API — útil para um desenvolvedor novo no time entender os endpoints disponíveis sem precisar ler 'server.js' ou 'database.json'.

## Boa prática: contrato como fonte de verdade

Quando 'database.json' muda de forma (um campo novo, um tipo diferente), o arquivo OpenAPI deve ser atualizado no mesmo commit. Deixá-lo dessincronizado é uma armadilha comum: a documentação vira uma mentira documentada, pior do que não ter documentação alguma.

## Alternativa: geração automática

Ferramentas existem para gerar OpenAPI a partir do schema do JSON Server automaticamente, eliminando o risco de dessincronia — trade-off entre automação (menos manutenção manual) e controle explícito sobre a documentação (mais trabalho, porém mais preciso quanto à intenção da API).
