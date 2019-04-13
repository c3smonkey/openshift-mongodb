# Helm MongoDB ReplicaSet

## Add Repo
```bash
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Download _values.yaml_

```bash
http https://raw.githubusercontent.com/helm/charts/master/stable/mongodb-replicaset/values.yaml --download                                                   
```
Change _openshift_ SecurityContext configuration _runAsUser_  to _1000320000_

```bash
securityContext:
  enabled: true
  runAsUser: 1000320000
  fsGroup: ""
  runAsNonRoot: true  
```

## install
```bash
helm install --name my-release -f values.yaml stable/mongodb-replicaset
```

### Delete
```bash
helm del --purge my-release
```

## Expose nodePort

```bash
oc expose pod my-release-mongodb-replicaset-0 --type=NodePort
oc expose pod my-release-mongodb-replicaset-1 --type=NodePort
oc expose pod my-release-mongodb-replicaset-2 --type=NodePort
```
```bash
oc get svc
```

```bash
NAME                                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
my-release-mongodb-replicaset          ClusterIP   None            <none>        27017/TCP         6h
my-release-mongodb-replicaset-0        NodePort    172.30.12.120   <none>        27017:32587/TCP   24s
my-release-mongodb-replicaset-1        NodePort    172.30.36.2     <none>        27017:31934/TCP   15s
my-release-mongodb-replicaset-2        NodePort    172.30.248.61   <none>        27017:32741/TCP   10s
my-release-mongodb-replicaset-client   ClusterIP   None            <none>        27017/TCP         6h
```
# MongoDB Connection String

```bash
mongodb://{IP-OF-THE-NODE-1}:32587,{IP-OF-THE-NODE-2}:31934,{IP-OF-THE-NODE-3}:32741/test\?replSet=rs0
```
```bash
mongodb://my-release-mongodb-replicaset-local-storage.apps.c3smonkey.ch:32587,my-release-mongodb-replicaset-local-storage.apps.c3smonkey.ch:31934,my-release-mongodb-replicaset-local-storage.apps.c3smonkey.ch:32741/test\?replSet=rs0
```

Connect to PRIMARY
Reconfigure replicaSet

```bash
cfg = rs.conf();

cfg.members[0].host="my-release-mongodb-replicaset-local-storage.apps.c3smonkey.ch:32587"
cfg.members[1].host="my-release-mongodb-replicaset-local-storage.apps.c3smonkey.ch:31934"
cfg.members[2].host="my-release-mongodb-replicaset-local-storage.apps.c3smonkey.ch:c"
rs.reconfig(cfg);
```

Check configuration
```bash
cfg = rs.conf();
```
