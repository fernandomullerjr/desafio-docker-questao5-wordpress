# Desafio Wordpress

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

    CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                    NAMES
    ef329f97e641   wordpress:5.8.3   "docker-entrypoint.s…"   54 minutes ago   Up 53 minutes   0.0.0.0:8080->80/tcp     wordpress-app
    7b01c2d6d511   mariadb:10.7.1    "docker-entrypoint.s…"   54 minutes ago   Up 54 minutes   0.0.0.0:3306->3306/tcp   wordpress-mariadb

Quando a aplicação for acessada através da URL http://localhost:8080teremos o seguinte resultado:

![Página de teste](https://github.com/nossadiretiva/imagens/blob/master/teste_wordpress.png?raw=true)

## Banco de dados

Após a execução da configuração inicial do Wordpress o banco de dados ao qual ele está conectado fica como mostrado na imagem abaixo:

![Banco de dados da aplicação](https://github.com/nossadiretiva/imagens/blob/master/database_wordpress.png?raw=true)