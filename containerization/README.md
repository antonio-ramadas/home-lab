# Containerization

:construction: Work In Progress :construction:

There are multiple alternatives to run containers (e.g., [Podman](https://podman.io/)). For our use case, experience tells that sticking with the popular mature alternative (i.e., [Docker](https://www.docker.com/)) eases development due to the existing support and size of the existing community.

## Docker

[Install](https://github.com/docker/docker-install):

```bash
curl -fsSL https://get.docker.com | sh
```

Do [post install steps](https://docs.docker.com/engine/install/linux-postinstall/):

```bash
# Add current user to `docker` group to remove need for `sudo`
sudo usermod -aG docker $USER

# Log out and log in, or log in to the group
newgrp docker
```

### Running GUI containers

:construction: Work In Progress :construction:

We're going to stick with [x11docker](https://github.com/mviereck/x11docker). Installation details are present on the [README file](https://github.com/mviereck/x11docker#installation).

----

### Debug notes :bug:

Some errors I was facing were due to the lxde image not supporting the platform arm/v7. Luckily, someone else already [stumbled on the problem and posted the solution](https://github.com/mviereck/x11docker/issues/308). The image [andrewufrank/x11docker-lxde](https://hub.docker.com/r/andrewufrank/x11docker-lxde) adds support for it.

```bash
docker pull andrewufrank/x11docker-lxde

docker image tag andrewufrank/x11docker-lxde:latest lxde:latest
```

I am still not able to run `x11docker --desktop lxde` and see the desktop. It shows mostly a black screen with some icons with an error (like missing to find the file). I don't it is related to performance, but at this point I need to do some more digging.

_Perhaps the issue [does it work on arm / RaspBerry Pi?](https://github.com/mviereck/x11docker/issues/100) can provide greater insight._
