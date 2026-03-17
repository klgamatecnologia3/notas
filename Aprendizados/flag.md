* **Flag** = parâmetro que controla o comportamento de um programa

**CLI (linha de comando):**

```bash
docker run -d --name app -e DEBUG=true imagem
# -d (booleano), --name (valor), -e (configuração)
```

**Código/script:**

```js
const DEBUG = true;        // flag interna
if (DEBUG) console.log("debug ativo");
```

👉 Resumo: flag = “chave de controle” (externa via CLI ou interna no código)

---

Um número em `soma(n1, n2)` não é uma flag.
Ele é um **argumento (dado de entrada)**, enquanto flag é um **controle de comportamento** (ex: ativar debug, modo verbose).
