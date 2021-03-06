# Docker Workshop - Docker Compose

## Sections:

* [Install Docker Compose](#install-docker-compose)
* [What is Docker Compose](#what-is-docker-compose)
* [Docker Compose Commands](#docker-compose-commands)
* [docker-compose.yml](#docker\-compose\.yml)
* [Docker Compose build files](#docker-compose-build-files)
* [Run the Golang API](#run-the-golang-api)
* [Bread Crumb Navigation](#bread-crumb-navigation)

## Install Docker Compose
_________________________

Download and Install [Docker Compose](https://docs.docker.com/compose/install/)

Check that docker-compose is installed with this command:

```bash
$ docker-compose version
# If docker-compose is installed you should see this output
docker-compose version 1.14.0, build c7bdf9e
docker-py version: 2.3.0
CPython version: 2.7.12
OpenSSL version: OpenSSL 1.0.2j  26 Sep 2016
```

## What is Docker Compose
_________________________

* Compose is a tool for defining and running multi-container Docker applications.
* With Compose, you use a Compose file to configure your application's services.
* Then, using a single command, you create and start all the services from your configuration.
* To learn more about all the features of Compose see [the list of features](https://docs.docker.com/compose/overview/#features)

Using Compose is basically a three-step process:

1. Define your app’s environment with a `Dockerfile` so it can be reproduced anywhere.
2. Define the services that make up your app in `docker-compose.yml` so they can be run together in an isolated environment.
3. Lastly, run `docker-compose up` and Compose will start and run your entire app.

## Docker Compose Commands
_________________________

Check the available commands of Docker Compose. Type in your terminal:

<details>

```bash
$ docker-compose
# output like this
...
Usage:
  docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
  docker-compose -h|--help

Options:
  -f, --file FILE             Specify an alternate compose file (default: docker-compose.yml)
  -p, --project-name NAME     Specify an alternate project name (default: directory name)
  --verbose                   Show more output
  -v, --version               Print version and exit
  -H, --host HOST             Daemon socket to connect to

  --tls                       Use TLS; implied by --tlsverify
  --tlscacert CA_PATH         Trust certs signed only by this CA
  --tlscert CLIENT_CERT_PATH  Path to TLS certificate file
  --tlskey TLS_KEY_PATH       Path to TLS key file
  --tlsverify                 Use TLS and verify the remote
  --skip-hostname-check       Don't check the daemon's hostname against the name specified
                              in the client certificate (for example if your docker host
                              is an IP address)
  --project-directory PATH    Specify an alternate working directory
                              (default: the path of the Compose file)
...
```

</details>

* Whenever you don't remember a command, just type docker-compose
* For more information on a particular command type `docker-compose help COMMAND`
    * (e.g. `docker-compose help start`)

| Command | Reference | Description |
| --- | --- | --- |
| build | [Documentation](https://docs.docker.com/compose/reference/build/) | Build or rebuild services |
| bundle | [Documentation](https://docs.docker.com/compose/reference/bundle/) | Generate a Docker bundle from the Compose file |
| config | [Documentation](https://docs.docker.com/compose/reference/config/) | Validate and view the Compose file |
| create | [Documentation](https://docs.docker.com/compose/reference/create/) | Create services |
| down | [Documentation](https://docs.docker.com/compose/reference/down/) | Stop and remove containers, networks, images, and volumes |
| events | [Documentation](https://docs.docker.com/compose/reference/events/) | Receive real time events from containers |
| exec | [Documentation](https://docs.docker.com/compose/reference/exec/) | Execute a command in a running container |
| help | [Documentation](https://docs.docker.com/compose/reference/help/) | Get help on a command |
| images | No Documention | List images |
| kill | [Documentation](https://docs.docker.com/compose/reference/kill/) | Kill containers |
| logs | [Documentation](https://docs.docker.com/compose/reference/logs/) | View output from containers |
| pause | [Documentation](https://docs.docker.com/compose/reference/pause/) | Pause services |
| port | [Documentation](https://docs.docker.com/compose/reference/port/) | Print the public port for a port binding |
| ps | [Documentation](https://docs.docker.com/compose/reference/ps/) | List containers |
| pull | [Documentation](https://docs.docker.com/compose/reference/pull/) | Pull service images |
| push | [Documentation](https://docs.docker.com/compose/reference/push/) | Push service images |
| restart | [Documentation](https://docs.docker.com/compose/reference/restart/) | Restart services |
| rm | [Documentation](https://docs.docker.com/compose/reference/rm/) | Create services |
| run | [Documentation](https://docs.docker.com/compose/reference/run/) | Run a one-off command |
| scale | [Documentation](https://docs.docker.com/compose/reference/scale/) | Set number of containers for a service |
| start | [Documentation](https://docs.docker.com/compose/reference/start/) | Start services |
| stop | [Documentation](https://docs.docker.com/compose/reference/stop/) | Stop services |
| top | [Documentation](https://docs.docker.com/compose/reference/top/) | Display the running processes |
| unpause | [Documentation](https://docs.docker.com/compose/reference/unpause/) | Unpause services |
| up | [Documentation](https://docs.docker.com/compose/reference/up/) | Create and start containers |
| version | [Documentation](https://docs.docker.com/compose/reference/version/) | Show the Docker-Compose version information |

## docker-compose.yml
_________________________

The `docker-compose.yml` file is a [YAML](http://yaml.org/) file defining [services](https://docs.docker.com/compose/compose-file/#service-configuration-reference), [networks](https://docs.docker.com/compose/compose-file/#network-configuration-reference) and [volumes](https://docs.docker.com/compose/compose-file/#volume-configuration-reference). The default path for a Compose file is `./docker-compose.yml`.

A service definition contains configuration which will be applied to each container started for that service, much like passing command-line parameters to `docker run`. Likewise, network and volume definitions are analogous to `docker network create` and `docker volume create`.

Options specified in the `Dockerfile` (e.g., `CMD`, `EXPOSE`, `VOLUME`, `ENV`) are respected by default - you don’t need to specify them again in `docker-compose.yml`.


## Docker Compose build files
_________________________

We've already created a simple app in `code/guestbook-node` that uses node.js with express and redis.

1. Go to `code/guestbook-node` folder

2. Review `Dockerfile`:

```dockerfile
FROM golang:1.8.3

LABEL maintainer "marcelbelmont@gmail.com"

# Set Environment variables
ENV appDir /var/www/app
ENV appMain /var/www/app/main

# Set the work directory
RUN mkdir -p ${appDir}
ADD . ${appDir}
WORKDIR ${appDir}

RUN go build

RUN go build -o main .
CMD [${appMain}]
```

1. We use the base image of `golang:1.8.3`.
2. Sets `LABEL` of maintainer
3. The `Dockerfile` then creates the directory where our code will be stored, `/var/www/app`
4. Copies all the code we have in the host machine and runs the command that will keep the container running.
5. Builds Go binary
6. Then gives main execution command

* Review `docker-compose.yml`:

```yml
version: '3'
services:
  redis:
    image: redis:alpine
  api:
    build: .
    ports:
      - "80:3000"
    depends_on:
      - redis
```

* The `docker-compose.yml` file describes the services that make your app.
* In this example those services are an api and an in memory database.
* The compose file also describes which Docker images these services use, how they link together services
* The compose file also describes any volumes the services might need mounted inside the containers.
* Finally, the `docker-compose.yml` file describes which ports these services expose.
* See the docker-compose.yml [reference](https://docs.docker.com/compose/compose-file/)
* In this case, we defined two services, `redis` that uses `redis:alpine` and `api`, our golang app.
* We linked the two of them, and `api` depends on `redis` as you can see in `depends_on`.
* Also, our golang app listens to the port `3000` so we linked host's port 80 to the docker container 3000 port.


## Run the Golang API
_________________________

### Build the images

The docker-compose yml script can build all the images at once by running the command:

```bash
$ docker-compose build
```

The `docker-compose build` reads the `docker-compose.yml` file and builds all the services defined in there.

### Run a command against a service
We can run a one-time command against a service. For example, the following command starts the `api` service and runs `main` as its command.

```bash
$ docker-compose run api
```

Commands you use with `run` start in new containers with the same configuration as defined by the service's configuration. This means the container has the same volumes, links, as defined in the configuration file. There two differences though.

First, the command passed by `run` overrides the command defined in the service configuration.
For example, if the `api` service configuration is started with `go`, then `docker-compose run api sh` overrides it with `sh`.

The second difference is the `docker-compose run` command does not create any of the ports specified in the service configuration. This prevents the port collisions with already open ports.

### Start services

We can run `docker-compose up` that builds, (re)creates, starts, and attaches to containers for a service. Unless they are already running, this command also starts any linked services.

Type in your terminal:

```bash
$ docker-compose up
```

This instructs Compose to run the services defined in the `docker-compose.yml` in containers, using the `redis` image and the `api` service's image and configuration.

The docker-compose up command aggregates the output of each container. When the command exits, all containers are stopped.

If we want, we can run the containers in background with `-d` flag:

```bash
$ docker-compose up -d
```

At this point, your Node app should be running at port `8088` on your Docker host. If you are using a Docker Machine VM, you can use the `docker-machine ip MACHINE_NAME` to get the IP address.

### Logs

We can see the log output from services running:

```bash
$ docker-compose logs
```

If we want to review the logs of a specific service, e.g. `api`:

```bash
$ docker-compose logs web
```

### List containers

We can run `ps` like in `docker ps` to list containers and their status:

```bash
$ docker-compose ps
```

### Stop containers

```bash
$ docker-compose stop
```

Stops running containers without removing them. They can be started again with `docker-compose start`.

If we want we can stop only one container:

```bash
$ docker-compose stop web
```

### Start container

Starts existing containers for a service, e.g. `api`:

```bash
$ docker-compose start web
```

### Remove containers

```bash
$ docker-compose rm
```

The previous command removes __stopped__ service containers.

If we want to stop and remove them:

```bash
$ docker-compose down
```

## Bread Crumb Navigation
_________________________

Previous | Next
:------- | ---:
← [Dockerhub](../dockerhub/README.md) | [README](../../README.md) →
