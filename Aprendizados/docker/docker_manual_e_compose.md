Perfeito. Vamos montar um **exemplo realista e pequeno**, conectando tudo:

* **frontend** em React/Nginx
* **backend** em Node
* **PostgreSQL**
* **Dockerfile** de front e back
* **docker-compose.yml**
* **comandos Docker**
* e comentários “linkando” uma parte na outra

A ideia é você enxergar o fluxo completo.

---

# Visão geral da arquitetura

Teremos esta estrutura hipotética:

```text
projeto/
├─ frontend/
│  ├─ Dockerfile
│  └─ ...arquivos do front
├─ backend/
│  ├─ Dockerfile
│  └─ ...arquivos do back
└─ docker-compose.yml
```

Funcionamento:

* o **Dockerfile do backend** cria a imagem do backend
* o **Dockerfile do frontend** cria a imagem do frontend
* o **docker-compose.yml** sobe os containers e conecta tudo
* o **Postgres** usa uma imagem pronta oficial
* o **volume** guarda os dados do banco
* a **rede** permite que os containers conversem entre si

---

# 1. Dockerfile do backend

Vamos supor um backend Node.js.

## `backend/Dockerfile`

```dockerfile
# Usa uma imagem base oficial do Node
# Esta imagem base será a "matéria-prima" do nosso backend
FROM node:20-alpine

# Define a pasta de trabalho dentro do container
# Tudo que vier depois será executado a partir de /app
WORKDIR /app

# Copia primeiro os arquivos de dependência
# Fazemos isso antes para aproveitar cache do Docker
COPY package*.json ./

# Instala as dependências do projeto
RUN npm install

# Copia o restante dos arquivos do backend
COPY . .

# Expõe a porta 3000 do container
# Isso é apenas documentação para a imagem; não publica a porta no host sozinho
EXPOSE 3000

# Comando que roda quando o container iniciar
# Este comando será executado quando alguém usar essa imagem com "docker run"
CMD ["npm", "start"]
```

## O que esse Dockerfile gera?

Ele gera uma **imagem do backend**.

Exemplo de build manual:

```bash
docker build -t meu-backend:v1 -f backend/Dockerfile ./backend
# docker build               -> comando para construir imagem
# -t meu-backend:v1          -> nome e tag da imagem gerada
# -f backend/Dockerfile      -> diz qual Dockerfile usar
# ./backend                  -> contexto do build; os COPY desse Dockerfile olham para esta pasta
```

### Ligação importante

Esse comando acima cria a imagem **`meu-backend:v1`**.

Depois, essa imagem pode ser usada em:

```bash
docker run meu-backend:v1
```

ou no Compose com:

```yaml
image: meu-backend:v1
```

Ou seja: **o build cria a imagem; o run/compose usa essa imagem**.

---

# 2. Dockerfile do frontend

Agora um frontend que gera arquivos estáticos e serve com Nginx.

## `frontend/Dockerfile`

```dockerfile
# Etapa 1: build da aplicação
FROM node:20-alpine AS build

# Pasta de trabalho
WORKDIR /app

# Copia arquivos de dependência
COPY package*.json ./

# Instala dependências
RUN npm install

# Copia o restante do front
COPY . .

# Gera a versão final do front para produção
RUN npm run build

# Etapa 2: servidor web leve para servir os arquivos estáticos
FROM nginx:alpine

# Copia os arquivos gerados na etapa "build"
# para a pasta que o Nginx usa para servir site estático
COPY --from=build /app/dist /usr/share/nginx/html

# Expõe porta 80 do container
EXPOSE 80

# Inicia o Nginx em foreground
CMD ["nginx", "-g", "daemon off;"]
```

## Build manual do frontend

```bash
docker build -t meu-frontend:v1 -f frontend/Dockerfile ./frontend
# este comando cria a imagem "meu-frontend:v1"
# ela será usada depois por docker run ou docker compose
```

### Ligação importante

Aqui você viu um **multi-stage build**:

* a etapa `build` compila o front
* a etapa final usa Nginx só para servir os arquivos gerados

Isso reduz o tamanho da imagem final e deixa produção mais limpa.

---

# 3. Rodando manualmente sem Compose

