## Find a Docker image on DockerHub

DockerHub is a repository where you can find pre-build Docker images for various applications. Let's use the official Ghost image as an example.
1. Go to DockerHub
	1. www.hub.docker.com
2. In the search bar, type "ghost" and select the official Ghost image from the results
	1. [ghost - Official Image | Docker Hub](https://hub.docker.com/_/ghost)

There are many images in Docker Hub, official images are marked as having been published by the official developer. Derivatives or modified versions of an image can also be uploaded to Docker hub. Use here the version that is suitable for the situation.

## Deployment of the container

Official Docker images are written in detail. Here you can find information about the version and the steps required for usage. For the purposes of the demonstration, we will focus here on the quick and practical deployment:

1. Download the docker image
````bash 
docker pull ghost
````
	
 2. The docker image is now downloaded. You can list all downloaded images by typing
````bash
 docker image ls
````

2. Run the container out of the downloaded image
	````bash 
	docker run -d --name some-ghost -p 2370:2368 -e NODE_ENV=development ghost
	````

	1. Here we have some manipulations on the deployment:
		1. -d : detached mode - the container runs in the background, freeing up the terminal for other tasks
		2. --name : provides the deployed container a name. The container can now be addressed with
		3. -e : environment variable. The Variable NODE_ENV is here specified.
		
1. According to the documentation, this will start a Ghost development instance listening on the default Ghost port of 2368.
2. Access the container on localhost:2370

As you can see, some functions are not available. This is because the links in the container have been set incorrectly. They are created with the default port `2368`, which does not correspond to our settings. However, we can change this by setting the environment variable `-url`:
```bash
 docker run -d --name some-ghost -p 2370:2368 -e NODE_ENV=development -e url=http://localhost:2370 ghost
```

## Stop, start and delete your container

Docker containers have a specific life cycle. See here: [[Docker Life Cycle.canvas|Docker Life Cycle]]. This can be determined as follows:

1. Stop den container
````bash
docker stop some-ghost
````
or
````bash
docker stop <4 first digits of the container-id>
````

2. Resume the container
````bash
docker start some-ghost
````
or
````bash
docker start <4 first digits of the container-id>
````

3. Delete the container
````bash
docker rm some-ghost
````
or
````bash
docker rm <4 first digits of the container-id> 
````

## Persistence of data in a container
Delete your container and restart it. You will notice that your changes are no longer available in Ghost. The reason for this is that containers do not come with persistent storage by default. A newly created container therefore has the original state given by its image. 
On the one hand, this gives you the option of always returning to a functioning state in the event of errors, but on the other hand it has the disadvantage that you always have to make desired changes again. See here: [[Docker volume]].
