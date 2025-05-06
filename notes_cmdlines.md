# Notes on DO180 - focused on command lines
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

### oc version
 When you run command remotely you get client version

```shell
➤ oc version
Client Version: 4.18.0-202502260503.p0.geb9bc9b.assembly.stream.el9-eb9bc9b
Kustomize Version: v5.4.2
Kubernetes Version: v1.31.6
```
When run the same command locally, you get server version

```shell
➤ oc version
Client Version: 4.18.2
Kustomize Version: v5.4.2
Server Version: 4.18.2
Kubernetes Version: v1.31.6
```

### Find the status of the clusteroperators

```shell
➤ oc status -n openshift-authentication --suggest
Warning: apps.openshift.io/v1 DeploymentConfig is deprecated in v4.14+, unavailable in v4.10000+

In project openshift-authentication on server https://api.crc.testing:6443

https://oauth-openshift.apps-crc.testing (passthrough) to pod port 6443 (svc/oauth-openshift)
  deployment/oauth-openshift deploys quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:10e985c52fa124f3224792f21f214d57aa4807dc1d6aeb4f2f6f1a05e08408eb
    deployment #6 running for 7 days - 1 pod
    deployment #5 deployed 7 weeks ago
    deployment #4 deployed 7 weeks ago
    deployment #3 deployed 7 weeks ago
    deployment #2 deployed 7 weeks ago
    deployment #1 deployed 7 weeks ago

Warnings:
  * pod/oauth-openshift-684d9f6f5c-czvkb has restarted 11 times
  * pod/oauth-openshift-684d9f6f5c-czvkb is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * pod/oauth-openshift-684d9f6f5c-czvkb is attempting to mount a missing secret secret/v4-0-config-user-template-error
  * deployment/oauth-openshift is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * deployment/oauth-openshift is attempting to mount a missing secret secret/v4-0-config-user-template-error
  * rs/oauth-openshift-58dfbc69c4 is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * rs/oauth-openshift-58dfbc69c4 is attempting to mount a missing secret secret/v4-0-config-user-template-error
  * rs/oauth-openshift-6484f8df85 is attempting to mount a missing secret secret/v4-0-config-user-template-error
  * rs/oauth-openshift-6484f8df85 is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * rs/oauth-openshift-655ccf98c6 is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * rs/oauth-openshift-655ccf98c6 is attempting to mount a missing secret secret/v4-0-config-user-template-error
  * rs/oauth-openshift-684d9f6f5c is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * rs/oauth-openshift-684d9f6f5c is attempting to mount a missing secret secret/v4-0-config-user-template-error
  * rs/oauth-openshift-7ffffb5c4b is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * rs/oauth-openshift-7ffffb5c4b is attempting to mount a missing secret secret/v4-0-config-user-template-error
  * rs/oauth-openshift-84f5fb8d77 is attempting to mount a missing secret secret/v4-0-config-user-template-provider-selection
  * rs/oauth-openshift-84f5fb8d77 is attempting to mount a missing secret secret/v4-0-config-user-template-error

View details with 'oc describe <resource>/<name>' or list resources with 'oc get all'.
```

### Explain but with --recursive

If you wish to use the code in your yaml file, you should use --recursive

here is the reason.

```shell
poseidon@satorni ~> oc explain deployment --recursive | grep -B5 -A7  rollingUpdate
        key	<string> -required-
        operator	<string> -required-
        values	<[]string>
      matchLabels	<map[string]string>
    strategy	<DeploymentStrategy>
      rollingUpdate	<RollingUpdateDeployment>
        maxSurge	<IntOrString>
        maxUnavailable	<IntOrString>
      type	<string>
      enum: Recreate, RollingUpdate
    template	<PodTemplateSpec> -required-
      metadata	<ObjectMeta>
        annotations	<map[string]string>
```

To construct a yaml file, you can easily file the indentation and relevant values

```yaml
strategy:
  rollingUpdate:
    maxSurge: 5
    maxUnavailable: 1
  type: RollingUpdate
```
Conversely, if you try below you do not get the identation but just explanations.

```bash
➤ oc explain deployment.spec.strategy
GROUP:      apps
KIND:       Deployment
VERSION:    v1

FIELD: strategy <DeploymentStrategy>


DESCRIPTION:
    The deployment strategy to use to replace existing pods with new ones.
    DeploymentStrategy describes how to replace existing pods with new ones.
    
FIELDS:
  rollingUpdate	<RollingUpdateDeployment>
    Rolling update config params. Present only if DeploymentStrategyType =
    RollingUpdate.

  type	<string>
  enum: Recreate, RollingUpdate
    Type of deployment. Can be "Recreate" or "RollingUpdate". Default is
    RollingUpdate.
    
    Possible enum values:
     - `"Recreate"` Kill all existing pods before creating new ones.
     - `"RollingUpdate"` Replace the old ReplicaSets by new one using rolling
    update i.e gradually scale down the old ReplicaSets and scale up the new
    one.
```