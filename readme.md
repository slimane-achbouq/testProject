<br />
<p align="center">
  <img width="350px" src="https://i.ibb.co/qD0jywm/docker-inside.png" />
</p>

# Intranet Inside Docker

Run intranet inside instances using docker.

## Services

- PHP7.3-FPM
- MariaDB 10.7
- Adminer (http://localhost:8030)
- Redis Alpine (latest)
- MailHog (http://localhost:8025)
- Solr 7.7.2 (http://localhost:8983)
- Apache2 (http://localhost:1337)

## Requirements

- Docker & Docker-compose.
- Ssh key in host machine (~/.ssh/id_rsa) that has permissions to pull dependencies.

## Installation
1. Clone the repository :
```sh
$ git clone git@bitbucket.org:maecia/demo.git
$ cd demo
```

2. build services and Create/Start containers :<br />

Put .docker folder inside the instance directory, folders structure :

```text
demo/
├─ .docker/
│ ├─ apache/
│ │   ├─ Dockerfile
│ │   └─ vhost.conf
│ ├─ php/
│ │   ├─ Dockerfile
│ │   └─ php.ini
│ ├─ solr/
│ │   └─ core
| ├─ data/
| ├─ .env
| ├─ docker-compose.yml
│ └─ Makefile
│ ...
└─ composer.json
```

update docker .env variables :

<p align="center">
    <img src="https://i.ibb.co/RBcjQkk/carbon-1.png" alt="carbon-1" border="0">
</p>

from inside .docker folder run this commands :
```sh
$ docker-compose up --build -d
$ docker ps
```

check if all of your containers is running :

<p align="center">
    <img src="https://i.ibb.co/6Pn3ntm/carbon.png" alt="carbon" border="0">
</p>

now we need to go inside our php container and install the dependencies of our instance :
```sh
$ make ssh
$ composer install
```

to install front dependencies we need to login with npm to maecia-guest account :

```sh
$ cd front
$ npm login
$ npm i && npm run build
```

back to the app directory inside the container and generate a new key :

```sh
$ cd ..
$ php artisan key:generate
```

update .env of our application :

<p align="center">
    <img src="https://i.ibb.co/wrrTDNj/carbon-2.png" alt="carbon-2" border="0">
</p>

from inside the container run :

```sh
$ php artisan pm2:config
$ pm2 start process.yml
```

our application is ready now we need to import a dump of database, from outside of container in .docker folder run :

```sh
$ make import-db file=(PATH_TO_THE_DATABASE_FILE)
```

and dont forget to update storage folder

run this commands in docker container to rebuild models & update solr indexation :

```sh
$ php artisan models:rebuild
$ php index:rebuild
```

<p align="center">
    <img src="https://i.ibb.co/ns4s1rP/screely-1655756297803.png" alt="screely-1655756297803" border="0">
</p>
