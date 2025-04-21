# Learning Goals

1. **Understand the difference between** `ENTRYPOINT` **and** `CMD`:
    - Learn that `CMD` provides default commands that can be fully overridden when running the container.
    - Discover how `ENTRYPOINT` ensures a specific executable always runs, allowing additional arguments without replacing the main command.
        
2. **Enhance container security**:
    - Recognize the importance of hardening containers by using `ENTRYPOINT` to restrict misuse and define a clear purpose.
        
3. **Combine** `ENTRYPOINT` and `CMD`:
    - Explore best practices for combining `ENTRYPOINT` for mandatory commands and `CMD` for optional arguments.
        
4. **Practice Dockerfile configuration**:
    - Write and understand Dockerfiles that implement `ENTRYPOINT` and `CMD` to customize container behavior.

# Step-by-step instrcutions
Create a new folder on your hosting machine and define the following dockerfile:
```bash
# Use an official Ubuntu base image
FROM ubuntu:20.04

# Install curl
RUN apt-get update && apt-get install -y curl

# Define a default command using CMD
CMD ["curl", "https://www.lgk.lu"]
```

>Starting from an Ubuntu base image, we install curl for our example. With the **RUN** command, the installation is executed **during the building process**, not when the container is started!

Now create a docker image from the given Dockerfile:
```bash
docker build -t step1 .
```

> Use the **-t** flag to define a name for your docker image. In this example, the name of the freshly created image is *step1*.

Start your container and take a loot at the output:
```bash
docker run step1
```

The functionality of our image is therefore to execute a cur command on a web page. But what happens if we pass a different command for execution when starting the container?
```bash
docker run step1 ls -la
```

>The initial command is completely overwritten! This is a peculiarity of the CMD instruction in our Dockerfile. It is therefore possible to misuse this container.

Now adapt your Dockerfile as follows and apply the two commands below:
```bash
FROM ubuntu:20.04

RUN apt-get update && apt-get install -y curl

# Define a command as Entrypoint
ENTRYPOINT ["curl"]

CMD ["https://www.lgk.lu"]
```

```bash
docker run <image> ls
```

```bash
docker run <image> https://www.lam.lu
```

>The curl command can no longer be overwritten. Only the CMD instruction can be customised. This gives the Docker container more flexibility.


# Explanation: `ENTRYPOINT` vs `CMD`

- **CMD**: Provides default arguments or commands to run in the container. It can be overridden by specifying a new command when starting the container.

>The initial command will be completely overwritten! This can lead to obusing your docker containers in a way that you do not want! 
>Harden you containers so that a user can only use them for its purpose. 

- **ENTRYPOINT**: Defines the executable that will always run in the container. It cannot be replaced, but additional arguments can be passed to it.

> An entrypoint can not be overwritten! A common way to combine ENTRYPOINT and CMD is, to use CMD for arguments and ENTRYPOINT for the main command itself.

# Summary
The handling of `ENTRYPOINT` and `CMD` is part of the area of securing Docker images. Under no circumstances should you leave room for the misuse of Docker containers in your own system. If these are started in a multi-container application, they can also access the functionalities of other containers.
