* Compose inicia serviços quase em paralelo; ordem não garante “pronto”
* `depends_on` define ordem de start, não de disponibilidade
* `healthcheck` define quando o serviço está realmente pronto
* `condition: service_healthy` faz outro serviço esperar de verdade
* Backend deve esperar DB saudável (boa prática)
* Frontend geralmente não precisa disso
* Use no front só em SSR/BFF ou dependência no startup
* Mesmo assim, apps devem ter retry de conexão


Exemplo:

```yaml
services:
  db:
    image: postgres:15

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser -d appdb"]
      # banco só é considerado pronto quando aceitar conexão de verdade
      interval: 5s
      timeout: 3s
      retries: 10

  backend:
    build: ./backend

    healthcheck:
      test: ["CMD-SHELL", "wget --spider -q http://localhost:3000/health || exit 1"]
      # opcional, mas recomendado: testa se a API backend está respondendo
      interval: 5s
      timeout: 3s
      retries: 10

    depends_on:
      db:
        condition: service_healthy
    # backend só sobe depois que o banco estiver saudável

  frontend:
    build: ./frontend

    healthcheck:
      test: ["CMD-SHELL", "wget --spider -q http://localhost:80 || exit 1"]
      # opcional: útil se o front for SSR, Next.js, BFF ou tiver servidor próprio
      # para front estático simples, geralmente não é necessário
      interval: 5s
      timeout: 3s
      retries: 10

    depends_on:
      backend:
        condition: service_healthy
    # opcional: faz sentido se o frontend precisar que o backend já esteja saudável no startup
    # para front estático comum, normalmente bastaria nem usar depends_on
```
