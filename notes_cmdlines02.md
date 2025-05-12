# More notes on command lines

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


### skopeo sync

What does the below command does? It sync the entire repo including all images and all tags. 

#### Why we need it?

useful when synchronizing a local container registry mirror or for populating registries running inside of air-gapped environments.

```bash
skopeo sync --src docker --dir registry.redhat.io/ubi9/nginx-122 /home/xpreetam/Documents/temp_enc
```
