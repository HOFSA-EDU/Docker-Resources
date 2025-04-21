If you want to examine a running container, but do not want to disturb the running process you can execute another process inside the container with `exec`.

This could be a shell, or a script of some sort. In that way you can debug an existing environment before starting a new up.

# Learning Goals
1. **Execute Secondary Processes in Containers**:
    - Learn how to access a running container using `docker exec` to execute additional commands.
        
2. **Modify Files Within a Container**:
    - Practice editing containerized files, such as the `index.html` page, using command-line text editors like Nano.
    
3. **Develop Container Management Skills**:
    - Enhance familiarity with container interactions and dynamic updates without stopping or recreating containers.
    - Understand the process of installing tools, such as Nano, in a container using package managers like `apt-get`.

# Exercise
In this exercise, we want to change a file in an already running container, by executing a secondary process.

## Step by step instructions
- Spin up a new NGINX container: 
``` bash
docker run -d -p 8080:80 nginx
```
- Visit the webpage to make sure that NGINX have been setup correctly.

Step into a new container by executing a bash shell inside the container:
```bash
docker exec -it CONTAINERNAME bash
```

> note that the CONTAINERNAME is the name of the NGINX container you just started.

Inside, we want to edit the `index.html` page, with a cli text editor called [nano](https://www.nano-editor.org/).
Because containers only have the bare minimum installed, we need to first install nano, and then use it:

> From the [DockerHub description](https://hub.docker.com/_/nginx) we know that the standard place for HTML pages NGINX serves is in /usr/share/nginx/html

- install nano on the container: 
```bash
apt-get update && apt-get install -y nano
```
- Edit the index html page: 
```bash
nano /usr/share/nginx/html/index.html
```
- Save and exit nano by pressing: `CTRL + O` and `enter` and `CTRL + X` to exit Nano
- Revisit the page to check that your edition is in effect.

## Summary
You have tried to start a new process by the `exec` command in order to look around in a container, or to edit something.
You have also seen that terminating any of the the processes created with `docker exec` will not make the container stop.
