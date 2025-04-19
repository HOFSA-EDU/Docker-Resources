## What is a Docker Image?

A **Docker image** is a lightweight, standalone, executable package that includes everything needed to run a piece of software, such as:
- Code
- Runtime
- Libraries
- Environment variables
- Configuration files

Docker images are used to create Docker containers. A container is a running instance of an image and shares the host's operating system kernel. Images are the blueprint for containers.

---

## Common Tasks for Handling Docker Images

### 1. **Building a Docker Image**
You can create a custom Docker image using a `Dockerfile`, which is a text file with instructions to build the image. To build an image, use:
```bash
docker build -t <image-name> <path-to-dockerfile>
```
Take a look at [[Docker-Resources/Resources/Dockerfile|Dockerfile]] if you want to know more.

### 2. **Pulling a Docker Image**

Docker images are usually stored on registries like Docker Hub. To pull an image from a registry:
```bash
docker pull <image-name>
```

### 3. **Listing Docker Images**

To see a list of all the images stored locally:
```bash
docker image ls
```

### 4. **Running a Docker Container from an Image**

To create and run a container from a Docker image:
```bash
docker run <image-name>
```
More about the use of the docker run command at [[Docker container]]

### 5. **Tagging a Docker Image**

To rename or tag an image:
```bash
docker tag <existing-image-name> <new-image-name>
```

### 6. **Pushing a Docker Image**

To share your image on a registry:
```bash
docker push <image-name>
```

### 7. **Removing a Docker Image**

To delete an image from your local system:
```bash
docker image rm <image-name> or <image-id>
```