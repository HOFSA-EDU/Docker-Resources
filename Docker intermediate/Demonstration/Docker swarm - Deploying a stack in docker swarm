## **Learning Objectives**

By the end of this exercise, you will be able to:
- Understand the difference between standalone Docker and Docker Swarm (Manager vs. Worker, Raft consensus, Overlay networks).
- Initialize a Docker Swarm and join multiple nodes.
- Deploy and scale services across the cluster.
- Perform rolling updates and observe load balancing.
- Put nodes into maintenance mode (drain) and verify self-healing behavior.

## **Setup & Requirements**

- A team of **3 Raspberry Pis**:
    - 1 Manager node (`pi-mgr`)
    - 2 Worker nodes (`pi-w1`, `pi-w2`)
- All Pis must be connected to the same network (e.g., `192.168.x.x`).
- Docker Engine and Docker Compose plugin installed.
- SSH access to each device.
- The `docker` group enabled for your user.

## **Part A — Initialize the Swarm (≈25 min)**

1. On the Manager, initialize the swarm:
    ```bash 
docker swarm init --advertise-addr <MANAGER_IP> docker swarm join-token worker
    ```

Copy the worker join token.

2. On each Worker, join the swarm:
	```bash 
docker swarm join --token <TOKEN> <MANAGER_IP>:2377
	```

3. Verify the cluster from the Manager:
	`docker node ls`
	
4. Label your nodes
```bash 
docker node update --label-add zone=front pi-w1
docker node update --label-add zone=back  pi-w2
```

## **Part B — Deploy a Sample Stack (≈25 min)**

We will deploy:
- **whoami** (lightweight HTTP echo service) with multiple replicas.
- **Visualizer** (a simple Swarm monitoring tool) running on the Manager only.

1. Create a `stack.yml` file on the Manager:
```yaml
version: "3.8"
services:
  whoami:
    image: traefik/whoami:latest
    deploy:
      replicas: 5
      update_config:
        parallelism: 1
        delay: 10s
        order: start-first
      restart_policy:
        condition: on-failure
      placement:
        preferences:
          - spread: node.id
    networks: front

  visualizer:
    image: dockersamples/visualizer:stable
    ports: "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints:
          - node.role == manager
    networks: front

networks:
  front:
    driver: overlay
    attachable: true
    ```
    
    
2. Deploy the stack:
```bash
docker stack deploy -c stack.yml demo_whoami 
docker stack services demo_whoami
docker stack ps demo_whoami
```
    
3. Test the service from any node:
```bash
curl http://<NODE_IP>/
```
    
Multiple requests should return different container IDs → load balancing works.
    
4. Open the Visualizer in your browser:  
`http://<MANAGER_IP>:8080`
    

---

## **Part C — Scaling and Rolling Updates (≈20 min)**

1. Scale the service:
    ```bash
docker service scale demo_whoami=8 
docker service ps demo_whoami
    ```
    
2. Trigger a rolling update:
    ```bash
docker service update --env-add VERSION=$(date +%H%M%S) demo_whoami
docker service ps demo_whoami
    ```
    
    Observe uninterrupted responses while containers are updated one by one.


---

## **Part D — Node Maintenance & Self-Healing (≈15 min)**

1. Put one Worker into maintenance mode:
    ```bash 
docker node update --availability drain pi-w1
docker service ps demo_whoami
    ```

    Tasks will be rescheduled to other nodes.
    
2. Reactivate the node:
    
    ```bash 
docker node update --availability active pi-w1
    ```
    
3. (Optional) Stop Docker on one Worker and observe automatic rescheduling:
    
    ```bash 
sudo systemctl stop docker
    ```
    
4. (Optional) Add constraints to distribute the tasks

```bash
docker service update \
  --constraint-add node.labels.zone==front \
  demo_whoami
docker service ps demo_whoami
```

Tasks are running on nodes with the label `zone=front`

---

## **Deliverables**

Each team must provide:
1. Screenshots or logs showing:
    - Cluster initialization (`docker node ls`)
    - Service deployment and scaling (`docker stack ps`)
    - Rolling update logs
    - Node drain/activation behavior
        
2. A short reflection (5–10 sentences):
    - What worked well?
    - What surprised you?
    - Where could this fail in a real production setup?
