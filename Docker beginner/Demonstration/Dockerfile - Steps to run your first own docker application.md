### Create the following files
```bash
touch Dockerfile
touch index.js
touch package.json
```

### Open VSC in the terminal with
```bash
code .
```

### Define the source code of the following files:

*index.js*
```javascript
const http = require('http')
const hostname = '0.0.0.0';
const port = 3000;

const server = http.createServer((req, res)=>{
    res.statusCode = 200;
    res. setHeader('Content-Type', 'text/plain');
    res.end('Hello World!\n');
});

server.listen(port, hostname, () =>{
    console.log(`Server running at http://${hostname}:${port}/`)
});
```

*package.json*
```json
"name": "dockerfile_example",
"version": "1.0.0",
"description": "My dockerized Node.js application",
"main": "index.js",
"dependencies": {},
"scripts": {
    "start": "node index.js"
}
```
---
### Containerize our application
To create a customised container for our application, we need to define a Docker image. We use a Dockerfile to describe the dependencies and resources that should be available in the container.

*dockerfile*
```dockerfile
FROM node:14-alpine

WORKDIR /app

COPY . /app

RUN npm install

EXPOSE 3000

ENV NAME=dockerfile-example

CMD ["npm", "start"]
```

#### Dockerfile explanation
- **FROM** defines our base image. In most cases, a slim OS like Linux Alpine oder Mint hits the spot. Sometimes you can find a preconfigured base image for a variety of programming languages or frameworks. Like in this case.

- **WORKDIR** - Defines the current working directory in the container. Every relativ path will now start from this location

- **COPY** - Copies files in the docker container

- **RUN** - Runs a command during the building phase of the docker image. Here we install the dependencies, provided from the package.json file, for our software with the Node Package Manager.

- **EXPOSE** - Defines the port where the container is listening. We have to user port 3000 because it is set in our index.js

- **ENV** - Sets environment variables needed from the software inside the container.

- **CMD** - Executes a command when the container starts. Here we start our software.

### Build your Dockerfile with the following commands
```bash
docker build -t dockerfiledemonstration .
```
<details>
The docker build command builds a docker image based on the dockerfile provided by the command. In our case, the dockerfile is located at our current working directory. So if your pwd is not the one of the save location from your dockerfile, you can adapt it in the command.

The -t tag stand for "tag" and allow us to name your dockerimage. Here, the name is "dockerfiledemonstration", but you can chance it as you like.

To see the created image, you can use the command
	docker image ls
</details>
### Run your container with the recent created dockerimage
```bash
docker run -p 3000:3000 dockerfiledemonstration
```
<details>
As you can remember, we allocated the port 3000 to our node.js server. The map the ports from our docker container to our hosting device, we have to specify this one.
The left side of the mapping stand for the hosting device and the right one for the container side.
Remember: the hosting port can be different from the container port! But as we exposed the port 3000 from our container, this as to be static to reach our node.js server.
</details>

The expected output should be like this
```bash
> dockerfile_example@1.0.0 start /app
> node index.js

Server running at http://0.0.0.0:3000/
```

Open your browser of choice and visit the website http://localhost:3000/