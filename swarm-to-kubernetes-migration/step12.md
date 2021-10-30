In a real life you will use the different [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces). So please, make sure you read this page in the docs. You can use any `kubectl` commands with namespace flag: `-n/--namespace`. To set the namespace in the manifests, set the `metadata.namespace` field.

Also, the very cool Kubernetes feature is [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap). You can create any configuration file as an object and mount it directly in your Pod. There is no need to store configuration files on a machine.

The very useful Kubernetes object is [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job). Unlike the Pod, the Job will run once and exit when the status code will be 0 (success code). You can configure its behavior using `restartPolicy` and other parameters. The Job is a good way to run a disposable application in a distributed environment. For example, you can run the database migration as a Job. When it will be successfully finished - it will never restart.

What if we need to periodically create a Job object? For example, our Job is creating a database backup, and we want to run this Job once a day. You can do this with a [CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs). It is like a highly available crontab right in your cluster. And everything is described in a declarative way. Sounds like a dream!

You can read more about different Kubernetes features to improve your application availability and convenience.

If you prefer documentation - read the [official documentation](https://kubernetes.io/docs/home).  
If you want to run the Kubernetes cluster locally, you have a several options:
- [Minikube](https://minikube.sigs.k8s.io/docs/start). It is a great util, because you can easily install the Kubernetes with all the needed features as addons (ingress, kubernetes dashboard);
- [Docker Desktop](https://birthday.play-with-docker.com/kubernetes-docker-desktop). One click installation, but you need to configure everything manually (ingress, dashboard);
