Sim — aqui vai um **guia direto com a sintaxe essencial dos comandos Docker** (os que você realmente usa no dia a dia).

---

# 🐳 Estrutura geral

```bash
docker [comando] [subcomando] [opções]
```

---

# 📦 1. Imagens

### Buildar imagem

```bash
docker build -t nome:tag caminho/
```

### Listar imagens

```bash
docker images
```

### Remover imagem

```bash
docker rmi nome:tag
```

---

# 🚀 2. Containers

### Rodar container

```bash
docker run [opções] imagem
```

### Exemplo completo

```bash
docker run -d \
  --name meu-container \
  -p 5000:3000 \
  -v meu_volume:/app/data \
  --network minha-rede \
  -e NODE_ENV=production \
  imagem:tag
```

### Flags principais

* `-d` → modo background
* `--name` → nome do container
* `-p host:container` → porta
* `-v` → volume
* `--network` → rede
* `-e` → variável de ambiente

---

### Listar containers

```bash
docker ps        # ativos
docker ps -a     # todos
```

### Parar container

```bash
docker stop nome
```

### Iniciar container

```bash
docker start nome
```

### Remover container

```bash
docker rm nome
```

---

# 🔧 3. Execução e debug

### Entrar no container

```bash
docker exec -it nome bash
```

### Ver logs

```bash
docker logs nome
```

---

# 🌐 4. Redes

### Criar rede

```bash
docker network create nome
```

### Listar redes

```bash
docker network ls
```

---

# 💾 5. Volumes

### Criar volume

```bash
docker volume create nome
```

### Listar volumes

```bash
docker volume ls
```

---

# 📤 6. Docker Compose

### Subir tudo

```bash
docker compose up
```

### Subir em background

```bash
docker compose up -d
```

### Rebuild

```bash
docker compose up -d --build
```

### Parar tudo

```bash
docker compose down
```

---

# 🧹 7. Limpeza

### Remover tudo não usado

```bash
docker system prune
```

---

# 🎯 RESUMO PRÁTICO

* `build` → cria imagem
* `run` → cria + executa container
* `ps` → lista
* `stop/start` → controla
* `exec` → entra no container
* `compose up` → sobe tudo

---

Se quiser, posso te montar um **fluxo real de desenvolvimento (build → run → update → deploy)** no padrão profissional.

---------------------------------------
Perfeito — agora vou te mostrar **sintaxe completa + exemplos comentados linha a linha** (foco em aprendizado real).

---

# 🐳 1. `docker build`

### Sintaxe

```bash
docker build [OPTIONS] PATH | URL | -
```

### Exemplo comentado

```bash
docker build \
  -t meu-backend:v1 \                # nomeia a imagem como "meu-backend" com tag "v1"
  -f Dockerfile.prod \              # usa um Dockerfile específico (não o padrão)
  --build-arg NODE_ENV=production \ # passa variável para o build (usada no Dockerfile)
  --build-arg API_URL=https://api.meusite.com \ # outra variável de build
  --no-cache \                      # ignora cache (força rebuild completo)
  --target production \             # usa estágio específico (multi-stage build)
  .                                 # caminho do contexto (diretório atual)
```

---

# 🚀 2. `docker run`

### Sintaxe

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

### Exemplo comentado

```bash
docker run -d \                     # roda em background (detached)
  --name backend \                 # nome do container
  -p 5000:3000 \                  # mapeia porta (host:container)
  -v dados_app:/app/data \        # monta volume para persistência
  --network app-net \             # conecta à rede Docker
  -e NODE_ENV=production \        # variável de ambiente
  -e DB_HOST=db \                 # variável usada para conexão com banco
  --restart always \              # reinicia automaticamente se cair
  meu-backend:v1                  # imagem que será executada
```

---

# 📦 3. `docker ps`

```bash
docker ps -a -q
# -a → mostra todos (inclusive parados)
# -q → mostra só os IDs (útil para scripts)
```

---

# ⛔ 4. `docker stop`

```bash
docker stop -t 10 backend
# -t 10 → aguarda 10 segundos antes de forçar parada
# backend → nome do container
```

---

# ▶️ 5. `docker start`

```bash
docker start backend
# inicia um container parado
```

---

# ❌ 6. `docker rm`

```bash
docker rm -f -v backend
# -f → força remoção (mata se estiver rodando)
# -v → remove volumes associados
# backend → container removido
```

---

# 🧱 7. `docker images`

```bash
docker images meu-backend:v1
# lista apenas a imagem específica
```

---

# 🗑️ 8. `docker rmi`

```bash
docker rmi -f meu-backend:v1
# -f → força remoção da imagem
```

---

# 🔍 9. `docker logs`

```bash
docker logs -f --tail 100 backend
# -f → acompanha logs em tempo real
# --tail 100 → mostra últimas 100 linhas
# backend → container
```

---

# 🧪 10. `docker exec`

```bash
docker exec -it backend bash
# -it → modo interativo com terminal
# backend → container
# bash → comando executado dentro do container
```

---

# 🌐 11. `docker network create`

```bash
docker network create \
  --driver bridge \                # tipo de rede (bridge é padrão)
  --subnet 172.18.0.0/16 \        # faixa de IP da rede
  --gateway 172.18.0.1 \          # gateway da rede
  app-net                         # nome da rede
```

---

# 🌐 12. `docker network connect`

```bash
docker network connect app-net backend
# conecta o container "backend" à rede "app-net"
```

---

# 💾 13. `docker volume create`

```bash
docker volume create --name dados_app
# cria volume chamado "dados_app"
```

---

# 💾 14. `docker volume inspect`

```bash
docker volume inspect dados_app
# mostra detalhes do volume (local físico, config)
```

---

# 🧩 15. `docker compose up`

```bash
docker compose up -d \
  --build \                       # reconstrói imagens antes de subir
  --force-recreate \             # recria containers mesmo sem mudança
  --no-deps backend              # sobe só o backend (ignora dependências)
```

---

# 🧩 16. `docker compose down`

```bash
docker compose down -v --remove-orphans
# -v → remove volumes
# --remove-orphans → remove containers não definidos no compose atual
```

---

# 🧩 17. `docker compose logs`

```bash
docker compose logs -f --tail=50 backend
# -f → acompanha logs
# --tail=50 → últimas 50 linhas
# backend → serviço específico
```

---

# 🧹 18. `docker system prune`

```bash
docker system prune -a --volumes -f
# -a → remove todas imagens não usadas
# --volumes → remove volumes não usados
# -f → não pede confirmação
```

---

# 🎯 RESUMO DIDÁTICO

* `build` → cria imagem (configuração do app)
* `run` → cria + executa container
* `exec` → entra no container
* `logs` → vê o que está acontecendo
* `network` → conecta containers
* `volume` → salva dados
* `compose` → automatiza tudo

---

Se quiser, posso montar um **cenário completo comentado (front + back + banco)** e te mostrar como cada comando entra no fluxo real.
