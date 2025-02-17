# Projeto: WhatsApp Bot com EvolutionAPI e ChatGPT

[![Docker](https://img.shields.io/badge/Docker-OK-blue.svg)](https://www.docker.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-13-blue.svg)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-OK-red.svg)](https://redis.io/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](./LICENSE)

Este repositório contém a configuração para rodar um bot de WhatsApp usando a **EvolutionAPI** (API não oficial do WhatsApp) integrada à **API da OpenAI (ChatGPT)**, com persistência de dados em **PostgreSQL** e cache em **Redis**. O objetivo é fornecer um ambiente containerizado para responder a mensagens de WhatsApp sobre o monitoramento do Rio Itabapoana, exibindo dados como nível do rio, gráficos e estatísticas em tempo real.

---

## Sumário

- [Recursos](#recursos)
- [Tecnologias](#tecnologias)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Estrutura de Pastas](#estrutura-de-pastas)
- [Configuração](#configuração)
- [Como Usar](#como-usar)
- [Conexão ao Banco de Dados](#conexão-ao-banco-de-dados)
- [Personalização do Bot](#personalização-do-bot)
- [Contribuindo](#contribuindo)
- [Licença](#licença)

---

## Recursos

- **EvolutionAPI**: Fornece a capacidade de enviar e receber mensagens de WhatsApp via API.
- **Integração com ChatGPT**: Permite que o bot responda de forma inteligente a perguntas dos usuários.
- **Scraper de dados do Rio Itabapoana**: Faz a extração de dados em tempo real de um site específico, retornando informações sobre o nível do rio, gráficos e estatísticas.
- **Banco de dados PostgreSQL**: Armazena informações persistentes, como histórico de mensagens ou registros de medições do rio.
- **Redis**: Gerencia cache para melhorar a performance e escalabilidade.

---

## Tecnologias

- [Docker](https://www.docker.com/) e [Docker Compose](https://docs.docker.com/compose/)
- [EvolutionAPI](https://github.com/AtendAi/Evolution-API) (API não oficial do WhatsApp)
- [OpenAI API (ChatGPT)](https://platform.openai.com/docs/introduction)
- [Python 3.10+](https://www.python.org/)
- [PostgreSQL 13](https://www.postgresql.org/)
- [Redis](https://redis.io/)
- [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/) (para web scraping)
- [Requests](https://docs.python-requests.org/en/master/) (para chamadas HTTP)

---

## Pré-requisitos

- **Docker** instalado (versão 20+)
- **Docker Compose** instalado (versão 1.29+ ou Docker Compose V2)
- Conta e chave de API da [OpenAI](https://platform.openai.com/)
- (Opcional) Ferramenta de acesso ao banco de dados PostgreSQL (pgAdmin, DBeaver, etc.)

---

## Instalação

1. **Clone este repositório**:

   ```bash
   git clone https://github.com/seu-usuario/seu-repositorio.git
   cd seu-repositorio
   ```

2. **Crie (ou edite) o arquivo .env (opcional, caso queira armazenar variáveis de ambiente fora do docker-compose.yml)**:

    ```bash
    AUTHENTICATION_API_KEY=apikey321
    DATABASE_ENABLED=true
    DATABASE_PROVIDER=postgresql
    DATABASE_CONNECTION_URI=postgresql://postgres:postgres@postgres:5432/postgres
    CACHE_REDIS_ENABLED=true
    CACHE_REDIS_URI=redis://redis:6379/6
    CACHE_REDIS_PREFIX_KEY=evolution
    CACHE_REDIS_SAVE_INSTANCES=true
    CACHE_LOCAL_ENABLED=false
    OPENAI_API_KEY=sua-chave-aqui
    ```

3. **Suba os containers**:

    ```bash
    docker-compose up -d
    ```
Isso iniciará:
    - evolution-api na porta 8080
    - PostgreSQL na porta 5441 (mapeada da porta interna 5432)
    - Redis na porta 6379

4. **Verifique os containers**:

    ```bash
    docker-compose ps
    ```
Você deverá ver algo como:

    ```nginx
    Name                Command               State         Ports
    --------------------------------------------------------------------
    evolution_api       ...                   Up            0.0.0.0:8080->8080
    postgres            ...                   Up            0.0.0.0:5441->5432
    redis               ...                   Up            0.0.0.0:6379->6379
    ```
## Estrutura de Pastas

Exemplo de organização:

    ```bash
    .
    ├── docker-compose.yml
    ├── .env
    ├── src/
    │   ├── main.py           # Ponto de entrada do seu bot (integra tudo)
    │   ├── whatsapp.py       # Integração com a EvolutionAPI
    │   ├── gpt.py            # Integração com a API do ChatGPT
    │   ├── scraper.py        # Coleta de dados do Rio Itabapoana
    │   ├── config.py         # Configurações de variáveis de ambiente
    │   └── requirements.txt  # Dependências do Python
    ├── README.md
    └── ...
    ```

## Configuração

Variáveis de ambiente:

A EvolutionAPI e o bot usarão as variáveis definidas no docker-compose.yml ou no arquivo .env.

Certifique-se de definir corretamente:

- AUTHENTICATION_API_KEY
- OPENAI_API_KEY
- DATABASE_CONNECTION_URI
- etc.

Portas:

- A EvolutionAPI estará acessível em http://localhost:8080.
- O PostgreSQL estará acessível externamente em localhost:5441.
- O Redis estará acessível externamente em localhost:6379.

## Como Usar

1. **Configurar o Bot no WhatsApp**

- Consulte a documentação da EvolutionAPI para criar e configurar uma instância de WhatsApp.
- Aponte os webhooks (se necessário) para o endpoint configurado do seu bot (por exemplo, http://seu-host:8080/webhook ou algo similar).

2. **Executar o Bot (modo desenvolvimento)**

- Se estiver desenvolvendo o Python localmente, inicie o bot:

    ```bash
    cd src
    python main.py
    ```

- Se estiver rodando via Docker, o container do bot (evolution-api) já deve estar em execução.

3. **Enviar Mensagens de Teste**

- Use seu celular para enviar mensagens para o número de WhatsApp configurado.
- O bot deverá responder usando as integrações com ChatGPT e com os dados do Rio Itabapoana.

## Conexão ao Banco de Dados

Para gerenciar ou inspecionar o banco de dados, você pode usar ferramentas como pgAdmin ou DBeaver. 

Acesse:

- Host: 127.0.0.1
- Porta: 5441
- Usuário: postgres
- Senha: postgres
- Banco: postgres

**Você também pode acessar o container diretamente via Docker**:

    ```bash
    docker exec -it postgres psql -U postgres
    ```

## Personalização do Bot

- Customizar prompts: Ajuste a forma como você constrói as perguntas para o ChatGPT no arquivo gpt.py.
- Dados do Rio Itabapoana:
    - Modifique scraper.py para coletar as informações específicas que você deseja.
    - Armazene ou cacheie dados no banco de dados ou no Redis, se necessário.
- Fluxo de conversa: Edite main.py e whatsapp.py para definir como as mensagens são tratadas e respondidas.

## Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para abrir **Issues** e **Pull Requests**. Antes de enviar um PR, por favor:

1. Crie uma branch com o nome da feature/fix (ex.: feature/monitoramento-graficos).
2. Faça commits claros e concisos.
3. Abra o Pull Request descrevendo as mudanças.

## Licença

Este projeto está licenciado sob a licença MIT.
Sinta-se livre para usar e modificar conforme necessário.