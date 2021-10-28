We already learned what the `kubectl` is, what the `Pod` is and even how the `Deployment` works. How cool is that? But know, we need to expose our application. What does it mean?

As you remember, in the Docker Swarm, the services have the DNS records and therefore you can communicate with a service by its name. The Kubernetes will not create a DNS record for your services automatically. We need to do it by our own with another primitive: `Service`.

An abstract way to expose an application running on a set of Pods as a network service.  
With Kubernetes you don't need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their own IP addresses and a single DNS name for a set of Pods, and can load-balance across them.

I will give you an example:  
```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

The following example will create a Kubernetes Service object with a name `my-service`.  
Under the hood, it will create a DNS record `my-service` and the endpoints of this DNS record will be the Pods that fit the selector. So, all of the Pods, that has label `app=MyApp` will be an endpoints of the service and they will be reachable by the `my-service` DNS record.

Let's define our own example, deploy it and understand everything in action. For this, create a new manifest file:

<pre class="file" data-filename="service.yml" data-target="replace">
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: quay.io/qwinkler/nginx:1.21
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
</pre>

And apply it: `kubectl apply -f service.yml`{{execute}}.

We created a new Pod and a DNS record `nginx-service` that matches our Pod IP.  
Let's verify. For this, we need to find the Pod IP: `kubectl get pod -l app=nginx -o wide`{{execute}}.  
Great. Now, let's describe our service: `kubectl describe service -l k8s-app=tv-k8s-dashboard`{{execute}}.  
As you can see, there is the `Endpoints` section which contains our Pod IP. Great, let's test it:

`kubectl exec -it nginx -- bash`{{execute}}.  
`curl -I --silent http://nginx-service | head -n1`{{execute}}.  
`exit`{{execute}}.  

Cool. Let's modify our service a bit and I will explain you the Service details:  
<pre class="file" data-filename="service.yml" data-target="replace">
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: quay.io/qwinkler/nginx:1.21
    ports:
    - name: nginx-port
      containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 8080
      targetPort: nginx-port
</pre>

Apply the modified file: `kubectl apply -f service.yml`{{execute}}.

Now we gave the `containerPort` a name. We called it `nginx-port`. And now, the Service may use this port name instead of hardcoding it in 2 places.  
I must also give you some details about `ports` section. Ports is a list of the ports that will be opened by this service. In our case, we only opened a single port.  
Also, we specified it's parameters:
- `protocol` - Since you will connect to your Pod, you need to set the protocol with which your Pod is running (usually TCP);
- `port` - Service port. You will connect to the Service by it's DNS name (metadata.name field) + port that you specified there;
- `targetPort` - Pod's port. May be different from Service port. In our case, application is running on port `80`, but Service will be active on port `8080`, so we need to connect to the Service DNS name + Service port. Let's verify:

`kubectl exec -it nginx -- bash`{{execute}}.
`curl http://nginx-service:8080`{{execute}}.
`exit`{{execute}}.

As you can see, everything works! Do the cleanup: `kubectl delete -f service.yml`{{execute}}.

There are some restrictions in the Service names. They must be RFC 1123 compatable: [DNS Subdomain Names 
](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names).

For example, the Service name `hello-world_app` is invalid, because you can't use the `_` symbol in DNS name.

To summarize:
- Unlike the Docker Swarm, if you want to interact with Pod by DNS name, you have to create a Service object!
- Service will select the Pods by labels;
- Service port may be different from container port. Not the Pod port, because we can have multiple containers in the Pod with multiple ports;
- Not all of the names are allowed!
