docker-ubuntu - Custom docker image for development
---------------------------------------------------------------

#### Docker Installation


#### Docker Cheatsheets

```
docker build -f <Dockerfile> -t <tagname>
```

#### Docker Cleanup Commands

```
docker ps -q -f status=exited | xargs --no-run-if-empty docker rm
docker images -q -f dangling=true | xargs --no-run-if-empty docker rmi
docker volume ls -qf dangling=true | xargs -r docker volume rm
docker-compose down
```

