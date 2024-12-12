Docker provides a robust networking system that allows containers to communicate with each other, the host machine, and external networks. Here are the key aspects of Docker networking:

# Network Drivers

Docker includes several built-in network drivers, each suited for different use cases:

1. **Bridge:**
    - The default network driver. It creates a private internal network on the host so that containers on this network can communicate with each other. Containers connected to the bridge network can also access external networks through the host.
1. **Host**:
    - Removes network isolation between the container and the Docker host. Containers using the host network share the host's IP address and network stack, which can improve performance but reduces isolation.
2. **Overlay**:
    - Connects multiple Docker daemons together, allowing containers running on different hosts to communicate. This is useful for distributed applications and Docker Swarm clusters.
3. **Macvlan**:
    - Assigns a MAC address to each container, making it appear as a physical device on the network. This allows containers to be directly accessible on the physical network.
4. **None**:
    - Disables all networking for the container. This is useful for containers that do not need network access.

# Key Networking Concepts

1. **Container Communication**:
    - Containers can communicate with each other using their IP addresses or container names if they are on the same network. User-defined networks allow for more complex setups and better control over container communication.

2. **Port Publishing**:
    - To make a container's service accessible from outside the Docker host, you can publish ports using the `-p` or `--publish` flag. This maps a port on the host to a port on the container.

3. **DNS Services**:
    - Docker provides built-in DNS resolution for containers. By default, containers use the same DNS servers as the host, but you can customize this with the `--dns` flag.

4. **IP Address Management**:
    - Docker dynamically assigns IP addresses to containers from the subnet of the network they are connected to. You can also specify static IP addresses if needed.

# Example Commands

- **Creating a Bridge Network**:
    
    ```bash
    docker network create -d bridge my-bridge-network
    ```
    
- **Running a Container on a Custom Network**:
    
    ```bash
    docker run --network=my-bridge-network -itd --name=my-container busybox
    ```
    
- **Publishing a Port**:
    
    ```bash
    docker run -p 8080:80 nginx
    ```
    

Docker's networking capabilities provide flexibility and control, making it easier to manage containerized applications in various environments.

# Network Troubleshooting

- Use the `docker network inspect` command to check the configuration and status of Docker networks:

```bash
  docker network inspect <network_name>
```

- Ensure that containers are connected to the correct network and that there are no IP conflicts.

# Links
[[Docker compose]]
[[Docker container]]
