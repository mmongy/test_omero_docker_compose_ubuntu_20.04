# syntax=docker/dockerfile:1
FROM ubuntu:20.04
LABEL maintainer="ome-devel@lists.openmicroscopy.org.uk"


RUN mkdir /opt/setup
WORKDIR /opt/setup
ADD ./postgresql/playbook.yml /opt/setup/
ADD ./postgresql/requirements.yml /opt/setup/

RUN apt update \
    #&& apt install -y epel-release \
    && apt install -y ansible coreutils curl sudo dumb-init openssl python-openssl\
    && ansible-galaxy install -p /opt/setup/roles -r requirements.yml \
    && apt -y autoclean \
    && apt -y autoremove \
    && rm -rf /var/lib/apt/lists/* /tmp/*

RUN ansible-playbook playbook.yml \
    && apt -y autoclean \
    && apt -y autoremove \
    && rm -rf /var/lib/apt/lists/* /tmp/*

USER postgres
EXPOSE 5432

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh", "postgres"]
#ENTRYPOINT ["/usr/local/bin/entrypoint.sh", "postgres"]
