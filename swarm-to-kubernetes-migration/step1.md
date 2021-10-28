Before going deeper with Kubernetes, let's setup an environment for that. We will be using a [Minikube](https://minikube.sigs.k8s.io/docs/start) because it is focusing on making it easy to learn and develop for Kubernetes. For you as a developer, Minikube is even to the other Kubernetes clusters.

We need to start the Minikube using the following command: `minikube start`{{execute}}.

Minikube will run the Kubernetes cluster, but how to interact with it? The Kubernetes brain is the Kubernetes API server. Since it is a REST API and every request is going throw it, you can use any util to interact with but, but the most common one is the [kubectl](https://kubernetes.io/docs/reference/kubectl/overview) CLI tool.

Let's verify the cluster status:

`kubectl get nodes`{{execute}}

The node status must be `Ready`.

The very important thing to remember: you always communicate with Kubernetes via REST API. So, you can (and probably you will) interact with the Kubernetes cluster via your local machine. There is no need to login to master nodes (as it was in the Docker Swarm).

All of your configuration stores in the `~/.kube/config` file (Linux, Mac OS). Lets inspect the file to understand it:

`cat ~/.kube/config`{{execute}}

There are a lot of sections:  
- `clusters` - contains information about known clusters and their names. We now only have a single cluster (minikube) and the master IP;
- `contexts` - contains information about clusters context: user names used to connect;
- `current-context` - contains information about current context. Since you can have a multiple clusters, you can also switch between them;
- `users` - users information. Here you define the way to connect to cluster, so Kubernetes API server may authenticate you. In this case we are using certificates, but it may be user + password or command to grabbing the token.

We are done with preparation steps. In the next steps we will understand the Kubernetes basics + we will deploy something to Kubernetes aswell using the `kubectl`.