O Dockerfile.frontend copia 'public/' para dentro da imagem Nginx. Como a SPA usa History API, o nginx.conf precisa de um fallback para que rotas internas não retornem 404 ao serem acessadas diretamente.

```nginx
location / {
  root /usr/share/nginx/html;
  try_files $uri $uri/ /index.html;  # fallback de SPA
}

location /api/ {
  proxy_pass http://json-server:3001/api/;  # evita CORS
}
```

## Por que 'try_files ... /index.html' é obrigatório aqui

Numa SPA com roteamento via History API, uma URL como '/prompts/42' não corresponde a nenhum arquivo físico — ela só faz sentido depois que 'router.js' a interpreta no navegador. Sem o fallback, recarregar a página nessa URL (ou compartilhar o link) resultaria em 404 do próprio Nginx, porque o arquivo '/prompts/42' literalmente não existe no disco. O fallback devolve sempre 'index.html', deixando o JavaScript decidir o que renderizar.

## Estrutura interna

```text
public/
├── index.html   # único ponto de entrada HTML
├── js/          # toda a lógica da aplicação
└── css/         # toda a apresentação visual
```

## Papel na arquitetura geral

Esta pasta representa a camada de apresentação pura — ela não sabe como os dados são persistidos (isso é responsabilidade de 'api/'), apenas como buscá-los via HTTP e exibi-los. Essa fronteira é o que permite trocar o backend inteiro (de JSON Server para qualquer linguagem) sem alterar uma única linha aqui, desde que o contrato de 'openapi.yaml' seja respeitado.

## Boas práticas e armadilhas comuns

- Nunca referenciar caminhos absolutos do sistema de arquivos do host — tudo deve ser relativo à raiz servida pelo Nginx.
- Evitar qualquer chamada de rede hardcoded para 'localhost:3001'; sempre passar pelo proxy '/api/' do próprio Nginx, que funciona tanto em desenvolvimento quanto em produção.
- Lembrar que qualquer arquivo colocado aqui é público — nunca commitar segredos, chaves ou configurações sensíveis dentro de 'public/'.
