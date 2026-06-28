Parte de uma imagem mínima ('nginx:alpine'), copia 'public/' e substitui a configuração padrão do Nginx pela do projeto, que inclui proxy de API e fallback de SPA.

```dockerfile
FROM nginx:alpine
COPY public/ /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
```
