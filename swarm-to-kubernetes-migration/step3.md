To interact with Kubernetes and with Pods in particular, we will use `kubectl`.  
To interact with Docker containers we will use `docker` cli.  

These both CLIs are very similar. You will use the same commands, like `run`, `exec`, `delete`, and others.

Our task is to create a Docker container and Pod with `ubuntu:20.04` image, run some commands and exit from it. Let's start with a docker:  
To run the container, simply run: `docker run --rm -it quay.io/qwinkler/ubuntu:20.04 bash`{{execute}}.  
You can execute any commands, like: `echo "hello from docker container"`{{execute}}.  
Great, type `exit` and container will be deleted, since we put the `--rm` flag.

Let's do the same in the Kubernetes:  
To run the Pod, simply run: `kubectl run --rm -it --image=quay.io/qwinkler/ubuntu:20.04 ubuntu -- bash`{{execute}}  
You can execute any commands, like: `echo "hello from Kubernetes!"`{{execute}}  
Great, type `exit` and the Pod will be deleted, since we put the `--rm` flag. As you can see, there is no much differences, to be honest.

The Pod and other Kubernetes resources may be created via CLI, but the prefered way is to use manifest file (it is like a docker-compose in a Docker ecosystem). We can see how our Pod manifest will look like. For this, simply run the following command: `kubectl run --dry-run=client --output yaml --image=quay.io/qwinkler/ubuntu:20.04 ubuntu -- bash`{{execute}}  

We just run the same command but with `dry-run` mode and made an `output` in yaml format. What is the key differences from docker-compose?  
- `apiVersion` - Is an API groups. They make it easier to extend the Kubernetes API and it is a mandatory field.  
- `kind` - The object type. Jumping ahead, there are a lot of Kubernetes objects, not just a Pods. And with `kind` we are just saying, what type of object we want to create.  
- `spec` - The object specification. Since we are creating a Pod, we put the Pod specification here.

As you can see in the object specification, there are a list of `containers` (because Pod is a list of containers), and their configuration, just like in docker-compose: image, name, resources and others. But a Pods have more differences from a containers:
- Pods have a shared storage and network resources;
- Pods have a shared specification for how to run the containers.

Most of the time, we can say that Pod is very similar to Docker container, because the "one-container-per-Pod" model is the most common Kubernetes use case.

Let's find the differences in practice!

> If you want to learn more, please follow the [Kubernetes Pod Documentation](https://kubernetes.io/docs/concepts/workloads/pods)
