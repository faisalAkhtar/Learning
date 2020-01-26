# Docker

## Working in docker-hub
- Commit : `docker commit <container-id> <new-name-for-image>`
- Pull : `docker pull <repo-url>`
- Push : `docker push <repo-url>`

## System functions
- Docker info : `docker info`

## Images
3 stages : Run, Delete, Stop
- Run : `docker run -it <image-id>`
- Delete : `docker rmi <image-id>`
- Stop : `docker stop <image-id>`
- Force-stop : `docker stop -f <image-id>`
- All Images : `docker images`

## Creating a container
- For a website : `docker run -it -p <my-port>:<port-on-which-webste-was-hosted-before> -d <image-id>`

## Container
- Stop container : `docker stop <container-id>`
  - Force-stop container : `docker stop -f <container-id>`
- Delete container : `docker rm <container-id>`
- Restart a container : `docker restart <container-id>`
- Start a container : `docker start <container-id>`
- Root of container : `docker exec -it <container-id> bash`
- Active containers : `docker ps`
  - All containers : `docker ps -a`
- Remove all containers : `docker rm -f ${sudo docker ps -a -q}`
