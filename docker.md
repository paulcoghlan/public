# Docker

## Examples

- Shell into container: `docker exec -it my-container bash`
- Remove images from repo: `docker rmi $(docker images -q <repository_name>)`
- Show lables: `docker ps --format "table {{.ID}}\t{{.Labels}}"`
- List container IDs: `docker ps -a | tail -n +2 | cut -d" " -f1`
- Remove stopped containers: `docker container prune`
- Stats: `docker stats $CONTAINER_ID`
- Update config: `docker update --cpu-shares 512 abebf7571666`

- Find Docker container relating to host PID:

```sh
pstree -aps <PID>
docker ps | grep <first 5 chars of long ID string>
```

- Use `nsenter` to do networking stuff (like tcpdump or tshark) inside containers

```sh
B_PID=$(docker inspect --format '{{ .State.Pid }}' <container_name>)
sudo nsenter -t $B_PID -n
```
## Distroless and Multi-stage builds

[multi-stage/](https://docs.docker.com/build/building/multi-stage/) builds use multiple `FROM` statements, each is a different stage which can pull in an artifact from a previous stage using `COPY --from=0`

https://github.com/GoogleContainerTools/distroless images do not contain package managers, shells or any other programs you would expect to find in a standard Linux distribution.