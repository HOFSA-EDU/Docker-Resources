docker build . -t python_app
docker run -d --name logs1 python_app
docker exec -it logs1 /bin/bash
cd logs
cat info.log
docker stop logs1 && docker rm logs1

Start a new container again and watch the logs
docker run -d --name logs1 -v python_data:/app/logs python_app
docker exec -it logs1 /bin/bash
docker run -d --name logs2 -v python_data:/app/logs python_app

See the available commands
docker volume

Create bind volume
docker docker run -d --name logs3 -v /hostlogs:/app/logs python_app
Bind volumes are not visible. So dockerD does not create a volume object for these kind of volumes
docker volume ls