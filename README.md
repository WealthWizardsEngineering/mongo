# percona-mongodb-36

## A fork of [Docker "Official Image"](https://docs.docker.com/docker-hub/official_repos/) for [mongo](https://hub.docker.com/_/mongo/)

This is a fork of [Docker "Official Image"](https://docs.docker.com/docker-hub/official_repos/) for [mongo](https://hub.docker.com/_/mongo/) (not to be confused with any official mongo image provided by mongo upstream). See [the Docker Hub page](https://hub.docker.com/_/mongo/) for the full readme on how to use this Docker image and for information regarding contributing and issues.

The [full description from Docker Hub](https://hub.docker.com/_/mongo/) is generated over in [docker-library/docs](https://github.com/docker-library/docs), specifically in [docker-library/docs/mongo](https://github.com/docker-library/docs/tree/master/mongo).

## What's different from [Docker "Official Image"](https://docs.docker.com/docker-hub/official_repos/) for [mongo](https://hub.docker.com/_/mongo/):
The main goal is to build **percona-mongodb-36** so it can be run on **Kubernetes** as a stateful set.
`docker pull quay.io/wealthwizards/percona-mongodb-36:latest`

  * Has an additional folder `/docker-entrypoint-extras.d/` where `.sh` scripts can be added and ran at each container startup
    For example: set correct permissions on `keyFile` when is stored on a Kube volume used in conjuction with:
	```
	  securityContext:
        runAsUser: 999
        fsGroup: 999
	```	
  * Removes `--keyFile` and `--clusterAuthMode` during init if they're passed to `mongod`
  * Mongo init sequence: shutdown changed to `mongod --pidfilepath="$pidfile" --shutdown` from `"$@" --pidfilepath="$pidfile" --shutdown`
    (original command causes a conflict if `--pidfilepath` is passed to `mongod`)
  * Build from: `debian:stretch-slim`