# Learning Goals

1. **Understand Key Terminology**: Familiarize yourself with essential Docker concepts, including:
    - _Docker Container_: Its purpose and role as an isolated environment for running applications.
    - _Docker Image_: Its function as a package containing all necessary code and dependencies.
    
2. **Run Your First Docker Command**: Execute the `docker run hello-world` command to:
    - Verify that Docker is installed and functioning correctly.
    - Observe how Docker pulls an image and creates a container.
        
3. **Interpret Terminal Output**:
    - Learn to analyze the output from Docker commands.
    - Gain insight into the steps performed by Docker, such as pulling an image, creating a container, and executing the container’s instructions.
        
4. **Explore Repetition Effects**:
    - Rerun the `docker run hello-world` command to compare outputs and understand caching and performance improvements.
        
5. **Develop Familiarity with Docker Documentation**:
    - Access further examples and resources to deepen your understanding of Docker usage.

# Terminology

*Docker Container*: An isolated, runnable environment that holds everything needed to run an application.

*Docker Image*: A lightweight, standalone package that contains all necessary code, libraries, and dependencies to run an application.
# Step-by-step instructions

Try running a command with Docker:
```bash
docker run hello-world
```

  Your terminal output should look like this:

``` bash
Unable to find image 'hello-world:latest' locally

latest: Pulling from library/hello-world

78445dd45222: Pull complete

Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7

Status: Downloaded newer image for hello-world:latest


Hello from Docker!

This message shows that your installation appears to be working correctly.


To generate this message, Docker took the following steps:

 1. The Docker client contacted the Docker daemon.

 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.

 3. The Docker daemon created a new container from that image which runs the

    executable that produces the output you are currently reading.

 4. The Docker daemon streamed that output to the Docker client, which sent it

    to your terminal.


To try something more ambitious, you can run an Ubuntu container with:

 $ docker container run -it ubuntu bash


Share images, automate workflows, and more with a free Docker ID:

 https://cloud.docker.com/

  
For more examples and ideas, visit:

 https://docs.docker.com/engine/userguide/

```

This message shows that your installation appears to be working correctly.

Try to run `docker run hello-world` again.

*Q: Was something different? Take a closer look at the time to deploy the container.*