What is a Kubernetes? The Kubernetes is a Production-Grade Container Scheduling and Management tool. So it has a lot of differences with Docker Swarm.

First of all, Kubernetes has its own primitives. Despite the fact, that Kubernetes is a container management tool, you can't run the **plain** Docker container inside Kubernetes. Sounds weird, right? In Kubernetes you can run **Pods**. Pods are the smallest deployable units of computing that you can create and manage in Kubernetes. **Important to remember**, that a Pod is a **group of one or more containers**, with shared storage and network resources, and a specification for how to run the containers. Most of the time, we can say that Pod is equal to Docker container, because the "one-container-per-Pod" model is the most common Kubernetes use case. Let's check it out in action.

> If you want to learn more, please follow the Kubernetes Documentation: [https://kubernetes.io/docs/concepts/workloads/pods](https://kubernetes.io/docs/concepts/workloads/pods/)

### Using Pods

To run the container using docker, you will create a next docker-compose file:

```yaml
version: '3.3'
services:
	app:
		image: quay.io/qwinkler/nginx:1.21
		container_name: nginx
```

Let's do the same, but with a Pod. The following manifest is an example of a Pod which consists of a container running the image `nginx:1.21`:

<pre class="file" data-filename="pod.yml" data-target="replace">
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: quay.io/qwinkler/nginx:1.21
    ports:
    - containerPort: 80
</pre>

Let's deploy this Pod. For this, we will use the `kubectl` CLI. We need the `apply` command which will simply apply our file to the Kubernetes:  

`kubectl apply --filename ./pod.yml`{{execute}}

To see the list of a Pods, simply run the following command: `kubectl get pod`{{execute}}.  
To see the detailed information about the pod, run the following command: `kubectl describe pod nginx`{{execute}}.  

Since the Pod shares storage and network resources between all of the containers, you can be very flexible with your configuration. You can initialize somehow your Pod before running the main image (using [initContainers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)), you can gracefully stop your Pod or run the command right after the Pod was started ([`preStop` and `postStart` hooks](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/#container-hooks)), and many many more. In most of the cases, you will never use it, but keep in mind, that you can do literally anything with these things.

Okay, let's verify our Pod count: `kubectl get pod`{{execute}}.  
The ready column must show `1/1`.  

The very cool feature is that we can access to the any Pod in the cluster. Let me remind you, that we are doing it from our **local machine**. It is very convinient!  
To do so, run the following command: `kubectl exec -it nginx -- bash`{{execute}}.  
Let's verify, that nginx works: `curl http://localhost`{{execute}}.  
To exit from the container simply run the `Ctrl+D` keys. We can use the `exit` command, but kotacoda may think, that we want to close current session, which is not cool.

We need to cleanup. You can delete the Pod with different maner: `kubectl delete pod nginx`{{execute}} or `kubectl delete --filename ./pod.yml`{{execute}}

So basically, you can delete the resources by their name, or delete the resources described in file. If you deploy a lot of resources, it is convinient to use the second approach, but it's up to you.

To summarize:

- Pods are the smallest deployable units of computing that you can create and manage in Kubernetes;
- Pods shares storage and network resources;
- Pod is a set of containers and it has a lot of features.
