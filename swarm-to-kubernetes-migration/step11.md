<pre class="file" data-filename="full.yml" data-target="replace">
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echoserver
  labels:
    app: echoserver
spec:
  replicas: 2
  selector:
    matchLabels:
      app: echoserver
  template:
    metadata:
      labels:
        app: echoserver
    spec:
      containers:
      - name: echoserver
        image: quay.io/qwinkler/echoserver:1.10.3
        livenessProbe:
          httpGet:
            path: /
            port: 8080
          periodSeconds: 30
          timeoutSeconds: 10
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /
            port: 8080
          periodSeconds: 30
          timeoutSeconds: 10
          failureThreshold: 3
        envFrom:
        - secretRef:
            name: echoserver-secret
        resources:
          limits:
            memory: 32M
            cpu: 100m
          requests:
            memory: 32M
            cpu: 100m
        ports:
        - name: echoserver-port
          containerPort: 8080
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
---
apiVersion: v1
kind: Service
metadata:
  name: echoserver-service
  labels:
    app: echoserver
spec:
  selector:
    app: echoserver
  ports:
    - protocol: TCP
      port: echoserver-port
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: echoserver-ingress
  labels:
    app: echoserver
spec:
  rules:
  - host: echoserver.test
    http:
      paths:
      - path: /
        backend:
          serviceName: echoserver-service
          servicePort: 8080
</pre>

Apply: `kubectl apply -f full.yml`{{execute}}

The only difference with Docker Swarm is that our service was available at ":8888" port. In the Kubernetes, it is not a good practice and is recommended to use DNS records: [Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview)

Get our objects: `kubectl get deployment,pod,service,secret,ingress -l app=echoserver`{{execute}}

Verify its working: `curl http://echoserver.test`{{execute}}

And the cleanup: `kubectl delete -f full.yml`{{execute}}