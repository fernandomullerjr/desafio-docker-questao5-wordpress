
# Questão 05 - Desafio Wordpress

Chegou um cliente pra você que possui todas as suas aplicações em data centers e a gestão dessas aplicações está cada vez mais complexa então pra iniciar um plano de
gestão unificada e migração pra um ambiente cloud, as aplicações serão migradas pra containers. E hoje você precisa iniciar esse processo com um projeto piloto, o portal de
conteúdos da empresa construido em Wordpress. Então hoje sua missão é criar esse ambiente wordpress pronto para a equipe de publicidade começar a popular.

## Wordpress

O Wordpress é uma aplicação CMS (Content Management System) que é usado para administrar sites, blogs, lojas virtuais, portais de notícia, áreas de membros e outros tipos de página.

## Criando a aplicação em uma única linha de comando

Para criação da aplicação (site e banco de dados) vamos utilizar o *Docker Compose*, onde vamos criar e executar o container da aplicação e criar e executar o container com o banco de dados da aplicação.

Foi criado, dentro do diretório, um arquivo chamado `docker-compose` contendo o script *yaml* de criação dos objetos necessários para execução da aplicação e do banco de dados, como mostrado abaixo:

    version: '3.8'
    
    services:
      wordpress:
        container_name: wordpress-app
        image: wordpress:5.8.3
        restart: always
        depends_on:
          - db
        ports:
          - 8080:80
        networks:
          - wordpress_network
        volumes:
          - wordpress_volume:/var/www/html
        environment:
          WORDPRESS_DB_HOST: wordpress-mariadb
          WORDPRESS_DB_USER: wordpressuser
          WORDPRESS_DB_PASSWORD: wordpresspwd
          WORDPRESS_DB_NAME: wordpress
    
      db:
        container_name: wordpress-mariadb
        image: mariadb:10.7.1
        ports: 
          - 3306:3306
        networks:
          - wordpress_network
        volumes:
          - mariadb_volume:/var/lib/mysql
        environment:
          MARIADB_DATABASE: wordpress
          MARIADB_USER: wordpressuser
          MARIADB_PASSWORD: wordpresspwd
          MARIADB_ROOT_PASSWORD: wordpressrootpwd
    
    volumes:
      wordpress_volume:
      mariadb_volume:
    
    networks:
      wordpress_network:
        driver: bridge

## Realizando teste com o *Docker Compose*

Para realização dos testes com script configurado no arquivo `docker-compose`, executamos o seguinte comando:

    docker-compose up -d

> Esse comando é para criar todos os objetos configurados no arquivo `docker-compose`.
Após a execução do comando teremos os seguintes containers em execução:

~~~~bash
fernando@debian10x64:~/cursos/kubedev/aula56-Desafio-Docker/questao5/desafio-docker-questao5-wordpress$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                                       NAMES
c45c076c36d7   wordpress:5.8.3       "docker-entrypoint.s…"   15 minutes ago   Up 15 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp       wordpress-app
eddf86294f5f   mariadb:10.7.1        "docker-entrypoint.s…"   21 minutes ago   Up 15 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   wordpress-mariadb
4e0cc1b8a495   portainer/portainer   "/portainer"             5 days ago       Up 2 hours      0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   frosty_easley
fernando@debian10x64:~/cursos/kubedev/aula56-Desafio-Docker/questao5/desafio-docker-questao5-wordpress$
~~~~

A aplicação pode ser acessada através da URL:
http://localhost:8080