Antes do Compose, é bom enxergar “na mão”.

## Criar rede

```bash
docker network create app-net
# cria uma rede Docker chamada app-net
# depois, os containers colocados nela conseguem se enxergar pelo nome
```

## Criar volume do banco

```bash
docker volume create postgres_data
# cria um volume nomeado
# este volume guardará os dados do PostgreSQL fora do ciclo de vida do container
```

## Subir banco

```bash
docker run -d \
  --name db \
  --network app-net \
  -e POSTGRES_DB=appdb \
  -e POSTGRES_USER=appuser \
  -e POSTGRES_PASSWORD=123456 \
  -v postgres_data:/var/lib/postgresql/data \
  postgres:15
# docker run                      -> cria e inicia container
# -d                              -> roda em background
# --name db                       -> nome do container será "db"
# --network app-net               -> entra na rede app-net
# -e ...                          -> variáveis de ambiente que a imagem postgres entende
# -v postgres_data:...            -> usa o volume criado antes
# postgres:15                     -> imagem usada; aqui é imagem pronta oficial
```

### Ligação importante

O valor `db` em `--name db` é muito importante.
Dentro da rede Docker, outros containers podem falar com o banco usando **host `db`**.

---

## Subir backend

```bash
docker run -d \
  --name backend \
  --network app-net \
  -p 5000:3000 \
  -e DB_HOST=db \
  -e DB_PORT=5432 \
  -e DB_NAME=appdb \
  -e DB_USER=appuser \
  -e DB_PASSWORD=123456 \
  meu-backend:v1
# usa a imagem "meu-backend:v1", que foi criada no docker build do backend
# -p 5000:3000  -> publica a porta 3000 do container na 5000 da máquina host
# DB_HOST=db    -> o backend procura o banco no host "db"
#                  e esse "db" funciona porque o container do banco está na mesma rede
```

### Ligação importante

Aqui, o backend está usando a imagem criada lá atrás por:

```bash
docker build -t meu-backend:v1 -f backend/Dockerfile ./backend
```

Ou seja:

* **Dockerfile** define como criar a imagem
* **docker build** cria a imagem
* **docker run** usa a imagem criada

---

## Subir frontend

```bash
docker run -d \
  --name frontend \
  --network app-net \
  -p 3000:80 \
  meu-frontend:v1
# usa a imagem do frontend criada no build manual
# -p 3000:80 -> acessa o front pela porta 3000 da máquina
```

---

# 4. O mesmo cenário com Docker Compose

Agora vamos transformar isso em um `docker-compose.yml`.

## `docker-compose.yml`

```yaml
services:
  db:
    image: postgres:15
    # Aqui usamos uma imagem pronta da Docker Hub
    # Repare que NÃO há "build", porque não estamos construindo o banco via Dockerfile

    container_name: db
    # Nome explícito do container
    # É semelhante ao --name db do docker run manual

    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: 123456
    # Equivale aos vários -e do docker run

    volumes:
      - postgres_data:/var/lib/postgresql/data
    # Equivale ao -v postgres_data:/var/lib/postgresql/data

    networks:
      - app-net
    # Equivale ao --network app-net

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    # Aqui o Compose faz o mesmo papel deste comando:
    # docker build -t algum_nome -f backend/Dockerfile ./backend
    #
    # "context: ./backend" = o mesmo papel do caminho final no docker build
    # "dockerfile: Dockerfile" = qual Dockerfile usar dentro dessa pasta

    container_name: backend
    # Equivale ao --name backend

    ports:
      - "5000:3000"
    # Equivale ao -p 5000:3000
    # host 5000 -> container 3000

    environment:
      DB_HOST: db
      DB_PORT: 5432
      DB_NAME: appdb
      DB_USER: appuser
      DB_PASSWORD: 123456
    # Equivale aos -e do docker run
    # DB_HOST=db funciona porque backend e db estão na mesma rede

    depends_on:
      - db
    # Diz ao Compose para subir o db antes do backend
    # Não garante que o banco já está "pronto para uso", só a ordem de inicialização

    networks:
      - app-net
    # Equivale ao --network app-net

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    # Mesma lógica do backend:
    # isso substitui um docker build manual do frontend

    container_name: frontend
    # Equivale ao --name frontend

    ports:
      - "3000:80"
    # Equivale ao -p 3000:80

    depends_on:
      - backend
    # Sobe o backend antes do frontend

    networks:
      - app-net
    # Também entra na mesma rede

volumes:
  postgres_data:
  # Este bloco declara o volume nomeado usado no serviço db
  # É o equivalente lógico de "docker volume create postgres_data"

networks:
  app-net:
  # Declara a rede usada pelos serviços
  # É o equivalente lógico de "docker network create app-net"
```

