What is a Kubernetes? The Kubernetes is a Production-Grade Container Scheduling and Management tool. So it has a lot of differences with Docker Swarm.

First of all, Kubernetes has its own primitives. Funny fact: despite that Kubernetes is a container management tool, you can't run the **plain** Docker container inside Kubernetes. Sounds weird, right? In Kubernetes you can run **Pods**. Pods are the smallest deployable units of computing that you can create and manage in Kubernetes. Generally speaking, a Pod is a **group of one or more containers**. Let's dive into it and compare it with a Docker container.

### Pod vs Docker container

To interact with Kubernetes and with Pods in particular, we will use `kubectl`.  
To interact with Docker containers we will use `docker` cli.  

Our task is to create a Docker container and Pod with `quay.io/qwinkler/ubuntu:20.04` image, run some commands and exit from it. Let's start with a docker:  
To run the container, simply run: `docker run --rm -it quay.io/qwinkler/ubuntu:20.04 bash`{{execute}}.  
You can execute any commands, like: `echo "hello from docker container"`{{execute}}.  
Great, type `exit` and container will be deleted, since we put the `--rm` flag.

Let's do the same in the Kubernetes:  
To run the Pod, simply run: `kubectl run --rm -it --image=quay.io/qwinkler/ubuntu:20.04 ubuntu  -- bash`{{execute}}.  
You can execute any commands, like: `echo "hello from Kubernetes!"`{{execute}}.  
Great, type `exit` and container will be deleted, since we put the `--rm` flag. As you can see, there is no much differences, to be honest.

The Pod and other Kubernetes resources may be created via CLI, but the prefered way is to use manifest file (like docker-compose). We can see how our Pod manifest will look like. For this, simply run the following command: `kubectl run --dry-run --output yaml --image=quay.io/qwinkler/ubuntu:20.04 ubuntu -- bash`{{execute}}.  

We just run in the `dry-run` mode the following command and made an output in yaml format. What is the key differences from docker-compose?  
- `apiVersion` - Is an API groups. They make it easier to extend the Kubernetes API and it is a mandatory field.  
- `kind` - The object type. As I breifely mentioned earlier, there are a lot of Kubernetes objects, not just a Pods. And with `kind` we are just saying, what type of object we want to create.  
- `spec` - The object specification. Since we are creating a Pod, we put the Pod specification here.

As you can see in the object specification, there are a list of `containers` (because Pod is a list of containers), and their configuration, just like in docker-compose: image, name, resources and others. But a Pods have more differences from a containers:
- Pods have a shared storage and network resources;
- Pods have a shared specification for how to run the containers.

Most of the time, we can say that Pod is very similar to Docker container, because the "one-container-per-Pod" model is the most common Kubernetes use case.

Let's find the differences in practice!

> If you want to learn more, please follow the [Kubernetes Pod Documentation](https://kubernetes.io/docs/concepts/workloads/pods)

### Using Pods

The following manifest is an example of a Pod which consists of a **single** container running the image `nginx:1.21`:

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

Let's deploy this Pod. We need the `apply` sub-command which will simply apply a configuration to a resource by filename or stdin:  

`kubectl apply --filename ./pod.yml`{{execute}}

To see the list of a Pods, simply run the following command: `kubectl get pod`{{execute}}.  
To see the detailed information about the Pod, run the following command: `kubectl describe pod nginx`{{execute}}.  

Since the Pod shares storage and network resources between all of the containers, you can be very flexible with your configuration. You can pre-initialize your Pod before running the main image using [initContainers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/). You can gracefully stop your Pod or run the command right after the Pod was started using [`preStop` and `postStart` hooks](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/#container-hooks), and many many more. In most of the cases, you will never use it, but keep in mind, that you can do literally everything to run your application smoothly in the Kubernetes cluster. For all of the available options please read the Kubernetes Documantation.

Okay, let's verify our Pod status: `kubectl get pod`{{execute}}.  
The ready column must show `1/1`.  

We can access to the any Pod in the cluster using `exec` command right from our local machine. No matter on which node the Pod is running.  
To do so, run the following command: `kubectl exec -it nginx -- bash`{{execute}}.  
Let's verify, that nginx works: `curl http://localhost`{{execute}}.  
To exit from the container run the `exit` command.

Cleanup. You can delete the Pod with different maner:  
- Delete the resources by their kind + name: `kubectl delete pod nginx`{{execute}};
- delete the resources by filenames: `kubectl delete --filename ./pod.yml`{{execute}}.

If you deploy a lot of resources, it is convinient to use the second approach, but it's up to you.

To summarize:

- Pods are the smallest deployable units of computing that you can create and manage in Kubernetes;
- Pods shares storage and network resources;
- Pod is a set of containers and it has a lot of features.
