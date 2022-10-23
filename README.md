# Okazanta Docker Image

This is the official repository of the [Docker image](https://hub.docker.com/r/okazantahq/docker/) for [Okazanta](https://github.com/Okazanta-core/Okazanta).

[Okazanta](https://github.com/Okazanta-core/Okazanta) is a beautiful and powerful open source status page system, a free replacement for services such as StatusPage.io, Status.io and others.

For full documentation, visit the [Installing Okazanta with Docker](https://docs.okazantahq.io/docs/get-started-with-docker) page.

# Supporting Okazanta

Okazanta is a BSD-3-licensed open source project. If you'd like to support future development, check out the [Okazanta Patreon](https://patreon.com/jbrooksuk) campaign.

# Quickstart

1. Clone this repository

  ```shell
  git clone https://github.com/Okazanta-core/Docker.git
  ```

2. Edit the docker-compose.yml file to specify your [ENV variables](/conf/.env.docker).


3. To build an image containing a specific Okazanta release, set the [`okazanta_ver` ARG in the docker-compose.yml](/docker-compose.yml)

  The *main* branch and *okazantahq/docker:latest* Docker automated build are a work in progress / development version of the upstream https://github.com/Okazanta-core/Okazanta project. As such, *main* or *latest* should not be used in a production environment as it can change at anytime.

  We strongly recommend specifying a stable [Okazanta Release](https://github.com/Okazanta-core/Okazanta/releases) at build time as mentioned above.

4. Build and run the image

  ```shell
  docker-compose build
  docker-compose up
  ```

5. `okazantahq/docker`  runs on port 8000 by default. This is exposed on host port 80 when using docker-compose.


6. Setup the APP_KEY

Whilst the container is up and running, find the name of the Okazanta container via `docker ps`.

Run `docker exec -i ID_OF_THE_CONTAINER php artisan key:generate`.

Replace `${APP_KEY:-null}` in `docker-compose.yml` with the newly generated Application key.

__Note:__ make sure you include `base64:` prefix. E.g. `base64:YOUR_UNIQUE_KEY`

Restart the Docker containers.


# Docker Hub Automated build

`okazantahq/docker` is available as a [Docker Hub Trusted Build](https://hub.docker.com/r/okazantahq/docker/).

For a full list of Okazanta versions released as Docker images  please see the [list of Docker hub tags](https://hub.docker.com/r/okazantahq/docker/tags/).

Please use a [tagged Okazanta Docker image release](https://github.com/Okazanta-core/Docker/releases) or one of the tagged builds from https://hub.docker.com/r/okazantahq/docker/tags/ with `docker pull okazantahq/docker:2.3.12`.

# Debugging

* The services such as Okazanta, supervisord, nginx, and php-fpm log to `stdout` and `stderr`, and are visible in the Docker runtime output. 

* Setting the `DEBUG` Docker environment variable within the `docker-compose.yml` file or at runtime to `true` will enable debugging of the container entrypoint init script.

# Testing

Pull requests must pass the [Bash Automated Testing System](https://github.com/sstephenson/bats) tests, which run on [Travis CI](https://travis-ci.org/Okazanta-core/Docker) via located in the [test](test) directory.

Use `make test` to manually run the tests.


# Development of Okazanta using this docker environment

1.  Clone the official repo of Okazanta-core/Docker:

  ```shell
  git clone https://github.com/Okazanta-core/Docker.git okazanta-docker
  cd okazanta-docker
  git tag -l
  git checkout $LATEST_TAG
  ```
2. Clone the official repo of Okazanta-core/Okazanta here and do composer install:

  ```shell
  git clone https://github.com/Okazanta-core/Okazanta.git
  ```

3. Setup the Okazanta project:

Note: This requires [Composer](https://getcomposer.org/) be installed on your Docker host.  

 ```shell
cd Okazanta
composer install
cp ../conf/.env.docker ./.env
cd ..
```

4. Edit the docker-compose.yml file to bind mount the repo as a volume:

  ```yaml
 okazanta:
    volumes:
      - ./Okazanta/:/var/www/html/
    ...  
  ```

5. Build and run the container:

  ```shell
  docker-compose up
  ```

6. Open new terminal and run the following commands after getting container name via `docker ps`:

  ```shell
  docker exec -i okazantadocker_okazanta_1  php artisan key:generate
  docker exec -i okazantadocker_okazanta_1  php artisan app:install
  ```
