The Kubernetes volumes is a very powerful tool! You can mount a lot of things as a volume.

[Secrets, ConfiMaps](https://kubernetes.io/docs/concepts/storage/volumes/#projected).  
With the file below, we will create a `ConfigMap` and mount it as a volume.  
Also we will create an `emptyDir` volume. It is just an empty folder, which may be shared with other containers in a Pod.

<pre class="file" data-filename="volume.yml" data-target="replace">
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-cfg
  labels:
    app: game
data:
  game.cfg: |
    enemies=10
    level=hard
---
apiVersion: v1
kind: Pod
metadata:
  name: game
spec:
  containers:
  - name: game
    image: quay.io/qwinkler/nginx:1.21
    volumeMounts:
    - mountPath: /app/configs
      name: cfg
      readOnly: true
    - mountPath: /app/emptyDir
      name: empty
  volumes:
  - name: cfg
    configMap:
      defaultMode: 0640
      name: game-cfg
  - name: empty
    emptyDir: {}
</pre>

Apply: `kubectl apply -f volume.yml`{{execute}}

Verify the files. Exec to the Pod `kubectl exec -it game -- bash`{{execute}}, and explore the `/app` folder.  
You will see the `ConfiMap` content in the `configs` folder and an empty folder in the `emptyDir` folder.  
Type `exit` to exit and delete the Pod: `kubectl delete -f volume.yml`{{execute}}