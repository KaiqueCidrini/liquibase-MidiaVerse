## Liquibase Mediaverse — Guia de Execução Local

Este guia explica como preparar o ambiente e executar as migrações do Liquibase localmente usando Docker Compose.

### Pré-requisitos
- Docker Desktop (Windows) — instale seguindo a documentação oficial: [Instalar Docker Desktop no Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
- Java (JRE ou JDK) versão X ou superior: [Downloads de Java](https://www.oracle.com/java/technologies/downloads/)
- Liquibase (opcional para uso via CLI local): [Liquibase Community](https://www.liquibase.com/community-vs-secure)

Observação: Para rodar este projeto, apenas o Docker Desktop é estritamente necessário. Java/Liquibase locais são úteis para rodar comandos fora do container.

### Clonar o repositório
```powershell
git clone https://github.com/KaiqueCidrini/liquibase-mediaverse.git
cd liquibase-MidiaVerse
```

### Estrutura relevante
- Changelog mestre: `database/changelog/master.yaml`
- Compose principal: `docker-compose.yml`

### Subir os containers (Postgres + Liquibase)
Execute na raiz do projeto:
```bash
docker-compose -f docker-compose.yml -p postgre-postgres --verbose up -d
```

O serviço `liquibase` aplica as migrações e encerra após concluir. O Postgres (`postgres-mediaverse`) permanece em execução.

### Acompanhar logs do Liquibase
```bash
docker-compose logs -f liquibase
```

### Reaplicar/rodar update manualmente
```bash
docker-compose run --rm liquibase update
```

### Conferir o banco via psql
```bash
docker-compose exec postgres-mediaverse psql -U postgres -d mediaverse -c "\\dt"
```

### Derrubar e limpar
```bash
docker-compose down -v
```

### Solução de problemas comuns
- "master.yaml does not exist":
  - Garanta que executou os comandos na raiz do projeto.
  - Verifique se o volume está montado corretamente:
    ```bash
    docker-compose run --rm liquibase sh -lc "ls -la /liquibase/changelog"
    ```
  - Se não listar `master.yaml`, revise o compartilhamento de pastas no Docker Desktop (Settings → Resources → File Sharing) e inclua o diretório do projeto.

- "Invalid argument '--changelog-file'":
  - No Compose, passe flag e valor no mesmo item usando '=' (ex.: `--changelog-file=master.yaml`).

- "service refers to undefined network":
  - Certifique-se de que o arquivo tenha o bloco no nível raiz:
    ```yaml
    networks:
      mediaverse-network:
        driver: bridge
    ```
  - Valide o arquivo: `docker-compose config`

### Notas
- Este projeto usa Postgres com rede `mediaverse-network` e serviços `postgres-mediaverse` e `liquibase`.
- O `liquibase` está configurado com `working_dir: /liquibase/changelog` e aponta para `master.yaml`.


