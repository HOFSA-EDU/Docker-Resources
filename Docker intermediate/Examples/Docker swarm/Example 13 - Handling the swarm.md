## Setup your swarm
For this example, you can play with the [Play-With-Docker Playground](https://labs.play-with-docker.com/) or you can group up in class to create your swarm.

> To create a swarm, you devices have to be in the same network!


## Docker swarm in a nutshell

### 1. **Initialize a Swarm Cluster**
```bash
docker swarm init
```
This command initializes the current Docker host as a Swarm manager.

### 2. **Join a Node to the Swarm**
```bash
docker swarm join --token <SWARM_TOKEN> <MANAGER_IP>:2377
```
Use this command on a worker node to join the Swarm. Replace `<SWARM_TOKEN>` with the token provided by the manager and `<MANAGER_IP>` with the manager's IP address.

### 3. **List Nodes in the Swarm**
```bash
docker node ls
```
Run this on the manager node to view all nodes in the Swarm and their roles (manager or worker).

### 4. **Create a Service**
```bash
docker service create --name my-service --replicas 3 nginx
```
This creates a service named `my-service` with 3 replicas running the `nginx` image.

### 5. **List Services**
```bash
docker service ls
```
Displays all services running in the Swarm.

### 6. **Inspect a Service**
```bash
docker service inspect my-service
```
Provides detailed information about the specified service.

### 7. **Scale a Service**
```bash
docker service scale my-service=5
```
Scales the `my-service` to 5 replicas.

### 8. **List Tasks of a Service**
```bash
docker service ps my-service
```
Shows the tasks (containers) associated with a service and their status.

### 9. **Update a Service**
```bash
docker service update --image nginx:latest my-service
```
Updates the service to use a new image version.

### 10. **Remove a Service**
```bash
docker service rm my-service
```
Deletes the specified service from the Swarm.

### 11. **Leave the Swarm**
```bash
docker swarm leave
```
Use this command on a worker node to leave the Swarm. Add the `--force` flag if you’re removing a manager node.

### 12. **Rotate Join Tokens**
```bash
docker swarm join-token --rotate worker
```
Rotates the join token for worker nodes to enhance security.