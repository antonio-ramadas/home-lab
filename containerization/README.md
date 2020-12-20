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

_Perhaps the issue [does it work on arm / RaspBerry Pi?](https://github.com/mviereck/x11docker/issues/100) can provide greater insight._
