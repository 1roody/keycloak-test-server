# Keycloak Test Server

#### Descrição

Este repositório oferece as configurações essenciais para configurar uma instância do Keycloak integrada com um banco de dados PostgreSQL. Todos os valores são genéricos, permitindo a execução e testes locais de forma simples e eficiente.

Além disso, este README fornecerá instruções detalhadas sobre como implementar **Single Sign-On (SSO)** em outras aplicações, utilizando o Keycloak como **Provedor de Identidade (IDP)**.

#### Pré-requisitos

- Docker e Docker Compose instalados.

#### Configuração do docker-compose para Keycloak e PostgreSQL

**1. PostgreSQL**

O serviço do PostgreSQL cria um container com a imagem oficial do PostgreSQL, configurado para armazenar os dados localmente.
Configuração:

- Nome do Container: `keycloak_database`
- Imagem: `postgres:16.2`
- Porta Exposta: `5432:5432` (porta padrão do PostgreSQL)
- Volume: O volume `postgres_data` é montado no diretório `/var/lib/postgresql/data` para persistência de dados.

Variáveis de Ambiente:

- `POSTGRES_DB:` Nome do banco de dados que será criado, neste caso keycloak_db.
- `POSTGRES_USER:` Nome do usuário do banco, neste caso keycloakuser.
- `POSTGRES_PASSWORD:` Senha do usuário, neste caso keycloakpassword.

**Exemplo:**

```
services:
  postgresql:
    container_name: keycloak_database
    image: postgres:16.2
    ports:
      - 5432:5432
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: keycloak_db
      POSTGRES_USER: keycloakuser
      POSTGRES_PASSWORD: keycloakpassword
    networks:
      - keycloak_network
```

**2. Keycloak**

Este serviço configura o Keycloak para ser executado no modo de desenvolvimento, conectado ao banco de dados PostgreSQL.
Configuração:

- Nome do Container: `keycloak_application`
- Imagem: `quay.io/keycloak/keycloak:25.0.6`
- Comando: `start-dev`, que executa o Keycloak em modo de desenvolvimento.
- Porta Exposta: `8080:8080` (porta padrão de administração do Keycloak)
- Restart Policy: `always` (o container será reiniciado automaticamente em caso de falha)
- Dependências: O Keycloak depende do serviço `postgresql` para iniciar.

**Configurações de Host:**

- `KC_HOSTNAME:` Define o hostname para o Keycloak, neste caso localhost.
- `KC_HOSTNAME_PORT:` Porta que o Keycloak usa, neste caso 8080.
- `KC_HOSTNAME_STRICT_HTTPS:` Desabilita a exigência de HTTPS em modo de desenvolvimento.
- `KC_HOSTNAME_STRICT_BACKCHANNEL:` Configura a comunicação backchannel para não exigir HTTPS.
- `KC_HTTP_ENABLED:` Habilita o protocolo HTTP.
- `KC_HEALTH_ENABLED:` Habilita endpoints de saúde.

**Administração do Keycloak:**

- `KEYCLOAK_ADMIN:` Usuário administrador, configurado como admin.
- `KEYCLOAK_ADMIN_PASSWORD:` Senha do administrador, configurada como admin.

**Configurações de Banco de Dados:**

- `KC_DB:` Define o tipo de banco de dados, neste caso postgres.
- `KC_DB_USERNAME:` Usuário do banco, igual ao configurado no PostgreSQL (keycloakuser).
- `KC_DB_PASSWORD:` Senha do usuário, igual ao configurado no PostgreSQL (keycloakpassword).
- `KC_DB_URL_HOST:` Host do banco de dados, configurado como postgresql (nome do serviço).
- `KC_DB_URL_PORT:` Porta do banco de dados, configurada como 5432.
- `KC_DB_URL_DATABASE:` Nome do banco de dados, configurado como keycloak_db.
