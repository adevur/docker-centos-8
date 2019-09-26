
# this Dockerfile is based on https://access.redhat.com/containers/?tab=docker-file#/registry.access.redhat.com/ubi8-init/images/8.0-22

FROM adevur/centos-8:latest

MAINTAINER adevur (madavurro@protonmail.com)

CMD ["/sbin/init"]

STOPSIGNAL SIGRTMIN+3

#mask systemd-machine-id-commit.service - partial fix for https://bugzilla.redhat.com/show_bug.cgi?id=1472439

RUN systemctl mask systemd-remount-fs.service dev-hugepages.mount sys-fs-fuse-connections.mount systemd-logind.service getty.target console-getty.service systemd-udev-trigger.service systemd-udevd.service systemd-random-seed.service systemd-machine-id-commit.service

RUN dnf clean all && dnf -y install procps-ng && dnf clean all

