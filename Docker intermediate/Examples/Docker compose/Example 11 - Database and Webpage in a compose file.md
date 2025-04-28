In **example 10**, we have created a container with a *database* and one with a *web server* in the same *network*. Now we try to start the infrastructure with *one command*.
We use *docker compose* for this.

#### **Step-by-Step Guide**
- First, we create a document with the name *compose.yml* in the same folder as example 10:
```bash
touch compose.yml
```

```yaml
version: "3"
services:
 database:
  image: mysql:latest
  ports:
   - "3306:3306"
  environment:
   - MYSQL_ROOT_PASSWORD=rootpassword
   - MYSQL_DATABASE=labdb
  networks:
   - web_database_network
 webserver:
  build: .
  ports:
   - "8080:80"
  networks:
  - web_database_network
networks:
 web_database_network:
  external: true
```

##### Explanation:
- we create two **services**: *database* and *webserver*
- **image** we access an already created image
- **build** we refer to a dockerfile that is built and used during deployment
- **ports** we define the ports on which our services listen
-  **networks** we define the networks on which the containers should run
- *At the end of a compose file*, networks and volumes are specified. Here we use the `external` reference to indicate that we want to use an *existing* network.
---

- Deploy your infrastructure with docker compose:
```bash
docker compose up -d
```

> Like in the docker run command, we use the `-d` tag to run your container(s) in *detached mode*.

- Shut down your infrastructure:
```bash
docker compose down
```

>Docker will clean up every part for the deployed infrastructure that was created during the deployment.