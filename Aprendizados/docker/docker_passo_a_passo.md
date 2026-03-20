Perfeito. Vamos fazer do jeito certo: **sem Compose por enquanto**, tudo **um por vez**, com **CLI** e **Docker Desktop**, e com a lógica de **criar → atualizar → deletar**.

## Visão geral do treino

Você vai criar:

1. **frontend Vue**
2. **backend NestJS**
3. **PostgreSQL**
4. **volume**
5. depois conectar tudo com **rede + `docker run`**

> Ideia-chave: em Docker você normalmente **não edita o container**; você **edita o código**, **rebuilda a imagem** e **recria o container**.

---

# 1) Frontend Vue

## 1.1 Criar a imagem do frontend

### Exemplo de `frontend/Dockerfile`

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "run", "dev", "--", "--host"]
```

> Aqui o front sobe em modo dev. Em produção, o ideal seria buildar e servir com Nginx.

---

## 1.2 Criar a imagem via CLI

Na pasta do frontend:

```bash
docker build -t meu-frontend:v1 .
```

### O que isso faz

* lê o `Dockerfile`
* cria a imagem `meu-frontend:v1`

---

## 1.3 Criar e iniciar o container via CLI

```bash
docker run -d --name frontend -p 5000:3000 meu-frontend:v1
```

### O que isso faz

* cria o container `frontend`
* roda em background
* host `5000` → container `3000`

---

## 1.4 Atualizar o frontend

Você **não edita o container**. Você:

1. edita o código Vue local
2. rebuilda a imagem
3. remove o container antigo
4. cria outro

### Rebuild

```bash
docker build -t meu-frontend:v2 .
```

### Remover o antigo

```bash
docker rm -f frontend
```

### Subir o novo

```bash
docker run -d --name frontend -p 5000:3000 meu-frontend:v2
```

---

## 1.5 Deletar frontend

### Remover container

```bash
docker rm -f frontend
```

### Remover imagem

```bash
docker rmi meu-frontend:v1
docker rmi meu-frontend:v2
```

---

## 1.6 Fazer isso no Docker Desktop

### Criar imagem

* Vá em **Images**
* você não costuma “buildar clicando” como fluxo principal; normalmente builda no terminal
* depois a imagem aparece ali

### Criar container

* em **Images**, encontre `meu-frontend:v1`
* clique em **Run**
* defina:

  * nome: `frontend`
  * porta host: `5000`
  * porta container: `3000`

### Atualizar

* rebuilda no terminal: `docker build -t meu-frontend:v2 .`
* em **Containers**, pare/remova `frontend`
* em **Images**, rode `meu-frontend:v2`

### Deletar

* remova o container em **Containers**
* remova a imagem em **Images**

---

# 2) Backend NestJS

## 2.1 Criar a imagem do backend

### Exemplo de `backend/Dockerfile`

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3001

CMD ["npm", "run", "start:dev"]
```

> Se seu Nest usar `3000`, ajuste. Aqui vou usar `3001` para diferenciar do front.

---

## 2.2 Criar imagem via CLI

Na pasta do backend:

```bash
docker build -t meu-backend:v1 .
```

---

## 2.3 Criar e iniciar container via CLI

```bash
docker run -d --name backend -p 5001:3001 meu-backend:v1
```

### Resultado

* host `5001` → backend `3001`

---

## 2.4 Atualizar backend

Edite o código local, depois:

```bash
docker build -t meu-backend:v2 .
docker rm -f backend
docker run -d --name backend -p 5001:3001 meu-backend:v2
```

---

## 2.5 Deletar backend

```bash
docker rm -f backend
docker rmi meu-backend:v1
docker rmi meu-backend:v2
```

---

## 2.6 No Docker Desktop

Mesma lógica do front:

* **Images** → ver imagem
* **Run** → criar container
* **Containers** → parar/remover
* **Images** → apagar imagens

---

# 3) PostgreSQL

Aqui normalmente você **não cria Dockerfile**, porque usa a imagem oficial pronta.

## 3.1 Criar container do Postgres via CLI

```bash
docker run -d \
  --name db \
  -e POSTGRES_DB=appdb \
  -e POSTGRES_USER=appuser \
  -e POSTGRES_PASSWORD=123456 \
  -p 5432:5432 \
  postgres:15
```

### O que isso faz

* usa a imagem pronta `postgres:15`
* cria banco `appdb`
* usuário `appuser`
* senha `123456`

---

## 3.2 “Editar” Postgres

Aqui cuidado: você normalmente **não edita o container do Postgres**.

Você pode:

* mudar variáveis e recriar o container
* criar scripts SQL de init
* usar volume para persistência

### Exemplo: trocar credenciais

Remova e recrie:

```bash
docker rm -f db
docker run -d \
  --name db \
  -e POSTGRES_DB=novo_db \
  -e POSTGRES_USER=novo_user \
  -e POSTGRES_PASSWORD=nova_senha \
  -p 5432:5432 \
  postgres:15
```

