# syntax=docker/dockerfile:1
FROM ubuntu:20.04
LABEL maintainer="ome-devel@lists.openmicroscopy.org.uk"

# Custom cache invalidation
ARG CACHEBUST=1

RUN mkdir /opt/setup
RUN mkdir /startup
WORKDIR /opt/setup
ADD ./omero-server/playbook.yml /opt/setup/
ADD ./omero-server/requirements.yml /opt/setup/

RUN apt update \
    #&& apt install -y epel-release \
    && apt install -y ansible coreutils curl sudo dumb-init openssl python-openssl\
    && ansible-galaxy install -p /opt/setup/roles -r requirements.yml \
    && apt -y autoclean \
    && apt -y autoremove \
    && rm -rf /var/lib/apt/lists/* /tmp/*

ARG OMERO_VERSION=5.6.6
ARG OMEGO_ADDITIONAL_ARGS=


RUN ansible-playbook playbook.yml \
    -e omero_server_release=$OMERO_VERSION \
    -e omero_server_omego_additional_args="$OMEGO_ADDITIONAL_ARGS" \
    && apt -y autoclean \
    && apt -y autoremove \
    && rm -rf /var/lib/apt/lists/* /tmp/*

COPY ./omero-server/entrypoint.sh /usr/local/bin/
RUN chmod 777 /usr/local/bin/entrypoint.sh
RUN ls -al /usr/local/bin/
COPY ./omero-server/50-config.py ./omero-server/60-database.sh ./omero-server/99-run.sh /startup/

RUN chmod -R 777 /opt/omero/server

USER omero-server
EXPOSE 4063 4064
VOLUME ["/OMERO", "/opt/omero/server/OMERO.server/var"]
ENV OMERODIR=/opt/omero/server/OMERO.server/
#ENV ICE_HOME /opt/ice-3.6.5
#ENV PATH "$ICE_HOME/bin:$PATH"
#ENV LD_LIBRARY_PATH "$ICE_HOME/lib64:$ICE_HOME/lib:$LD_LIBRARY_PATH"
#ENV SLICEPATH "$ICE_HOME/slice"
#ENV OMERODIR /opt/omero/server/OMERO.server
#WORKDIR /opt/omero/server/venv3/bin
#CMD ["./omero", "admin", "diagnostics"]

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]