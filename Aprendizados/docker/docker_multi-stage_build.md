Dockerfile:

# Etapa 1: build da aplicação
FROM node:20-alpine AS build

# Pasta de trabalho
WORKDIR /app

# Copia arquivos de dependência
COPY package*.json ./

# Instala dependências
RUN npm install

# Copia o restante do front
COPY . .

# Gera a versão final do front para produção
RUN npm run build

# Etapa 2: servidor web leve para servir os arquivos estáticos
FROM nginx:alpine

# Copia os arquivos gerados na etapa "build"
# para a pasta que o Nginx usa para servir site estático
COPY --from=build /app/dist /usr/share/nginx/html

# Expõe porta 80 do container
EXPOSE 80

# Inicia o Nginx em foreground
CMD ["nginx", "-g", "daemon off;"]

Build manual do frontend:

docker build -t meu-frontend:v1 -f frontend/Dockerfile ./frontend
# este comando cria a imagem "meu-frontend:v1"
# ela será usada depois por docker run ou docker compose

Aqui você viu um multi-stage build:

    a etapa build compila o front
    a etapa final usa Nginx só para servir os arquivos gerados

Isso reduz o tamanho da imagem final e deixa produção mais limpa.