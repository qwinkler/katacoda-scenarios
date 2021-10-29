Congratulations! You learned a lot: Pod, Deployment, ReplicaSet, Ingress, Service, Secrets. You did a pretty good job! Now we are ready to migrate the Docker Swarm service to the Kubernetes.

Let's take the service from [`swarm-repetition` scenario](https://www.katacoda.com/qwinkler/scenarios/swarm-repetition) as an example. Here is it:  
```
# echoserver.yml
---
version: '3.3'
services:
  app:
    image: quay.io/qwinkler/echoserver:1.10.3
    env_file:
      - echoserver.env
    ports:
      - 8888:8080
    healthcheck:
      test: ["CMD", "curl", "http://localhost:8080"]
      interval: 30s
      timeout: 10s
      retries: 3
    labels:
      foo: bar
    deploy:
      mode: replicated
      replicas: 2
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
      resources:
        limits:
          cpus: '0.1'
          memory: 32M
        reservations:
          cpus: '0.1'
          memory: 32M


# echoserver.env
PORT=8080
```

The next step will have a full Kubernetes manifest. If you want to try it on your own, do it right now and compare it with the results in the next step.
Also, we didn't cover the `resources` section, since it is very familiar with Docker Swarm, and you can see the difference in the [Kubernetes Resources Documentation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers)
