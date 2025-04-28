In this demonstration we will create a container with a database. This should have a graphical user interface to perform configurations. The deployment should be executed with docker compose, so that the containers can be started one after the other with a single command and they can communicate with each other.

## Note
Docker compose not only allows the deployment of multiple containers, but also creates a user-defined bridge network for the infrastructure. This offers several advantages, such as the DNS function of Docker for communication with the containers.

## Step-by-step guide
First we have to create a file named: *compose.yml:*
```yaml
services:
  postgres:
    container_name: postgres
    image: postgres:latest
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PW}
      - POSTGRES_DB=${POSTGRES_DB} #optional (specify default database instead of $POSTGRES_DB)
    ports:
      - "5432:5432"
    restart: always

  pgadmin:
    container_name: pgadmin
    image: dpage/pgadmin4:latest
    environment:
      - PGADMIN_DEFAULT_EMAIL=${PGADMIN_MAIL}
      - PGADMIN_DEFAULT_PASSWORD=${PGADMIN_PW}
    ports:
      - "5050:80"
    restart: always
```

All instances that are part of our infrastructure are listed under services. The parameters are the same as those that can be used in a docker run command:
- **container-name**: defines the name of the running instance form our image
- **image**: the docker image that we want to use for the service
- **environment**: the environment variables that we need for a proper deployment of the service
- **ports**: the port where we can access the serves as also the internal port where the software is listening

> As you can see, we use variables here for sensitive data like password or usernames. The accomplish this, we use a .env file. Docker compose automatically looks for this file to link the variables.

Create the *.env* file:
```bash
POSTGRES_USER=user
POSTGRES_PW=test..123
POSTGRES_DB=postgres
PGADMIN_MAIL=hofsa@lgk.lu
PGADMIN_PW=test..123
```

