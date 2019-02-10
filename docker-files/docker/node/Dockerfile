FROM centos:7

# CentOS port by Jim O'Halloran.  Based on work by Chris Fidao in the
# Shipping Docker Vessel project.
LABEL maintainer="Jim O'Halloran <jim@jimohalloran.com>"

WORKDIR /var/www/html

ARG uid=999

ADD setup_10.x /tmp/setup_10.x
ADD yarn.repo /etc/yum.repos.d/yarn.repo

RUN yum install -y gcc-c++ make git \
    && chmod a+x /tmp/setup_10.x \
    && /tmp/setup_10.x \
    && rm /tmp/setup_10.x \
    && yum install -y nodejs yarn \
    && yum clean all

RUN useradd -u $uid node

CMD [ "node" ]
