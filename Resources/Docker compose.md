**Docker Compose** is a tool that simplifies the process of defining and running multi-container Docker applications. It allows you to manage multiple containers as a single service, making it easier to orchestrate complex applications.

## Docker vs. Docker compose
Let's break down the differences between Docker and Docker Compose:
### Docker

**Docker** is a platform that allows you to develop, ship, and run applications inside containers. Containers are lightweight, portable, and self-sufficient units that include everything needed to run a piece of software, including the code, runtime, libraries, and system tools.

#### Key Features of Docker:

- **Containerization**: Docker packages applications and their dependencies into containers, ensuring consistency across different environments.
- **Isolation**: Each container runs in its own isolated environment, which helps prevent conflicts between applications.
- **Portability**: Containers can run on any system that supports Docker, making it easy to move applications between development, testing, and production environments.
- **Efficiency**: Containers share the host OS kernel, making them more lightweight and faster to start compared to virtual machines.

### Docker Compose

**Docker Compose** is a tool specifically designed for defining and running multi-container Docker applications. It uses a YAML file to configure the application's services, networks, and volumes.
See here: [[Docker volume]] [[Docker network]] [[Docker container]]

#### Key Features of Docker Compose:

- **Multi-Container Management**: Docker Compose allows you to define and manage multiple containers as a single application. This is particularly useful for applications that consist of several interconnected services.
- **YAML Configuration**: The `docker-compose.yml` file is used to specify the configuration of all the services, including their images, ports, volumes, and dependencies.
- **Simplified Commands**: With Docker Compose, you can start, stop, and manage all the services defined in the YAML file using simple commands like `docker-compose up` and `docker-compose down.
- **Service Dependencies**: Docker Compose allows you to define dependencies between services, ensuring that they start in the correct order.

### Summary of Differences

- **Scope**:
    - **Docker**: Focuses on containerizing individual applications.
    - **Docker Compose**: Manages multi-container applications, making it easier to orchestrate complex setups.
    
- **Configuration**:
    - **Docker**: Uses command-line instructions to run and manage containers.
    - **Docker Compose**: Uses a YAML file to define and manage multiple containers and their configurations.
    
- **Use Case**:
    - **Docker**: Ideal for running single containers or simple applications.
    - **Docker Compose**: Best suited for applications that require multiple interconnected services.

In essence, Docker is great for containerizing and running individual applications, while Docker Compose excels at managing and orchestrating multi-container applications.

#### How Docker Compose Works

1. **Configuration with YAML**:
    - Docker Compose uses a YAML file (`docker-compose.yml`) to define the services, networks, and volumes for your application. This file describes how to build and run each container, including details like the image to use, environment variables, ports to expose, and dependencies.

2. **Single Command Operations**:
    - With Docker Compose, you can start, stop, and manage all the services defined in your `docker-compose.yml` file using simple commands. For example, `docker-compose up` starts all the services, while `docker-compose down` stops and removes them.

3. **Service Dependencies**:
    - Docker Compose allows you to define dependencies between services. This ensures that services start in the correct order and wait for their dependencies to be ready before starting.

4. **Networking**:
    - By default, Docker Compose creates a network for all the services defined in the `docker-compose.yml` file. This network allows the containers to communicate with each other using their service names as hostnames.

5. **Volumes**:
    - Docker Compose supports defining volumes to persist data across container restarts. This is useful for databases and other services that need to retain state.

---
## Example `docker-compose.yml` File
Here's a simple example of a `docker-compose.yml` file for a web application with a front-end, back-end, and database service:

```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
  app:
    image: myapp
    depends_on:
      - db
    environment:
      - DATABASE_ACCESS_URL=postgres://db:5432/mydb
  db:
    image: postgres
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

## Key Commands
- **Start Services**:

```bash
  docker-compose up
```

- **Stop and Remove Services**:

```bash
  docker-compose down
```

- **View Running Services**:

```bash
  docker-compose ps
```

- **View all Services**:

```bash
  docker service ls
```

### Use Docker Compose Logs
- If you are using Docker Compose, you can view logs for all services with:

```sh
  docker-compose logs
```

- To follow logs in real-time, add the `-f` flag:

```sh
  docker-compose logs -f
```

[[Docker container]]
[[Docker network]]