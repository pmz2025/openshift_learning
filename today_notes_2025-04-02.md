# Date: 02. April 2025

## Config Maps and Secrets

Objectives: Configure applications by using Kubernetes secrets and configuration maps to initialize environment variables and to provide text and binary configuration files.

- Configmaps are there to define environmental variables
- Secrets are again type of environment but sensitive information but they are not encrypted rather by default encoded.

> To encrypt your secret data at rest, you must encrypt the Etcd database

Both of these are always associated with deployment instead of pod

>A secret is a namespaced object and it can store any type of data.

### How to create config maps and secrets?

create a generic secret using key, value pairs

`oc create secret generic <name_OfThe_Secret> --from-literal key1=secret1 --from-literal key2=secret2`

#### Create a generic secret from files

oc create secret generic ssh-keys --from-file id_rsa=<path_ToThe_File> --from-file -id_rsa.pub=<path_ToTheFile>

#### Create a tls secret

oc create secret tls tls-test --cert=<path_ToThe_Cert> --key=<path_ToTheKey>

#### Creating configuration maps

Creating configmaps is same as secret and much simplier because there are not types in config maps

`oc create configmap <name_OfThe_Map> --from-literal key1=secret1 --from-literal key2=secret2`

 you can also use something called as --prefix i.e. you define secrets e.g.

```bash
# create generic secret
oc create secret generic mysqlcred --from-literal user=redhat --from-literal password=purplepant --from-literal database=purpledb
# > secret/mysqlcred created

# check the secret
oc get secret mysqlcred -o json
{
    "apiVersion": "v1",
    "data": {
        "database": "cHVycGxlZGI=",
        "password": "cHVycGxlcGFudA==",
        "user": "cmVkaGF0"
    },
    "kind": "Secret",
    "metadata": {
        "creationTimestamp": "2025-05-21T15:46:51Z",
        "name": "mysqlcred",
        "namespace": "myworld",
        "resourceVersion": "477799",
        "uid": "02eb35c2-864a-4d9c-9fda-a0aaba9c359c"
    },
    "type": "Opaque"
}

# create secret to store ssh keys

oc create secret generic sshdata \
--from-file ssh-privatekey=/home/xpreetam/.ssh/id_dposeidon_satorni \
--from-file ssh-public-key=/home/xpreetam/.ssh/id_dposeidon_satorni.pub

➤ oc get secrets sshdata -o json
{
    "apiVersion": "v1",
    "data": {
        "ssh-privatekey": "LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQ21GbGN6STFOaTFqZEhJQUFBQUdZbU55ZVhCMEFBQUFHQUFBQUJBcmhjdENwVQpqTk5Ld29TaEk0cWR6TkFBQUFHQUFBQUFFQUFBQXpBQUFBQzNOemFDMWxaREkxTlRFNUFBQUFJQ01KQUNWQkgyaENMRlM5CjBBTnRGY1BPWVY0REk1NTNLdUZ1ZjE4ZTNROTRBQUFBb0pUMFlyRjVVeHZ0V2k0V2F4MU4raDZ2UGwvUld3YlhNMWV4bjEKejM5K1BMVmxPRnE1QzdxeC9nRU5jb3A3c0dkYnJDVEFSakM5dUdrUU53YXE1TnNiMFowKzgxc0prTm80L0JQVjN0dnE5NgptVmg2S3pFeGpoVXEvZEZobVA2MUJpRTZuV2w0cy9TM2R2NzB0VXpBUUlTbjhuUFg2UlM4S21CQzV3bXdrcDJ1Yk5tL2VYCk9DRTZiajhoa21IS2N5a3YySGxtTzZnTHZWNm1WS2FSRnEwUVU9Ci0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=",
        "ssh-public-key": "c3NoLWVkMjU1MTkgQUFBQUMzTnphQzFsWkRJMU5URTVBQUFBSUNNSkFDVkJIMmhDTEZTOTBBTnRGY1BPWVY0REk1NTNLdUZ1ZjE4ZTNROTQgZHBvc2VpZG9uX19zYXRvcm5pMjAyNS0wNS0wOQo="
    },
    "kind": "Secret",
    "metadata": {
        "creationTimestamp": "2025-05-21T15:58:48Z",
        "name": "sshdata",
        "namespace": "myworld",
        "resourceVersion": "479295",
        "uid": "11a06615-1c07-4c38-88f5-27d0a43291d5"
    },
    "type": "Opaque"
}

```

