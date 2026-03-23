**Prós**

* sobe mais rápido
* evita reinstalar dependências toda hora
* melhora muito o fluxo de desenvolvimento
* reduz uso de rede e processamento em builds repetidos

**Contras**

* pode “mascarar” mudanças e parecer que o Docker “ignorou” algo
* imagem pode ficar desatualizada sem você perceber
* aumenta uso de disco
* às vezes dificulta debug, porque você acha que está testando código novo, mas o build reutilizou camada antiga

**Na prática**
Cache é ótimo quase sempre.
O problema não é usar cache, e sim **não saber quando invalidar**.

Use cache no dia a dia.
Limpe ou force rebuild quando:

* mudou `Dockerfile`
* mudou dependências
* comportamento ficou estranho
* parece que a imagem não refletiu seu código

Comandos úteis:

```bash
docker compose build --no-cache
docker compose up --build
```

Resumo: **cache ajuda desempenho; atrapalha quando esconde alteração.**
