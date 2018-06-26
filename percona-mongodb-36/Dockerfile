FROM debian:stretch-slim

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r mongodb && useradd -r -g mongodb mongodb

RUN apt-get update && apt-get install -y --no-install-recommends \
      ca-certificates jq numactl procps lsb-release wget gnupg dirmngr; \
	rm -rf /var/lib/apt/lists/*

# grab gosu for easy step-down from root (https://github.com/tianon/gosu/releases)
ENV GOSU_VERSION 1.10
# grab "js-yaml" for parsing mongod's YAML config files (https://github.com/nodeca/js-yaml/releases)
ENV JSYAML_VERSION 3.12.0

RUN set -ex; \
	dpkgArch="$(dpkg --print-architecture)"; \
	wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-${dpkgArch}"; \
	wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-${dpkgArch}.asc"; \
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
	gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
	rm -r "${GNUPGHOME}" /usr/local/bin/gosu.asc; \
	chmod +x /usr/local/bin/gosu; \
	gosu nobody true; \
	wget -O /js-yaml.js "https://github.com/nodeca/js-yaml/raw/${JSYAML_VERSION}/dist/js-yaml.js"
# TODO some sort of download verification here

RUN mkdir /docker-entrypoint-initdb.d
RUN mkdir /docker-entrypoint-extras.d

ENV MONGO_PACKAGE percona-server-mongodb
ENV MONGO_MAJOR 36

# Add Percona key (https://www.percona.com/blog/2016/10/13/new-signing-key-for-percona-debian-and-ubuntu-packages/)
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8507EFA5
RUN echo "deb http://repo.percona.com/apt "$(lsb_release -sc)" main" | tee /etc/apt/sources.list.d/percona.list

RUN apt-get update && apt-get install -y \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-server \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-shell \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-mongos \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-tools

RUN apt-get purge -y --auto-remove wget lsb-release gnupg dirmngr; \
    rm -rf /var/lib/apt/lists/*; \
    rm -rf /var/lib/mongodb;

RUN mv /etc/mongod.conf /etc/mongod.conf.orig; \
    mkdir -p /data/db /data/configdb; \
	chown -R mongodb:mongodb /data/db /data/configdb

VOLUME /data/db /data/configdb

COPY docker-entrypoint.sh /usr/local/bin/

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 27017
CMD ["mongod"]