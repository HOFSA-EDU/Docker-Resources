# Docker volumes

> _Hint: This lab only covers volumes on Docker for Linux. If you are on windows or mac, things can look different._

Containers should be ephemeral.
The whole idea is that you can start, stop and delete the containers without losing data.
But how can we do that for persistent workloads like databases?
We need a way to store data outside of the containers.pch
You have two different ways of mounting data from your container `bind mounts` and `volumes`.

### Bind mount

Is the simpler one to understand. It takes a host path, like `/data`, and mounts it inside your container eg. `/opt/app/data`.

The good thing about bind mount is that they are easy and allow you to connect directly to the host filesystem.

The downside is that you need to specify it at runtime, and path to mount might vary from host to host, which can be confusing when you want to run your containers on different hosts.

With bind mount you will also need to deal with backup, migration etc. in an tool outside the Docker ecosystem.

As an example, let's look at the [Nginx](https://hub.docker.com/_/nginx/) container.

The server itself is of little use, if it cannot access our web content on the host.

We need to create a mapping between the host system, and the container with the `-v` command:

```bash
docker run --name some-nginx -v /some/content:/usr/share/nginx/html:ro -d nginx
```

That will map whatever files are in the `/some/content` folder on the host to `/usr/share/nginx/html` in the container.

> The `:ro` attribute is making the host volume read-only, making sure the container can not edit the files on the host.

### A docker Volume

This is where you can use a `named` or `unnamed` volume to store the external data. The data will still be stored locally unless you have configured a storage driver for your system.

Volumes are entities inside docker, and can be created in three different ways.

- By explicitly creating it with the `docker volume create <volume_name>` command.
- By creating a named volume at container creation time with `docker container run -d -v DataVolume:/opt/app/data nginx`
- By creating an anonymous volume at container creation time with `docker container run -d -v /opt/app/data nginx`

In the next section, you will get to try all of them.

## Step-by-Step Instructions

### Bind mount

Try to do the following:

- `git clone` this repository down.  If you are at training the repository is already cloned on your training workstation.
- Navigate to the `labs/volumes/` directory, which contains a file we can try to publish: `index.html`.
- We need change `/some/content` to the right path, you can define it with the absolut or the relative path:
	- ***absolut:*** starting from the root of the filesystem, (which in linux is `/`). You can use the command `pwd` (Print working directory) to display the path to where you are.
	- ***relativ:*** navigate from your current position to the source
- Now try to run the container with the `labs/volumes` directory bind mounted.

This will give you a nginx server running, serving your static files... _But on which port?_

- Run a `docker ps` command to find out if it has any ports forwarded from the host.

Remember the [[Example 4 - Port-forward]] on port forwarding in Docker.

- Make it accessible on port 8080

<details>
<summary>How to do this?</summary>

The parameter `-p 8080:80` will map port 80 in the container to port 8080 on the host.
docker run -v ./labs/volumes:/usr/share/nginx/html:ro -p 8080:80 nginx
</details>

- Check that it is running by navigating to the hostname or IP with your browser, and on port 8080.
- Stop the container with `docker stop <container_name>` or `docker stop <container ID>`.

### Use a bind-mount in a docker-compose file

As in the scenario above, we define the html file in labs/volumes in a docker-compose file. For more information on docker-compose, take look at the [[Docker compose]] section.

```yaml
services:
  web:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - ./labs/volumes:/usr/share/nginx/html
```

Deploy your compose file with the native docker compose command. If you are at the same location as your compose file. The command looks like this:

```bash
docker compose up
```
### Volumes

First off, lets try to make a data volume called `data`:

```bash
docker volume create data
```

Docker creates the volume and outputs the name of the volume created.

If you run `docker volume ls` you will have a list of all the volumes created and their driver:

```outputs
DRIVER              VOLUME NAME
local               data
```

Unlike the bind mount, you do not specify where the data is stored on the host.

In the volume API, like for almost all other of Docker’s APIs, there is an `inspect` command giving you low level details. 

- run `docker volume inspect data` to see where the data is stored on the host.

```json
[
    {
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/data/_data",
        "Name": "data",
        "Options": {},
        "Scope": "local"
    }
]
```

You can see that the `data` volumes is mounted at `/var/lib/docker/volumes/data/_data` on the host.

> **Note** we will not go through the different drivers. For more info look at Dockers own [example](https://docs.docker.com/engine/admin/volumes/volumes/#use-a-volume-driver).

You can now use this data volume in all containers. Try to mount it to an nginx server with the `docker container run --rm --name www -d -p 8080:80 -v data:/usr/share/nginx/html nginx` command.

> **Note:** If the volume refer to is empty and we provide the path to a directory that contains data in the base image, that data will be copied into the volume.

Try now to look at the data stored in `/var/lib/docker/volumes/data/_data` on the host:

```
sudo ls /var/lib/docker/volumes/data/_data/
```

Expected output:

```
50x.html  index.html
```

Those two files comes from the Nginx image and is the standard files the webserver has.

### Attaching multiple containers to a volume

Multiple containers can attach to the same volume with data. 

Docker doesn't handle any file locking, so applications must account for the file locking themselves.


Let's try to go in and make a new html page for nginx to serve. 

We do this by making a new ubuntu container that has the `data` volume attached to `/tmp`, and thereafter create a new html file with the `echo` command:

Start the container:

```
docker run -it --rm -v data:/tmp ubuntu bash
```

In the container run:

```
echo "<html><h1>hello world</h1></html>" > /tmp/hello.html
```

Verify the file was created by running in the container:

```
ls /tmp
```

Expected output:

```
hello.html  50x.html  index.html
```

Head over to your newly created webpage at: `localhost:8080/hello.html`.


## cleanup

Exit out of your ubuntu server and execute a `docker stop www` to stop the nginx container.

Run a `docker ps` to make sure that no other containers are running.

```
docker ps
```

Expected output:

```
CONTAINER ID    IMAGE       COMMAND       CREATED    STATUS       PORTS     NAMES
```

The data volume is still present, and will be there until you remove it with a `docker volume rm data` or make a general cleanup of all the unused volumes by running `docker volume prune`.

## Tips and tricks

As you have seen, the `-v` flag can both create a bind mount or name a volume depending on the syntax. If the first argument begins with a / or ~/ you're creating a bind mount. Remove that, and you're naming the volume. For example:

- `-v /path:/path/in/container` mounts the host directory, `/path` at the `/path/in/container`
- `-v path:/path/in/container` creates a volume named *path* with no relationship to the host.

### Sharing data

If you want to share volumes or bind mount between two containers, then use the `--volumes-from` option for the second container. The parameter maps the mapped volumes from the source container to the container being launched.

## Summary

Now you have tried to bind volumes to a container to connect the host to the container.
