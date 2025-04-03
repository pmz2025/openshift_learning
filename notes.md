# Notes on DO180
DO 180 stands for => Red Hat OpenShift Administration I: Operating a Production Cluster.

## Various commands learned today

oc api-resources | less

There is column called APIVERSION which divides the objects into API Groups. It uses the format
<API-Group>/<API-Version>. For kubernetes resources, API-Group is blank hence you seen --api-group=''

```bash
poseidon@delltb1542 ~/Documents » oc api-resources --api-group=''
NAME                     SHORTNAMES   APIVERSION   NAMESPACED   KIND
bindings                              v1           true         Binding
componentstatuses        cs           v1           false        ComponentStatus
configmaps               cm           v1           true         ConfigMap
endpoints                ep           v1           true         Endpoints
events                   ev           v1           true         Event
limitranges              limits       v1           true         LimitRange
namespaces               ns           v1           false        Namespace
nodes                    no           v1           false        Node
persistentvolumeclaims   pvc          v1           true         PersistentVolumeClaim
persistentvolumes        pv           v1           false        PersistentVolume
pods                     po           v1           true         Pod
podtemplates                          v1           true         PodTemplate
replicationcontrollers   rc           v1           true         ReplicationController
resourcequotas           quota        v1           true         ResourceQuota
secrets                               v1           true         Secret
serviceaccounts          sa           v1           true         ServiceAccount
services                 svc          v1           true         Service
poseidon@delltb1542 ~/Documents » 
```

`oc api-resources --namespaced=false --api-group config.openshift.io`

You can also group resources using apps

```bash
poseidon@delltb1542 ~/Documents » oc api-resources --api-group='apps'
NAME                  SHORTNAMES   APIVERSION   NAMESPACED   KIND
controllerrevisions                apps/v1      true         ControllerRevision
daemonsets            ds           apps/v1      true         DaemonSet
deployments           deploy       apps/v1      true         Deployment
replicasets           rs           apps/v1      true         ReplicaSet
statefulsets          sts          apps/v1      true         StatefulSet
```


oc api-resources --namespaced=false


Get cluster operators

oc get clusteroperators
short form is 
oc et co
oc get cluster version

## Get cluster version

oc get clusterversion

### custom resource definition e.g. crds

oc get crds | grep -i oauth

### find operators

oc get projects | grep -- '-operator'

### Find all pods with custom column

oc get pods -A -o custom-columns=Name:".metadata.name",PROJECT:".metadata.namespace",NODE:".spec.NodeName"

### Find utilization across namespaces, --sum gives sum at the end

oc adm top pods -A --sum | less

### Find log entry

oc adm node-logs master01 -u crio --tail 2

oc logs podname

oc logs --namespace <namespace_name> <nameofthepod>

oc get events -n openshift-authenticator-operator --sort-by=".lastTimestamp"


### must gather

oc must-gather --dest-dir debug_data

### debug data for specific resource

oc adm inspect <nameoftheresource> --dest-dir <nameoftheresource>
oc adm inspect clusteroperator/openshift-apiserver --dest-dir ~/openshift-apiserver

### List operators
oc get operators

### Get the containers running inside the pods
oc admin top pods <nameofthepod> -n <namespace> --containers

### Cluster info
oc cluster-info
oc get nodes
