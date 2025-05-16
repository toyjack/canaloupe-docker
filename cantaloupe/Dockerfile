FROM ubuntu:25.04

LABEL maintainer="Guanwei Liu <toyjack@gmail.com>"

ENV CANTALOUPE_VERSION=5.0.7 \
    CANTALOUPE_HOME=/opt/cantaloupe 

ARG CANTALOUPE_DOWNLOAD_URL=https://github.com/cantaloupe-project/cantaloupe/releases/download/v${CANTALOUPE_VERSION}/cantaloupe-${CANTALOUPE_VERSION}.zip

RUN groupadd -r cantaloupe && useradd -r -g cantaloupe -d ${CANTALOUPE_HOME} cantaloupe

WORKDIR /tmp
ADD ${CANTALOUPE_DOWNLOAD_URL} /tmp/cantaloupe-${CANTALOUPE_VERSION}.zip


# 安装依赖，解压并清理
RUN apt-get update && \
    apt-get install -y --no-install-recommends unzip openjdk-17-jre-headless ca-certificates && \
    unzip cantaloupe-${CANTALOUPE_VERSION}.zip -d /opt/ && \
    mv /opt/cantaloupe-${CANTALOUPE_VERSION}/cantaloupe.properties.sample /opt/cantaloupe-${CANTALOUPE_VERSION}/cantaloupe.properties && \
    ln -s /opt/cantaloupe-${CANTALOUPE_VERSION} ${CANTALOUPE_HOME} && \
    mkdir -p /var/log/cantaloupe /var/cache/cantaloupe && \
    chown -R cantaloupe:cantaloupe ${CANTALOUPE_HOME} /var/log/cantaloupe /var/cache/cantaloupe && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/*

# 设置工作目录
WORKDIR ${CANTALOUPE_HOME}

# 配置卷
VOLUME ["/var/cache/cantaloupe", "/var/log/cantaloupe"]

# 暴露端口
EXPOSE 8182

# 切换到非root用户
USER cantaloupe

# 添加配置文件
COPY cantaloupe.properties ${CANTALOUPE_HOME}/cantaloupe.properties

# 启动Cantaloupe
ENTRYPOINT ["java"]
CMD ["-Dcantaloupe.config=/opt/cantaloupe/cantaloupe.properties", "-Xmx2g", "-jar", "/opt/cantaloupe/cantaloupe-5.0.7.jar"]