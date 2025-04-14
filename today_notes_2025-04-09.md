# Application Health Probes

## Readiness Probes - Checks if the container is ready to accept traffic.

Removes the pod IP from the endpoint

If the readiness probe fails, then Kubernetes prevents client traffic from reaching the application by removing the pod's IP address from the service resource.

To detect temporary issues that might affect your applications.

It is similar to the liveness probe, it can be an HTTP request, TCP check, or an exec command, but it checks the application’s readiness to serve requests.
Example Use Case: When a web server or database needs to initialize connections, or pre-load data before serving requests.

## Liveness Probes - restarts pod -Checks if the container is still running.

If an application fails its liveness probe **enough times**, then the cluster restarts the pod according to its restart policy.
The liveness probes are called after the application's initial start process.

### Use Case:

It is used to detect and remedy situations where an application is running but stuck in a **non-recoverable** state.

###  How it works:

It could be an HTTP request, TCP check, or an execution of a command within the container.

### Example Use Case:

If an application crashes or hangs, Kubernetes will detect the issue and restart the container to restore it to a working state.

### Types of Liveness Probes

Kubernetes supports four types of liveness probes:

HTTP (httpGet) Probe : Set up a lightweight /healthz endpoint for health checks to avoid performance overhead. 
TCP (tcpSocket) Probe: It does not go beyond check the port is reachable, this same as telent port 80. Only for simple application it is recommended
gRPC (grpc) Probe: This probe is typically used in microservices built with gRPC.
Command Execution (exec) Probe: It checks if the command (e.g., cat /tmp/healthy) runs successfully. This can be used for more custom checks inside the container.


> Kubernetes continues to run the Readiness and Liveness probe even after the application fails.

## Startup Probes - Checks if the container has successfully started.

A startup probe is not called after the probe succeeds. A pod is restarted based on its `restartPolicy` value.

Use Case: It is beneficial for applications that require extended startup time or have a slow initialization process.


