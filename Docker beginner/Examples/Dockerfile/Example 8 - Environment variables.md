When you realise applications with Docker, you often have to define environment variables that the software accesses. As with the definition of a `venv`, you can also define these variables in Dockerfiles.
In this example, we will look at how to find out the predefined variables of a base image.
# Learning Goals

1. Research which predefined variables exist
	- Inside a docker container
	- Inspecting a docker image
	- Reading the image documentation

2. Define environment variables in different areas
	- In the docker run command
	- In a dockerfile

3. Define your own environment variables
# Step-by-step instrcutions

There are three approaches to finding out which environment variables can be set:

## Inspection the docker image
1. Use the docker pull command to download your desired image on the hosting device
```bash 
docker pull mariadb
```

2. Inspect the docker image with the docker inspect command
	- Look for the Env section in the output
```bash
docker inspect mariadb
```

The output for this example shout look like this:
```bash
"Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.17",
                "LANG=C.UTF-8",
                "MARIADB_VERSION=1:11.7.2+maria~ubu2404"
            ],
```

Here you can see the predefined environmental variables from this image.

## Inspection the environmental variables inside a running container

For this example, we run a fresh Ubuntu container. 
```bash
docker run -it ubuntu:latest bash
```

Inside of it, we execute the following command:
```bash
root@6e8e793848c5:/# printenv
```

> This is only possible, if your container hosts a shell to interact with! The command may also change depending on the OS.

## Reading the documentation of the image

The default location, where to find tutorials from docker images, is docker hub. For our example, we can take a closer look at the mariadb image. https://hub.docker.com/_/mariadb

## Definition of environment variables in the docker run command

There are many ways to define an environment variable. In addition to a Dockerfile, you can also specify it in the docker run command. To do this, use the tag `--env`:
```bash
$ docker run -d --name some-mariadb --env MARIADB_ROOT_PASSWORD=my-secret-pw  mariadb:latest
```

## Definition of environment variables in a dockerfile
To set an environment variable in the dockerfile, use the `ENV` command. This is a key-value tuple where the **right-hand** side defines the **value** of the variable and the l**eft-hand** side the **name**:

```yaml
# Start with a base image
FROM ubuntu:latest

# Set environment variables
ENV APP_NAME="MyApp"
ENV APP_ENV="production"
ENV APP_PORT=8080

# Install dependencies
RUN apt-get update && apt-get install -y curl

# Set the working directory
WORKDIR /app

# Add application files to the container
COPY . /app

# Expose the port specified in the environment variable
EXPOSE $APP_PORT

# Command to run the application
CMD ["./start.sh"]
```

>Of course, you can also define your own variables that are not specifically defined by the image. The same notation is used as for given variables.
# Summary
There are many ways to define environment variables. Whether it is for one-time use or the core of an application. The important thing is to choose the right place to set them.