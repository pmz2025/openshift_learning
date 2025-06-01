# More notes on command lines

What is included?

- skopeo
- oc events

oc api-resources

## Skopeo commands

### skopeo list-tags

For listing tags only.

```bash

skopeo list-tags docker://quay.io/pmz2025/nginx-122
# only latest tag is seen below
{
    "Repository": "quay.io/pmz2025/nginx-122",
    "Tags": [
        "latest"
    ]
}
```

### skopeo copy

Always remember, you have to give destination only as repo name e.g. docker://quay.io/pmz2025 and anything after that is the name of the images and only latest image is copied.

```bash
skopeo copy docker://registry.redhat.io/ubi9/nginx-122 docker://quay.io/pmz2025/nginx-122

# sign the image, encryption with gpg is not working. need to see this later
skopeo copy --sign-by preetamzare@gmail.com docker://quay.io/preetamzare/nginx-122 docker://quay.io/preetamzare/nginx_encrypted-122
```

### skopeo delete

Not much here. Hence left blank.

### skopeo sync

What does the below command does? It sync the entire repo including all images and all tags.

#### Why we need it?

useful when synchronizing a local container registry mirror or for populating registries running inside of air-gapped environments.
Use this command to copy all container images from a source to a destination. T

```bash
skopeo sync --src docker --dir registry.redhat.io/ubi9/nginx-122 /home/xpreetam/Documents/temp_enc
```

### oc image

You use oc image to get

- identify the ID/hash SHA and
- to list the image layers of a container image

But you need skopeo to get all tags.

### skopei inspect

#### Why

To display information about an image from a **remote** container registry **before** you pull the image to your system.

```bash
# Check if the container can run as rootless. Since there is no 'user', it will run as root.
# by default this image will fail in OpenShift
skopeo inspect --config docker://docker.io/nginx | jq .config
{
  "ExposedPorts": {
    "80/tcp": {}
  },
  "Env": [
    "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
    "NGINX_VERSION=1.27.5",
    "NJS_VERSION=0.8.10",
    "NJS_RELEASE=1~bookworm",
    "PKG_RELEASE=1~bookworm",
    "DYNPKG_RELEASE=1~bookworm"
  ],
  "Entrypoint": [
    "/docker-entrypoint.sh"
  ],
  "Cmd": [
    "nginx",
    "-g",
    "daemon off;"
  ],
  "Labels": {
    "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
  },
  "StopSignal": "SIGQUIT"
}
```

#### When to use `--config` flag

In case you wish to find

- image name

```bash
# release version
skopeo inspect --config docker://registry.redhat.io/ubi9/httpd-24 | jq .config.Labels.release
"1747331925"

# image name
skopeo inspect --config docker://registry.redhat.io/ubi9/httpd-24 | jq .config.Labels.name
"rhel9/httpd-24"
```

- available tags (this is available only when you inspect without `--config` flag)
- environmental variables

```shell
➤ skopeo inspect --config docker://registry.redhat.io/ubi9/httpd-24 | jq .config.Env
[
  "container=oci",
  "STI_SCRIPTS_URL=image:///usr/libexec/s2i",
  "STI_SCRIPTS_PATH=/usr/libexec/s2i",
  "APP_ROOT=/opt/app-root",
  "HOME=/opt/app-root/src",
  "PATH=/opt/app-root/src/bin:/opt/app-root/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
  "PLATFORM=el9",
  "HTTPD_VERSION=2.4",
  "HTTPD_SHORT_VERSION=24",
  "NAME=httpd",
  "SUMMARY=Platform for running Apache httpd 2.4 or building httpd-based application",
  "DESCRIPTION=Apache httpd 2.4 available as container, is a powerful, efficient, and extensible web server. Apache supports a variety of features, many implemented as compiled modules which extend the core functionality. These can range from server-side programming language support to authentication schemes. Virtual hosting allows one Apache installation to serve many different Web sites.",
  "HTTPD_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/httpd/",
  "HTTPD_APP_ROOT=/opt/app-root",
  "HTTPD_CONFIGURATION_PATH=/opt/app-root/etc/httpd.d",
  "HTTPD_MAIN_CONF_PATH=/etc/httpd/conf",
  "HTTPD_MAIN_CONF_MODULES_D_PATH=/etc/httpd/conf.modules.d",
  "HTTPD_MAIN_CONF_D_PATH=/etc/httpd/conf.d",
  "HTTPD_TLS_CERT_PATH=/etc/httpd/tls",
  "HTTPD_VAR_RUN=/var/run/httpd",
  "HTTPD_DATA_PATH=/var/www",
  "HTTPD_DATA_ORIG_PATH=/var/www",
  "HTTPD_LOG_PATH=/var/log/httpd"
]
```

