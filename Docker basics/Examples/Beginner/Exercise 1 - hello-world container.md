## Learning Goals

The goal of this scenario is to make you run your first Docker container.

## Terminology

*Docker Container*: An isolated, runnable environment that holds everything needed to run an application.

*Docker Image*: A lightweight, standalone package that contains all necessary code, libraries, and dependencies to run an application.
## Exercise

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

*Q: So what did this do?*



Try to run `docker run hello-world` again.

*Q: Was something different? Take a closer look at the time to deploy.*