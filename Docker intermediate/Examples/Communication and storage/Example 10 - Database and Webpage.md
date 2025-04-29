#### **Learning Goals**
- Learn how to set up and connect multiple containers using Docker networks.
- Understand how to deploy a database container and a web application container.
- Test communication between the database and the webpage via a custom Docker network.

#### **Step-by-Step Guide**

**Step 1: Preparation**
- Create a directory for the lab:
```bash
mkdir example9 && cd example9
```

**Step 2: Create a Custom Network**
- Set up a custom network named `web_database_network`:
    ```bash
docker network create web_database_network
    ```
    
**Step 3: Deploy a Database Container**
- Run a MySQL database container connected to the custom network:
```bash
docker run -d \
--name database \
--network web_database_network \
-e MYSQL_ROOT_PASSWORD=rootpassword \
-e MYSQL_DATABASE=labdb \
mysql:latest
```
> The --network tag in the docker run command defines the network for our docker container

Environment:
- `MYSQL_ROOT_PASSWORD`: Password for the root user.
- `MYSQL_DATABASE`: Name of the database (`labdb`).

-  Verify the database container is running:
```bash
docker ps
```
    
**Step 4: Create a Simple Web Application**

-  Create a file named `index.php` with the following PHP code:
```php
    <?php
    $host = 'database';
    $db = 'labdb';
    $user = 'root';
    $password = 'rootpassword';
    
    $conn = new mysqli($host, $user, $password, $db);
    
    if ($conn->connect_error) {
        die('Connection failed: ' . $conn->connect_error);
    }
    echo "Connected to the database successfully!";
    ?>
```

>This PHP script attempts to connect to the database container using its network alias (`database`).

-  Create a `Dockerfile` for the web application:
```dockerfile
FROM php:7.4-apache
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
COPY index.php /var/www/html/index.php
EXPOSE 80
```
    
>The `Dockerfile` sets up a PHP environment and copies the web application script (index.php) into the container.
    
-  Build the web application container image:
```bash
docker build -t webapp .
```
    
>With `-t` you define a tag for your image. In this case: `webapp`.

**Step 5: Deploy the Web Application Container**

-  Run the container with the user defined network:
```bash
docker run -d \
--name webapp \
--network web_database_network \
-p 8080:80 \
webapp
```

> The `-p` tag maps port `8080` of your host to port `80` of the web application container.
    
- Verify the web application is running:
``` bash
docker ps
```


**Step 6: Test the Setup**
- Open your web browser and navigate to:
```
http://localhost:8080
```
    
- You should see the message: "Connected to the database successfully!"

**Step 7: Check the connection**
You can now check the connection by switching off the database:
```bash
docker stop database
```
Reload the website and view the output.

Then restart the database:
```bash
docker start database
```

After reloading, the existing connection to the database should be confirmed again by the website.

**Step 8: Cleanup**
1. Stop and remove the containers:
``` bash
docker stop webapp database
docker rm webapp database
```

2. Remove the custom network:
```bash
docker network rm web_database_network
```

### **Summary**
In this lab, you created a custom Docker network (a user defined bridge) and deployed two containers: 
- 1 MySQL database 
- 1 PHP-based web application 

You demonstrated how containers communicate using Docker networks, and successfully tested their connectivity. This setup mimics real-world applications where a backend database interacts with a frontend. 
