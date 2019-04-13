# Openshift MongoDB


[see openshift manual](https://docs.openshift.com/container-platform/3.9/using_images/db_images/mongodb.html)


## Create New Project

```bash
oc new-project mongo-demo \
         --description="MongoDB Demo" \
         --display-name="MongoDB Demo Project"
```




### Add ReplicaSet Template to OpenShift
```bash
oc create -f \
    https://raw.githubusercontent.com/sclorg/mongodb-container/master/examples/petset/mongodb-petset-persistent.yaml
```


### MongoDB Replication Example Using a StatefulSet (ex-PetSet)
[See GitHub](https://github.com/sclorg/mongodb-container/tree/master/examples/petset)

```
oc new-app https://raw.githubusercontent.com/sclorg/mongodb-container/master/examples/petset/mongodb-petset-persistent.yaml
```







# Mongo DB with Local Storage

[MongoDB Openshit](http://people.redhat.com/jrivera/openshift-docs_preview/openshift-origin/glusterfs-review/using_images/db_images/mongodb.html)

Storage :
- [install and config configuring local storage](https://docs.openshift.com/container-platform/3.11/install_config/configuring_local.html#install-config-configuring-local)
- [selector_label_binding](https://docs.okd.io/latest/install_config/persistent_storage/selector_label_binding.html)

```bash
oc new-project local-storage
```

```bash
oc create serviceaccount local-storage-admin
```
```bash
oc adm policy add-scc-to-user privileged -z local-storage-admin
```

```
oc create -f ./local-volume-config.yaml
```

```bash
oc create -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/storage-examples/local-examples/local-storage-provisioner-template.yaml
```
```bash
oc new-app -p CONFIGMAP=local-volume-config \
  -p SERVICE_ACCOUNT=local-storage-admin \
  -p NAMESPACE=local-storage \
  -p PROVISIONER_IMAGE=registry.redhat.io/openshift3/local-storage-provisioner:v3.11 local-storage-provisioner
```

```bash
oc create -f ./storage-class-ssd.yaml
```
```bash
oc create -f ./storage-class-hdd.yaml
```

### Import Template

```bash
oc create -f \
  https://raw.githubusercontent.com/openshift/mongodb/master/examples/petset/mongodb-petset-persistent.yaml
```




# Helm MongoDB ReplicaSet

[helm/README.md](helm/README.md)
