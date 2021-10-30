The following manifest is an example of a Pod consisting of a **single** container running the image `nginx:1.21`.  
Create this file in your editor.

> Click on "Copy to Editor" button at the right upper corner of the file content. The file with required name will be created and saved.

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

Let's deploy this Pod. We need the `apply` sub-command, which will apply a configuration to a resource by filename or stdin:  

`kubectl apply --filename ./pod.yml`{{execute}}

To see the list of Pods, run: `kubectl get pod`{{execute}}.  
To see the detailed information about the Pod, run: `kubectl describe pod nginx`{{execute}}.  

Okay, let's verify our Pod status: `kubectl get pod`{{execute}}.  

> The ready column must show `1/1`. If it is not, just wait for a while and try again.

We can access any Pod in the cluster using the `exec` command from our local machine, no matter which node the Pod is running.  
To do so, run the following command: `kubectl exec -it nginx -- bash`{{execute}}.  
Let's verify that nginx works: `curl http://localhost`{{execute}}.  
To exit from the container, run the `exit` command.

Cleanup. You can delete the Pod differently:  
- Delete the resources by their kind + name: `kubectl delete pod nginx`{{execute}};
- Delete the resources by filenames: `kubectl delete --filename ./pod.yml`{{execute}}.

> If you deploy many resources, it is convenient to use the second approach.

With Kubernetes, you can pre-initialize your Pod before running the main image using [initContainers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/). You can gracefully stop your Pod or run the command right after the Pod was started using [`preStop` and `postStart` hooks](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/#container-hooks), and many many more. In most cases, you will rarely use it, but keep in mind that you can do everything to run your application smoothly in the Kubernetes cluster. For all of the available options, please read the Kubernetes Documentation.

To summarize:

- Pods are the smallest deployable units of computing that you can create and manage in Kubernetes;
- Pods shares storage and network resources;
- Pod is a set of containers, and it has many features.

