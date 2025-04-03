htpasswd -c -B -b <file name to store HTPASSWD> <username> <password>

STEP 2: Load htpasswd file from OpenShift

In your Red Hat OpenShift, log in as one of the users with a cluster-admin privilege. Please follow these steps:

    Click Administration on the left menu
    Select Cluster Settings
    Select the Global configuration tab
    Scroll down to select OAuth
    Select the HTPASSWD option
    Select the file




### create group
oc adm groups new admins007

### add user to the group
oc adm groups add-users admins007 admin

### add role to the group

oc adm policy add-cluster-role-to-group cluster-admin admins007


## Reference: 

https://www.redhat.com/en/blog/openshift-htpasswd-oauth
https://www.tutorialworks.com/openshift-cluster-admin/