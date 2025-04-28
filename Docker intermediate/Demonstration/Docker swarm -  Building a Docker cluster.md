## Prerequisites
- Docker installed on all three nodes.
- Three machines with unique IP addresses (can be physical or virtual).

> For this demonstration, we use the [Play With Docker Website](https://labs.play-with-docker.com/).

### Step 1: Initialize Docker Swarm on the Manager Node
1. Log in to the `manager-node`.
2. Run the following command to initialize the Swarm:
```bash
docker swarm init --advertise-addr <MANAGER_IP>
```

>Replace `<MANAGER_IP>` with the actual IP address of the manager node. This command will output a `join token`.


### Step 2: Join Worker Nodes to the Swarm
1. Log in to `worker-node-1`.
2. Use the token provided by the `manager-node` to join the swarm:
```bash
docker swarm join --token <SWARM_TOKEN> <MANAGER_IP>:2377
```

>Replace `<SWARM_TOKEN>` with the token and `<MANAGER_IP>` with the manager node’s IP.

3. Repeat the same steps on `worker-node-2`.
4. On the manager node, verify the nodes in the swarm using:
```bash
docker node ls
```

>You can see your swarm architecture with the docker swarm visualizer:
```bash
docker run -it -d -p 8080:8080 -v /var/run/docker.sock:/var/run/docker.sock dockersamples/visualizer
```
### Step 3: Deploy a Service to the Swarm
1. On the `manager-node`, create and deploy a service. For example:  
```bash
docker service create --replicas 3 --name mynginx-service nginx
```

>This command deploys the `nginx` service with three replicas (containers) distributed across the swarm.

### Step 4: Verify the Service
1. On the `manager-node`, check the service status:    
```bash
docker service ls
```
    
2. To view the specific tasks and their node assignments:
```bash
docker service ps mynginx-service
```
    
3. On any worker node, verify that the service is running by checking container status:
```bash
docker ps    
```


## Docker container vs. Docker services
The difference between a *service* and a *container* in Docker lies in their scope and purpose:

- A **service** is a *higher-level abstraction* in Docker Swarm used for *orchestrating containers* across a *cluster* of *nodes*.
- **Services** define how containers are *deployed*, *replicated*, and *distributed* in a *swarm*. For instance, you specify the image, number of replicas, and networking settings for a service.
- **Services** ensure *load balancing*, *redundancy*, and *fault tolerance*. If a container within a service fails, Docker Swarm *automatically* *redeploys* another one to maintain the desired state.
- **Services** are only available when using Docker Swarm mode for container orchestration.