---

# 5. Como o Compose substitui os comandos manuais

Agora vou “ligar” cada pedaço.

## Banco

### Manual

```bash
docker run -d \
  --name db \
  --network app-net \
  -e POSTGRES_DB=appdb \
  -e POSTGRES_USER=appuser \
  -e POSTGRES_PASSWORD=123456 \
  -v postgres_data:/var/lib/postgresql/data \
  postgres:15
```

### Compose

```yaml
db:
  image: postgres:15
  container_name: db
  environment:
    POSTGRES_DB: appdb
    POSTGRES_USER: appuser
    POSTGRES_PASSWORD: 123456
  volumes:
    - postgres_data:/var/lib/postgresql/data
  networks:
    - app-net
```

### Tradução

* `image:` = imagem do `docker run`
* `container_name:` = `--name`
* `environment:` = `-e`
* `volumes:` = `-v`
* `networks:` = `--network`

---

## Backend

### Manual build

```bash
docker build -t meu-backend:v1 -f backend/Dockerfile ./backend
```

### Manual run

```bash
docker run -d \
  --name backend \
  --network app-net \
  -p 5000:3000 \
  -e DB_HOST=db \
  -e DB_PORT=5432 \
  -e DB_NAME=appdb \
  -e DB_USER=appuser \
  -e DB_PASSWORD=123456 \
  meu-backend:v1
```

### Compose

```yaml
backend:
  build:
    context: ./backend
    dockerfile: Dockerfile
  container_name: backend
  ports:
    - "5000:3000"
  environment:
    DB_HOST: db
    DB_PORT: 5432
    DB_NAME: appdb
    DB_USER: appuser
    DB_PASSWORD: 123456
  depends_on:
    - db
  networks:
    - app-net
```

### Tradução

O `build:` do Compose substitui o `docker build`.

O resto substitui o `docker run`.

---

## Frontend

### Manual build

```bash
docker build -t meu-frontend:v1 -f frontend/Dockerfile ./frontend
```

### Manual run

```bash
docker run -d \
  --name frontend \
  --network app-net \
  -p 3000:80 \
  meu-frontend:v1
```

### Compose

```yaml
frontend:
  build:
    context: ./frontend
    dockerfile: Dockerfile
  container_name: frontend
  ports:
    - "3000:80"
  depends_on:
    - backend
  networks:
    - app-net
```

---

# 6. Comandos reais para operar esse projeto

## Subir tudo

```bash
docker compose up -d --build
# docker compose up         -> sobe os serviços do arquivo docker-compose.yml
# -d                        -> roda em background
# --build                   -> antes de subir, constrói as imagens dos serviços que têm "build:"
#
# Aqui ele usa:
# - o Dockerfile do backend
# - o Dockerfile do frontend
# - a imagem pronta postgres:15
```

### Ligação importante

Esse comando lê o `docker-compose.yml` e:

* constrói backend e frontend porque eles têm `build:`
* baixa `postgres:15` se necessário
* cria rede
* cria volume
* sobe os containers

---

## Ver containers rodando

```bash
docker ps
# lista os containers em execução
# você deve ver db, backend e frontend
```

---

## Ver logs do backend

```bash
docker compose logs -f backend
# logs do serviço backend definido no docker-compose.yml
# -f acompanha em tempo real
```

### Ligação importante

O nome `backend` aqui é o nome do serviço no Compose:

```yaml
backend:
```

---

## Entrar no container do backend

```bash
docker exec -it backend sh
# entra no container cujo nome é "backend"
# esse nome existe porque no compose definimos:
# container_name: backend
```

---

## Parar tudo

```bash
docker compose down
# para e remove os containers, mas mantém volumes por padrão
```

