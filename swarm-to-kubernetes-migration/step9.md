Healthcheck is a very important part of the Kubernetes. However, the health check in Docker is very limited. You can just execute a command inside your container. In Kubernetes, you have more things to configure.

The Kubernetes has special configurations called Probes. We have 3 probe types:
- `liveness`. We need it to know when to restart a container;
- `readiness`. We need to know when your container is ready to receive traffic. For example, an application might need to load large data or configuration files during startup or depend on external services after startup. In such cases, you don't want to kill the application, but you don't want to send it requests either. Jumping ahead, the Service will add the Pod IP to a list of Endpoints only when the readiness probe will succeed;
- `startup`. Kubernetes uses startup probes to know when a container application has started. Sometimes, your application may have a slow start, but when it's loaded, it works pretty fast. For this type of application, you need to configure the `startup` probe.

Okay, let's compare it with the Docker health check:
```
version: '3.3'
services:
  app:
    image: quay.io/qwinkler/echoserver:1.10.3
    healthcheck:
      test: ["CMD", "curl", "http://localhost:80"]
      interval: 30s
      timeout: 10s
      retries: 3
```

To do the same in Kubernetes, we don't even need to have the `curl` command in our image:

```
apiVersion: v1
kind: Pod
metadata:
  name: echoserver
spec:
  containers:
  - name: echoserver
    image: quay.io/qwinkler/echoserver:1.10.3
    livenessProbe:
      httpGet:
        path: /
        port: 80
      periodSeconds: 30
      timeoutSeconds: 10
      failureThreshold: 3
```

To see how to configure other probes, please refer to [Kubernetes Probes Documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes)
