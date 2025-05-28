FROM ubuntu:25.04

LABEL maintainer="Guanwei Liu <toyjack@gmail.com>"

ARG CANTALOUPE_VERSION=5.0.7 \
    JAVA_OPTS=-Xmx2g
ENV CANTALOUPE_HOME=/opt/cantaloupe \
    CANTALOUPE_DOWNLOAD_URL=https://github.com/cantaloupe-project/cantaloupe/releases/download/v${CANTALOUPE_VERSION}/cantaloupe-${CANTALOUPE_VERSION}.zip\
    CANTALOUPE_VERSION=${CANTALOUPE_VERSION} \
    JAVA_OPTS=${JAVA_OPTS} \
    DEBIAN_FRONTEND=noninteractive

RUN groupadd -r cantaloupe && useradd -r -g cantaloupe -d ${CANTALOUPE_HOME} cantaloupe

WORKDIR /tmp
ADD ${CANTALOUPE_DOWNLOAD_URL} /tmp/cantaloupe-${CANTALOUPE_VERSION}.zip

RUN apt-get update && \
    apt-get install -y --no-install-recommends unzip openjdk-17-jre-headless ca-certificates && \
    unzip cantaloupe-${CANTALOUPE_VERSION}.zip -d /opt/ && \
    mv /opt/cantaloupe-${CANTALOUPE_VERSION}/cantaloupe.properties.sample /opt/cantaloupe-${CANTALOUPE_VERSION}/cantaloupe.properties && \
    ln -s /opt/cantaloupe-${CANTALOUPE_VERSION} ${CANTALOUPE_HOME} && \
    mkdir -p /var/log/cantaloupe /var/cache/cantaloupe && \
    chown -R cantaloupe:cantaloupe ${CANTALOUPE_HOME} /var/log/cantaloupe /var/cache/cantaloupe && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/*

WORKDIR ${CANTALOUPE_HOME}
VOLUME ["/var/cache/cantaloupe", "/var/log/cantaloupe"]
EXPOSE 8182
USER cantaloupe
COPY cantaloupe.properties ${CANTALOUPE_HOME}/cantaloupe.properties

HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8182/health || exit 1

ENTRYPOINT ["java"]
CMD ["-Dcantaloupe.config=$CANTALOUPE_HOME/cantaloupe.properties", "$JAVA_OPTS", "-jar", "/opt/cantaloupe/cantaloupe-$CANTALOUPE_HOME.jar"]
