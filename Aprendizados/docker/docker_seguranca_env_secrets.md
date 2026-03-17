Perfeito — aqui vão **3 formas** + nível de segurança 👇

---

# 🔴 1. `.env` (baixo nível de segurança)

### Exemplo `.env`

```env
DB_HOST=db
DB_USER=appuser
DB_PASSWORD=123456
```

### Uso (Node com dotenv)

```js
require('dotenv').config()

console.log(process.env.DB_PASSWORD)
```

### ⚠️ Problemas

* Pode ir pro Git sem querer
* Fica em texto puro no disco
* Qualquer pessoa com acesso lê

👉 **Segurança: BAIXA**

---

# 🟠 2. Variáveis com `-e` ou Compose (médio)

### Exemplo `docker run`

```bash
docker run -e DB_PASSWORD=123456 meu-backend
```

### Exemplo Compose

```yaml
environment:
  DB_PASSWORD: 123456
```

### Onde fica?

```bash
docker inspect nome-container
```

→ dá pra ver as variáveis

### ⚠️ Problemas

* Visível no Docker
* Pode aparecer em logs/scripts

👉 **Segurança: MÉDIA**

---

# 🟢 3. Docker Secrets (alto nível)

### Exemplo (Compose)

```yaml
services:
  backend:
    image: meu-backend
    secrets:
      - db_password

secrets:
  db_password:
    file: ./db_password.txt
```

### Dentro do container

```bash
/run/secrets/db_password
```

### Uso no código (Node)

```js
const fs = require('fs')
const password = fs.readFileSync('/run/secrets/db_password', 'utf8')
```

### ✔ Vantagens

* Não aparece em `docker inspect`
* Não fica em variável de ambiente
* Acesso controlado

👉 **Segurança: ALTA**

---

# 🧠 RESUMO

| Método         | Segurança | Uso comum             |
| -------------- | --------- | --------------------- |
| `.env`         | 🔴 Baixa  | Dev local             |
| `-e / Compose` | 🟠 Média  | Produção simples      |
| Secrets        | 🟢 Alta   | Produção profissional |

---

# 🎯 Regra prática

* Dev → `.env`
* Produção simples → `environment`
* Produção séria → **secrets**

---

Se quiser, posso te mostrar **como integrar isso com AWS (Secrets Manager)**, que é o próximo nível.
