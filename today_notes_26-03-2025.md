# Date: 26-03-2025

## How to deploy apps from template?

Before this, let me talk about builtin template and how to use or find them
oc get template -n openshift
e.g.
`oc describe template mysql-persistent -n openshift`

Now deploy new app using template

`oc new-app -l team=blue --template mysql-persistent --param MYSQL_USER=redhat --param MYSQL_PASSWORD=redhat123`

Now check everything ran well.
oc get pods

### Get all operators
oc get clusteroperators.config.openshift.io authentication

### create a job and cronjob

Job is one time same at in linux and repeated job is exactly like cronjob.
I'm writing here because if you run the following command

`oc create job -h`, you get an simple example to use and follow

similarly, if you execute the following command

`oc create cronjob -h`, you get an example to use and follow

### Create a deployment

creating deployment very easy, but you can set the environment for the deployment

oc create deployment mysql --image=registry.redhat.io/rhel9/mysql-80

the above deployment will fail because environment variables are not for mysql.

Now can do that (normally we delete the pod and re-create), with deployment you can do this
on the fly e.g.

oc set env deployment/mysql MYSQL_USER=myhat MYSQL_PASSWORD=mypassword123 MYSQL_DATABASE=notsample

lets access the database, you need a image where mysql is present.

mysql -h <ipofthedatabase> -u myhat 

## DNS

The first entry in the search directive enables a pod to use the service name to specify another pod in the same project. 
> In RHOCP, a project is also the namespace for the pod. The service name alone is sufficient for pods in the same RHOCP project:

```bash
sh-5.1$ cat /etc/resolv.conf 
search deploy-workloads.svc.cluster.local svc.cluster.local cluster.local crc.testing
nameserver 10.217.4.10
options ndots:5
# here the project name is deploy-workloads
```

```bash
% oc get network/cluster -o json | jq '.spec'                                                                                                                                                                                                               poseidon@delltb1542
{
  "clusterNetwork": [
    {
      "cidr": "10.217.0.0/22", # pod network for all pods in the cluster.
      "hostPrefix": 23
    }
  ],
  "externalIP": {
    "policy": {}
  },
  "networkDiagnostics": {
    "mode": "",
    "sourcePlacement": {},
    "targetPlacement": {}
  },
  "networkType": "OVNKubernetes",
  "serviceNetwork": [
    "10.217.4.0/23" #service network for all services in the cluster
  ]
}
```