> Sem volume, ao recriar você perde os dados.

---

## 3.3 Deletar Postgres

```bash
docker rm -f db
```

Se quiser remover a imagem:

```bash
docker rmi postgres:15
```

---

## 3.4 No Docker Desktop

### Criar

* em **Images**, procure `postgres:15` se já existir
* se não existir, o mais comum é puxar rodando no terminal ou usando busca do Desktop
* clique em **Run**
* configure:

  * nome: `db`
  * portas: `5432:5432`
  * envs:

    * `POSTGRES_DB=appdb`
    * `POSTGRES_USER=appuser`
    * `POSTGRES_PASSWORD=123456`

### Atualizar

* pare/remova o container
* crie outro com as novas envs

### Deletar

* remova o container
* remova a imagem se quiser

---

# 4) Volume

O volume é separado do container.

## 4.1 Criar volume via CLI

```bash
docker volume create postgres_data
```

---

## 4.2 Ver volumes

```bash
docker volume ls
```

---

## 4.3 Usar volume no Postgres

```bash
docker run -d \
  --name db \
  -e POSTGRES_DB=appdb \
  -e POSTGRES_USER=appuser \
  -e POSTGRES_PASSWORD=123456 \
  -p 5432:5432 \
  -v postgres_data:/var/lib/postgresql/data \
  postgres:15
```

### O que isso faz

* tudo que o Postgres gravar em `/var/lib/postgresql/data`
* vai persistir no volume `postgres_data`

---

## 4.4 “Editar” volume

Você não edita volume como código.
Você pode:

* inspecionar
* montar em outro container
* apagar

### Inspecionar

```bash
docker volume inspect postgres_data
```

---

## 4.5 Deletar volume

Primeiro remova containers que usam o volume. Depois:

```bash
docker volume rm postgres_data
```

---

## 4.6 No Docker Desktop

* vá em **Volumes**
* crie, veja, delete por ali
* ao rodar o Postgres, associe o volume

---

# 5) Orquestrando um a um, sem Compose

Agora vamos conectar tudo manualmente.

## 5.1 Criar rede

```bash
docker network create app-net
```

---

## 5.2 Subir banco com rede + volume

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

---

## 5.3 Subir backend conectado ao banco

```bash
docker run -d \
  --name backend \
  --network app-net \
  -p 5001:3001 \
  -e DB_HOST=db \
  -e DB_PORT=5432 \
  -e DB_NAME=appdb \
  -e DB_USER=appuser \
  -e DB_PASSWORD=123456 \
  meu-backend:v1
```

### Leitura

* `DB_HOST=db` funciona porque o nome do container do banco é `db`
* ambos estão na rede `app-net`

---

## 5.4 Subir frontend

```bash
docker run -d \
  --name frontend \
  --network app-net \
  -p 5000:3000 \
  meu-frontend:v1
```

---

## 5.5 Ver tudo rodando

```bash
docker ps
```

---

## 5.6 Ver logs

Frontend:

```bash
docker logs frontend
```

Backend:

```bash
docker logs backend
```

Banco:

```bash
docker logs db
```

---

## 5.7 Atualizar só o backend

```bash
docker build -t meu-backend:v2 ./backend
docker rm -f backend
docker run -d \
  --name backend \
  --network app-net \
  -p 5001:3001 \
  -e DB_HOST=db \
  -e DB_PORT=5432 \
  -e DB_NAME=appdb \
  -e DB_USER=appuser \
  -e DB_PASSWORD=123456 \
  meu-backend:v2
```

---

## 5.8 Atualizar só o frontend

```bash
docker build -t meu-frontend:v2 ./frontend
docker rm -f frontend
docker run -d \
  --name frontend \
  --network app-net \
  -p 5000:3000 \
  meu-frontend:v2
```

---

## 5.9 Derrubar tudo

```bash
docker rm -f frontend backend db
```

Rede:

```bash
docker network rm app-net
```

Volume:

```bash
docker volume rm postgres_data
```

---

# 6) Como pensar esse processo

## Front e back

* você escreve/edita código local
* faz `docker build`
* sobe com `docker run`

## Banco

* geralmente usa imagem pronta
* configura com `-e`
* persiste com `-v`

## Volume

* guarda os dados fora do container

## Rede

* faz os containers se enxergarem por nome

---

# 7) Ordem prática recomendada

Sem Compose, suba nessa ordem:

1. volume
2. rede
3. banco
4. backend
5. frontend

---

# 8) Observação importante sobre “editar”

Em Docker, o fluxo certo quase sempre é:

* **editar fonte/config**
* **rebuildar**
* **recriar container**

Não é “entrar no container e mexer”, porque isso vira mudança descartável.

Se você quiser, no próximo passo eu transformo exatamente esse treino em uma **lista de exercícios práticos**, tipo “faça você mesmo”, para você executar e conferir.
