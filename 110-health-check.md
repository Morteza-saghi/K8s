# Kubernetes Health Checks: Liveness and Readiness Probes

In Kubernetes (K8s), **readiness** and **liveness** probes are used to check the health of containers running inside pods. These probes help Kubernetes to manage and monitor the lifecycle of containers, ensuring that applications are functioning correctly.


---

## Liveness Probe

The **liveness probe** checks if a container is still running. If the liveness probe fails, Kubernetes will automatically restart the container. This is useful for detecting situations where the application might be alive but stuck (e.g., in an infinite loop or deadlock).

### How Liveness Probe Works:
- Kubernetes periodically checks the container's health using the liveness probe.
- If the probe fails for a set number of attempts, Kubernetes restarts the container.

### Example Configuration for a Liveness Probe:

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5  # Time to wait before starting the probe
  periodSeconds: 10       # How often to perform the probe
  failureThreshold: 3     # Number of failed attempts before restarting the container
```
#### Types
- httpGet: Sends an HTTP GET request to the /healthz endpoint on port 8080.
- initialDelaySeconds: Time to wait before starting the liveness probe after the container starts.
- periodSeconds: How frequently the probe runs.
- failureThreshold: Number of failed probes before the container is considered unhealthy and gets restarted.


---

### Readiness Probe
The readiness probe checks if a container is ready to accept traffic. If the readiness probe fails, Kubernetes stops sending traffic to the container, but it does not restart the container. This is useful during initialization or if the container experiences temporary issues.

How Readiness Probe Works:
Kubernetes periodically checks if the container is ready to handle incoming traffic.
If the probe fails, Kubernetes marks the container as unready and stops routing traffic to it, without restarting the container.
Example Configuration for a Readiness Probe:

```yml
readinessProbe:
  httpGet:
    path: /readiness
    port: 8080
  initialDelaySeconds: 5  # Time to wait before starting the probe
  periodSeconds: 10       # How often to perform the probe
  failureThreshold: 3     # Number of failed attempts before considering the container unready
```
#### Types

- httpGet: Sends an HTTP GET request to the /readiness endpoint on port 8080.
- initialDelaySeconds: Time to wait before starting the readiness probe after the container starts.
- periodSeconds: How frequently the probe runs.
- failureThreshold: Number of failed probes before the container is marked as unready.


### Key Differences Between Liveness and Readiness Probes
Probe Type	Purpose	Action if Probe Fails
Liveness Probe	Ensures the container is running	Restarts the container
Readiness Probe	Ensures the container is ready to handle traffic	Stops routing traffic to the container (does not restart)
Liveness Probe: Used to detect if a container is still alive and functioning. If it fails, Kubernetes will restart the container.
Readiness Probe: Used to check if a container is ready to handle traffic. If it fails, traffic is stopped from being routed to the container, but it is not restarted.

### Probe Types
#### Both probes can use different types of checks:

- HTTP GET: Sends an HTTP GET request to a specific endpoint in the container.
- TCP Socket: Attempts to establish a TCP connection to the container.
- Exec: Executes a command inside the container.


### Example of Pod Configuration with Both Probes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  containers:
  - name: myapp-container
    image: myapp-image
    ports:
    - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
      failureThreshold: 3
    readinessProbe:
      httpGet:
        path: /readiness
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
      failureThreshold: 3
```

### To Sum Up

Liveness Probe: The container will be restarted if it fails to respond to /healthz.
Readiness Probe: Traffic will be stopped to the container if it fails to respond to /readiness.

### Conclusion

Liveness Probes: Used to determine if a container is running. If it fails, Kubernetes will restart the container.

Readiness Probes: Used to determine if a container is ready to accept traffic. If it fails, Kubernetes stops sending traffic to the container but does not restart it.

Proper configuration of these probes ensures that Kubernetes can efficiently manage containers and maintain application availability.
