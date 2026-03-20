Use:

`docker run --name postgres -e POSTGRES_PASSWORD=senha -p 5432:5432 -d postgres`

(opcional, com usuário e banco):
`docker run --name postgres -e POSTGRES_USER=user -e POSTGRES_PASSWORD=senha -e POSTGRES_DB=meubanco -p 5432:5432 -d postgres`
