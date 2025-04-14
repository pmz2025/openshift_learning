# Notes - Date : 07-04-2025

Storage class

- Administrator selects the default storage class for dynamic provisioning. When no storage class is selected while creating PVC, default storage class is selected.
- Because an administrator can change the default storage class, a developer should explicitly set the storage class for an application.

### Reclaim Policy

- delete - data is deleted, the moment 
- retain - The delete reclaim policy is the default setting for all storage provisioners that adhere to the Kubernetes Container Storage Interface (CSI) standards.

### Create a PVC using command line

```bash
oc set volumes deployment/db-pod --add --name db-pod-vol \
    --claim-size 1Gi \
    --claim-mode rwo \
    --claim-class lvms-vg1 \
    --mount-path /var/lib/db
    --claim-name db-pod-pvc
```

Now use pvc to create pv and attach it to the deployment

oc set volumes deployment/db-pod --add --volume db-pod-volume --claim-name db-pod-pvc --mount-path /var/lib/db


### Create pvc claim and attach it as volume

In case you wish to attach existing PVC to another pod, simple refer --name --claim-name --type pvc --mount-path (do not forget the path)