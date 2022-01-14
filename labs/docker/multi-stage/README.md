# Multi-Stage Builds

Multi-stage builds use the standard Dockerfile syntax, with multiple stages separated with `FROM` commands. They give you a repeatable build with minimal dependencies.

You won't see multi-stage builds used everywhere, but they're a great way to centralize your toolset - developers and build servers just need Docker and the source code, all the tools come packaged in Docker images, so everyone's using the same versions.

## Reference

- [Multi-stage build docs](https://docs.docker.com/develop/develop-images/multistage-build/)

<details>
  <summary>Sample Dockerfiles</summary>

It's the standard `docker build` command for multi-stage builds. The Dockerfile syntax uses multiple `FROM` instructions; the patterns are the same for all languages, but the individual details are specific.

These are samples in the major languages:

Java app using Maven build
```
FROM maven:3.6.3-jdk-11 AS builder

WORKDIR /usr/src/api
COPY pom.xml .
RUN mvn -B dependency:go-offline

COPY . .
RUN mvn package

# app
FROM openjdk:11.0.11-jre-slim-buster

WORKDIR /app
COPY --from=builder /usr/src/api/target/products-api-0.1.0.jar .

EXPOSE 80
ENTRYPOINT ["java", "-jar", "/app/products-api-0.1.0.jar"]

ENV JRE_VERSION="11.0.11" \
    APP_VERSION="0.3.0"
```

Go application using Go modules
```
FROM golang:1.16.13-alpine AS builder

WORKDIR /go/banking
COPY banking/src/. .

RUN chmod +x build.sh  && ./build.sh

FROM alpine:3.14

ENV SERVER_ADDRESS=0.0.0.0 \
    SERVER_PORT=8000 \
    DB_USER=root \
    DB_PASSWD=student \
    DB_ADDR=customers-db \
    DB_PORT=3306 \
    DB_NAME=banking \
    AUTH_ENABLED=0

EXPOSE 8000

WORKDIR /app
COPY --from=builder /server .
CMD ["/app/server"]
```
</details><br/>

## Multi-Stage Dockerfiles

We'll start by using the original build engine so it's clear what's happening in the build - later we'll switch to BuildKit which has better performance:

```
# on macOS or Linux:
export DOCKER_BUILDKIT=0

# OR with PowerShell:
$env:DOCKER_BUILDKIT=0
```

Here's a [simple multi-stage Dockerfile](./simple/Dockerfile):

- the `base` stage uses Alpine and simulates adding some dependencies
- the `build` stage builds on the base and simulates an app build
- the `test` stage starts from the build output and simulates automated testing
- the final stage starts from base and copies in the build output

📋 Build an image called `simple` from the `labs/multi-stage/simple` Dockerfile.

<details>
  <summary>Not sure how?</summary>

```
# just a normal build:
docker build -t simple ./labs/multi-stage/simple/
```

</details><br/>

> All the stages run, but the final app image only has content explicitly added from earlier stages.

Run a container from the image and it prints content from the base and build stages:

```
docker run simple
```

> The final image doesn't have the additional content from the test stage.

# BuildKit

BuildKit is an alternative build engine in Docker. It's heavily optimized for multi-stage builds, running stages in parallel and skipping stages if the output isn't used.

Switch to BuildKit by setting an environment variable:

```
# on macOS or Linux:
export DOCKER_BUILDKIT=1

# OR with PowerShell: 
$env:DOCKER_BUILDKIT=1
```

Now repeat the build for the simple Dockerfile - this time Docker will use BuildKit:

```
docker build -t simple:buildkit ./labs/multi-stage/simple/
```

> You'll see output from different stages at the same time - and if you look closely you'll see the test stage is skipped.

📋 Run a container from the new image. Is the output the same? Compare the image details.

<details>
  <summary>Not sure how?</summary>

```
# run a container - the output is the same:
docker run simple:buildkit

# list images - they're the same size but not the same image:
docker image ls simple
```

</details><br/>

BuildKit skips the test stage because none of the output is used in later stages. 

📋 Run a container from the test build, printing the contents of the build.txt file.

<details>
  <summary>Not sure how?</summary>

```
# no output here - the test stage has no CMD instruction
docker run simple:test

# run the cat command to see the output
docker run simple:test cat /build.txt
```

</details><br/>

> The output is from the build stage plus the test stage.

___
## Cleanup

Cleanup by removing all containers:

```
docker rm -f $(docker ps -aq)
```