# Understanding Docker Port Mapping

## What is Docker Port Mapping?

Docker port mapping is a mechanism that allows a container's internal ports to be made accessible from the host machine. Containers run in isolated environments, each with its own network stack. To expose a service running in a container (e.g., a web server), you need to map the container's internal port to a port on the host machine.

---

## How to Map Ports in Docker

Port mapping in Docker can be done using the `-p` or `--publish` option when running a container.

### Syntax
```bash
docker run -p <host-port>:<container-port> <image-name>
```

For example:
```bash
docker run -p 8080:80 nginx
```

- **8080**: Port on the host machine.
- **80**: Port inside the container (where the service is running).
- **nginx**: The name of the Docker image being used.
    
Now, accessing `http://localhost:8080` on the host machine will forward traffic to port `80` inside the container.

## Common Use Cases

1. **Running a Web Server**  on port 3000
    ```
    docker run -p 3000:3000 my-web-app
    ```
    
2. **Multiple Containers with Different Ports** Running two containers with the same internal port but mapping them to different host ports:
    ```
    docker run -p 8081:80 nginx
    docker run -p 8082:80 nginx
    ```
    
3. **Random Port Mapping** Docker can assign a random host port using the `-P` or `--publish-all` option:
    ```
    docker run -P nginx
    ```

## Checking Port Mappings

To inspect a container's port mappings, use:
```bash
docker ps
```

This command lists all running containers and their port mappings.
To view details of a specific container:
```bash
docker inspect <container-id>
```

