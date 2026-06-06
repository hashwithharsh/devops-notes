# Docker Troubleshooting Playbook

When a container doesn't work, I try to identify which layer is actually failing:

```text
image
 ↓
container process
 ↓
configuration
 ↓
network
 ↓
volume
 ↓
application
```

## Container exits

```bash
docker ps -a
docker logs <container>
docker inspect <container>
```

The first question is what process was supposed to remain running.

## Port is not reachable

```bash
docker ps
docker port <container>
docker inspect <container>
curl -v http://127.0.0.1:<port>
```

A common application issue is binding to `127.0.0.1` inside the container instead of `0.0.0.0`.

## Container cannot reach another container

```bash
docker network ls
docker network inspect <network>
```

I check whether both containers are attached to the intended network and whether the service is listening.

## Data disappeared

I ask whether the data lived in:

```text
container writable layer
or
volume / bind mount
```

Removing the container removes data that was only in its writable layer.

## Image is too large

```bash
docker history <image>
```

Then I look for unnecessary packages, large build artifacts, missing `.dockerignore` and opportunities for multi-stage builds.

My main habit is to inspect the actual container state rather than guessing what Docker did.
