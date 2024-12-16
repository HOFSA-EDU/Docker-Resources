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
	 1. The docker image is now downloaded. You can list all downloaded images by typing
		````bash
		docker image ls
````
2. Run the container out of the downloaded image
	````bash 
	docker run -d --name some-ghost -e NODE_ENV=development ghost
	````
	1. Here we have some manipulations on the deployment:
		1. -d : detached mode - the container runs in the background, freeing up the terminal for other tasks
		2. --name : provides the deployed container a name. The container can now be addressed with
		3. -e : environment variable. The Variable NODE_ENV is here specified.
3. According to the documentation, this will start a Ghost development instance listening on the default Ghost port of 2368.
4. Access the container on localhost:2368

