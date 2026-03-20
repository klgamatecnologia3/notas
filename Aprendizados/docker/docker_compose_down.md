Não.

`docker compose down`:

* ✅ Remove **containers**
* ✅ Remove **network** criada pelo compose
* ❌ **NÃO remove imagens** (por padrão)
* ❌ **NÃO remove volumes** (por padrão)

Se quiser remover tudo:

* Imagens: `docker compose down --rmi all`
* Volumes: `docker compose down -v`

👉 Completo:

```bash
docker compose down --rmi all -v
```
