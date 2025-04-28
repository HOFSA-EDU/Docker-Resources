# Docker volumes

> _Hint: This lab only covers volumes on Docker for Linux. If you are on windows or mac, things can look different._

Containers should be ephemeral.
The whole idea is that you can start, stop and delete the containers without losing data.
But how can we do that for persistent workloads like databases?
We need a way to store data outside of the containers.
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
docker run --name mynginx -v /www:/usr/share/nginx/html:ro -d nginx
```

That will map whatever files are in the `/www` folder on the host to `/usr/share/nginx/html` in the container.

> The `:ro` attribute is making the host volume read-only, making sure the container can not edit the files on the host.

### Docker volumes

This is where you can use a `named` or `unnamed` volume to store the external data. The data will still be stored locally unless you have configured a storage driver for your system.

Volumes are entities inside docker, and can be created in **three different ways**:
- By explicitly creating it with the `docker volume create <volume_name>` command.
- By creating a named volume at container creation time with `docker container run -d -v DataVolume:/opt/app/data nginx`
- By creating an anonymous volume at container creation time with `docker container run -d -v /opt/app/data nginx`

In the next section, you will get to try all of them.

## Step-by-Step Instructions
### Bind mount
- Create an empty directory and navigate to it.
- Create a file with the name index.html with the following content:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Docker for Intermediates</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #0db7ed; /* Docker CI Blau */
            color: #ffffff; /* Weiß */
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            text-align: center;
        }
        .container {
            padding: 20px;
        }
        img {
            width: 150px;
            margin-bottom: 20px;
        }
        h1 {
            font-size: 2em;
        }
    </style>
</head>
<body>
    <div class="container">
        <img src="https://www.docker.com/wp-content/uploads/2022/03/Moby-logo.png" alt="Docker Logo">
        <h1>Docker for Intermediates</h1>
    </div>
</body>
</html>
```

- Now we need to specify the path to the directory that we want to include in our container. The path can be included in two ways:
	- ***absolut:*** starting from the root of the filesystem, (which in linux is `/`). You can use the command `pwd` (Print working directory) to display the path to where you are.
	- ***relativ:*** navigate from your current position to the source. If you are already in the right place in the system, you can enter your current position with `.`.
- Now try to run the container with the bind mount.
```bash
docker run --name mynginx -v /<your_folder_name>:/usr/share/nginx/html:ro -d nginx
```
in our case:
```bash
docker run --name mynginx -v .:/usr/share/nginx/html:ro -d nginx
```
This will give you a nginx server running, serving your static files... _But on which port?_
- Run a `docker ps` command to find out if it has any ports forwarded from the host.

Remember the [[Example 4 - Port-forward]] on port forwarding in Docker:
- Make it accessible on port 8080

<details>
<summary>How to do this?</summary>

The parameter `-p 8080:80` will map port 80 in the container to port 8080 on the host.
docker run --name mynginx -v /yourfoldername:/usr/share/nginx/html:ro -p 8080:80 -d nginx

</details>

- Check, if it is running, by navigating to the hostname or IP with your browser, and on port 8080.
- Stop the container with `docker stop <container_name>` or `docker stop <container ID>`.

### Docker volumes

First off, lets try to make a data volume called `data`:
```bash
docker volume create data
```

Docker creates the volume and outputs the name of it after the creation.
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

You can see that the `data` volume is mounted at `/var/lib/docker/volumes/data/_data` on the host machine.

> **Note** we will not go through the different drivers. For more info look at Dockers own [example](https://docs.docker.com/engine/admin/volumes/volumes/#use-a-volume-driver).

You can now use this *data* volume in all containers. Try to mount it to an nginx server with the command:
```bash 
docker container run --rm --name www -d -p 8080:80 -v data:/usr/share/nginx/html nginx
``` 

> **Note:** If the refered volume is empty and we provide the path to a directory that contains data in the base image, that data will be copied into the volume.

Try now to look at the data stored in the volume by using docker desktop. Click on the *Volumes* section on the left side from the menu and select the Volume with the name *data*.

Expected output:

```
50x.html  index.html
```

Those two files comes from the Nginx image and are the standard files from our nginx.

### Attaching multiple containers to a volume
Multiple containers can attach to the same volume with data. 

Docker doesn't handle any file locking, so applications must account for the file locking themselves.

Let's try to go in and make a new html page for our nginx webserver. 

We do this by making a *new ubuntu container* that has the `data` volume attached to `/tmp`. After that, we create a new *html file* by using the `echo` command:

Start the container:
```bash
docker run -it --rm -v data:/tmp ubuntu bash
```

In the container run:
```bash
echo "<html><h1>hello docker intermediate</h1></html>" > /tmp/hello.html
```

Verify the file was created:
```bash
ls /tmp
```

Expected output:
```bash
hello.html  50x.html  index.html
```

Head over to your newly created webpage at: `localhost:8080/hello.html`.

>The Ubuntu and the Nginx container share a volume. If one of the two makes a change to the contents of the volume, the other is also affected and can access the modified data.

## cleanup

Exit out of your ubuntu server and execute a `docker stop www` to stop the nginx container.

Run a `docker ps` to make sure that no other containers are running.

```bash
docker ps
```

Expected output:

```bash
CONTAINER ID    IMAGE       COMMAND       CREATED    STATUS       PORTS     NAMES
```

>The data volume is still present, and will be there until you remove it with a `docker volume rm data` or make a general cleanup of all the unused volumes by running `docker volume prune`.

## Tips and tricks

As you have seen, the `-v` flag can both create a bind mount or name a volume depending on the syntax. If the first argument begins with a / or ~/ you're creating a bind mount. Remove that, and you're naming the volume. For example:

- `-v /path:/path/in/container` mounts the host directory, `/path` at the `/path/in/container`
- `-v path:/path/in/container` creates a volume named *path* with no relationship to the host.

### Sharing data
If you want to share volumes or bind mount between two containers, then use the `--volumes-from` option for the second container. The parameter maps the mapped volumes from the source container to the container being launched.

## Summary
Volumes are the easiest way to back up persistent data in a Docker container! Bind mounts and volumes differ in their handling and should be selected individually according to the needs of the service.