- exposed ports

```shell
skopeo inspect --config docker://registry.redhat.io/ubi9/httpd-24 | jq .config.ExposedPorts
{
  "8080/tcp": {},
  "8443/tcp": {}
}
# similar output is possible with image info command, just add .config

oc image info registry.redhat.io/ubi9/httpd-24:latest \
--filter-by-os linux/amd64 -o json \
| jq .config.config.ExposedPorts
{
  "8080/tcp": {},
  "8443/tcp": {}
}
```

- entrypoint

```shell
skopeo inspect --config docker://registry.redhat.io/ubi9/httpd-24 | jq .config.Entrypoint
[
  "container-entrypoint"
]

oc image info registry.redhat.io/ubi9/httpd-24:latest \
--filter-by-os linux/amd64 -o json \
| jq .config.config.ExposedPorts
```

When you compare redhat nginx image, the output is bit different

```bash
skopeo inspect --config docker://registry.access.redhat.com/ubi8/nginx-122 | jq .config | head
{
  "User": "1001", # it means, container can be rootless
  "ExposedPorts": {
    "8080/tcp": {},
    "8443/tcp": {}
  },
  "Env": [
    "container=oci",
    "STI_SCRIPTS_URL=image:///usr/libexec/s2i",
    "STI_SCRIPTS_PATH=/usr/libexec/s2i",
```

Check the configuration of bitnami image

```bash
skopeo inspect --config docker://docker.io/bitnami/mysql:5.7 | jq .config
{
  "User": "1001",
  "ExposedPorts": {
    "3306/tcp": {}
  },
  "Env": [
    "PATH=/opt/bitnami/common/bin:/opt/bitnami/mysql/bin:/opt/bitnami/mysql/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
    "HOME=/",
    "OS_ARCH=amd64",
    "OS_FLAVOUR=debian-11",
    "OS_NAME=linux",
    "APP_VERSION=5.7.43",
    "BITNAMI_APP_NAME=mysql"
  ],
  "Entrypoint": [
    "/opt/bitnami/scripts/mysql/entrypoint.sh"
  ],
  "Cmd": [
    "/opt/bitnami/scripts/mysql/run.sh"
  ],
  "Labels": {
    "com.vmware.cp.artifact.flavor": "sha256:1e1b4657a77f0d47e9220f0c37b9bf7802581b93214fff7d1bd2364c8bf22e8e",
    "org.opencontainers.image.base.name": "docker.io/bitnami/minideb:bullseye",
    "org.opencontainers.image.created": "2023-10-09T17:45:01Z",
    "org.opencontainers.image.description": "Application packaged by VMware, Inc",
    "org.opencontainers.image.licenses": "Apache-2.0",
    "org.opencontainers.image.ref.name": "5.7.43-debian-11-r73",
    "org.opencontainers.image.title": "mysql",
    "org.opencontainers.image.vendor": "VMware, Inc.",
    "org.opencontainers.image.version": "5.7.43"
  },
  "ArgsEscaped": true
}
```

### Review labels and release and name of the container image

```bash
↪ cat ~/Documents/temp_enc/info-todelete | jq .Labels.release
"1745887640"
↪ cat ~/Documents/temp_enc/info-todelete | jq .Labels.name
"rhel9/mysql-80"
↪ cat ~/Documents/temp_enc/info-todelete | jq .Digest
"sha256:95db7ace6d0635f8e0721cbdc0cab7e4cf059c8bc15c2d7711ff04a72653c5c5"
```

#### Some information about tags

The latest tag and 1 tags are alias to other tags e.g.9.6-1747641968

```shell
➤ skopeo inspect docker://registry.redhat.io/rhel9/mysql-80:1 | jq .Labels.release
"1747641968" # same release number as latest

➤ skopeo inspect docker://registry.redhat.io/rhel9/mysql-80:latest | jq .Labels.release
"1747641968" # same release number as 1


➤ skopeo inspect docker://registry.redhat.io/rhel9/mysql-80:9.6-1747641968 | jq .Labels.release
"1747641968" # <-- same release number as latest and 1

# Below is the fixed tag
➤ skopeo inspect docker://registry.redhat.io/rhel9/mysql-80:1-1730502686 | jq .Labels.release
"1730502270"
```

>Any tag which ends with `-source` is the one which provides necessary sourcces and license terms to rebuild and distribute the images.

When you create database based on mysql-80, the database is created inside /usr/lib/data. lets verify this

