services:
  db:
    image: postgres:15
    # Aqui usamos uma imagem pronta da Docker Hub
    # Repare que NÃO há "build", porque não estamos construindo o banco via Dockerfile

    container_name: db
    # Nome explícito do container
    # É semelhante ao --name db do docker run manual

    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: 123456
    # Equivale aos vários -e do docker run

    volumes:
      - postgres_data:/var/lib/postgresql/data
    # Equivale ao -v postgres_data:/var/lib/postgresql/data

    networks:
      - app-net
    # Equivale ao --network app-net

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    # Aqui o Compose faz o mesmo papel deste comando:
    # docker build -t algum_nome -f backend/Dockerfile ./backend
    #
    # "context: ./backend" = o mesmo papel do caminho final no docker build
    # "dockerfile: Dockerfile" = qual Dockerfile usar dentro dessa pasta

    container_name: backend
    # Equivale ao --name backend

    ports:
      - "5000:3000"
    # Equivale ao -p 5000:3000
    # host 5000 -> container 3000

    environment:
      DB_HOST: db
      DB_PORT: 5432
      DB_NAME: appdb
      DB_USER: appuser
      DB_PASSWORD: 123456
    # Equivale aos -e do docker run
    # DB_HOST=db funciona porque backend e db estão na mesma rede

    depends_on:
      - db
    # Diz ao Compose para subir o db antes do backend
    # Não garante que o banco já está "pronto para uso", só a ordem de inicialização

    networks:
      - app-net
    # Equivale ao --network app-net

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    # Mesma lógica do backend:
    # isso substitui um docker build manual do frontend

    container_name: frontend
    # Equivale ao --name frontend

    ports:
      - "3000:80"
    # Equivale ao -p 3000:80

    depends_on:
      - backend
    # Sobe o backend antes do frontend

    networks:
      - app-net
    # Também entra na mesma rede

volumes:
  postgres_data:
  # Este bloco declara o volume nomeado usado no serviço db
  # É o equivalente lógico de "docker volume create postgres_data"

networks:
  app-net:
  # Declara a rede usada pelos serviços
  # É o equivalente lógico de "docker network create app-net"