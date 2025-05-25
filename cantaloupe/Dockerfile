FROM eclipse-temurin:17-jre-jammy

LABEL maintainer="Guanwei Liu <toyjack@gmail.com>" \
      version="5.0.7" \
      description="Cantaloupe IIIF Image Server"

# Use non-root user ID for better security
ARG CANTALOUPE_VERSION=5.0.7
ARG CANTALOUPE_UID=1000
ARG CANTALOUPE_GID=1000

ENV CANTALOUPE_HOME=/opt/cantaloupe \
    JAVA_OPTS="-Xmx2g" \
    CANTALOUPE_CONFIG=/opt/cantaloupe/cantaloupe.properties

# Create user early to use consistent UID/GID
RUN groupadd -g ${CANTALOUPE_GID} cantaloupe && \
    useradd -u ${CANTALOUPE_UID} -g cantaloupe -d ${CANTALOUPE_HOME} -s /bin/bash cantaloupe

# Download and verify in a single layer
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

# Copy configuration file with proper ownership
COPY --chown=cantaloupe:cantaloupe cantaloupe.properties ${CANTALOUPE_HOME}/cantaloupe.properties

# Set working directory
WORKDIR ${CANTALOUPE_HOME}

# Create mount points
VOLUME ["/var/cache/cantaloupe", "/var/log/cantaloupe"]

# Expose port with documentation
EXPOSE 8182

# Switch to non-root user
USER cantaloupe

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:8182/iiif/2 || exit 1

# Use exec form for better signal handling
ENTRYPOINT ["java"]
CMD ["-Dcantaloupe.config=/opt/cantaloupe/cantaloupe.properties", \
     "-Dfile.encoding=UTF-8", \
     "-Djava.awt.headless=true", \
     "-server", \
     "-Xmx2g", \
     "-XX:+UseG1GC", \
     "-XX:+UseStringDeduplication", \
     "-jar", \
     "/opt/cantaloupe/cantaloupe-5.0.7.jar"]