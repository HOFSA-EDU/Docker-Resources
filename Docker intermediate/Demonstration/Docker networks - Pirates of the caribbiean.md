>This demonstration is based on the illustrations [[Network illustration1.png]], [[Network illustration2.png]], [[Network illustration2.png]]. Open them to help you follow the commands.

Docker has different network drivers that are used to configure how containers communicate with each other and with the outside world. To list all currently running networks, you can use this command:
```bash
docker network ls
```

default output:
```bash
NETWORK ID     NAME                   DRIVER    SCOPE
4e0ffa217146   bridge                 bridge    local
f9aec3ec204f   host                   host      local
e4212e179f50   none                   null      local
```

 Among these, *default*, *bridge*, and *none* networks each serve specific purposes:
 - **Default network:** This is not a specific type of network but refers to the network configuration applied to containers if you don't explicitly specify one. Typically, this will be the "bridge" network unless you've customized Docker's settings.
    
- **Bridge network:** The most common type, it's a private internal network created by Docker. Containers within the bridge network can communicate with each other directly, and they can also access external networks via NAT (Network Address Translation). It's great for isolating containers while allowing limited external connectivity.
    
- **None network:** This network essentially disables networking for the container. Containers attached to the "none" network can’t communicate with other containers or the host system. This is useful for scenarios where you want to strictly isolate a container.

### Let's deploy some containers: (shown in the illustration)
```bash
docker run -itd --name jack busybox
docker run -itd --name gibbs busybox
docker run -itd --name barbossa busybox
```
*Jack*, *Gibbs* and *Barbosses* were created without specifying the network. They are thus connected to the *default bridge*.

You can see all the connected containers to a network by inspecting it:
```bash
docker inspect bridge
```

Output:
```bash
"Containers": {
            "46ce289e855a34c2ded32152b1ca1d5b09f1f66fd01473db1bda8d946ea4bdd2": {
                "Name": "jack",
                "EndpointID": "3297255de2098bf8f0190fdda4959285b9c84221b5646e43103f502e93bace69",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
```

One container can also talk to other containers in the same network. Let's jump into the container *Gibbs* and ping container *Jack*:
```bash
docker exec -it gibbs sh
ping 172.17.0.2
```

That works perfectly! Now let's see, if we can ping Jack by using the container name:
```bash
docker exec -it gibbs sh
ping jack
```

Saidly, this will not work!
> The reason for this is, that the default bridge network does not provide an *internal DNS*. This behaviour limits our possibility to create an infrastructure with *multiple containers*. With this method, we have to create our own ip-table with the addresses from all our containers.
> If a container restarts, it will also changes his ip-address! *Lucky us, that there is an easy way to handle this!*

### User-defined bridge network

Let's create our own network:
```bash
docker network create blackpearl
docker network ls
```

Output:
```bash
8fb7e0204db2f751eb72d3d03a3496da54f1cd71e8fdb3d226bc996fbd197c88
NETWORK ID     NAME                   DRIVER    SCOPE
8fb7e0204db2   blackpearl             bridge    local
4e0ffa217146   bridge                 bridge    local
f9aec3ec204f   host                   host      local
e4212e179f50   none                   null      local
```

To deploy containers on our network, we can us the *--network parameter* in the docker run command:
```bash
docker run -itd --name will --network blackpearl busybox
docker run -itd --name jan --network blackpearl busybox
```

### Reasons to use user-defined bridge networks
Using user-defined bridge networks to deploy our containers in Docker gives us several advantages, making it a popular choice for developers:

- **Improved container communication**: Containers connected to the same user-defined bridge network can easily communicate with each other using container names as hostnames. This simplifies the setup for inter-container networking.
    
- **Isolation and security**: By creating a user-defined bridge network, you can isolate your containers from others on the default bridge network, enhancing security and preventing unwanted interactions.
    
- **Customizable settings**: User-defined networks allow you to configure advanced options, such as IP address ranges, subnet masks, and DNS settings, giving you better control over your container networking.
    
- **Support for container scalability**: With user-defined bridge networks, you can seamlessly add or remove containers from the network without disrupting the communication between existing containers.

---
We now repeat the exercise from above. This means that *container Will* now tries to reach *container Jan* with a *ping*. This time we use the direct name of the container directly:
```bash
docker exec -it will sh
ping jan
```

>By using the internal DNS form docker, we are able to reach containers with the provided name. No ip-address needed.

### Conclusion
User-defined bridge networks are the way to go for deploying infrastructure in docker. They provide us a lot of features to handle them and to build up a communication pipeline. These networks are the key for docker compose an needed to be understand before we continue with deeper docker features.