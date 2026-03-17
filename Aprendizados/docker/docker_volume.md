O Docker cria automaticamente o volume postgres_data se ele não existir.

Ou seja, se não rodar esse comando:

    docker volume create postgres_data
    # cria um volume nomeado
    # este volume guardará os dados do PostgreSQL fora do ciclo de vida do container

antes de:

        docker run -d \
    --name db \
    --network app-net \
    -e POSTGRES_DB=appdb \
    -e POSTGRES_USER=appuser \
    -e POSTGRES_PASSWORD=123456 \
    -v postgres_data:/var/lib/postgresql/data \
    postgres:15
    # docker run                      -> cria e inicia container
    # -d                              -> roda em background
    # --name db                       -> nome do container será "db"
    # --network app-net               -> entra na rede app-net
    # -e ...                          -> variáveis de ambiente que a imagem postgres entende
    # -v postgres_data:...            -> usa o volume criado antes
    # postgres:15                     -> imagem usada; aqui é imagem pronta oficial

O trecho de código:

    -v postgres_data:/var/lib/postgresql/data

criará o volume nomeando-o como postgres_data.


