```yaml
# Ideia: portas internas podem ser iguais, independente de quantas redes existam

services:
  frontend:
    ports:
      - "5000:3000"        # host 5000 → frontend:3000

  backend:
    ports:
      - "5001:3000"        # host 5001 → backend:3000

  db:
    # sem ports → não exposto ao host
```

```text
# Comunicação interna (entre containers)
frontend → http://backend:3000   # mesma porta interna, sem conflito
backend  → http://db:3000        # idem

# Comunicação externa (host)
localhost:5000 → frontend
localhost:5001 → backend

# Regra-chave
- Portas internas podem repetir (isolamento por container)
- Portas do host NÃO podem repetir
- Redes NÃO interferem nisso (só controlam quem pode se comunicar)
```
