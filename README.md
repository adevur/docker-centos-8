# `adevur/centos-8` Docker Image

## Description
This is an unofficial Docker image with CentOS 8.0 installed. This image should be very similar to [`registry.redhat.io/ubi8`](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8): the main two differences are that `ubi8` is based on Red Hat Enterprise Linux 8.0, while `adevur/centos-8` is based on CentOS 8.0; and that `ubi8` has access to a very limited package repository of RHEL 8.0, while `adevur/centos-8` has access to the entire package repository of CentOS 8.0 (i.e. `Base`, `Extras` and `AppStream`).

## Tags
- [`latest`](https://github.com/adevur/docker-centos-8/blob/master/tag-latest/Dockerfile): this is similar to Red Hat's `ubi8`.
- [`init`](https://github.com/adevur/docker-centos-8/blob/master/tag-init/Dockerfile): this is similar to Red Hat's `ubi8-init`.

## Usage
In order to use this image, just type:
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

## Building
In order to build this image, you need:
1) A `rootfs` tarball that contains the filesystem. I've generated the tarball already (you can find it at `./tag-latest/centos-8-adevur.tar.xz`), but you can also generate it by yourself.
2) A kickstart script, in case you want to build the tarball yourself. I've already written a kickstart script (you can find it at `./tag-latest/centos-8-adevur.ks`). You can try to write one yourself too, if you want to customize something.
3) A `Dockerfile` (you can find an example at `./tag-latest/Dockerfile`).

### Create the Docker image itself
In case you already have the tarball, you can simply type:
```sh
# building latest tag
docker build --tag local/centos-8:latest ./tag-latest

# building init tag
# NOTE: you need to edit file './tag-init/Dockerfile' and change 'FROM adevur/centos-8:latest' to 'FROM local/centos-8:latest'
docker build --tag local/centos-8:init ./tag-init
```

## Create a tarball using a kickstart script
WORK IN PROGRESS
