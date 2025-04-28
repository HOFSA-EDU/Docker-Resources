In **example 11** we have created a compose file that allows *two containers* to work on the *same network*. Now we modify this file and add the *volume* that we created in **example 9**. This contains an *HTML file* with the name `hello.html`, which we now want to integrate into the infrastructure:
```yaml
.
.
.
  volumes:
   - data:/var/www/html
.
.
.

volumes:
 data:
  external: true
```

This is a *named volume*. We therefore only need the name of the volume to refer to it (`data`). The *right-hand* side of the assignment points to the storage location of our web server, where the HTML files are stored.

Just as with *networks*, *volumes* must also be defined more precisely at the end of the compose file. As we are again accessing an *existing volume* here, we need the option `external: true`.

---
The *final compose file* will then looked like this:
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
  volumes:
   - data:/var/www/html
  networks:
   - web_database_network
networks:
 web_database_network:
  external: true
volumes:
 data:
  external: true
```

---
### Start and stop
Now start the infrastructure with the command:
```bash
docker compose up -d
```

Then open your web browser and navigate to the address:
`127.0.0.1:8080/hello.html`

To shut down the infrastructure, you can use this command:
```bash
docker compose down
```
