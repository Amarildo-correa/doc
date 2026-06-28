Parte de 'node:lts-alpine', instala dependências, copia a pasta 'api/' e expõe a porta 3001, executando o server.js como processo principal.

```dockerfile
FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY api/ ./api/
EXPOSE 3001
CMD ["node", "api/server.js"]
```
