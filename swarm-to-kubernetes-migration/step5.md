We understood how to run the simple container inside a Kubernetes cluster - using Pods! But what about scalability? Here is where Deployment primitive comes! Before I explain the Deployment, let me also tell you about Kubernetes Controllers.

The core of the Kubernetes primitives is built on top of [Controllers](https://kubernetes.io/docs/concepts/architecture/controller). The Controllers are just controlling loops. In robotics and automation, a *control loop* is a non-terminating loop that regulates the state of a system. For example, the Deployment is one of the Controllers that regulates ReplicaSet and the ReplicaSet regulates Pod replicas, states, etc. There are also other primitives in Kubernetes except for Deployment and ReplicaSet: StatefulSet, DaemonSet, Job, CronJob, and many many more. We need them for different manipulations, but the most important to remember: they are just a Controllers.

Now we are ready to explore the Deployment Controller because you will use them most of the time. The [Deployment Object](https://kubernetes.io/docs/concepts/workloads/controllers/deployment) is just like a Docker Swarm service. You define the template of a Pod that you want to create and other properties, like `replicas`, for example. Let's compare them.

To create a Docker Swarm service with `nginx` image in 3 replicas you will need to create the following manifest:

```
version: '3.3'
services:
  app:
    image: quay.io/qwinkler/nginx:1.21
    replicas: 3
```

To do the same in the Kubernetes, we need to create a Deployment object:

<pre class="file" data-filename="basic-deployment.yml" data-target="replace">
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: quay.io/qwinkler/nginx:1.21
</pre>

As you can see, our manifest file looks bigger now and we introduced `labels`. I will explain it to you. The Deployment contains the `template` section. In this section, you just define a Pod template that will be running. So, our Pods will have an image that we set, name, and labels.  
Great, but what about the `selector` and `matchLabels`? Deployment uses `labels` to identify the Pods that need to be managed. You can learn more about it [in this medium article](https://medium.com/@zwhitchcox/matchlabels-labels-and-selectors-explained-in-detail-for-beginners-d421bdd05362).

Okay, enough theory. Let's deploy our deployment: `kubectl apply -f basic-deployment.yml`{{execute}}

In this step, we created a lot of Kubernetes resources. Namely: Deployment, ReplicaSet, and Pod. Why there are so many resources if we only created a single Deployment? Let me explain:  
- `Pod`. We know what the Pod is. It is like a list of containers in a Docker Swarm, but with some peculiarities;  
- `ReplicaSet`. A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods. So, all of the dirty jobs made the ReplicaSet. But why did we use the Deployment then? We used Deployment because ReplicaSet can't change the image and the Pod template. Also, it can perform the rolling update, for example. Again, it just maintains a stable set of replica Pods running at any given time. Okay, what is the Deployment job then?
- `Deployment`. A Deployment's purpose is to maintain a set of ReplicaSets. For example, we deployed the application version 1.0.0 in a single replica. What does Kubernetes do? It created 1 Deployment object. Deployment object spawned 1 ReplicaSet object. ReplicaSet spawned 1 Pod. If we update the Deployment object (for example, we will change the image to 2.0.0), the Deployment will create a NEW Replicaset. The Deployment will scale this new ReplicaSet, so it will create another Pod. When the new Pod will be up and running, and healthcheck will is fine, the Deployment will scale down the previous ReplicaSet (just scale not delete), and the Pod count will be 1 with a new version.

Why do the Kubernetes do it in that way? So, with this approach, we can do a very fine-tuning of the Deployment behavior. For example, we can configure Deployment to scale the current ReplicaSet before creating a new Pods, or we can configure it to create ALL of the new Pods and only then remove the old ones (like blue/green deployment strategy). So, with these features, we can do a very great configuration of our deployment process.

Sorry for such a long epilogue, I guess it wasn't so boring. Let's back to our Deployment. To prove it, let's get all of the resources that Kubernetes created with our Deployment file: `kubectl get deployment,replicaset,pod`{{execute}}.  
There are 1 Deployment, 1 ReplicaSet, and 3 Pods.

Since we gave our resources the `app: nginx` label, let's filter the output in this way. Of course, it will not change anything in our case, but in real life, it must be so hard to navigate through hundreds of resources. For this, run the following command: `kubectl get deployment,replicaset,pod -l app=nginx`{{execute}}.

Good. Let's update our Deployment and see what will happen. For this, update our Deployment file:  
<pre class="file" data-filename="basic-deployment.yml" data-target="replace">
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: quay.io/qwinkler/nginx:latest
</pre>

And apply it once again: `kubectl apply -f basic-deployment.yml`{{execute}}.

We can see what is happening with Pods in real-time. For this, run: `kubectl get pod -l app=nginx --watch`{{execute}}

> Press Ctrl+C to stop the watch process

As you saw, the new Pods were created and the old ones removed. At this moment, we have 1 Deployment, 2 ReplicaSet, and 3 Pods. Let's verify:

`kubectl get deployment,replicaset,pod`{{execute}}

Looks good. Now, do the cleanup: `kubectl delete -f basic-deployment.yml`{{execute}}.

Great! You understood how to create and operate with deployments.

To summarize:

- Kubernetes has many Controllers: Deployments, ReplicaSet, and others;
- ReplicaSet controlling the Pods replicas;
- Deployment controlling ReplicaSets;
- Deployment is the most common way to deploy your application in a Kubernetes;