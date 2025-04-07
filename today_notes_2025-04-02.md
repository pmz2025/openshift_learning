# Date: 02. April 2025

## Config Maps and Secrets

Objectives: Configure applications by using Kubernetes secrets and configuration maps to initialize environment variables and to provide text and binary configuration files.

- Configmaps are there to define environmental variables
- Secrets are again type of environment but sensitive information but they are not encrypted rather by default encoded. 

> To encrypt your secret data at rest, you must encrypt the Etcd database

Both of these are always associated with deployment instead of pod


### How to create config maps and secrets?

create a generic secret using key, value pairs
`oc create secret generic <name_OfThe_Secret> --from-literal key1=secret1 --from-literal key2=secret2`

create a generic secret from files

oc create secret generic ssh-keys --from-file id_rsa=<path_ToThe_File> --from-file -id_rsa.pub=<path_ToTheFile>

create a tls secret

oc create secret tls tls-test --cert=<path_ToThe_Cert> --key=<path_ToTheKey>

#### Creating configuration maps

Creating configmaps is same as secret and much simplier because there are not types in config maps

`oc create configmap <name_OfThe_Map> --from-literal key1=secret1 --from-literal key2=secret2`

 

### How to use them in a deployment

`oc set env deployment/<nameOfTheDeployment> --from cm/<nameOfTheConfigMap>` here cm stands for configMap

`oc set env deployment/<nameOfTheDeployment> --from secret/<nameOfTheSecret>` , here we are pulling a secret from secret

Now for the secrets and configmaps which are stored in a file

`oc set volume deployment/<name_OfThe_Deployment> --add --name <name_OfThe_Volume> -t configmap --configmap-name <name_OfThe_configmap> -m /<where_To_Mount>`

`oc set volume deployment/<name_OfThe_Deployment> --add --name <name_OfThe_Volume> -t secret --secret-name <name_ofThe_Secret> -m /<where_To_Mount>`

To confirm volume is attached to the deployment

oc set volume deployment/demo

```bash
oc set volume deployment/webconfig
 webconfig
  configMap/webfiles as webconfig-vol
    mounted at /var/www/html
```

Suppose you make some mistake in creating a configmap or secret, you can edit the configmap but the volume is not going to change.
You must either remove or overwrite

use case: You changed the index.html file in the configmap and now you wish to ensure it is in the container,
simply use the following command

`oc set volume  deployment/webconfig --add --overwrite --name webconfig-vol`

or there is another way

oc set data deployment/webconfig --from-file secret/<nameOfTheSecret> --from-file id_rsa=/etc/fstab

In case you wish to delete the volume

`oc set volume deployment/webconfig --remove --name webconfig-vol`


### Secrets can be ?

**Opaque**
secrets can be Opaque. It means there is validation if they are key, value pair or not.

**Service Tokens**

**Basic Authentication Secrets**

**SSH Keys**

**TLS Certificates**

**Docker Configuration Secrets**


