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
# Check if the container can run as rootless. Since there is no 'user', it won't
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

- In case you wish to find
  - environmental variables
  - exposed ports
  - entrypoint

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
xpreetam at dellidali in ~
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


When you create database based on mysql-80, the database is created inside /usr/lib/data. lets verify this

```shell
# Lets create a container database

oc run mysql90 --image registry.redhat.io/rhel9/mysql-80:1-1730502686 \
--env MYSQL_USER=user1 \
--env MYSQL_PASSWORD=test123 \
--env MYSQL_DATABASE=itemsx

# verify the database dir
oc exec -it mysql90 -- ls -la /var/lib/mysql/data/itemsx
total 4
drwxr-x---. 2 1000650000 root    6 May 13 14:56 .
drwxrwxr-x. 1 mysql      root 4096 May 13 14:56 ..
```

### Check events

```shell
oc events

13s     Normal    Created          Pod/mydb9         Created container: mydb9
13s     Normal    AddedInterface   Pod/mydb9         Add eth0 [10.217.0.78/23] from ovn-kubernetes
13s     Normal    Pulled           Pod/mydb9         Container image "registry.redhat.io/rhel9/mysql-80:1-1730502686" already present on machine
13s     Normal    Started          Pod/mydb9         Started container mydb9
```
