# `adevur/centos-8` Docker Image

## Description
This is an unofficial Docker image with CentOS 8.0 installed. This image should be very similar to [`registry.redhat.io/ubi8`](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8): the main two differences are that `ubi8` is based on Red Hat Enterprise Linux 8.0, while `adevur/centos-8` is based on CentOS 8.0; and that `ubi8` has access to a very limited package repository of RHEL 8.0, while `adevur/centos-8` has access to the entire package repository of CentOS 8.0 (i.e. `Base`, `Extras` and `AppStream`).

## Tags
- `latest` (for all supported architectures): this is similar to Red Hat's [`ubi8`](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8).

- [`latest-amd64`](https://github.com/adevur/docker-centos-8/blob/master/tag-latest/Dockerfile.amd64): same as `latest`, but only for `amd64` architecture.

- [`latest-arm64v8`](https://github.com/adevur/docker-centos-8/blob/master/tag-latest/Dockerfile.arm64v8): same as `latest`, but only for `arm64v8` architecture.

- [`init` (at the moment, only for `amd64` arch)](https://github.com/adevur/docker-centos-8/blob/master/tag-init/Dockerfile.amd64): this is similar to Red Hat's [`ubi8-init`](https://access.redhat.com/containers/?tab=overview#/registry.access.redhat.com/ubi8-init).

- [`systemd` (at the moment, only for `amd64` arch)](https://github.com/adevur/docker-centos-8/blob/master/tag-systemd/Dockerfile.amd64): this is similar to CentOS [`centos/systemd`](https://hub.docker.com/r/centos/systemd).

## Usage

### Tag `latest`
In order to use the `latest` tag, just type:
```sh
docker run -it --rm adevur/centos-8:latest /bin/bash
```

And you will get a `bash` terminal inside the container. You can check that you're running CentOS 8.0 by typing:
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
- In order to build tag `latest`, you need:

  1) A `rootfs` tarball that contains the filesystem. I've generated the tarball already (you can find it at `./tag-latest/centos-8-adevur0-amd64.tar.xz` for `amd64`, and at `./tag-latest/centos-8-adevur0-aarch64.tar.xz` for `arm64v8`), but you can also generate it by yourself.
  
  2) A kickstart script, in case you want to build the tarball yourself. I've already written a kickstart script (you can find it at `./tag-latest/centos-8-adevur0-amd64.ks` for `amd64`, and at `./tag-latest/centos-8-adevur0-aarch64.ks` for `arm64v8`). You can write one yourself too, if you want to customize something.
  
  3) A `Dockerfile` (you can find an example at `./tag-latest/Dockerfile.amd64` for `amd64`, and at `./tag-latest/Dockerfile.arm64v8` for `arm64v8`).
  
- In order to build `adevur/centos-8:init` and `adevur/centos-8:systemd`, you just need their `Dockerfile`s.

### Create the Docker image itself
In case you already have the tarball, you can simply type:
```sh
# building latest tag (for AMD64 arch)
docker build --tag local/centos-8:latest --file ./tag-latest/Dockerfile.amd64 ./tag-latest

# building latest tag (for ARM64v8 arch)
docker build --tag local/centos-8:latest --file ./tag-latest/Dockerfile.arm64v8 ./tag-latest

# building init tag
# NOTE: you need to edit file './tag-init/Dockerfile.amd64' and change 'FROM docker.io/adevur/centos-8:latest' to 'FROM local/centos-8:latest'
docker build --tag local/centos-8:init --file ./tag-init/Dockerfile.arm64v8 ./tag-init

# building systemd tag
# NOTE: you need to edit file './tag-systemd/Dockerfile.amd64' and change 'FROM docker.io/adevur/centos-8:latest' to 'FROM local/centos-8:latest'
docker build --tag local/centos-8:systemd --file ./tag-systemd/Dockerfile.arm64v8 ./tag-systemd
```

### Create a tarball using a kickstart script
In order to generate a `centos-8.tar.xz` tarball, we need a CentOS 7.x/8.x machine. In this tutorial, we're gonna use a container with `adevur/centos-8:latest` (that provides a CentOS 8 environment), so that we can generate the tarball on any Linux machine.

- First, let's create a directory on our system, where we'll put all the files we need (including the newly-generated tarball):
  ```sh
  mkdir /tarball-builder
  ```

- Let's put the kickstart script into `/tarball-builder`:
  ```sh
  # in this tutorial, we're gonna use the already-written kickstart script found on this GitHub,
  #   but you can also edit or rewrite this kickstart if you want to
  curl -sL https://raw.githubusercontent.com/adevur/docker-centos-8/master/tag-latest/centos-8-adevur0-amd64.ks > /tarball-builder/centos-8.ks
  ```

- Let's set up our environment with CentOS 8 installed in it:
  ```sh
  # NOTE: you can also set up the container with CentOS 7 installed, and it should work the same, but it's not been tested
  docker run -v /tarball-builder:/tarball-builder --privileged --name tarball-builder --rm -it adevur/centos-8:latest /bin/bash
  ```

- Now that we're inside the container, let's install the software needed to generate the tarball (i.e. packages `lorax` and `anaconda-tui`):
  ```sh
  yum clean all && yum install -y lorax anaconda-tui && yum clean all
  ```

- We can now generate the tarball and save it to `/tarball-builder`:
  ```sh
  cd /tarball-builder

  livemedia-creator --no-virt --make-tar --ks centos-8.ks --image-name=centos-8.tar.xz --project "CentOS 8 Docker" --releasever "8"

  mv /var/tmp/centos-8.tar.xz /tarball-builder/centos-8.tar.xz

  exit
  ```

- Now we can exit from the container: our tarball has been generated and is located at path `/tarball-builder/centos-8.tar.xz` on our computer. You can use this tarball to build tag `latest` of `adevur/centos-8` Docker image.
