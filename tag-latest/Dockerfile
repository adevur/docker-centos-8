

FROM scratch

MAINTAINER adevur (madavurro@protonmail.com)

ADD centos-8-adevur0-amd64.tar.xz /

RUN yum clean all && yum update -y && yum autoremove -y && yum clean all

CMD ["/bin/bash"]

