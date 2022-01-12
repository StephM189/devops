# Lab Solution

Here's a sample solution:

Copy the contents to _lab/Dockerfile_

```
# Pull the base image as ubuntu from Dockerhub
FROM ubuntu:18.04

# update cache and install openjdk 11
RUN apt-get update && \
    apt-get install openjdk-11-jdk -y

# creating a working directory /opt/tomcat
WORKDIR /opt/tomcat

# download the apache tomcat to the working directory
ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.56/bin/apache-tomcat-9.0.56.tar.gz .

# extracting from the tar to the working directory
RUN tar -xvzf apache-tomcat-9.0.56.tar.gz

# moving the extracted contents to /opt/tomcat
RUN mv apache-tomcat-9.0.56/* /opt/tomcat

# Exposing port 8080 as this is the default port used by tomcat
EXPOSE 8080

# default command to start tomcat when the container starts
CMD ["/opt/tomcat/bin/catalina.sh", "run"]
```

Build the image specifying the path to the Dockerfile:

```
docker build -t tomcat:9.0 -f ./lab/Dockerfile ./lab
```

Run a container from the image:

```
docker run -d --rm -p 8080:8080 tomcat:9.0
```

> Check the url at http://localhost:8080
