FROM eclipse-temurin:17-jre-jammy

LABEL maintainer="Guanwei Liu <toyjack@gmail.com>" \
      version="5.0.7" \
      description="Cantaloupe IIIF Image Server"

ARG CANTALOUPE_VERSION=5.0.7 \
    JAVA_OPTS=-Xmx2g
ENV CANTALOUPE_HOME=/opt/cantaloupe \
    CANTALOUPE_DOWNLOAD_URL=https://github.com/cantaloupe-project/cantaloupe/releases/download/v${CANTALOUPE_VERSION}/cantaloupe-${CANTALOUPE_VERSION}.zip\
    CANTALOUPE_VERSION=${CANTALOUPE_VERSION} \
    JAVA_OPTS=${JAVA_OPTS} \
    DEBIAN_FRONTEND=noninteractive

# Create user early to use consistent UID/GID
RUN groupadd -g ${CANTALOUPE_GID} cantaloupe && \
    useradd -u ${CANTALOUPE_UID} -g cantaloupe -d ${CANTALOUPE_HOME} -s /bin/bash cantaloupe

WORKDIR /tmp
ADD ${CANTALOUPE_DOWNLOAD_URL} /tmp/cantaloupe-${CANTALOUPE_VERSION}.zip

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        wget \
        unzip \
        ca-certificates && \
    # Download with checksum verification would be ideal here
    wget -O /tmp/cantaloupe.zip \
        "https://github.com/cantaloupe-project/cantaloupe/releases/download/v${CANTALOUPE_VERSION}/cantaloupe-${CANTALOUPE_VERSION}.zip" && \
    # Create directories
    mkdir -p /opt /var/log/cantaloupe /var/cache/cantaloupe && \
    # Extract and setup
    unzip /tmp/cantaloupe.zip -d /opt/ && \
    mv "/opt/cantaloupe-${CANTALOUPE_VERSION}" "${CANTALOUPE_HOME}" && \
    # Set permissions
    chown -R cantaloupe:cantaloupe ${CANTALOUPE_HOME} /var/log/cantaloupe /var/cache/cantaloupe && \
    chmod -R 755 ${CANTALOUPE_HOME} && \
    # Cleanup in same layer
    apt-get remove -y wget unzip && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR ${CANTALOUPE_HOME}
VOLUME ["/var/cache/cantaloupe", "/var/log/cantaloupe"]
EXPOSE 8182
USER cantaloupe
COPY cantaloupe.properties ${CANTALOUPE_HOME}/cantaloupe.properties

HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8182/health || exit 1

ENTRYPOINT ["java"]
CMD ["-Dcantaloupe.config=$CANTALOUPE_HOME/cantaloupe.properties", "$JAVA_OPTS", "-jar", "/opt/cantaloupe/cantaloupe-$CANTALOUPE_HOME.jar"]
