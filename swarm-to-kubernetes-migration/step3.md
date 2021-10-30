To interact with Kubernetes and with Pods, in particular, we will use `kubectl`.  
To interact with Docker containers, we will use `docker` CLI.  

These both CLIs are very similar. You will use the same commands, like `run`, `exec`, and others.

Our task is to create a Docker container and Pod with the `ubuntu:20.04` image, run some commands, and exit from it. Let's start with a docker.

To start the container, run: `docker run --rm -it quay.io/qwinkler/ubuntu:20.04 bash`{{execute}}.  
You can execute any commands, like: `echo "hello from docker container"`{{execute}}.  
Great. Type `exit` to leave the container, and the container will be deleted, since we put the `--rm` flag.

Let's do the same in the Kubernetes.

To start the Pod, run: `kubectl run --generator=run-pod/v1 --rm -it --image=quay.io/qwinkler/ubuntu:20.04 ubuntu -- bash`{{execute}}  

> We are using the `generator` flag because the Kubernetes version in Katacoda is outdated. You don't need this flag anymore to run this command.

You can execute any commands, like: `echo "hello from Kubernetes!"`{{execute}}  
Great. Type `exit` to leave the container. The container will automatically be deleted since we put the `--rm` flag. As you can see, there are not many differences, to be honest.

> Use the `clear`{{execute}} command if you need to cleanup the terminal.

You can create a Pod and other Kubernetes resources via CLI, but the preferred way is to use a manifest file (it is like a docker-compose in a Docker ecosystem). We can see how our Pod manifest will look. For this, run the following command: `kubectl run --generator=run-pod/v1 --dry-run --output yaml --image=quay.io/qwinkler/ubuntu:20.04 ubuntu -- bash`{{execute}}  

We executed the same command but with `dry-run` mode and made an `output` in YAML format. What are the differences from docker-compose?  
- `apiVersion` - Is an API group. They make it easier to extend the Kubernetes API, and it is a mandatory field.  
- `kind` - The object type. Jumping ahead, there are a lot of Kubernetes objects, not just Pods. And with kind, we are just saying what type of object we want to create.  
- `spec` - The object specification. Since we are creating a Pod, we put the Pod specification here.

As you can see in the object specification, there is a list of `containers` (because Pod is a list of containers) and their configuration, just like in docker-compose: image, name, resources, and others. But a Pods have more differences from containers:
- Pods have shared storage and network resources;
- Pods have a shared specification for how to run the containers.

Most of the time, we can say that Pod is very similar to Docker container because the "one-container-per-Pod" model is the most common Kubernetes use case.

Let's find the differences in practice!

> If you want to learn more, please follow the [Kubernetes Pod Documentation](https://kubernetes.io/docs/concepts/workloads/pods)
