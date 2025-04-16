# Requests and limits

## Date: 14.04.2025

How to view requests and limits?

```bash
$ oc adm top pods
NAME                         CPU(cores)   MEMORY(bytes)   
myworld10-6d848c5b88-wk6v4   0m           2Mi             
myworld10-6d848c5b88-xm9pc   0m           1Mi             
myworld10-6d848c5b88-xzfch   0m           1Mi 

$ oc adm top pods -n openshift-console
NAME                         CPU(cores)   MEMORY(bytes)   
console-7bdb6857d5-vhrv2     7m           114Mi           
downloads-7954f5f757-w92dw   1m           44Mi            

# utilitization across all namespaces
$ oc adm top pod --all-namespaces
```

## Image Stream

how to find images in Openshift?

with admin login

oc get images

### How to import image

oc import-image approved-apache:2.4 --from=bitnami/apache:2.4 --confirm

approved-apache is name of the image stream
2.4 is the tag
--from we do not have to provide full path, because default registries are searched for the image.

You can get more information of the image using the following command (i.e. without downloading image)

oc image-info quay.io/redhattraining/loadtest --filter-by-os linux/amd64

In the above, you do not need to download image and this output is same as podman inspect image