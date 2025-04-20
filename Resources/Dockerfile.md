# Introduction to Dockerfile

A **Dockerfile** is a script that contains a series of instructions to build a Docker image. It serves as a blueprint for creating containerized applications, specifying what the container's operating system looks like, what software it includes, and how it should run.

## Basic Structure of a Dockerfile

Here are some common commands used in a Dockerfile:

1. **FROM**: Specifies the base image to start with (e.g., `FROM ubuntu:20.04`).
2. **RUN**: Executes commands to **install software** or make **modifications to the image**.
3. **WORKDIR**: Set the working directory in the container OS.
4. **COPY**: Copies files or directories from the host to the container.
5. **CMD**: Provides a command to execute when the container starts.
6. **EXPOSE**: Declares the network ports on which the container listens.

## Example: Writing a Dockerfile

Below is an example of a simple Dockerfile:

```dockerfile
# Use an official Python runtime as the base image
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy the application files
COPY . .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose the application port
EXPOSE 5000

# Define the command to run the application
CMD ["python", "app.py"]
```

**This Dockerfile:**

- Starts with a lightweight Python image.
    
- Sets up the working directory inside the container.
	- /app
    
- Copies the application files into the container.
    
- Installs dependencies listed in `requirements.txt`.
    
- Exposes port 5000 for the application.
    
- Specifies the command to run the app when the container starts.
	- python app.py

## Building the docker image

Use the `docker build` command to generate the image:
```bash
docker build -t <image_name>:<tag> <context_path>
```

- `-t` tags the image with a name and an optional version tag.
    
- `<context_path>` is the path to the directory containing the Dockerfile.

## Caching Layers

Each instruction creates a new layer in the image. Docker uses caching to speed up subsequent builds by reusing unchanged layers.

## List your created image

With the following command, you can list up your docker images.

```bash
docker image ls
```

If you can not find your freshly created image, then the building process was not successfull.
# Dockerfile Troubleshooting

- If you encounter issues while building an image, check your Dockerfile for errors. Use the `docker build` command with the `--no-cache` flag to ensure that all steps are executed from scratch:

```sh
  docker build --no-cache -t <image_name> .
```

# Other commands used in dockerfiles

- **LABEL**: Adds metadata to an image, such as a description or version. Example: `LABEL maintainer="hoffmann.sam@education.lu"`.
    
- **ENTRYPOINT**: Defines the main executable for the container. It works with CMD for arguments. Example: `ENTRYPOINT ["python"]`.
    
- **ADD**: Similar to COPY but with extra functionality, such as extracting archives or downloading files from URLs. Example: `ADD app.tar.gz /usr/src/app`.
    
- **EXPOSE**: Indicates the port on which the container will listen. Example: `EXPOSE 8080`.
    
- **ENV**: Sets environment variables for the container. Example: `ENV APP_ENV=production`.
    
- **VOLUME**: Creates mount points for persistent storage. Example: `VOLUME /data`.
    
- **USER**: Specifies the user under which the container will run. Example: `USER nonroot`.
    
- **ARG**: Defines build-time variables. Example: `ARG build_version=1.0`.
    
- **ONBUILD**: Adds a trigger instruction to an image for actions in child images. Example: `ONBUILD RUN apt-get install -y dependency`.
    
- **HEALTHCHECK**: Adds a health check for the container to ensure it’s running properly. Example: `HEALTHCHECK CMD curl http://localhost:8080`.
    
- **SHELL**: Changes the default shell used in the RUN command. Example: `SHELL ["/bin/bash", "-c"]`.
[[Docker image]]
