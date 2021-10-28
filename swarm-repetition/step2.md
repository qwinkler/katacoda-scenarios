# Deploy

To deploy your application, verify that cluster works:

`docker node ls`{{execute}}

Continue, if you see the information about your nodes. Next, deploy our application:

`docker stack deploy --compose-file=./echoserver.yml echoserver`{{execute}}

So now, we deployed the service, exposed it via port and configured healthcheck.

In order to access this application you can use the worker IP (or use the [localhost](http://localhost) since we already on the Swarm instance). To verify it works, lets describe the service status:

`docker service ls`{{execute}}

We must have `1/1` replicas. If it is, run the following command:

`curl http://localhost:8888`{{execute}}

The port is `8888` because we exposed it in our application. However, the application is running on a port `8080`, so if you want to connect to application inside a cluster, you have to use the `8080` port.

Moreover, let's discuss the internal communication between the services. In the Docker Swarm, the services have the DNS records and therefore you can communicate with a service by its name. Example:

```bash
$ curl -I http://echoserver_app:8080 | head -n1
HTTP/1.1 200 OK
```
