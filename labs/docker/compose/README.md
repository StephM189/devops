
# Running a real application

In this exercise you will be running a real application. There are three components of the application and each has its own Docker image.

Here's the application architecture:

![](/img/banking-application-architecture.png)

- Customers database - a MySql database, built with some sample data. <b>Image:</b> `thecodecamp/customers-db:latest`

- Banking API - a Go REST API application that reads data from Customers database. <b>Image:</b> `thecodecamp/banking-api:latest`

- Banking UI - a jQuery based website that used Banking API to show data. <b>Image:</b> `thecodecamp/banking-ui:latest`

🥅 Goals

- Verify you can run a container from each image

- The containers should start - most will print application logs, one will exit with errors, that's OK

<details>
  <summary>Not sure how?</summary>

Follow the steps below:

_Database_

```
docker run --rm thecodecamp/customers-db:latest

# you should see log entries about tables being created and the database being ready to accept connections

# ctrl-c to exit
# if ctrl-c doesn't work, below command will stop and remove all running containers

docker rm -f $(docker ps -aq)
```

_Banking API_

```
docker run --rm thecodecamp/banking-api:latest

# you should see log entries for the app starting on port 8000

# ctrl-c to exit
```

_Banking UI_

```
docker run --rm thecodecamp/banking-ui:latest

# you should see log entries for the app starting

# then the app errors because it can't find the api-server and the app exits - this is OK

```

</details><br/>

___
## Cleanup

Cleanup by removing all containers:

```
docker rm -f $(docker ps -aq)
```
<br>

___

# Docker Compose

Docker Compose is a specification for describing apps which run in containers, and a command-line tool which takes those specs and runs them in Docker.

It's a _desired-state_ approach, where you model your apps in YAML and the Compose command line creates or replaces components to get to your desired state.

## Reference

- [Docker Compose manual](https://docs.docker.com/compose/)
- [Compose specification - GitHub](https://github.com/compose-spec/compose-spec/blob/master/spec.md)
- [Docker Compose v3 syntax](https://docs.docker.com/compose/compose-file/compose-file-v3/)


<details>
  <summary>CLI overview</summary>

The original Docker Compose CLI is a separate tool:

```
docker-compose --help

docker-compose up --help
```

</details><br/>

## Multi-container apps

Docker can run any kind of app - the container image could be for a lightweight microservice or a legacy monolith. They all work in the same way, but containers are especially well suited to distributed applications, where each component runs in a separate container.

When you tried running `banking-ui` sample app, it failed because it requires `banking-api`:

Instead you can use Docker Compose to model all containers.

## Compose app definition

Docker Compose can define the services of your app - which run in containers - and the networks that join the containers together.

You can use Compose even for simple apps - this just defines an Nginx container:

- [docker-compose.yml](./app/docker-compose.yml)

> Why bother putting this in a Compose file? It specifies an image version and the ports to use; it acts as project documentation, as well as being an executable spec for the app.

Docker Compose has its own command line - this tells you the available commands:

```
docker-compose
```

📋 Run this application using the `docker-compose` CLI.

<details>
  <summary>Not sure how?</summary>

```
# run 'up' to start the app, pointing to the Compose file
docker-compose -f labs/docker/compose/app/docker-compose.yml up
```

</details><br/>

> The Banking application containers starts in interactive mode; you can browse to http://localhost:8082 to check it's working.

Use Ctrl-C / Cmd-C to exit - that stops the container.

## Multi-container apps in Compose

Compose is more useful with more components. [app/docker-compose.yml](./app/docker-compose.yml) defines the three parts of the banking app:

- there are two services, one for the API and one for the web
- each service defines the image to use and the ports to expose
- the banking-api web service adds an environment variable to configure AUTH
- all services and db are set to connect to the same container network
- the network is defined but it has no special options set.

📋 Run the app using detached containers and use Compose to print the container status and logs.

<details>
  <summary>Not sure how?</summary>

```
# run the app:
docker-compose -f labs/docker/compose/app/docker-compose.yml up -d

# use compose to show just this app's containers:
docker-compose -f labs/docker/compose/app/docker-compose.yml ps

# and this app's logs:
docker-compose -f labs/docker/compose/app/docker-compose.yml logs

```

</details><br/>

These are just standard containers - the Compose CLI sends commands to the Docker engine in the same way that the usual Docker CLI does.

You can manage containers created with Compose using the Docker CLI too:

```
docker ps
```

Finally shutdown all containers
```
# Shutdown all containers started with this compose file:
docker-compose -f labs/docker/compose/app/docker-compose.yml down
```
___
## Cleanup

Cleanup by removing all containers:

```
docker rm -f $(docker ps -aq)
```