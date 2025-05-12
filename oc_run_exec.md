# All oc run commands
Sunday : 11.05.2025

Primarily focused on running pods directly or interactively.

```bash
oc run myapp -it --image registry.access.redhat.com/ubi9/ubi \
--restart Never \
--command -- echo "Today is the $(date)"
```

## oc run or oc exec command can use `-it` option

You can also run the command against the container using `-c <nameoftheContainer>`

```bash
oc exec myapp -it -c myruby -- ls
```

### There is no "--" when you crictl exec -it containerid cat /etc/redhat-release

Since in oc run we get used to separate the command which needs to be 
executed inside the container using  `--` but when done with crictl you must skip it


```bash
crictl exec -it ffcdc01f497e4 cat /etc/redhat-release 
Red Hat Enterprise Linux release 9.5 (Plow)
```

Similarly, if wish to run multiple command, you should use `-c` option as shown below

```bash
oc exec -it myuser-ubi -- whoami && id
```

If you try something like, it will execute first command only

```bash
[dposeidon@satorni ~]$ oc exec -it myuser-ubi -- whoami && id
1000650000
uid=1000(dposeidon) gid=1000(dposeidon) groups=1000(dposeidon),10(wheel),987(libvirt) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```
