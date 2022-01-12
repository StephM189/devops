
## Container filesystem 

Containers filesystems are virtual disks, put together by Docker. Every container starts with the disk contents set up in the container package.

You can mount a directory from your local machine into the container filesystem. You can use that to add new content, or to override existing files.

- [index.html](./html/index.html) is a web page you can display from Nginx when you mount the local folder as a volume.

Run a container to show the new web page - you need to use the full path as the source for the volume:

```
# run a container mounting the local volume to the HTML directory:
docker run -d \
  -p 8082:80 \
  -v ${PWD}/labs/docker/filesystem/html:/usr/share/nginx/html \
  --name nginx \
  nginx:alpine
```

- `-v` mounts a local directory to the container - the variables store the full path and mean we can use the same Docker command on any OS
- `${PWD}` is the environment variable holding the absolute path to present working directory

> Browse to http://localhost:8082 and you'll see the custom HTML response.

The container is reading data from your local machine. You can edit [index.html](./html/index.html) and when you refresh your browser you'll see your changes straight away.

___
## Cleanup

Cleanup by removing all containers:

```
docker rm -f $(docker ps -aq)
```