when you attached this secret as configmap you use --prefix MYSQL_ i.e. user becomes MYSQL_user and password becomes MYSQL_password and MYSQL_DATABASE
This also means that you can use these environment variables inside the container

`mysql -u$MYSQL_USER -p$MYSQL_PASSWORD -d $MYSQL_DATABASE -e "show tables;"`

### How to use them in a deployment

``` bash
oc set env deployment/<nameOfTheDeployment> --from cm/<nameOfTheConfigMap> #here cm stands for configMap

oc set env deployment/<nameOfTheDeployment> --from secret/<nameOfTheSecret> # here we are pulling a secret from secret
```

Now for the secrets and configmaps which are stored in a file

``` bash
oc set volume deployment/<name_OfThe_Deployment> --add --name <name_OfThe_Volume> -t configmap --configmap-name <name_OfThe_configmap> -m /<where_To_Mount>

oc set volume deployment/<name_OfThe_Deployment> --add --name <name_OfThe_Volume> -t secret --secret-name <name_ofThe_Secret> -m /<where_To_Mount>
```

To confirm volume is attached to the deployment

oc set volume deployment/demo

```bash
oc set volume deployment/webconfig
 webconfig
  configMap/webfiles as webconfig-vol
    mounted at /var/www/html
```

## Updating Secrets and Configuration Maps

Suppose you make some mistake in creating a configmap or secret, you can edit the configmap but the volume is not going to change.
You must either remove or overwrite

use case: You changed the index.html file in the configmap and now you wish to ensure it is in the container,
simply use the following command

`oc set volume  deployment/webconfig --add --overwrite --name webconfig-vol`

or there is another way

oc set data deployment/webconfig --from-file secret/<nameOfTheSecret> --from-file id_rsa=/etc/fstab

In case you wish to delete the volume

`oc set volume deployment/webconfig --remove --name webconfig-vol`

### Updating Configuration Maps

It is two step process. Extract the configmap into a folder and update the desired value and then use oc set data

```shell
# extra the data of the configuration map
➤ oc extract cm/config-map-example --to /tmp/cmdata --confirm
/tmp/cmdata/database
/tmp/cmdata/user

# check the data inside the file
➤ cat /tmp/cmdata/database
itemsx

# check the data inside the file
➤ cat /tmp/cmdata/user
transdba

# lets update the user

echo customdba > /tmp/cmdata/user

# update the configuration --from-file
➤ oc set data cm/config-map-example --from-file /tmp/cmdata/user
# configmap/config-map-example data updated

# check the data is updated
➤ oc get cm config-map-example -o yaml
apiVersion: v1
data:
  database: itemsx
  user: |
    customdba # data is updated
kind: ConfigMap
metadata:
  creationTimestamp: "2025-05-22T10:31:13Z"
  name: config-map-example
  namespace: myworld
  resourceVersion: "503420"
  uid: 344da10a-23c0-4dce-8999-3eabf49ba62a

```

### Secrets can be ?

**Opaque**
secrets can be Opaque. It means there is NO validation if they are key,value pair or not.

**Service Tokens**
Storing your Service Tokens e.g. API Tokens

**Basic Authentication Secrets**
Basic authentication data. It must be base64 encoded

**SSH Keys**
Store data used for SSH Authentication

**TLS Certificates**
Store a Certificate and a Key

**Docker Configuration Secrets**
Credentials to access docker registry

### Configmaps and .sql file

Interesting thing I learned today, you can create a configmap of any file and mount them as volume, here is how it looks

```bash
# - create a config map
oc create configmap myconfigmap --from-file sample_db.sql
# - mount that as volume
oc set volumes deployment/db-pod --add --name db-pod-vol --type configmap --configmap-name myconfigmap --mount-path /var/db/config
oc rsh db-pod # <-- enter the pod
mysql -uuser1 -ppa55w0rd items </var/db/config/sample_db.sql # inserting table, interesting thing, the configmap is simply copying the file with same name
```
