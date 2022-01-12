# Building Container Images

*Images* are the packages which containers run from. You'll build an image for each component of your application, and the image has all the pre-requisites installed and configured, ready to run.

You can think of images as the filesystem for the container, plus some metadata which tells Docker which command to run when the container starts.

## Base images

Images are built in a hierarchy - you may start with an OS image which sets up a particular Linux distro, then build on top of that to add your application runtime.

Before we build any images we'll set the Docker to use the original build engine:

```
# on macOS or Linux:
export DOCKER_BUILDKIT=0

# OR with PowerShell:
$env:DOCKER_BUILDKIT=0
```

> [BuildKit](https://docs.docker.com/develop/develop-images/build_enhancements/) is the new Docker build engine. It produces the same images as the original engine, but the printed output doesn't show the Dockerfile instructions being executed. We're using the original Engine so you can see all the steps.

We'll build a really simple base image:

- [base image Dockerfile](./base/Dockerfile)

```
docker build -t my-base-image -f labs/docker/images/base/Dockerfile labs/docker/images/base
```

- `-t` or `--tag` gives the image a name
- you end the build command with the path to the Dockerfile folder
- the `-f` flag specifies the Dockerfile name - you need it if you're not using the standard name
- the folder path is called the *context* - it contains the Dockerfile and any files it references

</details><br/>

> These are the images stored in your local Docker engine cache.

The new base image doesn't add anything to the [official Ubuntu image](https://hub.docker.com/_/ubuntu), which is available in lots of different [versions](https://hub.docker.com/_/ubuntu?tab=tags&page=1&ordering=last_updated).


## Commands and entrypoints

The Dockerfile syntax is straightforward to learn:

- every image needs to start `FROM` another image
- you use `RUN` to execute commands as part of the build
- and `CMD` sets the command to run when the container starts

> Basic instructions in the Dockerfile

| Syntax      | Description |
| ----------- | ----------- |
| FROM        | Pull the base image       |
| RUN         | Execute command        |
| CMD         | Provide default command for the executing container        |
| WORKDIR     | Sets the working directory        |
| COPY        | Copy a directory/file from your local machine to the docker image        |
| ADD         | Same as COPY but also allows <src> as URL        |
| EXPOSE      | Exposes the port of the docker container where the application runs        |
| ENV         | Sets the environment variables        |


## Reference

- [Dockerfile syntax](https://docs.docker.com/engine/reference/builder/)
- [Image build command](https://docs.docker.com/engine/reference/commandline/image_build/)

### Let's start with a simple example:

There's a simple Java app in this folder which has already been compiled into the file `java/HelloWorld.class`.

Build a Docker image which packages that app, and run a container to confirm it's working. The command your container needs to run is `java HelloWorld`.

<details>
  <summary>Not sure how?</summary>

Follow the steps below:

create _Dockerfile_ at _labs/docker/images/_

```
FROM openjdk:8-jre-alpine

# creating a working directory called `/app` in the container filesystem, and copying the Java class file.
WORKDIR /app

COPY java/HelloWorld.class .

CMD java HelloWorld
```

Build the image

```
docker build -t java-hello-world -f labs/docker/images/Dockerfile labs/docker/images
```

Run a container from the image:

```
docker run java-hello-world
```

> The output should say `Hello, World`


</details><br>

## Lab

Your turn to write a Dockerfile. 

Write a Dockerfile to build an image for Tomcat version 9.

| Instruction      | Command |
| ----------- | ----------- |
| Pull Ubuntu:18.04 as base image        | FROM       |
| Install OpenJDK-11              | RUN        |
| Create working directory at /opt/tomcat         | WORKDIR        |
| Download tomcat package from https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.56/bin/apache-tomcat-9.0.56.tar.gz     | ADD        |
| Extract tomcat package        | RUN  |
| Move the extracted contents to /opt/tomcat         | RUN |
| Expose port 8080      | EXPOSE|
| Set the default command to run tomcat when the container starts          | CMD |

> Stuck? Try [hints](hints.md) or check the [solution](solution.md).

___
## Cleanup

Cleanup by removing all containers:

```
docker rm -f $(docker ps -aq)
```