To deploy your application, we need to setup a Swarm cluster:

`docker swarm init`{{execute}}

Verify that cluster works. Run the following command and verify that node state is `Ready`:

`docker node ls`{{execute}}

Continue, if you see the information about your nodes. Next, pull the docker image (just in case):

`docker pull quay.io/qwinkler/echoserver:1.10.3`{{execute}}

You can faced with the following message:  

```
Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limit
```

Nothing to worry about, just try again in a minute or so.

And finally, deploy our application:

`docker stack deploy --compose-file=./echoserver.yml echoserver`{{execute}}

So now, we deployed the service, exposed it via port and configured healthcheck.

We need to verify, that we have `1/1` replicas:

`docker service ls`{{execute}}

In order to access this application you can use the worker/manager IP. We will save this IP into the environment variable:

`export NODE_IP=$(docker node inspect host01 | jq -r '.[0].Status.Addr')`{{execute}}

Verify it is not empty:

`echo $NODE_IP`{{execute}}

If everything is fine, run the following command:

`curl http://$NODE_IP:8888`{{execute}}

Great. You just received an information about the service deployed in the Swarm cluster!

The port is `8888` because we exposed it in our stack file. However, the application is running on a port `8080`, so if you want to connect to application inside a cluster, you have to use the `8080` port. Moreover, let's discuss the internal communication between the services. In the Docker Swarm, the services have the DNS records and therefore you can communicate with a service by its name.

Let's verify these statements. To find the service name, simply run the following command:  

`export SERVICE=$(docker service ls --format="{{ .Name }}")`{{execute}}

To see the value of this variable:

`echo $SERVICE`{{execute}}

Dive into the container:

`docker exec -e SERVICE=$SERVICE -it $(docker ps -q) bash`{{execute}}

Try to fetch the information about our service:

`curl -I --silent http://$SERVICE:8080 | head -n1`{{execute}}

If you see the `HTTP/1.1 200 OK` message - everything is fine.