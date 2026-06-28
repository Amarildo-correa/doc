A cada push em 'main', primeiro roda os testes. Só se eles passarem (o job 'deploy' depende do job 'test' via 'needs: test') é que o workflow conecta via SSH na Vultr e reconstrói os containers.

```yaml
jobs:
    test:
        runs-on: ubuntu-latest
        steps:
            - run: npm install
            - run: npm run test
            - run: npm run test:coverage

    deploy:
        needs: test  # só roda se os testes passarem
        steps:
            - uses: appleboy/ssh-action@v1
              with:
                  host: ${{ secrets.VULTR_HOST }}
                  script: |
                      cd /var/www/promptdown-ui-v3
                      git pull origin main
                      docker compose up -d --build
```

## Secrets necessários no GitHub

- VULTR_HOST — IP público da VM
- VULTR_USER — usuário SSH
- VULTR_SSH_KEY — chave privada SSH
