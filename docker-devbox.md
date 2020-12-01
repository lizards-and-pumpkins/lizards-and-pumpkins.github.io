---
layout: wiki
title: Docker DevBox
---
## Docker DevBox

### Run with docker-compose
Edit `docker-compose.yaml` and set `BASE_URL`, `PORT` according to your needs.

```
docker-compose up -d
```

### Run docker container

```
docker run --name lizards-devbox -p 127.0.0.1:8080:80 --env BASE_URL=127.0.0.1 --env PORT=8080 -tid lizardsandpumpkins/devbox:0.3
```

### Build new docker image

```
docker build -t lizardsandpumpkins/devbox:<version> .
```
