# Notes daily

Today formatted the dell laptop from windows to linux
Install openshift local
Needs to pull all information from the old laptop

## Date: 23.03.2025

If you wish to run the command inside a container in pod, please use the following format

oc exec -it <nameofthepod> -c <nameofthecontainer> 

### On which node (worker node) the pod is running?
In the node column
```bash
$ oc get pods -o wide
NAME      READY   STATUS    RESTARTS   AGE   IP            NODE   NOMINATED NODE   READINESS GATES
mysql-8   1/1     Running   0          72m   10.217.0.79   crc    <none>           <none>
web-app   1/1     Running   0          92m   10.217.0.67   crc    <none>           <none>
```
### lsns
new command learned to which is to list namespaces, you can also use nsenter
```bash
lsns -p # where is p is PID which you get 

crictl inspect <containerid> | grep pid

nsenter -n -t <containerpid> <command e.g. ss -pants>
```

### jq and zsh

what works in bash e.g. podman inspect <image_name> | jq .[].Config may not work in zsh

```bash
podman inspect ubi | jq .[].Config # won't in zsh but in bash

# This works in zsh

[] ~ podman inspect ubi | jq '.[].Config.Env'
[
  "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
  "container=oci"
]
```

### jq and oc

There is slight difference when you use oc and jq. See the difference

```bash
~ oc image info registry.redhat.io/rhel9/mysql-80:latest --filter-by-os linux/amd64 -o json | jq '.config.config.Labels'
{
  "architecture": "x86_64",
  "build-date": "2025-03-13T17:43:18Z",
  "com.redhat.component": "mysql-80-container",
  "com.redhat.license_terms": "https://www.redhat.com/en/about/red-hat-end-user-license-agreements#rhel",
  "description": "MySQL is a multi-user, multi-threaded SQL database server. The container image provides a containerized packaging of the MySQL mysqld daemon and client application. The mysqld server daemon accepts connections from clients and provides access to content from MySQL databases on behalf of the clients.",
  "distribution-scope": "public",
  "io.buildah.version": "1.39.0-dev",
  "io.k8s.description": "MySQL is a multi-user, multi-threaded SQL database server. The container image provides a containerized packaging of the MySQL mysqld daemon and client application. The mysqld server daemon accepts connections from clients and provides access to content from MySQL databases on behalf of the clients.",
  "io.k8s.display-name": "MySQL 8.0",
  "io.openshift.expose-services": "3306:mysql", # <-- Very Important field among others>
  "io.openshift.s2i.scripts-url": "image:///usr/libexec/s2i",
  "io.openshift.tags": "database,mysql,mysql80,mysql-80",
  "io.s2i.scripts-url": "image:///usr/libexec/s2i",
  "maintainer": "SoftwareCollections.org <sclorg@redhat.com>",
  "name": "rhel9/mysql-80",
  "release": "1741887798",
  "summary": "MySQL 8.0 SQL database server",
  "url": "https://www.redhat.com",
  "usage": "podman run -d -e MYSQL_USER=user -e MYSQL_PASSWORD=pass -e MYSQL_DATABASE=db -p 3306:3306 rhel9/mysql-80",
  "vcs-ref": "2961c1255fa2797b2a148ddadec6b974699d4ca3",
  "vcs-type": "git",
  "vendor": "Red Hat, Inc.",
  "version": "1"
}
```

## Date: 24.03.2025

### How to do port forwarding using oc?

`oc port-forward <podname> serverPort:containerPort`

`oc port-forward RESOURCE EXTERNAL_PORT:CONTAINER_PORT`

### Copy files between container and container host using oc?

oc cp index.html <podname>:/var/tmp/index.html <-- copy from container host to container

In case, there are multiple containers in a need then you must using --container

oc cp index.html <podName> -c <containerName>

> Note:  If you omit the -c container_name option, then the command targets the first container in the pod.

### Events and sort-by

It is bit basic but i would like to make a note of it, because i keep forgetting

oc get events --sort-by=.metadata.creationTimestamp

#### Reference

- [Sort events and pods by their creationTimestamp in RHOCP 4.x](https://access.redhat.com/solutions/6375331)

