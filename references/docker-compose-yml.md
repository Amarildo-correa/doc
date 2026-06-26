Define o serviço 'frontend' (Nginx, porta 80) e 'json-server' (Node, porta 3001), e o volume nomeado que persiste o database.json fora do ciclo de vida dos containers.

```yaml
services:
    frontend:
        build:
            context: .
            dockerfile: Dockerfile.frontend
        ports:
            - "80:80"
        depends_on:
            - json-server

    json-server:
        build:
            context: .
            dockerfile: Dockerfile.api
        ports:
            - "3001:3001"
        volumes:
            - db-data:/app/api/data

volumes:
    db-data:
```
