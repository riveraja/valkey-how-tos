# Docker Compose setup

```
cd tools/docker
docker compose up -d
docker exec -it docker-valkey1-1 sh
```
### From inside the docker container
```
valkey-cli --cluster create valkey1:6379 valkey2:6379 valkey3:6379 valkey4:6379 valkey5:6379 valkey6:6379 valkey7:6379 valkey8:6379 valkey9:6379 --cluster-replicas 2
```

### Test routing through one of the Valkey containers
```
docker exec -it docker-valkey1-1 sh
data /# redis-cli -c -h haproxy