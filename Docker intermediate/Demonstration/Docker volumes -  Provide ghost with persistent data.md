This demonstration is based on the **Docker Life-Cycle demonstration** from the Docker for beginners course. If you want to repeat it again, take a look at it here:[[Docker Life Cycle - Fast deployment of a single container service]].

## Introduction
A container without a volume has *no persistent data*. This means that the changes made are deleted if the original container disappears.
*Docker Volumes* solves this problem.
The aim is to retain the contributions of our Ghost CMS *regardless* of the container status. To do this, we create a volume in the Docker Run command.

## Step-by-step guide
Here is our outgoing command for the deployment of a ghost container:
```bash
docker run -d --name some-ghost -p 2370:2368 -e NODE_ENV=development -e url=http://localhost:2370 ghost
```

To determ a volume, we use the `-v` tag to hand-over a named volume:
```bash
docker run -d \
--name some-ghost \
-e NODE_ENV=development \
-e database__connection__filename='/var/lib/ghost/content/data/ghost.db' \
-e url=http://localhost:2370 \
-p 2370:2368 \
-v some-ghost-data:/var/lib/ghost/content \
ghost
```

Docker volume are functional and an easy way to archiev persistent data! But it leads to some trouble to handle them in a manuel way.
To solve this, we use a *bind mount*. This is a linked folder on the hosting machine linked inside of our docker container:
```bash
docker run -d \
--name some-ghost \
-e NODE_ENV=development \
-e database__connection__filename='/var/lib/ghost/content/data/ghost.db' \
-e url=http://localhost:2370 \
-p 2380:2368 \
-v /var/lib/ghost-files:/var/lib/ghost/content \
ghost:alpine
```

You can now see and edit the files on your hosting machine:
```bash
ls /var/lib/ghost-files
```

Expected output:
```bash
apps  data  files  images  logs  media  public  settings  themes
```

## Stop the container
To stop the container you can use the following command:
```bash
docker stop some-ghost
docker rm some-ghost
```

To delete the named volume, you have to delete at first the connected container. Then, you can use the following command:
```bash
docker volume rm some-ghost-data
```

