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
