# Date 28.03.2025

## Scale and Expose Applications to External Access

What is service or route?

A service defines a single IP/port combination, and provides a single IP address to a **pool of pods**, and a load-balancing client request among member pods.

By default, services connect clients to pods in a round-robin fashion. The SVC IP address comes from an internal OpenShift virtual network, is visible only to pods. Each pod that matches the **selector** is added to the service resource as an endpoint.

### Service Types

#### Cluster IP

- default
- only available inside the cluster
- Pod-to-Pod routing

#### Load Balancer

- cloud environment only

#### NodePort

- Service exposed on the port of the Node and the port is exposed to all nodes in the cluster
- It needs direct connection to the node, it is considered as security risk

#### ExternalName

- resource is external and it is denoted by externalName field in the location of the resource

### External Connectivity

Routes and services are the main resources for handling ingress traffic.

oc expose service api-frontend --hostname api.apps.acme.com

If you omit --hostname, hostname is resourceName-projectName.default-domain


### Sticky sessions

Nothing new to learn. Session must remain persistent and here it is achieved using cookie.

#### Cookie Name
RHOCP auto-generates the cookie name for ingress and route resources.
You can overwrite the default cookie name by using the annotate command with either the kubectl or the oc commands.

`oc annotate route route-example router.openshift.io/cookie_name=myapp`

Use curl command