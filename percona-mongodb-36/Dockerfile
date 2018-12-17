FROM debian:stretch-slim

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r mongodb && useradd -r -g mongodb mongodb

RUN apt-get update && apt-get install -y \
      ca-certificates jq numactl procps wget gnupg lsb-release dirmngr git gcc make; \
	rm -rf /var/lib/apt/lists/*

# grab gosu for easy step-down from root (https://github.com/tianon/gosu/releases)
# ENV GOSU_VERSION 1.10
RUN git clone https://github.com/ncopa/su-exec.git
RUN cd su-exec && make all
RUN mv /su-exec/su-exec /usr/local/bin/su-exec && rm -rf /su-exec
# grab "js-yaml" for parsing mongod's YAML config files (https://github.com/nodeca/js-yaml/releases)
ENV JSYAML_VERSION 3.12.0

RUN	wget -O /js-yaml.js "https://github.com/nodeca/js-yaml/raw/${JSYAML_VERSION}/dist/js-yaml.js"
# TODO some sort of download verification here

RUN mkdir /docker-entrypoint-initdb.d
RUN mkdir /docker-entrypoint-extras.d

ENV MONGO_PACKAGE percona-server-mongodb
ENV MONGO_MAJOR 36

# Add Percona key (https://www.percona.com/blog/2016/10/13/new-signing-key-for-percona-debian-and-ubuntu-packages/)
RUN apt-key adv --no-tty  --keyserver keyserver.ubuntu.com --recv-keys 8507EFA5
RUN echo "deb http://repo.percona.com/apt "$(lsb_release -sc)" main" | tee /etc/apt/sources.list.d/percona.list

RUN apt-get update && apt-get install -y \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-server \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-shell \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-mongos \
      ${MONGO_PACKAGE}-${MONGO_MAJOR}-tools

RUN apt-get purge -y --auto-remove wget lsb-release gnupg dirmngr git gcc make; \
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
