The environment variables are essential for a good application according to [12 factors app](https://12factor.net).

In the Docker Swarm, you had 2 choices: store env files in the docker-compose file or store them as a separate file. The most common way is to use the Secrets object and fetch the Secrets directly from this file. There are some caveats, so I will show them to you.

A [Secrets](https://kubernetes.io/docs/concepts/configuration/secret) is an object that contains a small amount of sensitive data such as a password, a token, or a key.  
In the documentation, you will find, that Secrets *intended to hold confidential data*. No, it is not! It is very important to remember:

> Caution:
Kubernetes Secrets are, by default, stored unencrypted in the API server's underlying data store.

So anyone who has the right to read Secrets object may see what's inside, so it is not about the security, it is about the convenience.

There are a lot of different [Secrets types](https://kubernetes.io/docs/concepts/configuration/secret/#secret-types), but most of the time you will use the `Opaque` type. It is like the `.env` file in the Kubernetes ecosystem. The only difference between the `.env` file and Secret object is that all of the data in the Secrets objects is stored as a base64 encoded string.

> Base64 is an encoding/decoding mechanism. The Kubernetes uses it for integrity. For example, the string '" %$.\' may cause some problems, but with base64 representation, it will look like: IiAlJC5c.

Example:

```
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: mysecret
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
```

So, if you want to store your data in Secret, it must be base64 encoded. To encode/decode the data, you can use `base64` util for Linux/Mac OS. For example, the data from the Secret above may be decrypted as: `echo -n 'YWRtaW4=' | base64 -d` and equals to `admin`. Let's learn it by example.

<pre class="file" data-filename="secret.yml" data-target="replace">
---
apiVersion: v1
kind: Pod
metadata:
  name: echoserver
  labels:
    app: echoserver
spec:
  containers:
  - name: echoserver
    image: quay.io/qwinkler/echoserver:1.10.3
    envFrom:
      - secretRef:
          name: echoserver-secret
    ports:
      - containerPort: 8080
---
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: echoserver-secret
  labels:
    app: echoserver
data:
  PORT: 8080
</pre>

Let's try to apply it: `kubectl apply -f ./secret.yml`{{execute}}

Your pod was created, but there is a problem with a Secret:  
```
... base64Codec: invalid input, error found in ...
```

As I said, we need to encode our env variables. Let's change it:  
<pre class="file" data-filename="secret.yml" data-target="replace">
---
apiVersion: v1
kind: Pod
metadata:
  name: echoserver
  labels:
    app: echoserver
spec:
  containers:
  - name: echoserver
    image: quay.io/qwinkler/echoserver:1.10.3
    envFrom:
      - secretRef:
          name: echoserver-secret
    ports:
      - containerPort: 8080
---
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: echoserver-secret
  labels:
    app: echoserver
data:
  PORT: ODA4MA==
</pre>

Apply it once again, and everything should be fine now: `kubectl apply -f ./secret.yml`{{execute}}

We created a Pod and Secret objects: `kubectl get pod,secrets -l app=echoserver`{{execute}}  
To get the Secret values, to the next: `kubectl get secrets -l app=echoserver -o yaml`{{execute}}  

To verify, that Pod works well, we need to check it's environment variables.  
- Exec to the Pod: `kubectl exec -it echoserver -- bash`{{execute}};
- Select the env variables: `env | grep "^PORT="`{{execute}};
- Press `exit`.

Great. Do the cleanup: `kubectl delete -f ./secret.yml`{{execute}}.
