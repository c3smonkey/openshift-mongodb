# Helm MongoDB ReplicaSet

# Install Tiller  
https://blog.openshift.com/getting-started-helm-openshift/

```bash
wget -q https://github.com/openshift/origin/raw/master/examples/helm/tiller-template.yaml

oc new-project tiller
set TILLER_NAMESPACE tiller
// oc process -f https://github.com/openshift/origin/raw/master/examples/helm/tiller-template.yaml -p TILLER_NAMESPACE=tiller -p HELM_VERSION=v2.14.0 | oc create -f -
// or
oc process -p TILLER_NAMESPACE=$TILLER_NAMESPACE -p HELM_VERSION=v2.14.0 -f tiller-template.yaml > tiller.yaml
oc create -f tiller.yaml
oc policy add-role-to-user edit \                                                                    
  "system:serviceaccount:$TILLER_NAMESPACE:tiller" \
      role "edit" added: "system:serviceaccount:tiller:tiller"

kubectl --namespace kube-system create serviceaccount tiller

kubectl create clusterrolebinding tiller-cluster-rule \
       --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

kubectl --namespace kube-system patch deploy tiller-deploy \
       -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'

```

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
helm install --name mymongo -f values.yaml stable/mongodb-replicaset
```

### Delete
```bash
helm del --purge myMongo
```

## Expose nodePort

```bash
oc expose pod mymongo-mongodb-replicaset-0 --type=NodePort
oc expose pod mymongo-mongodb-replicaset-1 --type=NodePort
oc expose pod mymongo-mongodb-replicaset-2 --type=NodePort
```
```bash
oc get svc
```

```bash
NAME                                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
mymongo-mongodb-replicaset          ClusterIP   None            <none>        27017/TCP         6h
mymongo-mongodb-replicaset-0        NodePort    172.30.12.120   <none>        27017:32587/TCP   24s
mymongo-mongodb-replicaset-1        NodePort    172.30.36.2     <none>        27017:31934/TCP   15s
mymongo-mongodb-replicaset-2        NodePort    172.30.248.61   <none>        27017:32741/TCP   10s
mymongo-mongodb-replicaset-client   ClusterIP   None            <none>        27017/TCP         6h
```
# MongoDB Connection String

```bash
mongodb://{IP-OF-THE-NODE-1}:32587,{IP-OF-THE-NODE-2}:31934,{IP-OF-THE-NODE-3}:32741/test\?replSet=rs0
```
```bash
mongodb://mymongo-mongodb-replicaset-local-storage.apps.c3smonkey.ch:32587,mymongo-mongodb-replicaset-local-storage.apps.c3smonkey.ch:31934,mymongo-mongodb-replicaset-local-storage.apps.c3smonkey.ch:32741/test\?replSet=rs0
```

Connect to PRIMARY
Reconfigure replicaSet

```bash
cfg = rs.conf();

cfg.members[0].host="mymongo-mongodb-replicaset-local-storage.apps.c3smonkey.ch:32587"
cfg.members[1].host="mymongo-mongodb-replicaset-local-storage.apps.c3smonkey.ch:31934"
cfg.members[2].host="mymongo-mongodb-replicaset-local-storage.apps.c3smonkey.ch:c"
rs.reconfig(cfg);
```

Check configuration
```bash
cfg = rs.conf();
```
