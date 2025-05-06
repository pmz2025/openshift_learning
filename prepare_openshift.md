# Steps to prepare Openshift deployment on local workstations

## install oc command on remote machine

### Download oc.tar 


mkdir -pv $HOME/bin
fish_add_path /home/xpreetam/bin
cd $HOME/Downloads && curl -vOL https://downloads-openshift-console.apps-crc.testing/amd64/linux/oc.tar
tar -xvf oc.tar -C $HOME/bin
oc login -u developer -p developer https://api.crc.testing:6443