# `adevur/centos-8` Docker Image

## Description
This is an unofficial Docker image with CentOS 8.0 installed. This image should be very similar to [`registry.redhat.io/ubi8`](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8): the main two differences are that `ubi8` is based on Red Hat Enterprise Linux 8.0, while `adevur/centos-8` is based on CentOS 8.0; and that `ubi8` has access to a very limited package repository of RHEL 8.0, while `adevur/centos-8` has access to the entire package repository of CentOS 8.0 (i.e. `Base`, `Extras` and `AppStream`).

## Tags
- [`latest`](https://github.com/adevur/docker-centos-8/blob/master/tag-latest/Dockerfile): this is similar to Red Hat's [`ubi8`](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8).
- [`init`](https://github.com/adevur/docker-centos-8/blob/master/tag-init/Dockerfile): this is similar to Red Hat's [`ubi8-init`](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8-init).
- [`systemd`](https://github.com/adevur/docker-centos-8/blob/master/tag-systemd/Dockerfile): this is similar to CentOS [`centos/systemd`](https://hub.docker.com/r/centos/systemd).

## Usage

### Tag `latest`
In order to use the `latest` tag, just type:
```sh
docker run -it --rm adevur/centos-8:latest /bin/bash
```

And you will get a `bash` terminal inside the container. I suggest to install at least package `ncurses`, in order to have essential commands like `clear`:
```sh
yum clean all
yum install ncurses -y
```

You can check that you're running CentOS 8.0 by typing:
```sh
cat /etc/redhat-release
# EXPECTED OUTPUT: CentOS Linux release 8.0.1905 (Core)
```

### Tag `init`
Have a look at Red Hat's documentation for [`registry.redhat.io/ubi8-init` image](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8-init). `adevur/centos-8:init` should work the same as `ubi8-init`.

### Tag `systemd`
Have a look at CentOS documentation for [`docker.io/centos/systemd` image](https://github.com/CentOS/CentOS-Dockerfiles/tree/master/systemd/centos7). `adevur/centos-8:systemd` should work the same as `centos/systemd`.

You can use this tag to run systemd services in the background. For example:
```sh
# let's start the container in the background
docker run --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro -d --name my-container adevur/centos-8:systemd

# let's start a bash shell inside the running container
docker exec -it my-container /bin/bash

# now that we're inside the container, let's install ssh
yum clean all && yum -y install openssh-server && yum clean all

# now we can start the systemd service of ssh
systemctl start sshd.service

# let's check that ssh is running
systemctl status sshd.service
```

## Building
- In order to build `adevur/centos-8:latest`, you need:

  1) A `rootfs` tarball that contains the filesystem. I've generated the tarball already (you can find it at `./tag-latest/centos-8-adevur0-amd64.tar.xz`), but you can also generate it by yourself.
  
  2) A kickstart script, in case you want to build the tarball yourself. I've already written a kickstart script (you can find it at `./tag-latest/centos-8-adevur0.ks`). You can write one yourself too, if you want to customize something.
  
  3) A `Dockerfile` (you can find an example at `./tag-latest/Dockerfile`).
  
- In order to build `adevur/centos-8:init` and `adevur/centos-8:systemd`, you just need their `Dockerfile`s.

### Create the Docker image itself
In case you already have the tarball, you can simply type:
```sh
# building latest tag
docker build --tag local/centos-8:latest ./tag-latest

# building init tag
# NOTE: you need to edit file './tag-init/Dockerfile' and change 'FROM adevur/centos-8:latest' to 'FROM local/centos-8:latest'
docker build --tag local/centos-8:init ./tag-init

# building systemd tag
# NOTE: you need to edit file './tag-systemd/Dockerfile' and change 'FROM adevur/centos-8:latest' to 'FROM local/centos-8:latest'
docker build --tag local/centos-8:systemd ./tag-systemd
```

### Create a tarball using a kickstart script
WORK IN PROGRESS
