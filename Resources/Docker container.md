### How Docker Containers Work: A Simple Explanation

Docker containers are lightweight, portable, and self-sufficient units that package an application and its dependencies together. This ensures that the application runs consistently across different environments. Here's a basic overview of how Docker containers work, focusing on the essential commands:

1. **Docker Images**:
    - Docker containers are created from Docker images. An image is a read-only template with instructions for creating a Docker container.
    - **Command**: `docker pull <image>` - This command downloads a Docker image from a registry (like Docker Hub).

2. **Creating and Running Containers**:
    - Once you have an image, you can create and run a container from it.
    - **Command**: `docker run <image>` - This command creates and starts a new container from the specified image.
    - Example: `docker run hello-world` - This runs a simple container that prints a "Hello, World!" message.

3. **Listing Containers**:
    - You can list all running containers to see their status.
    - **Command**: `docker ps` - This command lists all currently running containers.
    - **Command**: `docker ps -a` - This command lists all containers, including those that are stopped.

4. **Stopping and Starting Containers**:
    - You can stop a running container and start it again later.
    - **Command**: `docker stop <container>` - This stops a running container.
    - **Command**: `docker start <container>` - This starts a stopped container.

5. **Removing Containers**:
    - Once you no longer need a container, you can remove it to free up resources.
    - **Command**: `docker rm <container>` - This removes a stopped container.

### Example Workflow

1. **Pull an Image**:

```sh
   docker pull nginx
```

2. **Run a Container**:

```sh
   docker run -d -p 80:80 nginx
```

- `-d` runs the container in detached mode (in the background).
- `-p 80:80` maps port 80 of the host to port 80 of the container. See also [[Docker port mapping]]

3. **List Running Containers**:

```sh
   docker ps
```

4. **Stop the Container**:

```sh
   docker stop <container_id>
```

5. **Remove the Container**:

```sh
   docker rm <container_id>
```

Docker containers make it easy to develop, test, and deploy applications in a consistent environment. By using these basic commands, you can start working with Docker containers and leverage their benefits for your projects.

### Most Important `docker run` Flags

Here are some of the most commonly used and important flags for the `docker run` command:

1. **-d, --detach**: Run the container in detached mode (in the background).

```sh
   docker run -d <image>
```

2. **-p, --publish**: Publish a container's port(s) to the host.

```sh
   docker run -p <host_port>:<container_port> <image>
```

3. **-e, --env**: Set environment variables.

```sh
   docker run -e MY_ENV_VAR=value <image>
```

4. **--name**: Assign a name to the container.

```sh
   docker run --name my_container <image>
```

5. **-v, --volume**: Bind mount a volume. See also [[Docker volume]]

```sh
   docker run -v /host/path:/container/path <image>
```

6. **--rm**: Automatically remove the container when it exits.

```sh
   docker run --rm <image>
```

7. **-it**: Run the container in interactive mode with a TTY (useful for running containers with a shell).

```sh
   docker run -it <image> /bin/bash
```

8. **--network**: Connect a container to a network. See also: [[Docker network]]

```sh
   docker run --network <network_name> <image>
```

9. **--cpus**: Limit the number of CPUs.
    
    ```sh
    docker run --cpus="2" <image>
    ```
    
10. **--memory**: Limit the memory usage.
    
    ```sh
    docker run --memory="256m" <image>
    ```
    
11. **--restart**: Define the restart policy for the container.
    
    ```sh
    docker run --restart unless-stopped <image>
    ```
    
12. **--env-file**: Read in a file of environment variables.
    
    ```sh
    docker run --env-file ./env.list <image>
    ```

These flags provide a wide range of options to customize the behavior and configuration of your Docker containers. 
## Docker container troubleshooting
Troubleshooting Docker containers can involve several steps to identify and resolve issues. Here are some common methods and tools to help you diagnose and fix problems:
### 1. Check Container Logs

- **Logs** are the first place to look when something goes wrong. Use the `docker logs` command to view the logs of a specific container:

```sh
  docker logs <container_id>
```

- For real-time log streaming, use the `-f` (follow) flag:

```sh
  docker logs -f <container_id>
```

### 2. Inspect the Container

- The `docker inspect` command provides detailed information about a container's configuration and state:

```sh
  docker inspect <container_id>
```

- This command outputs JSON data, which includes network settings, environment variables, and more.

### 3. Access the Container Shell

- Sometimes, you need to get inside the container to investigate issues. Use the `docker exec` command to start a shell session:

```sh
  docker exec -it <container_id> /bin/bash
```

- For containers based on Alpine Linux, use `/bin/sh` instead:

```sh
  docker exec -it <container_id> /bin/sh
```

### 4. Check Resource Usage

- Use the `docker stats` command to monitor real-time resource usage (CPU, memory, network, and disk I/O) of your containers:

```sh
  docker stats
```

### 5. Review Docker Events

- The `docker events` command shows a real-time stream of Docker events, which can help you understand what is happening with your containers:

```sh
  docker events
```

### 6. Check Docker Daemon Logs

- Sometimes, issues might be related to the Docker daemon itself. Check the Docker daemon logs for any errors or warnings. The location of these logs depends on your operating system:
    - **Linux**: `/var/log/docker.log`
    - **Windows**: Event Viewer
    - **macOS**: Console application

# Links
[[Docker port mapping]]
[[Docker image]]
[[Docker volume]]
