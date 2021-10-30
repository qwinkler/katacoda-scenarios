In the Docker Swarm, if you want to give your application external access, you need to expose it via port (see the [`swarm-repetition` scenario](https://www.katacoda.com/qwinkler/scenarios/swarm-repetition) if you don't understand).

In a Kubernetes, we have [Ingress Object](https://kubernetes.io/docs/concepts/services-networking/ingress). An API object that manages external access to the services in a cluster, typically HTTP. Ingress may provide load balancing, SSL termination, and name-based virtual hosting.

> I would recommend you to read the official documentation for a better explanation of that.

You already what the Service and Pod are, and how they relate. Here is the diagram of Ingress and how it works:

![](./files/ingress.png)

So, it is just an entry point to your cluster. To learn the Ingress on practice, we need to configure it on our host.

> In real life, you will probably have the configured Ingress Controller + DNS. But in our sandbox environment, it will be so hard to configure everything manually. Therefore, you can review the manifest, and see how it works, but you will be unable to the the live demo. If you are doing everything on your laptop or in configured cluster - you can try to run this demo.

Create a new manifest file:

<pre class="file" data-filename="ingress.yml" data-target="replace">
---
apiVersion: v1
kind: Pod
metadata:
  name: echoserver
  labels:
    app: echoserver
spec:
  containers:
  - name: echoserver
    image: quay.io/qwinkler/echoserver:1.10.3
    envFrom:
      - secretRef:
          name: echoserver-secret
    ports:
      - containerPort: 8080
---
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: echoserver-secret
data:
  PORT: ODA4MA==
---
apiVersion: v1
kind: Service
metadata:
  name: echoserver-service
  labels:
    app: echoserver
spec:
  selector:
    app: echoserver
  ports:
    - protocol: TCP
      port: 8080
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: echoserver-ingress
spec:
  rules:
  - host: echoserver.test
    http:
      paths:
      - path: /
        backend:
          serviceName: echoserver-service
          servicePort: 8080
</pre>

Apply this manifest: `kubectl apply -f ingress.yml`

You already know what the Service, Secret, and Pod are. The only new object is Ingress. Let's dive deeper into Ingress specification.

> In a real-life, you would probably have a newer Kubernetes cluster with newer apiVersion and a little bit different spec, but the general Ingress idea is the same.

- `rules` - we have rules which will be configured by this Ingress object;
- `host` - the DNS name where your application will be available;
- `paths` - the base path where your application will be available. Maybe you want your app to be available with some prefix, like `/v1`, or `/app`;
- `backend` - Service to which traffic will be pointed;

Again, in real-life you have some DNS provider (AWS Route53 for example), and you need to configure the DNS records there. If you are running this demo on your laptop, you can configure the record in `/etc/hosts` file:
```
<worker_ip> echoserver.test
```

Verify that everything works: `curl http://echoserver.test`

Great! We can do the cleanup: `kubectl delete -f ingress.yml`
