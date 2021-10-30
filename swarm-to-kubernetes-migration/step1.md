Before going deeper with Kubernetes, let's set up an environment for that. Right now, the new Kubernetes cluster is spinning up for you.

Okay, we have a running Kubernetes cluster, but how to interact with it? The Kubernetes brain is the Kubernetes API server. Since it is a REST API and every request is going throw it, you can use any util to interact with it, but the most common one is the [kubectl](https://kubernetes.io/docs/reference/kubectl/overview) CLI tool.  
We will use the `kubectl` in this scenario as well. So, let's get started!

> You can click on the command that rounded in a black box and it will be automatically executed

Verify the cluster status: `kubectl get nodes`{{execute}}

> The node's status must be `Ready`. If it is not, wait for a while, and run the command above once again

If you see the following note:  
```
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

Or this note:  
```
error: no configuration has been provided, try setting KUBERNETES_MASTER environment variable
```

Just wait for a while. The Minikube needs some time to spin up.

An important thing to remember: you always communicate with Kubernetes via REST API. So, you can (and probably you will) interact with the Kubernetes cluster **from your local machine**. There is no need to log in to master nodes (as it was in the Docker Swarm).

The Kubernetes saves your configurations in the `~/.kube/config` file (Linux, Mac OS). Find the file in the visual editor (above the terminal) at a path: `.kube/config`.

There are many sections:  
- `clusters` - contains information about known clusters and their names. We now only have a single cluster and the master IP;
- `contexts` - contains information about clusters context: usernames used to connect to the certain clusters;
- `current-context` - contains information about current context. Since you can have multiple clusters, you can also switch between them;
- `users` - users information. In this section, you define a user to connect to a cluster. Therefore the Kubernetes API server may authenticate you. In this case, we are using certificates, but it may be a user + password or command to grab the token.

We finished with the preparation steps. Right now, we will understand the Kubernetes basics. We will also deploy something to Kubernetes as well using the `kubectl`.