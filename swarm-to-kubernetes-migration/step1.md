Before going deeper with Kubernetes, let's setup an environment for that. Right now, the new Kubernetes cluster is spinning up for you.

Okay, we have a running Kubernetes cluster, but how to interact with it? The Kubernetes brain is the Kubernetes API server. Since it is a REST API and every request is going throw it, you can use any util to interact with it, but the most common one is the [kubectl](https://kubernetes.io/docs/reference/kubectl/overview) CLI tool.  
We will use the `kubectl` in this scenario aswell. So, let's get started!

Verify the cluster status: `kubectl get nodes`{{execute}}

The nodes status must be `Ready`.

If you see the following note:  
> The connection to the server localhost:8080 was refused - did you specify the right host or port?

Or this note:  
> error: no configuration has been provided, try setting KUBERNETES_MASTER environment variable

Just wait for a while. The Kubernetes cluster need some time in order to spin up.

The very important thing to remember: you always communicate with Kubernetes via REST API. So, you can (and probably you will) interact with the Kubernetes cluster **from your local machine**. There is no need to login to master nodes (as it was in the Docker Swarm).

All of your configuration stores in the `~/.kube/config` file (Linux, Mac OS). Find the file in the visual editor (above the terminal) at a path: `.kube/config`.

There are a lot of sections:  
- `clusters` - contains information about known clusters and their names. We now only have a single cluster and the master IP;
- `contexts` - contains information about clusters context: user names used to connect;
- `current-context` - contains information about current context. Since you can have a multiple clusters, you can also switch between them;
- `users` - users information. Here you define the way to connect to cluster, so Kubernetes API server may authenticate you. In this case we are using certificates, but it may be user + password or command to grabbing the token.

We are done with preparation steps. In the next steps we will understand the Kubernetes basics + we will deploy something to Kubernetes aswell using the `kubectl`.