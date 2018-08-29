# Percona Mongo replicaset on Kubernetes using StatefulSets

Uses [cvallance/mongo-k8s-sidecar](https://github.com/cvallance/mongo-k8s-sidecar) to setup the replicaset.


Prereqs:
- a Kubernetes secret to called `mongo`. Stores the mongo cluster shared secret to be used by `--keyFile` mongod argument:  
`kubectl create secret generic mongo --from-literal=cluster-key=mysupersecretkey`
