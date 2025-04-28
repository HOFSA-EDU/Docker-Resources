To deploy a *Docker Compose file* in *Docker Swarm*, you can use the `docker stack deploy` command.

## Step-by-Step guide
>For this guide, make sure, that your docker swarm is running correctly!

### Prepare your compose file:
Ensure your `docker-compose.yml` file is compatible with Docker Swarm. Use *version 3* *or* *higher* of the Compose file format, as it supports Swarm mode:

```yaml
version: "3.8"
services:
 nginx:
  image: nginx:latest
  ports:
   - "8082:80"
 apache:
  image: httpd:latest
  ports:
   - "8081:80"
```

---
### Deploy your compose file as a stack
Use the `docker stack deploy` command to deploy the Compose file as a stack:
```bash
docker stack deploy --compose-file docker-compose.yml my-stack
```
Here we use the compose file `docker-compose.yml` and we name your stack `my-stack`.

### Verify the deployment
Check the status of your *stack*:
```bash
docker stack ls
```

or for a specific *stack*:
```bash
docker stack ls my-stack
```

### Scale your stack
We can scale *services* in the stack with:
```bash
docker service scale my-stack_nginx=5
```