```shell
# Lets create a container database

oc run mysql90 --image registry.redhat.io/rhel9/mysql-80:1-1730502686 \
--env MYSQL_USER=user1 \
--env MYSQL_PASSWORD=test123 \
--env MYSQL_DATABASE=itemsx

> The best way to remember the database Dir is to remember the variable name HOME (printenv HOME)
# verify the database dir
oc exec -it mysql90 -- ls -la /var/lib/mysql/data/itemsx
total 4
drwxr-x---. 2 1000650000 root    6 May 13 14:56 .
drwxrwxr-x. 1 mysql      root 4096 May 13 14:56 ..
```

#### How to use variables inside the container

In the below example, HOME is the variable defined and is pointing to /usr/lib/

```shell
oc image info registry.redhat.io/rhel9/mysql-80:1-1730502686 \
--filter-by-os linux/amd64 -o json | jq .config.config.Env | grep HOME
# output
  "HOME=/var/lib/mysql",
# use the HOME variable to check the data folder, where database are located
oc exec -it pods/oursql -- /bin/bash -c 'ls -l $(printenv HOME)/data'
total 94668
-rw-r-----. 1 1000670000 root   196608 Jun  1 09:39 '#ib_16384_0.dblwr'
-rw-r-----. 1 1000670000 root  8585216 Jun  1 09:37 '#ib_16384_1.dblwr'
drwxr-x---. 2 1000670000 root     4096 Jun  1 09:37 '#innodb_redo'
drwxr-x---. 2 1000670000 root      187 Jun  1 09:37 '#innodb_temp'
-rw-r-----. 1 1000670000 root       56 Jun  1 09:37  auto.cnf
-rw-r-----. 1 1000670000 root     2168 Jun  1 09:37  binlog.000001
-rw-r-----. 1 1000670000 root      157 Jun  1 09:37  binlog.000002
-rw-r-----. 1 1000670000 root       32 Jun  1 09:37  binlog.index
-rw-------. 1 1000670000 root     1705 Jun  1 09:37  ca-key.pem
-rw-r--r--. 1 1000670000 root     1112 Jun  1 09:37  ca.pem
-rw-r--r--. 1 1000670000 root     1112 Jun  1 09:37  client-cert.pem
-rw-------. 1 1000670000 root     1705 Jun  1 09:37  client-key.pem
-rw-r-----. 1 1000670000 root     4095 Jun  1 09:37  ib_buffer_pool
-rw-r-----. 1 1000670000 root 12582912 Jun  1 09:37  ibdata1
-rw-r-----. 1 1000670000 root 12582912 Jun  1 09:37  ibtmp1
drwxr-x---. 2 1000670000 root      143 Jun  1 09:37  mysql
-rw-r-----. 1 1000670000 root 29360128 Jun  1 09:37  mysql.ibd
-rw-r-----. 1 1000670000 root        2 Jun  1 09:37  oursql.pid
drwxr-x---. 2 1000670000 root     8192 Jun  1 09:37  performance_schema
-rw-------. 1 1000670000 root     1705 Jun  1 09:37  private_key.pem
-rw-r--r--. 1 1000670000 root      452 Jun  1 09:37  public_key.pem
-rw-r--r--. 1 1000670000 root     1112 Jun  1 09:37  server-cert.pem
-rw-------. 1 1000670000 root     1705 Jun  1 09:37  server-key.pem
drwxr-x---. 2 1000670000 root       28 Jun  1 09:37  sys
drwxr-x---. 2 1000670000 root        6 Jun  1 09:37  transdb # <-- database i have created>
-rw-r-----. 1 1000670000 root 16777216 Jun  1 09:39  undo_001
-rw-r-----. 1 1000670000 root 16777216 Jun  1 09:39  undo_002
```

### Run command against sql database

I normallly use as any mysql container with restart option set to Never. This does not require, that you put any required environmental variables required by the container.\
Note: You will need a database IP for this to work. DNS does not work here. Why?

```shell
oc run mysqlclient --restart Never --rm -it \
--image registry.redhat.io/rhel9/mysql-80:latest \
-- mysql -h 10.217.0.103 -utransdba -ppassit -e "show databases;"
# output starts
+--------------------+
| Database           |
+--------------------+
| information_schema |
| performance_schema |
| transdb            |
+--------------------+
# output finished
```

### Check events

```shell
oc events

13s     Normal    Created          Pod/mydb9         Created container: mydb9
13s     Normal    AddedInterface   Pod/mydb9         Add eth0 [10.217.0.78/23] from ovn-kubernetes
13s     Normal    Pulled           Pod/mydb9         Container image "registry.redhat.io/rhel9/mysql-80:1-1730502686" already present on machine
13s     Normal    Started          Pod/mydb9         Started container mydb9
```
