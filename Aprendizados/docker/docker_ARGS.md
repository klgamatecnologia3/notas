* `ARG` = variável usada **só no build** (não existe em runtime)
* Definida no Dockerfile e passada no `docker build`

```dockerfile
ARG NODE_VERSION=20
FROM node:${NODE_VERSION}-alpine   # usa a variável no build
```

```bash
docker build -t app:v1 --build-arg NODE_VERSION=18 .
# passa valor para o ARG durante o build
```

* Sem `ARG` (fixo):

```dockerfile
FROM node:20-alpine
```

* ⚠️ Não usar para segredos
* ✔ Use para versões/configs de build

---

A do **comando de build** tem prioridade.
O valor passado em `--build-arg` sobrescreve o padrão definido no Dockerfile.
