* Tags são rótulos que apontam para imagens (ex: `meu-backend:v1`)
* Você define no build: `docker build -t nome:tag .`
* Pode ter várias tags para a mesma imagem
* Nova tag: `docker tag nome:v3 nome:latest`
* O comando `docker tag` cria **uma tag por vez**

    docker tag meu-backend:v3 meu-backend:azul
    docker tag meu-backend:v3 meu-backend:latest

    Aqui o docker usa a versão meu-backend:v3 para criar uma tag adiconal para ela, a meu-backend:azul.
    E depois o mesmo com meu-backend:latest.

* `latest` não é especial, é só convenção
* Atualizar `latest` faz ela apontar para a nova imagem/tag
* Versionamento (`v1`, `v2`, `v3`) é manual pelo dev


