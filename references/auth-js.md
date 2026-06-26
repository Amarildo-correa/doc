Mesmo num backend mock, simular autenticação é valioso: permite que o frontend seja desenvolvido e testado contra respostas reais de 401 (não autenticado) e 403 (sem permissão), sem depender de um serviço de auth de verdade ainda não implementado.

Um middleware Express/JSON Server é apenas uma função '(req, res, next)' que decide se a requisição segue adiante ('next()') ou é interrompida ali mesmo (respondendo direto com 'res.status(...).json(...)').

```js
function auth(req, res, next) {
    // 1. Lê o header padrão HTTP de autenticação
    const token = req.headers.authorization;

    // 2. Sem header algum → 401 Unauthorized (cliente não se identificou)
    if (!token) {
        return res.status(401).json({ error: "Token ausente" });
    }

    // 3. Header presente mas valor errado → 403 Forbidden (identificou-se,
    //    mas a credencial não é válida)
    if (token !== "Bearer mock-token") {
        return res.status(403).json({ error: "Token inválido" });
    }

    // 4. Token válido → libera a requisição para o próximo middleware/rota
    next();
}

module.exports = auth;
```

## 401 vs 403 — por que a distinção importa

Misturar os dois códigos é um erro comum: 401 diz 'eu não sei quem você é, mande uma credencial'; 403 diz 'eu sei quem você é, mas você não pode fazer isso'. O frontend (em 'public/js/api.js') pode reagir diferente a cada um — por exemplo, redirecionar ao login só no 401, e mostrar uma mensagem de permissão no 403.

## Onde ele entra no pipeline

Middlewares são aplicados em ordem com 'server.use(...)', antes do router do JSON Server. Isso significa que 'auth' roda para toda requisição que chegar em '/api', interceptando-a antes mesmo dela tocar em 'database.json'.

```js
const auth = require("./middleware/auth");

server.use(auth);
server.use("/api", router);
```

## Segurança: por que isso é só um mock

- O token é uma string fixa hardcoded ('mock-token') — qualquer pessoa que leia o código-fonte o conhece. Em produção real, tokens devem ser JWT assinados ou sessões opacas validadas contra um store seguro.
- Não há expiração, refresh ou revogação — um backend real precisa de TTL no token e um mecanismo de logout que invalide sessões.
- Seguindo a convenção do projeto, um backend real guardaria esse token num cookie 'HttpOnly; SameSite=Lax', nunca em 'localStorage', para reduzir a superfície de ataque de XSS.

## Escalando esta ideia

Se o número de regras de autorização crescer (papéis, permissões por recurso), a recomendação é extrair a lógica de decisão para uma função pura testável separadamente — `canAccess(role, resource)` — e manter o middleware apenas como a 'cola' que lê o request e chama essa função, preservando testabilidade sem reescrever o pipeline.
