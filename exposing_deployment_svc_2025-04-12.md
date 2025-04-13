# Exposing deployment and services

Exposing the deployment name myworld8 using the following command by directing from 8090:8080

```bash
oc expose deployment myworld8 --port 8090 --target-port 8080
oc get svc myworld8 
NAME       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
myworld8   ClusterIP   10.217.4.206   <none>        8090/TCP   3m9s
```

But this will not work as it works with docker/podman e.g. http://localhost:8090, because service is available on port 8090 at the cluster level.

You can try the following command, where you are creating runtime pod inside the namespace where the pod is deployed. It should work

`oc run test --rm -it --restart Never --image registry.redhat.io/openshift4/network-tools-rhel8 -- curl http://myworld8:8090`

Now let's expose the service, when we expose the service we are only saying, please allow this service outside the cluster. But I was wondering what is happening on port 8090? This is only the cluster aware and plays no role further.

```bash
~ ⌚ 7:46:21
$ oc expose svc myworld8 
route.route.openshift.io/myworld8 exposed

~ ⌚ 7:46:28
$ oc get route
NAME       HOST/PORT                                    PATH   SERVICES   PORT   TERMINATION   WILDCARD
myworld8   myworld8-web-applications.apps-crc.testing          myworld8   8080                 None
```
Because in the outpout above, 8080 is saying that I sending traffic to endpoints which are listening on port 8080. But then 8090 is very specific to the cluster. 
Here the route will be sending the traffic to endpoints which are exposed on 8080, which are the endpoints. You can check this using describe

```bash
$ oc describe route myworld8
Name:			myworld8
Namespace:		web-applications
Created:		3 seconds ago
Labels:			<none>
Annotations:		openshift.io/host.generated=true
Requested Host:		myworld8-web-applications.apps-crc.testing
			   exposed on router default (host router-default.apps-crc.testing) 3 seconds ago
Path:			<none>
TLS Termination:	<none>
Insecure Policy:	<none>
Endpoint Port:		8080

Service:	myworld8
Weight:		100 (100%)
Endpoints:	10.217.0.51:8080, 10.217.0.52:8080, 10.217.0.55:8080
```