Em produção é montado como volume Docker ('db-data'): sobrevive a 'docker compose up --build' e a redeploys. O container pode ser recriado, mas o arquivo (e os dados dos usuários) permanece.

## Como o JSON Server usa este arquivo

Cada chave de topo-nível do JSON ('prompts', por exemplo) se torna automaticamente uma 'coleção' com rotas REST completas: 'GET /api/prompts', 'GET /api/prompts/:id', 'POST', 'PUT', 'PATCH' e 'DELETE'. Não há schema declarado nem migrations — a 'forma' dos dados é simplesmente o que está escrito no arquivo neste instante.

```json
{
    "prompts": [
        { "id": 1, "title": "Resumo de texto", "tags": ["produtividade"] }
    ]
}
```

## Caso de uso real: leitura via api.js

```js
// public/js/api.js faz a ponte HTTP — nunca lê este arquivo diretamente
export async function getPrompts() {
    const res = await fetch("/api/prompts");
    return res.json(); // espelha exatamente o array "prompts" deste JSON
}
```

## Riscos da ausência de schema

- Sem validação de tipos: nada impede gravar 'id' como string num registro e number no outro.
- Concorrência: duas escritas simultâneas podem causar 'race condition' no arquivo, já que é I/O de disco simples, sem transações.
- Sem índices: buscas filtradas pelo JSON Server são O(n) sobre o array inteiro — aceitável para um mock, inviável em produção real.

## Por que existe backup automático

Como este é o único arquivo com estado mutável persistente do projeto, ele é o ativo mais frágil — perder este arquivo é perder todos os dados. Por isso existe um backup diário automático via cron, ver 'scripts/backup-db.sh', que copia o arquivo com timestamp e mantém um histórico rotativo.

## Alternativa e trade-off

Um banco real (Postgres/SQLite) daria transações, schema e índices, mas exigiria migrations e um driver de conexão — peso desproporcional para um protótipo de frontend. A escolha por um arquivo JSON plano otimiza para velocidade de iteração no frontend, sacrificando garantias de integridade que só importarão quando o backend real for implementado.
