FROM ubuntu:18.04 as nacos-release

ARG NACOS_VERSION=2.1.1
ARG HOT_FIX_FLAG=""

# set environment
ENV MODE="standalone" \
    EMBEDDED_STORAGE="true" \
    PREFER_HOST_MODE="hostname"\
    BASE_DIR="/home/nacos" \
    FUNCTION_MODE="all" \
    NACOS_USER="nacos" \
    NACOS_GROUP="nacos" \
    JVM_XMS="1g" \
    JVM_XMX="1g" \
    JVM_XMN="512m" \
    JVM_MS="128m" \
    JVM_MMS="320m" \
    NACOS_DEBUG="n" \
    TOMCAT_ACCESSLOG_ENABLED="false" \
    TIME_ZONE="Asia/Shanghai"

ENV CLUSTER_CONF="${BASE_DIR}/conf/cluster.conf"

RUN set -eux \
    && apt-get update \
    && apt-get install -y --no-install-recommends wget \
    && apt-get install -y --no-install-recommends openjdk-11-jre \
    && apt-get install -y --no-install-recommends tzdata \
    && wget --no-check-certificate  https://github.com/alibaba/nacos/releases/download/${NACOS_VERSION}${HOT_FIX_FLAG}/nacos-server-${NACOS_VERSION}.tar.gz -P /home \
    && tar -xzvf /home/nacos-server-${NACOS_VERSION}.tar.gz -C /home \
    && rm -rf /home/nacos-server-${NACOS_VERSION}.tar.gz \
    && mkdir -p ${BASE_DIR}/logs \
	  && touch ${BASE_DIR}/logs/start.out \
    && touch ${BASE_DIR}/logs/start.error \
  	&& ln -sf /dev/stdout ${BASE_DIR}/logs/start.out \
	  && ln -sf /dev/stderr ${BASE_DIR}/logs/start.error \
    && groupadd -r ${NACOS_USER} \
    && useradd -r -g ${NACOS_USER} ${NACOS_GROUP} \
    && chown -R ${NACOS_USER}:${NACOS_GROUP} ${BASE_DIR} \
    && ln -fs /usr/share/zoneinfo/$TIME_ZONE /etc/localtime && dpkg-reconfigure -f noninteractive tzdata \
    && rm -rf ${BASE_DIR}/bin \
    && rm -rf ${BASE_DIR}/conf/1.4.0-ipv6_support-update.sql ${BASE_DIR}/conf/application.properties.example ${BASE_DIR}/conf/cluster.conf.example \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false

WORKDIR ${BASE_DIR}
USER ${NACOS_USER}

COPY --chown=${NACOS_USER}:${NACOS_GROUP} ./docker-startup.sh bin/docker-startup.sh

EXPOSE 8848 9848 9849 9555

ENTRYPOINT [ "bin/docker-startup.sh" ]


FROM ubuntu:18.04 as nacos-snapshot

# set environment
ENV MODE="standalone" \
    EMBEDDED_STORAGE="true" \
    PREFER_HOST_MODE="hostname"\
    BASE_DIR="/home/nacos" \
    FUNCTION_MODE="all" \
    NACOS_USER="nacos" \
    NACOS_GROUP="nacos" \
    JVM_XMS="1g" \
    JVM_XMX="1g" \
    JVM_XMN="512m" \
    JVM_MS="128m" \
    JVM_MMS="320m" \
    NACOS_DEBUG="n" \
    TOMCAT_ACCESSLOG_ENABLED="false" \
    TIME_ZONE="Asia/Shanghai"
    
ENV CLUSTER_CONF="${BASE_DIR}/conf/cluster.conf"

ADD nacos-server-snapshot.tar.gz /home

RUN set -eux \
    && apt-get update \
    && apt-get install -y --no-install-recommends wget \
    && apt-get install -y --no-install-recommends openjdk-11-jre \
    && apt-get install -y --no-install-recommends tzdata \
    && mkdir -p ${BASE_DIR}/logs \
	  && touch ${BASE_DIR}/logs/start.out \
    && touch ${BASE_DIR}/logs/start.error \
	  && ln -sf /dev/stdout ${BASE_DIR}/logs/start.out \
	  && ln -sf /dev/stderr ${BASE_DIR}/logs/start.error \
    && groupadd -r ${NACOS_USER} \
    && useradd -r -g ${NACOS_USER} ${NACOS_GROUP} \
    && chown -R ${NACOS_USER}:${NACOS_GROUP} ${BASE_DIR} \
    && ln -fs /usr/share/zoneinfo/$TIME_ZONE /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata \
    && rm -rf ${BASE_DIR}/bin \
    && rm -rf ${BASE_DIR}/conf/1.4.0-ipv6_support-update.sql ${BASE_DIR}/conf/application.properties.example ${BASE_DIR}/conf/cluster.conf.example \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false

WORKDIR ${BASE_DIR}
USER ${NACOS_USER}

COPY --chown=${NACOS_USER}:${NACOS_GROUP} ./docker-startup.sh bin/docker-startup.sh

EXPOSE 8848 9848 9849 9555

ENTRYPOINT [ "bin/docker-startup.sh" ]



FROM mysql:5.7 as mysql5.7

ENV NACOS_BASE_DIR="/home/nacos" \
    MYSQL_ROOT_PASSWORD="root" \
    MYSQL_DATABASE="nacos" \
    MYSQL_USER="nacos" \
    MYSQL_PASSWORD="nacos"

COPY --from=nacos-release ${NACOS_BASE_DIR}/conf/mysql-schema.sql  /docker-entrypoint-initdb.d/nacos-mysql.sql

FROM mysql:5.7 as mysql5.7-snapshot

ENV NACOS_BASE_DIR="/home/nacos" \
    MYSQL_ROOT_PASSWORD="root" \
    MYSQL_DATABASE="nacos" \
    MYSQL_USER="nacos" \
    MYSQL_PASSWORD="nacos"

COPY --from=nacos-snapshot ${NACOS_BASE_DIR}/conf/mysql-schema.sql  /docker-entrypoint-initdb.d/nacos-mysql.sql


FROM mysql:8 as mysql8

ENV NACOS_BASE_DIR="/home/nacos" \
    MYSQL_ROOT_PASSWORD="root" \
    MYSQL_DATABASE="nacos" \
    MYSQL_USER="nacos" \
    MYSQL_PASSWORD="nacos"

COPY --from=nacos-release ${NACOS_BASE_DIR}/conf/mysql-schema.sql  /docker-entrypoint-initdb.d/nacos-mysql.sql

FROM mysql:8 as mysql8-snapshot

ENV NACOS_BASE_DIR="/home/nacos" \
    MYSQL_ROOT_PASSWORD="root" \
    MYSQL_DATABASE="nacos" \
    MYSQL_USER="nacos" \
    MYSQL_PASSWORD="nacos"

COPY --from=nacos-snapshot ${NACOS_BASE_DIR}/conf/mysql-schema.sql  /docker-entrypoint-initdb.d/nacos-mysql.sql