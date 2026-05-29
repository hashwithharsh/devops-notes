# Container Docker Notes

**Author:** Harsh Yadav

My Docker/container notes covering images, Dockerfiles, networking, storage, Compose and debugging.

I am keeping this as a long-term study/reference repo. The useful part for me is the combination of explanation, practical commands, small labs and the troubleshooting notes I tend to forget.

I don't expect every note to have exactly the same structure. I write more detail for topics that I actually need to revisit often.


## My placement reference

## My Docker troubleshooting habit

A running container is not automatically a healthy application.

I separate:

```text
container process
application
port
network
configuration
storage
```

Then I inspect the layer that matches the symptom. `docker ps` tells me the container state; it does not tell me that the application is behaving correctly.

## How I study this repo

I learn Docker by taking one small application and gradually adding features.

```text
Docker basics
   ↓
images
   ↓
Dockerfile
   ↓
build / run
   ↓
ports
   ↓
environment variables
   ↓
volumes
   ↓
networks
   ↓
Compose
   ↓
image optimization / security
   ↓
troubleshooting
```

After running a container I inspect it rather than assuming it is correct:

```bash
docker ps -a
docker inspect <container>
docker logs <container>
```

I deliberately break ports, configuration or application startup in a lab and troubleshoot the result.

For interview revision, I focus on the difference between an image and a container, container networking, persistent storage, Dockerfile layers and why a container can be running while the application is still unhealthy.
