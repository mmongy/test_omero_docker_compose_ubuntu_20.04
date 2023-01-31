# syntax=docker/dockerfile:1
FROM ubuntu:20.04
LABEL maintainer="ome-devel@lists.openmicroscopy.org.uk"

# Custom cache invalidation
ARG CACHEBUST=1

RUN mkdir /opt/setup
RUN mkdir /startup
WORKDIR /opt/setup
ADD ./omero-web/playbook.yml /opt/setup/
ADD ./omero-web/requirements.yml /opt/setup/

RUN apt update \
    #&& apt install -y epel-release \
    && apt install -y ansible coreutils curl sudo dumb-init \
    && ansible-galaxy install -p /opt/setup/roles -r requirements.yml \
    && apt -y autoclean \
    && apt -y autoremove \
    && rm -rf /var/lib/apt/lists/* /tmp/*

RUN ansible-playbook playbook.yml \
    && apt -y autoclean \
    && apt -y autoremove \
    && rm -rf /var/lib/apt/lists/* /tmp/*

COPY ./omero-web/entrypoint.sh /usr/local/bin/
RUN chmod 777 /usr/local/bin/entrypoint.sh
RUN ls -al /usr/local/bin/
COPY ./omero-web/50-config.py ./omero-web/60-default-web-config.sh ./omero-web/98-cleanprevious.sh ./omero-web/99-run.sh /startup/
ADD ./omero-web/ice.config /opt/omero/web/OMERO.web/etc/

RUN chmod -R 777 /opt/omero/web

USER omero-web
EXPOSE 4080
VOLUME ["/opt/omero/web/OMERO.web/var"]
ENV OMERODIR /opt/omero/web/OMERO.web/
#WORKDIR /opt/omero/web/venv3/bin
#CMD ["./omero", "web", "diagnostics"]

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
