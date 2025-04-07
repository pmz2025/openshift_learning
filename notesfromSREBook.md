# Notes from the SRE Book

oc new-project <name_OfThe_Project> <- This will automatically switch to the new project

A Project in OpenShift is same as Namespace but project has additional annotations

All oc command supports --namespace (-n)

when you run the following command, it will spin up pod

`oc run game --image=quay.io/mdewald/s3e`

But the above command does not expose any port, to expose port

`oc port-forward game 8080`

But if you change the command from oc run game with the following, it creates deployment and pods with SCC

`oc create deployment game --image=quay.io/mdewald/s3e`

You can easily scale the deployment using 

oc scale deployment game --replica=3 && watch -g oc get pods

To access these instances you need to expose deployment as service

`oc expose deployment game --port=8080`

oc get svc, you will get cluster-ip of the service and with oc get endpoints you will get all the ips of the pods which are under the cluster-ip. Total number of IPs will match the no of replicas.

in case you just need to access this service from the local host, then you can do so using

`oc port-forward service/game 8080` <-- remember we are not YET using route. Route is way to expose service outside the cluster.

You can directly deploy the app from Github repo.

`oc new-app https://github.com/OperatingOpenShift/s3e --context-dir=highscore --name=highscore` Here the important part is `--context-dir` which denotes, there is directory under the s3e github repo.

## To Do

- [ ] Check the SCC annotation. SCC is Security Context Constraints (SCC)
- [ ] Try watch command with `-g` option 

