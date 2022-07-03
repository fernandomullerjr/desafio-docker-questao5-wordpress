
# Questão 05 - Desafio Wordpress

Chegou um cliente pra você que possui todas as suas aplicações em data centers e a gestão dessas aplicações está cada vez mais complexa então pra iniciar um plano de
gestão unificada e migração pra um ambiente cloud, as aplicações serão migradas pra containers. E hoje você precisa iniciar esse processo com um projeto piloto, o portal de
conteúdos da empresa construido em Wordpress. Então hoje sua missão é criar esse ambiente wordpress pronto para a equipe de publicidade começar a popular.

## Wordpress

O Wordpress é uma aplicação CMS (Content Management System) que é usado para administrar sites, blogs, lojas virtuais, portais de notícia, áreas de membros e outros tipos de página.

## Criando a aplicação em uma única linha de comando

Para criação da aplicação (site e banco de dados) vamos utilizar o *Docker Compose*, onde vamos criar e executar o container da aplicação e criar e executar o container com o banco de dados da aplicação.

Foi criado, dentro do diretório, um arquivo chamado `docker-compose` contendo o script *yaml* de criação dos objetos necessários para execução da aplicação e do banco de dados, como mostrado abaixo:

~~~~YAML
version: '3'

services:
  db:
    image: mysql:8.0
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_DATABASE=wordpress
    volumes:
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network

  wordpress:
    depends_on:
      - db
    image: wordpress:php8.1-fpm-alpine
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=$MYSQL_USER
      - WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network

  webserver:
    depends_on:
      - wordpress
    image: nginx:1.23.0-alpine
    container_name: webserver
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d
    networks:
      - app-network

volumes:
  wordpress:
  dbdata:

networks:
  app-network:
    driver: bridge  
~~~~

## Realizando teste com o *Docker Compose*

Para realização dos testes com script configurado no arquivo `docker-compose`, executamos o seguinte comando:

    docker-compose up -d

> Esse comando é para criar todos os objetos configurados no arquivo `docker-compose`.
Após a execução do comando teremos os seguintes containers em execução:

~~~~bash
$ docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS         PORTS                               NAMES
29d3185ec5e7   nginx:1.23.0-alpine           "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   webserver
bb0a51b82cea   wordpress:php8.1-fpm-alpine   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   9000/tcp                            wordpress
a4851ac1609d   mysql:8.0                     "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   3306/tcp, 33060/tcp                 db
~~~~

A aplicação pode ser acessada através da URL:
<http://localhost:80>