---

## Parar e remover inclusive o volume do banco

```bash
docker compose down -v
# remove containers e também remove o volume postgres_data
# se remover o volume, os dados do banco serão perdidos
```

---

# 7. Atualizando só o backend

Suponha que você alterou código do backend.

## Comando

```bash
docker compose up -d --build backend
# sobe apenas o serviço backend
# --build força reconstrução da imagem dele
#
# Ele usa novamente o Dockerfile do backend
# e recria só o container backend
# db e frontend continuam como estão
```

### Ligação importante

Esse comando é o equivalente conceitual de:

1. rebuildar a imagem do backend
2. parar o container antigo do backend
3. subir um novo container com a nova imagem

Sem mexer no banco e no front.

---

# 8. E se eu quiser usar `image:` em vez de `build:`?

Também dá.

## Exemplo

```yaml
backend:
  image: meu-backend:v1
  container_name: backend
  ports:
    - "5000:3000"
  environment:
    DB_HOST: db
    DB_PORT: 5432
    DB_NAME: appdb
    DB_USER: appuser
    DB_PASSWORD: 123456
  networks:
    - app-net
```

Nesse caso, o Compose **não builda**.
Ele espera que a imagem `meu-backend:v1` já exista.

Ou seja, antes você precisaria rodar:

```bash
docker build -t meu-backend:v1 -f backend/Dockerfile ./backend
# cria a imagem antes
```

Depois:

```bash
docker compose up -d
# agora o compose só usa a imagem pronta
```

### Diferença mental

* `build:` = Compose constrói a imagem para você
* `image:` = Compose só usa uma imagem já pronta

---

# 9. Onde entra o `--build-arg` nisso tudo?

Boa conexão com o que você pediu.

## Dockerfile do backend com ARG

```dockerfile
FROM node:20-alpine

WORKDIR /app

ARG APP_VERSION=dev
# ARG existe no momento do build, não necessariamente em runtime

COPY package*.json ./
RUN npm install
COPY . .

RUN echo "Versão do app: $APP_VERSION"

EXPOSE 3000
CMD ["npm", "start"]
```

## Build manual com build-arg

```bash
docker build \
  -t meu-backend:v2 \
  -f backend/Dockerfile \
  --build-arg APP_VERSION=2.0.0 \
  ./backend
# --build-arg APP_VERSION=2.0.0
# envia valor para o ARG APP_VERSION do Dockerfile
```

### Ligação importante

Esse `--build-arg` conversa diretamente com:

```dockerfile
ARG APP_VERSION=dev
```

---

## No Compose, o equivalente é:

```yaml
backend:
  build:
    context: ./backend
    dockerfile: Dockerfile
    args:
      APP_VERSION: "2.0.0"
```

### Tradução

* `docker build --build-arg APP_VERSION=2.0.0`
* vira
* `build.args.APP_VERSION: "2.0.0"`

---

# 10. Resumo mental final

Pensa assim:

## Dockerfile

Responde: **como construir a imagem?**

Exemplo:

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]
```

---

## docker build

Responde: **quero transformar esse Dockerfile em imagem**

Exemplo:

```bash
docker build -t meu-backend:v1 -f backend/Dockerfile ./backend
```

---

## docker run

Responde: **quero criar e rodar um container usando essa imagem**

Exemplo:

```bash
docker run -d --name backend -p 5000:3000 meu-backend:v1
```

---

## docker-compose.yml

Responde: **quero definir vários serviços juntos**

Exemplo:

* banco
* backend
* frontend
* rede
* volume

---

## docker compose up

Responde: **quero subir tudo que foi definido no compose**

Exemplo:

```bash
docker compose up -d --build
```

---

# Fluxo completo em uma linha lógica

1. você escreve o **Dockerfile**
2. o Docker gera uma **imagem**
3. a imagem vira um **container** quando executada
4. o Compose organiza **vários containers**
5. rede + volume + portas fazem eles funcionarem juntos

---

Se quiser, no próximo passo eu posso montar esse mesmo exemplo com um **backend mínimo em Node e um frontend mínimo de verdade**, para você ver inclusive como ficariam os arquivos `package.json` e como o backend acessaria o Postgres.
